# Unit 2 - Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of my claim and reproduction on the issue I chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`.

---

## Your identity upstream

**GitHub username**

RadRebelSam

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62#issuecomment-5804854017

I'd like to investigate this one as my first contribution here.

What I plan to check: `health_check()` in `api/routes/health.py` builds its Redis client with `host=settings.redis_host, port=settings.redis_port`, and `Settings` in `core/config.py` defines only `redis_url`. So the probe should raise `AttributeError: 'Settings' object has no attribute 'redis_host'` before it ever pings Redis, get swallowed by the `except Exception` around it, and report `"redis": "unhealthy"` with a 503 while Redis is actually up.

My next step is to reproduce that on a fresh clone: bring up Redis with `docker compose up -d`, confirm it answers `PING` directly, then call `GET /health` and post what I get back - the response body, the status code, and the `redis_health_check_failed` log line with its error string. I'll report what I find either way, including if the failure turns out to have a different cause than the one in the issue.

I'm new to this codebase, so I'm not promising a timeline or a fix - just the reproduction and what the logs show.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62#issuecomment-5804855385

Reproduced on a fresh clone at `f89c06f`. The Redis probe never reaches Redis: it raises `AttributeError` on `settings.redis_host`, the surrounding `except Exception` swallows it, and `/health` reports Redis as down while Redis is up.

**Environment:** Windows 10 (10.0.19045), Python 3.11.3, `redis` 8.1.0 (pip), Redis 7.4.9 and PostgreSQL 16 in Docker via the repo's `docker-compose.yml`, app run with `uvicorn api.main:app --host 127.0.0.1 --port 8000`.

**Steps**

1. `docker compose up -d db redis`
2. Confirm Redis is actually reachable: `docker exec <redis-container> redis-cli ping` → `PONG`
3. `alembic upgrade head` (see the note at the end about why I ran migrations instead of `make setup`)
4. `uvicorn api.main:app --host 127.0.0.1 --port 8000`
5. `curl -s -i http://127.0.0.1:8000/health`

**What I get**

```
$ docker exec pathreview-ai301-fa26-s1-redis-1 redis-cli ping
PONG

$ curl -s -i http://127.0.0.1:8000/health
HTTP/1.1 503 Service Unavailable
content-type: application/json

{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-09-23T23:41:44.138541"}}
```

The log line for that request names the attribute:

```
2026-09-23 16:41:44 [error] redis_health_check_failed error="'Settings' object has no attribute 'redis_host'" request_id=a665f174-4a70-40a6-b8b5-46c25bd6631d
```

**Control run** — Redis is reachable using the field `Settings` actually defines, from the same environment and the same config object:

```
$ python -c "import redis; from core.config import settings; \
print('redis_url =', settings.redis_url); \
print('has redis_host:', hasattr(settings, 'redis_host')); \
print('ping:', redis.from_url(settings.redis_url, decode_responses=True).ping())"
redis_url = redis://localhost:6379/0
has redis_host: False
ping: True
```

So the probe fails on the attribute, not on the connection: `redis_host` does not exist, `redis_url` does, and Redis answers through it.

**Expected:** with Redis running, `/health` reports `"redis": "healthy"`.
**Actual:** `"redis": "unhealthy"` and a 503, with `AttributeError: 'Settings' object has no attribute 'redis_host'` in the log.

Two honest notes about my run:

