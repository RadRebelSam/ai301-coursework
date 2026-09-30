# Unit 3 - Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of my plan, the branch I built it on, and the evaluation runs that produced
`eval-run.txt`.

---

## Posted upstream

**GitHub username**

RadRebelSam

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62#issuecomment-5921825420

Following up on my reproduction above with the plan I'm working from.

**Diagnosis.** The probe never reaches Redis: `health_check()` builds its client with `host=settings.redis_host, port=settings.redis_port`, and `Settings` (`core/config.py:14`) defines only `redis_url`, so the attribute access raises and the surrounding `except Exception` reports Redis down. My control run rules out a connection problem - from the same process and the same config object:

```
redis_url = redis://localhost:6379/0
has redis_host: False
ping: True
```

**Change.** One call site in `api/routes/health.py`: build the client with `redis.from_url(settings.redis_url)`, keeping `decode_responses=True` and the existing `ping()`.

**Not in this change,** so it stays reviewable: the `postgres` probe failure in the same endpoint (that's #61), narrowing the broad `except Exception` blocks around each probe, and adding `redis_host`/`redis_port` fields to `Settings` - the issue says `redis_url` is the field to use, and adding the other two would leave two ways to configure one dependency.

**How I'll show it works.** Re-run the reproduction with Redis up: `dependencies.redis` goes from `"unhealthy"` to `"healthy"` and the `redis_health_check_failed error="'Settings' object has no attribute 'redis_host'"` line stops appearing. The response stays 503 overall until #61 is fixed, so the `redis` key is the observable here, not the status code. I'm also adding a unit test that patches `redis.from_url` and asserts the probe passes the configured URL, so a regression fails in CI without needing a live Redis.

**Unknown I'm carrying:** `from_url` honors whatever database the URL names, where the old code forced `db=0`. For the default `redis://localhost:6379/0` that's identical; for a URL without a database path it would differ. I went with honoring the configured URL - happy to force `db=0` instead if a maintainer prefers that.

Disclosure: AI-assisted (Claude Code). I ran every command myself and I understand the change I'm proposing.

---

## Your branch

**Branch**

fix/62-redis-health-url

**Evidence**

My Unit 2 reproduction re-run against the built change. Both runs used the same
containers in the same session; the only difference is the fix, stashed for the
before and restored for the after, so nothing else moved between them.

```
===== BEFORE (fix reverted: settings.redis_host) =====
$ docker exec pathreview-ai301-fa26-s1-redis-1 redis-cli ping
PONG

$ curl -s -i http://127.0.0.1:8000/health
HTTP/1.1 503 Service Unavailable
content-type: application/json

{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-09-30T23:49:34.853301"}}

$ grep redis_health_check $UVICORN_LOG
redis_health_check_failed count: 2
2026-09-30 16:49:34 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'" request_id=6833d8fa-5b55-4e90-9860-a22ab52faa9a
redis_health_check_passed count: 0

===== AFTER (fix applied: redis.from_url(settings.redis_url)) =====
$ docker exec pathreview-ai301-fa26-s1-redis-1 redis-cli ping
PONG

$ curl -s -i http://127.0.0.1:8000/health
HTTP/1.1 503 Service Unavailable
content-type: application/json

{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"healthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-09-30T23:49:42.074229"}}

$ grep redis_health_check $UVICORN_LOG
redis_health_check_failed count: 0
redis_health_check_passed count: 2
```

Two observables, exactly as the plan's test plan predicted:

- `dependencies.redis` goes from `"unhealthy"` to `"healthy"` while `redis-cli ping`
  returns `PONG` in both runs.
- `redis_health_check_failed` goes from 2 occurrences to 0, and
  `redis_health_check_passed` from 0 to 2.

The response stays 503 in both runs because `postgres` is still failing on the
SQLAlchemy `text()` issue (#61), which this plan put out of scope.

The regression test was also checked in both directions:

```
$ .venv/Scripts/python.exe -m pytest tests/unit/test_health.py -q      # with the fix
1 passed, 1 warning in 1.83s

$ git stash push -q api/routes/health.py
$ .venv/Scripts/python.exe -m pytest tests/unit/test_health.py -q      # fix reverted
FAILED tests/unit/test_health.py::test_redis_probe_uses_configured_redis_url
1 failed, 1 warning in 1.87s
```

## Eval iterations

**Run history**

1. Smoke run, `--limit 3` (pkg-01, pkg-02, pkg-03): 3/3 agreement. Partial run, so no
   file was written; I used it to confirm the harness worked before spending a full run.
2. Full run, all 20 scored packages: **20/20 agreement (bar: 18/20: PASS)**, categories
   `clear-accept 7/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3
   wrong-cause 4/4`.

The second run is the one saved with `--save-run`, and 20/20 is the agreement line in
the committed `eval-run.txt`. There was no revise loop: the first full run already
agreed on every package and matched every category, so no `--only` re-grade was needed.

**Package analysis**

`pkg-16`. My rubric said **reject**; the gold label is **reject**; they agree, and the
check that decided it was `cause-grounded`.

pkg-16 is the hardest kind of reject in the set, because nothing about it looks wrong.
The plan is long, confident, well organised, and names a real mechanism: it blames a
post-read cast for the zero-padding being lost. The gold note calls it "long and
confident" for that reason. What sinks it is one line of the package's own repro
evidence: at step 4, the zeros are already gone in pyarrow's inferred int64 table,
before any cast runs. A cause that sits downstream of where the evidence shows the
damage cannot be the cause, and the plan's proposed re-padding cannot recover widths
that were never read.

My rubric read it that way because `cause-grounded` does not ask whether the stated
cause is plausible; it asks whether the package's own evidence leaves it standing. The
pass condition names the exact failure shape - a cause "asserted about a layer the
evidence never exercised" - and my procedure forces the order that catches it: the
repro evidence is read and its controls written down *before* the plan is read at all.
That ordering is the whole defence against this package, and against pkg-01, pkg-07,
pkg-11 and calib-03, which fail the same way. Read the plan first and its confidence
does the work; read the evidence first and the contradiction is already on the page.

**Check rationale**

`cause-grounded`, quoted as it reads in the `rubric.md` uploaded to `tools/plan-check/`:

> | cause-grounded | The plan's stated cause, read against the repro-evidence block: its steps, its outputs, and especially any control run or comparison it contains | Pass if the stated cause explains the behavior the repro evidence actually shows, and nothing in that evidence rules it out. Fail if a control run in the package already eliminates the named culprit (the same input without the flag behaves correctly, the named module prints fine in the same build, the symptom persists with the accused feature disabled), if the cause contradicts what an artifact shows, or if the cause is asserted about a layer the evidence never exercised. A cause adopted from the thread is graded against the evidence too: agreement with a confident commenter does not make it grounded. | required |

Two decisions are doing the work here.

The first is naming the elimination shapes rather than asking for a judgment. An earlier
draft of this check said the cause must "follow from the evidence", which is the kind of
wording that sounds rigorous and grades inconsistently: a confident plan reads as
following from anything. Listing what a control run eliminates - the same input without
the flag, the module printing fine in the same build, the symptom surviving with the
feature disabled - turns the check into something an executor can answer by reading two
things side by side. Each of those three phrasings is the shape of an actual package in
this set.

The second is the last sentence, about a cause adopted from the thread. I added it
because of calib-03, the operator-swap package from the activity: it adopts a confident
key-binding diagnosis straight from the thread, and the timing matrix in the same
package shows the cost with no pager in the loop at all. Without that sentence, an
executor can reason that a maintainer-endorsed cause is grounded by endorsement. The
sentence says the endorsement is not evidence, and the same rule applies to the plan's
own certainty.

**Trade-offs**

`cause-grounded` gives up the plans whose diagnosis is wrong in a way the package cannot
see. It grades a cause only against the repro evidence in the package, so a plan that
blames the wrong function inside a layer the evidence *does* exercise - right symptom,
right stage, wrong line - passes this check and gets caught later by review, not by me.
I took that deliberately: the alternative is a check that tries to verify causes against
the codebase, which the grader cannot read in eval mode and which would turn a
mechanical comparison into a guess.

Nothing changed elsewhere as a result, and here is how I know: the first full run
agreed on all 20 packages with every category matched, so there was no revision to make
and no loosened check to canary. The strictness lands where I intended, on the four
`wrong-cause` packages plus calib-03, and it costs nothing on the seven clear accepts -
including pkg-09 and pkg-14, which scope themselves down and could plausibly have
tripped a clumsier version of this check.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; my skill's files in
`tools/plan-check/`.
