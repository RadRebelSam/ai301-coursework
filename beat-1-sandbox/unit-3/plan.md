# Plan: fix the Redis probe in `GET /health` (issue #62)

## Diagnosis

The Redis probe never reaches Redis. `health_check()` in
`api/routes/health.py` builds its client from fields that do not exist on
`Settings`:

```python
r = redis.Redis(
    host=settings.redis_host,
    port=settings.redis_port,
    ...
)
```

`core/config.py:14` defines one Redis field, `redis_url`. So attribute access
raises before `r.ping()` runs, the surrounding `except Exception` swallows it,
and the endpoint reports Redis down.

My reproduction (posted on the issue,
[comment 5804855385](https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62#issuecomment-5804855385))
pins this down. Redis was up:

```
$ docker exec pathreview-ai301-fa26-s1-redis-1 redis-cli ping
PONG
```

and `/health` still reported it down:

```
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},...}}
```

with the log line naming the attribute:

```
[error] redis_health_check_failed error="'Settings' object has no attribute 'redis_host'"
```

The control run in that reproduction rules out a connection problem: using the
field `Settings` actually defines, from the same process and the same config
object, Redis answers.

```
redis_url = redis://localhost:6379/0
has redis_host: False
ping: True
```

So the fault is the attribute names at the call site, not the client, the
network, or the container.

## Scope

**In scope:** the Redis client construction inside `health_check()`, so the
probe uses `settings.redis_url`.

**Not in scope, deliberately:**

- The `postgres` probe's `AttributeError`-adjacent failure
  (`Textual SQL expression 'SELECT 1' should be explicitly declared as
  text('SELECT 1')`). That is issue #61 and it has its own fix.
- The broad `except Exception` blocks in this route that hid the bug in the
  first place. Narrowing them is a real improvement and a separate change;
  widening this PR to include it would mix a one-line correctness fix with a
  design change to every probe in the file.
- The duplicate `ix_profiles_user_id` index in `core/models/profile.py` that
  breaks `create_all` on a fresh database. Found while setting up; unrelated to
  this issue.
- Adding `redis_host` / `redis_port` fields to `Settings`. The issue states
  `redis_url` is the field the probe should use, and adding the two missing
  fields would leave two ways to configure one dependency.

## Files

- `api/routes/health.py` - the Redis block inside `health_check()` (the
  `redis.Redis(...)` call).
- `tests/unit/test_health.py` - new, one regression test (see Test plan).

## Approach

Replace the host/port construction with `redis.from_url(settings.redis_url)`,
keeping `decode_responses=True` and the existing `r.ping()`. `from_url` is the
redis-py constructor that takes exactly the string `Settings` carries, so the
change is the call site only - no new config fields, no new dependency, no
change to the probe's control flow or to what the endpoint returns.

## Test plan

Two observations, both from the reproduction's own steps.

1. **Re-run the repro.** With Redis up (`redis-cli ping` returning `PONG`), call
   `GET /health` and read `dependencies.redis`.
   - Before: `"redis": "unhealthy"`, and the log carries
     `redis_health_check_failed error="'Settings' object has no attribute 'redis_host'"`.
   - After: `"redis": "healthy"`, and no `redis_health_check_failed` line for
     that request id.
   - Note: the response stays 503 overall because `postgres` is still unhealthy
     via #61, which is out of scope here. The observable for this fix is the
     `redis` key and the disappearance of that log line, not the status code.
2. **Regression test.** `tests/unit/test_health.py` asserts the probe reads
   `settings.redis_url` - it patches `redis.from_url` and checks it was called
   with the configured URL, so the test fails again if the call site goes back
   to `redis_host`. Runs without a live Redis, so CI catches a regression.

## Risks and unknowns

- `from_url` parses the scheme, so a `rediss://` (TLS) or a URL with
  credentials behaves differently from the old host/port pair. For the
  configured default `redis://localhost:6379/0` the behavior is the same; I have
  not tested a TLS URL.
- The old code passed `db=0` explicitly. `redis://localhost:6379/0` carries the
  same database in the path, but a deployment whose `REDIS_URL` omits the
  database would now use the URL's default rather than a forced `0`. I judged
  honoring the configured URL to be the intended behavior, since that is the
  field the issue says to use.
- I could not run `make setup` as documented (the duplicate-index bug above), so
  my environment came up via `alembic upgrade head`. That affects how the schema
  was created, not the Redis probe.

## Deviations

Nothing in the plan changed during the build. The fix is the one call site
this plan named, the test plan ran as written, and both observables came out
as predicted: `dependencies.redis` went from `"unhealthy"` to `"healthy"`, the
`redis_health_check_failed` line stopped appearing (2 occurrences before, 0
after), and the response stayed 503 because `postgres` is still failing via
issue #61.

How I know nothing changed: I captured the before by stashing the fix and
running the same `GET /health` against the same containers, then restoring it
and running again in the same session, so the two outputs differ only by this
change. The regression test was checked in both directions as well - it passes
with the fix and fails with the fix stashed, so it is testing the call site and
not just passing by construction.

**Added during unit 4 submission prep, after the plan was posted:** the
`attr-defined` code was removed from the `api.routes.health` mypy override in
`pyproject.toml`, and the regression test gained `@pytest.mark.unit`.

The suppression is this bug: the comment above it in `pyproject.toml` names
issue #62 and says "Fixing a seeded bug should remove its entry here, the same
way it removes the test's xfail marker." The plan did not name it because I had
not read that part of the repo's contributing docs when I wrote the plan. With
the fix in place `mypy api/ core/ ingestion/ rag/ agent/ safety/` reports
`Success: no issues found in 76 source files`, so the suppression is no longer
load-bearing. The module's other two codes (`call-overload`, `index`) stay,
because they belong to issue #61, which this change leaves alone. The marker on
the test is what makes `make test-unit` (`pytest tests/unit -m unit`) collect
it; without it the test existed but never ran in the repo's own check.

Two environment notes that do not affect the change:

- The `db=0` risk I flagged did not materialize. The configured
  `redis://localhost:6379/0` names database 0 in its path, so `from_url`
  selects the same database the old explicit `db=0` did.
- I hit the duplicate-index bug again while rebuilding the database, and again
  ran `alembic upgrade head` instead of `make setup`. On this machine `alembic`
  also had to be imported before changing into the repo, because the repo's own
  `alembic/` package shadows the installed one. Neither touches the fix.