- **`postgres` also reports unhealthy in my output above, and that is a different bug.** Its log line is `postgres_health_check_failed error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"`, which is the SQLAlchemy 2.x raw-string issue tracked separately (#61). It does not affect the Redis result: the two probes are independent `try` blocks, and the Redis failure is its own `AttributeError`.
- **I ran `alembic upgrade head` instead of the documented `make setup`.** On a fresh database the app's `Base.metadata.create_all` fails with `DuplicateTableError: relation "ix_profiles_user_id" already exists`, because `core/models/profile.py` declares that index twice - once via `index=True` on the `user_id` column (line 27) and again in `__table_args__` (line 51). Running the migrations first creates the tables, so `create_all` finds them and skips. That is unrelated to this issue; I mention it so the steps above are re-runnable as written, and I can open it separately if it is not already tracked.

Disclosure: this work is AI-assisted - I used Claude Code while navigating the codebase and drafting this comment. I ran every command shown here myself on my own machine, and the output above is what came back.

Next: the fix looks like building the client with `redis.from_url(settings.redis_url)` in `api/routes/health.py`. Before I touch it I want to check whether anything else in the repo reads `redis_host`/`redis_port`, so the fix does not leave a second copy of the same mistake behind.

## Eval iterations

**Run history**

1. Smoke run, `--limit 3` (pkg-01, pkg-02, pkg-03): 3/3 agreement. Partial run, no file
   written; I used it to confirm the harness and my CLI login worked before spending a
   full run.
2. First full run, all 20 scored packages: **18/20 agreement (bar: 18/20: PASS)**,
   categories `clear-accept 6/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3
   wrong-target 4/4`. The two misses were pkg-03 and pkg-05, both gold `accept` and both
   rejected by my own over-strict wording.
3. Canary run after revising three checks, `--only pkg-03,pkg-05,pkg-20,pkg-18,pkg-16,pkg-13
   --include-calibration`: 6/6 agreement. pkg-03 and pkg-05 flipped to `accept` as intended,
   and the four canaries (one from each category my loosening could have touched - disclosure,
   unfollowable-comms, wrong-target, no-evidence) all held their previous verdicts.
4. Confirming full run, all 20 scored packages: **20/20 agreement (bar: 18/20: PASS)**,
   categories `clear-accept 8/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3
   wrong-target 4/4`.

Run 4 is the one saved with `--save-run`, and 20/20 is the agreement line in the committed
`eval-run.txt`.

**Package analysis**

`pkg-03` (BurntSushi/ripgrep#2779). Gold label: **accept**. My first run said **reject**,
failing it on two required checks; after revision it agrees at **accept**.

The two failures were instructive in different ways.

`policy-respected` failed with the evidence: "Policy requires comments 'written by humans in
their own words'; neither comment addresses this on AI-assisted course work". The repo-facts
line says AI-assisted coding is welcome with a human in the loop, and that "comments to
maintainers must be written by humans in their own words". My check had one rule for every
policy shape - meet the policy's stated conditions - so the grader treated a *manner*
condition (write like a human) as if it were a *disclosure* condition (say that you used AI),
and demanded a sentence the policy never asked for. The package's comment already was a
specific human account of the issue, which is exactly what that policy wants.

`claims-match-evidence` failed because the report mentions a control run - the minus-replace
form printing "1, 4, 7, 10 correctly" - without pasting its transcript. My pass condition
said every claim must be backed by what the report shows, and the grader applied it to a
passing aside as strictly as to the main result. But the main claim, the reported buggy
behavior, *was* shown, and the missing control is exactly what a `preferred` check is for.

Both misses came from the same root: I wrote conditions that were correct about the failure
mode I had in mind and silent about the shape of an acceptable package, so the grader
resolved the ambiguity against the package.

**Check rationale**

`policy-respected`, quoted as it now reads in `tools/repro-check/rubric.md`:

> | policy-respected | The repo-facts "contribution policy" line (and any AI policy it names), read against what the claim and repro comments actually say | Only an explicit DISCLOSURE requirement creates an obligation to write something. Fail only when the policy says AI use must be disclosed (wording like "all AI usage must be disclosed", "state the tool and the extent of the assistance") and neither comment discloses it; course packages are AI-assisted work, so silence there is a fail. Every other policy shape passes unless a comment visibly contradicts it: a policy asking that comments be written by a human in their own words is met by a comment that reads as a specific human account of this issue (no disclosure sentence required), and policies asking the contributor to understand, test, or human-review the work are met unless the package says otherwise. A silent or permissive-with-responsibility policy always passes. | required |

It reads that way because of pkg-03 and pkg-20 together. pkg-20 is the whole reason the check
exists: ghostty's policy states that all AI usage must be disclosed, the package is an
excellent reproduction on every proof check, and it must still be held - it is the single
package in the `disclosure` category, so a rubric without this check cannot clear the category
floor no matter how well it does elsewhere. My first wording caught pkg-20 but also caught
pkg-03, because "meet the policy's stated conditions" does not distinguish a policy that asks
you to *say something* from one that asks you to *write a certain way*.

What I rejected: the easy fix of dropping the AI clause entirely, or making the check
`preferred`. Either would have restored pkg-03 and lost pkg-20, and losing pkg-20 loses the
category. The wording I kept names the trigger explicitly - policy language that requires
disclosure - and makes every other policy shape pass unless the comment visibly contradicts
it. The check now fires on one condition rather than on a general feeling of policy-adjacency.

**Trade-offs**

The narrowed `policy-respected` gives up the policies that require disclosure without saying
so in words my check recognises. It keys on explicit wording ("all AI usage must be
disclosed"), so a repo that means the same thing obliquely - "we expect contributors to be
transparent about their tooling" - now passes a package that discloses nothing. I accept
that miss: the alternative cost me a clear accept on every repo whose policy merely asks for
human-written comments, and a false reject teaches a newcomer to pad every comment with
boilerplate disclosure, which is its own kind of noise.

I did not take that on faith. Because all three of my revisions loosened checks, I ran a
canary pass before spending a confirming full run: `--only pkg-03,pkg-05,pkg-20,pkg-18,pkg-16,pkg-13`,
one package from each single-package-risk category the loosening could touch. pkg-20
(`disclosure`) still rejected on the disclosure clause, pkg-18 (`unfollowable-comms`) still
rejected on its private monorepo despite the loosened `steps-rerunnable`, and pkg-16
(`wrong-target`) still rejected on its unacknowledged version deviation despite the loosened
`claims-match-evidence`. 6/6, nothing flipped, and the confirming full run then came back
20/20 with `disclosure 1/1` intact - so the loosening bought two clear accepts and cost
nothing measurable on this set.

---

Related paths: `eval-run.txt` in this directory; my skill's files in `tools/repro-check/`.
