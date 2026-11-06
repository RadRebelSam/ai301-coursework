# Toolkit Portfolio

**Student:** Dexin Yang

**GitHub:** RadRebelSam

**Evidence cutoff:** November 5, 2026

## Featured Tools

I chose issue-scout, repro-check, the voice guard, and the PR monitor because they show where my rules met real contribution work: finding work still wanted, proving a claim, checking words before posting, and deciding when to wait. Repro-check and issue-scout changed after specific mistakes; the voice guard then caught an unsupported claim in my actual PR. The monitor is the newest and least tested entry. Its first run exposed a limit rather than a success I can generalize.

Dates come from saved term write-ups, eval transcripts, and local logs. Course commits preserve the records linked below. Installed tools under `~/.claude/` are not tracked in the course repo; their dated excerpts are local records, not claims of committed tool changes.

**issue-scout**

**What it is, and the judgment hole I wrote:** This grew out of Unit 1's issue-select into a live search and grading workflow. My judgment hole is the search strategy and rubric: search my Python stack, rank bugs before features, take one issue per repo, then check ownership, activity, policy, and whether useful work remains. A newcomer label is a lead, not proof of availability. These decisions live in my installed `search-strategy.md`, `rubric.md`, and `scope.md`.

**How it evolved, dated:** On October 17, the first scan found 199 unique issues, graded five, and accepted none; four were already claimed. I added `-linked:pr` to all five queries. A free search-and-rank rerun removed three previously surfaced issues. That day, pytest-asyncio also failed my activity rule because its recent commits were bot-authored even though people merged the PRs. I changed the rule to count a recorded human merger; this did not change its rejection on scope.

Direct grading exposed another gap: OpenSearch #9 passed although PR #164 had covered four of its six cases. On October 17 I added a check for already-landed work, with an exception when a maintainer confirms the remainder is wanted. After that confirmation, the October 18 re-grade still rejected #9 because its assignee and claim were mine. I made live ownership account-aware; applying that correction by hand made #9 acceptable. On October 21 I adopted `scope-feasible` plus a difficulty note instead of treating every ambitious issue as unavailable. That latest patch has no confirming scout or Unit 1 harness run.

**Evidence:** These excerpts come from my own installed `issue-scout/scan-notes.md`:

> 2026-10-17 Scan 1: 199 unique results, 114 survived the drop filters, all 5 graded candidates rejected. 4 of 5 were already claimed (polyfactory#734 by a comment, litestar#2548 and apipkg#32 by open linked PRs, sqlalchemy#8693 by a comment).

> Checked with a free search-and-rank rerun (no grading): polyfactory#734, litestar#2548 and apipkg#32 no longer surface. Cost: it also drops issues whose only linked PR closed unmerged.

The October 18 ownership correction records its validation boundary:

> Applied by hand to the evidence already checked, not re-run through the scout: agent-server#9's assignee and claim comment are both mine, #139 is closed and #164 is merged, so unclaimed passes and #9 now accepts.

The course record is [41caa24, October 17](https://github.com/RadRebelSam/ai301-coursework/commit/41caa24) and [8cd4f72, October 21](https://github.com/RadRebelSam/ai301-coursework/commit/8cd4f72). The deciding fact still came from asking the maintainer on [issue #9](https://github.com/opensearch-project/opensearch-agent-server/issues/9) whether the remaining tests were wanted.

**repro-check**

**What it is, and the judgment hole I wrote:** This checks whether a claim and reproduction are ready to post. My judgment hole is `rubric.md`: require an obtainable starting state, the actual trigger, an artifact showing the reported behavior, and a conclusion no stronger than that artifact. I decide which omissions block posting and which merely weaken a report. A missing transcript for a secondary observation should not invalidate a demonstrated main result.

**How it evolved, dated:** On September 23, the first full eval agreed on 18/20 packages but falsely rejected pkg-03 and pkg-05. My policy rule treated human-written comments as requiring AI disclosure; other rules demanded too much from secondary observations and reconstructable inputs. I revised `policy-respected`, `claims-match-evidence`, and `steps-rerunnable`. Six canaries then agreed 6/6, and the confirming full run agreed 20/20. The initial 18/20 survives in my dated write-up; the saved harness transcript is the final run.

On October 21 I used its live scope on my OpenSearch claim and repro. It accepted the package but noted that my follow-up mentioned a leading-space input the numbered reproduction did not test. I added ` search-relevance` to step 3 and recorded its fallback to `olly`. This changed my report, not the router. Checking the claim was retrospective because I had already posted it before this live setup existed.

**Evidence:** My saved Unit 2 harness transcript, committed in [16af4f1, September 23](https://github.com/RadRebelSam/ai301-coursework/commit/16af4f1), contains:

```text
# eval run written by run_eval.py at 2026-09-23T23:37:21Z
agreement: 20/20 scored items  (bar: 18/20: PASS)
```

My own September 23 Pathreview reproduction separated a broken probe from a broken Redis server:

```text
2026-09-23 16:41:44 [error] redis_health_check_failed error="'Settings' object has no attribute 'redis_host'" request_id=a665f174-4a70-40a6-b8b5-46c25bd6631d

redis_url = redis://localhost:6379/0
has redis_host: False
ping: True
```

This artifact, saved in `beat-1-sandbox/unit-2/reproduction.md`, supports the configuration-access failure. HTTP 503 alone would not: PostgreSQL was independently failing too. On my later OpenSearch report, `ai301-unit6-starter/repro-check-output.md` identified the missing input:

> The 2026-10-18 follow-up says ` search-relevance` (with a leading space) falls back to the default agent. Step 3 does not test that input

The corrected run recorded `' search-relevance' -> olly`. My [October 21 course commit](https://github.com/RadRebelSam/ai301-coursework/commit/8cd4f72) preserves the updated reproduction steps and Toolkit account.

**voice guard**

**What it is, and the judgment hole I wrote:** This Claude Code hook checks outgoing `gh issue/pr comment/create` text against my canonical voice guide. My judgment hole is the guide's rules: claims name a first step and update time, questions show what I checked, behavior claims carry command output, and pings respect the repo's pace. The hook supplies enforcement; the guide defines what I am willing to send.

**How it evolved, dated:** On October 21, working with maintainers made my ban on all dates too broad: I cannot promise a fix date, but I can promise an update. I added the Upstream rules and linked repro-check, plan-check, and pr-precheck to one guide. Git Bash's `ln -s` had silently copied files, letting the guides drift, so I recreated native symlinks and checked their targets.

The terminal model route was still broken then, so wiring the hook was not evidence that it worked. On November 1, I replaced broken `claude/...` model names with plain names and added `-X utf8` to the voice hook after a Windows encoding failure. Later that morning it stopped my first real PR attempt for a claim without a run behind it. I ran the check and added its output before trying again.

**Evidence:** Comparing my tracked Unit 4 guide with the canonical guide shows a real rule change:

```text
Before: A date, a deadline, or a promise to fix something.
After:  A fix date, a merge deadline, or a promise to fix something. (A time
        by which I will post an update is fine: that one I control.)
```

My installed `voice-guard/guard-log.md` records the actual gate:

```text
- 2026-11-01 04:26 [hook] stop: rule broken: "Every claim about behavior comes with the command and the output that produced it." | target: gh pr create --title Add router tests for empty and case-mismatched page contexts
- 2026-11-01 04:29 [hook] pass | target: gh pr create --title Add router tests for empty and case-mismatched page contexts
```

Between attempts I added this run to `opensearch-agent-server/test_evidence.md` and my PR description:

```text
registry.get_agent_for_context(""): None
route("") with the if page_context: check removed -> olly
```

That backed my claim: the empty-string test pins fallback, not the `if page_context:` statement itself. [3a9feb2, November 1](https://github.com/RadRebelSam/ai301-coursework/commit/3a9feb2) records the stop, correction, and successful second attempt for my own [PR #177](https://github.com/opensearch-project/opensearch-agent-server/pull/177).

**PR monitor**

**What it is, and the judgment hole I wrote:** This reads PR state and drafts a next move. My judgment hole is `~/.claude/pr-monitor/playbook.md`: answer a reviewer's point with evidence, do not reply to bot notices, wait according to measured repo activity, and use a bounded nudge and walk-away policy. The monitor drafts; I decide what to send.

**How it evolved, dated:** On November 5, four quiet days on #177 made a generic reminder threshold unhelpful. I measured first maintainer contact on five recent merged outside-contributor PRs: 14.1, 12.7, 18.4, 0.0, and 1.7 days. Their median was 12.7 days, so I set the first nudge after 26 quiet days: November 27 for #177. After reading Copilot's quota notice, I added a no-reply rule for bot notices before the draft run.

This is still a first version. The state run called that notice review feedback, and its check summary concealed workflows awaiting maintainer approval. My playbook led the draft to wait despite the classification. I inspected the PR page and sent nothing; I have not fixed or rerun those weaknesses.

**Evidence:** My `monitor-log.md` records the first live run. The following lines are excerpted from its November 5, 23:26 block:

```text
- state: feedback-in: review feedback is in (latest from the thread: review (commented) 4 days ago)
- move: Wait note - nothing to send on PR #177 (via the review response form)
```

My `drafted-move.md` then says:

> No comment to post. The only item in the thread is Copilot's notice that it could not review because of quota, so the playbook calls for no reply. No human has reviewed, and the nudge threshold is not reached.

Its check summary was only “1 passing and 1 skipped or cancelled.” My November 5 record distinguishes DCO passing from CI, lint, and changelog workflows waiting for approval. [0943d22, November 5](https://github.com/RadRebelSam/ai301-coursework/commit/0943d22) preserves the wait measurements, no-post decision, and shortcomings. As of the evidence cutoff, #177 is open and awaiting human review.

## Inventory

- **issue-select:** Unit 1 sandbox issue-selection rubric, committed September 16 in `5b2ec54`; saved eval 18/20 with two false rejects. Its live successor is the featured issue-scout.
- **plan-check:** Evidence-first plan rubric, committed September 30 in `2c6d51f`, with a saved 20/20 eval and no later sandbox revision loop. On November 1 it flagged overclaims in my OpenSearch plan, including what the empty-string test pins; that live output was not saved separately, so the contribution story records the use.
- **pr-precheck:** Diff/plan/evidence review rubric, committed October 7 in `3b8958c`; saved eval 19/20. Its pkg-05 false rejection on `evidence-decisive` remains unfixed, even though all seven checks later passed inside the OpenSearch ship-gate.
- **CLAUDE.local.md repo memory:** My 57-line OpenSearch note, established October 21 and excluded from git, records file locations, DCO real-name sign-off, and the 90% coverage requirement. Its original four-test baseline needs updating after my six-test branch; it does not synchronize itself.
- **ship-gate and ship-manifest.md:** November 1 gate combining my unit/coverage, Ruff lint, and Ruff format commands with pr-precheck. Its saved manual run passed three commands and seven review checks. It cannot run the GitHub-only changelog check, and its console crashed on an emoji after logging PASS.

## The Arc

**Beat 1 - Labels did not decide suitability**

Date: 2026-09-16

Friction: My Unit 1 bounded-scope check rejected issue-01's single documentation task across five pages and issue-04's terse maintainer-filed bug. It treated the number of pages and the brevity of the report as evidence of unbounded work.

Change: I recorded both false rejects and proposed allowing a terse, maintainer-filed bug or cleanup when there is no umbrella or open design debate. I retained the current rubric so it matched the fingerprint in the saved run; that proposed revision was not implemented at this stage.

Result: The saved eval reached 18/20, enough for the course bar but evidence of an imperfect rubric. The record is `beat-1-sandbox/unit-1/selection.md` and `eval-run.txt` at course commit `5b2ec54`.

**Beat 2 - Tighten the claim, not the report's length**

Date: 2026-09-23

Friction: Repro-check rejected two acceptable reports, while my Pathreview health endpoint returned 503 for more than one dependency. Strict wording and an undifferentiated status code both obscured what the evidence proved.

Change: I revised three rubric checks, ran six canaries, and wrote the Redis reproduction around its exception, configured URL, and successful direct ping.

Result: The confirming eval reached 20/20 with the disclosure rejection intact. The Redis report isolated nonexistent Settings attributes while showing Redis itself answered. Course commit `16af4f1` preserves the saved transcript and reproduction quoted above.

**Beat 3 - A bounded fix produced a visible local result**

Date: 2026-09-30

Friction: Pathreview's Redis probe read `settings.redis_host` and `settings.redis_port`, but configuration exposed `redis_url`. A healthy Redis instance was reported unhealthy.

Change: Using plan-check, I kept the fix to `redis.from_url(settings.redis_url, decode_responses=True)` and a regression test. Pathreview commit `28b07a7588d0f6a47c0dbaaf097c8ddfe1c0e46f` records the change; course commit `2c6d51f` records the plan and run.

Result: My same-session before/after changed `dependencies.redis` from `"unhealthy"` to `"healthy"`; the regression test failed with the fix stashed and passed with it applied. HTTP stayed 503 because PostgreSQL issue #61 remained out of scope. The saved Unit 3 write-up includes:

```text
===== BEFORE (fix reverted: settings.redis_host) =====
redis_health_check_failed count: 2
redis_health_check_passed count: 0

===== AFTER (fix applied: redis.from_url(settings.redis_url)) =====
redis_health_check_failed count: 0
redis_health_check_passed count: 2
```

The constructor diff in `api/routes/health.py` is:

```diff
-        r = redis.Redis(
-            host=settings.redis_host,
-            port=settings.redis_port,
-            db=0,
-            decode_responses=True,
-        )
+        r = redis.from_url(settings.redis_url, decode_responses=True)
```

**Beat 4 - Local checks needed the repo's actual conventions**

Date: 2026-10-07

Friction: A regression test without the unit marker missed the repo's marker-filtered target. The Redis fix also left an unnecessary `attr-defined` suppression, while separate PostgreSQL suppressions remained necessary. My pr-precheck eval still falsely rejected pkg-05.

Change: Pathreview commit `474c27685ae3024b2a16ebb6344873406c903f47` added `@pytest.mark.unit` and removed only the resolved suppression. I documented precheck's 19/20 instead of claiming its false reject was fixed.

Result: The clone's `pathreview-ai301-fa26-s1/test_evidence.md` records `376 passed, 53 xfailed` and mypy clean across 76 files. The integration target collected zero tests and exited 5, so it was not an integration pass. The code change is in `474c276`; course commit `3b8958c` preserves the precheck eval and PR link. These are local outcomes, not a merge claim.

**Beat 5 - Availability needed thread history and my identity**

Date: 2026-10-17 to 2026-10-18

Friction: The live scout graded five candidates without accepting one. It then accepted OpenSearch #9 without noticing four of six cases had landed, and later rejected it because I had claimed it myself.

Change: I filtered linked PRs, recognized human merges of bot PRs, checked already-landed work, asked about the remaining cases, and made live ownership account-aware.

Result: The free rerank removed three linked candidates. Maintainer mingshl confirmed the remaining tests and assigned #9 to me; applying the ownership correction by hand made it acceptable. The scan excerpts above and October 17/21 course commits preserve the corrections.

**Beat 6 - Sandbox acceptance missed live communication gaps**

Date: 2026-10-21

Friction: My posted claim omitted an update time, and the reproduction did not run the leading-space input I had mentioned. Voice-guide copies could drift, and a broken terminal model route prevented an end-to-end guard check.

Change: I added the missing input, wrote the Upstream rules, replaced guide copies with native symlinks, and adopted `scope-feasible` with a difficulty note in the scout.

Result: The recorded leading-space run returned `olly`, and three checkers shared one canonical guide. I still had no working guard run that day; its enforcement result belongs to November 1. Course commit `8cd4f72` records this stage.

**Beat 7 - Tests and descriptions both had to earn their claims**

Date: 2026-11-01

Friction: The router had 100% line coverage, but its four tests did not stop case-insensitive matching. My empty-string test proved less than my plan suggested, and the guard caught a missing supporting run.

Change: I added two tests, tried a case-insensitive mutation in memory, disclosed the empty-string test's narrower protection, ran the manifest checks, and supplied the missing run before opening the PR. OpenSearch commit `2040b3741443a2b436e4caef3c8ced6cad52fccf` adds 11 lines to one test file, with no production change.

Result: The old tests passed under the mutation; the new case test failed under it. The real branch passed six router tests and 884 unit tests at 97.69% coverage. After the guard's stop and correction, PR #177 opened. These are excerpts from separate runs in my saved `test_evidence.md`:

| Run | Recorded result |
|---|---|
| Original tests, case-insensitive mutation | `4 passed in 0.04s` |
| New suite, same mutation | `1 failed, 5 passed in 0.50s`; `AssertionError: assert 'art' == 'olly'` |
| New suite, real code | `6 passed in 0.06s` |
| Full unit suite, real code | `884 passed, 15 warnings in 23.15s` |

Course commit `3a9feb2` records the evidence and PR opening. Opening is the landed workflow result; the PR has not merged as of November 5.

**Beat 8 - A useful next move was to wait**

Date: 2026-11-05

Friction: Four quiet days felt stale, but the monitor misclassified Copilot's quota notice and summarized checks too coarsely to reveal pending workflow approval.

Change: I measured five outside-contributor PRs, set a 26-day first-review threshold, added the bot-no-reply rule, ran the monitor, and checked the PR page myself.

Result: The saved draft recommended no comment, and I posted none. I had a specific first-nudge date, November 27. Human review and approval for CI, lint, and changelog workflows remained pending; course commit `0943d22` records the state.

## Limitations and What's Next

The record is small: one sandbox Redis fix and one open OpenSearch PR. My local checks ran on Windows; they do not establish Linux or macOS success, maintainer approval, or an upstream merge. A 20/20 course eval is agreement on that set, not proof of live reliability. Plan-check's live output was not saved separately, and I have not rerun the Unit 1 harness after scout patches. The monitor's first run required my playbook and a manual page read.

My first follow-up is to fix the monitor's state reporting: distinguish a bot quota notice from actionable human review and show workflows awaiting approval separately from passed, failed, or skipped checks. I will replay the saved #177 state alongside a human-review case, save before/after output, and check that the bot-only case still drafts no reply while human review identifies the point needing an answer. That tests the exact error this run exposed.

I also have a bounded precheck correction waiting: narrow `evidence-decisive` so a supporting observation is not mistaken for another failure mode of the issue. I will rerun pkg-05 with pkg-04/07/10/14 as canaries, then save a full confirming eval before calling it fixed. For #177, I will follow my playbook if review arrives; if it stays quiet through November 27, one nudge will bring fresh checks and ask for review or workflow approval. The suggested router integration tests remain a separate, unstarted follow-up.

**Tool limits.**

issue-scout: Ranking cannot see comment claims; `-linked:pr` also hides issues whose only linked PR closed unmerged. The latest scope patch has no confirming run, and SQLAlchemy's acceptance-label gate remains uncovered.

repro-check: Its narrowed policy rule can miss indirect disclosure requirements. It can accept while flagging a missing secondary run or update-time violation, and my live claim check happened after posting.

voice guard: It judges received text, not elapsed review time or repository state. It cannot establish that a ping respects the repo's pace, and it depends on a working model route and Windows UTF-8 setup.

PR monitor: It misclassified a bot quota notice as review feedback and hid approval-gated workflows behind a check count. One wait draft does not demonstrate handling maintainer feedback; those classification problems remain unfixed.
