# Contribution: Add unit tests for Page-Context Router

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Dexin Yang
**GitHub Username:** RadRebelSam
**Issue:** https://github.com/opensearch-project/opensearch-agent-server/issues/9
**Pull Request:** https://github.com/opensearch-project/opensearch-agent-server/pull/177

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: installed issue-scout, wrote a five-query strategy, ran and patched the first scan (2026-10-17)**

I installed the scout over my Unit 1 skill and wrote a strategy that
searches my own stack (owner queries for Litestar, pydantic, SQLAlchemy,
Pallets and pytest-dev, plus a broad Python bug fallback) and ranks bugs
first, docs second, features last, one issue per repo. The first scan
graded five issues and rejected all five: four were already claimed by a
comment or a linked PR, which ranking cannot see, and the rubric's
repo-alive check failed pytest-asyncio even though people had merged
every one of its recent dependabot PRs. I added `-linked:pr` to every
query so linked issues never reach grading, and changed repo-alive to
count a person merging a bot's PR; a free re-rank then showed my stack's
newcomer pool is thin, so widening the owner list is my next change.

**Unit 6: voice guard with an Upstream section, and a repo-memory file for agent-server (2026-10-21)**

I added Upstream rules to my voice guide (claims, questions, pings),
made it one canonical file that repro-check, plan-check and pr-precheck
all link to, and wired the guard hook into Claude Code. Two things
broke: Git Bash's `ln -s` silently copies files unless native symlinks
are turned on, which would have let the three copies drift apart, so I
forced real links and checked them; and my terminal `claude` routes
through a local proxy that served no model, so the guard's wiring test
and its draft check could not run yet. Checked after the fact,
repro-check accepted the claim I had already posted, but my own new
claim rule caught that it gave no time for an update, so my next
comment will carry one. My `CLAUDE.local.md` (57 lines, kept out of
git with `.git/info/exclude`) records the rules I would otherwise have
broken: OpenSearch's DCO needs my real name while my git name is a
handle, and CI fails under 90% coverage.

**Unit 7: ship-gate and a three-check manifest; PR #177 opened through both hooks (2026-11-01)**

My manifest lists the repo's three pre-PR checks (unit tests with the 90%
coverage gate, `ruff check`, `ruff format --check`), written as my venv's
own `python.exe` and `ruff.exe`, because my venv lives outside the repo;
the changelog check sits in the excluded table, since it only runs on
GitHub. The ship-gate passed my branch on the first run, all seven
pr-precheck checks included, but then crashed printing the model's reply
in Windows' `gbk` console, and my terminal `claude` had been broken until
I replaced the `claude/...` model names in my settings. When I opened the
PR, my voice guard stopped it for a behavior claim with no run behind it,
so I ran the check, pasted the output, and the second attempt went
through.

**Unit 8: PR monitor and a playbook built from the repo's merge times; the first run drafted a wait (2026-11-05)**

My playbook follows the repo's real pace: CONTRIBUTING.md states no wait,
and the five latest merged PRs from outside contributors waited 0 to 18.4
days for a maintainer (median 12.7), so I nudge after 26 quiet days, twice
that, to match the ping rule in my voice guide. On PR #177 the monitor
drafted a wait note with nothing to post and a nudge date of 2026-11-27,
and I sent nothing. It fell short twice: it called the PR "review feedback
is in" because of Copilot's automatic notice that it had no quota to
review, and its "1 passing, 1 skipped" check count hid the CI, lint and
changelog workflows still waiting for a maintainer's approval. I had added
a rule that bot notices get no reply after reading that notice, and the
draft cited it; for the checks, I read the PR page myself before deciding
anything.

---

## Why I Chose This Issue

My scout searched the stack I know: owner queries for Litestar, pydantic,
SQLAlchemy, Pallets and pytest-dev, plus a broad search for labeled Python
bugs as a fallback. The first scan graded five issues and rejected all
five, four of them because someone had already claimed them. So I took my
picks from the course's Issue Selection site and graded two directly:
opensearch-agent-server#9 and opensearch-benchmark#228. I chose #9 because
it is exactly the kind of work my profile asks for: unit tests for one
class (`PageContextRouter`), with the expected behaviors listed and a
result I can check locally with pytest, in a codebase I did not write.
Looking closer, PR #164 had already covered four of the six cases, so
before choosing I asked on the issue whether the other two were still
wanted. The maintainer said yes and assigned it to me. My scout's latest
verdict (2026-10-21, after this week's scope patch) is accept, with the
difficulty note "straightforward". What I hope to learn is how to find
exactly where a behavior lives in someone else's code, and how to write
tests that pin a behavior down rather than just cover lines: the router
already has 100% line coverage, yet nothing stops someone from making its
matching case-insensitive.

Honestly, the scout fell short more than it held up on a real search. Its
first scan accepted nothing; its ranking could not see claims; it failed
a live repo (pytest-asyncio) because people, not bots, merged the bot's
pull requests; it accepted #9 even though most of the work had already
landed; and after I claimed #9, it rejected my own issue as "claimed".
Each of those became a rubric or strategy fix (logged with dates in my
`scan-notes.md`). Its verdicts were useful evidence, but the fact that
decided my choice, whether the remaining work was still wanted, came from
asking the maintainer, not from the tool.

Claim comment: https://github.com/opensearch-project/opensearch-agent-server/issues/9#issuecomment-6242969864

I posted that claim on 2026-10-17, a week before this unit's voice guard
and claim check existed, so I did not post a second one. Instead I saved
the posted text as my claim draft and checked it afterwards: repro-check
accepted it, and my own new voice-guide rule caught that it gave no time
for an update.

**Toolkit**

- Tool: issue-scout (my search strategy and my rubric), run through Claude
  Code: one scan, then direct grades of my two picks, a re-grade after
  the maintainer replied, and a final grade after this week's scope patch.
- What it produced: scan 1 searched 199 issues and graded the top 5, all
  rejected. Direct grades accepted #9 and #228. The final grade of #9 is
  accept with the difficulty note "straightforward"; #228 is accept but
  "ambitious", because a maintainer's later commit contradicts its 2023
  decision.
- Difficulties: claims were invisible to ranking; repo-alive misread
  human-merged bot commits as no human activity; no check asked whether
  the work had already landed; my own claim counted as someone else's;
  and the strict scope check rejected most real issues.
- What changed: I added `-linked:pr` to every query; repo-alive now counts
  a person merging a bot's PR; unclaimed now ignores my own account; and
  maintainer-responsive ignores bot-opened issues. This week I replaced my
  `bounded-scope` check with `scope-feasible` plus a difficulty note,
  keeping "the work has already landed" as a reason to reject.

---

## Understanding the Issue

**Problem Description**

`PageContextRouter` decides which agent answers a request, based on the
page the user is on. The issue lists six behaviors that should have unit
tests. PR #164 tested four of them; two still have no test: an empty-string
page context falling back to the default agent, and page-context matching
being case-sensitive. Without tests, someone could change either behavior
and every test would still pass.

**Expected Behavior**

The test suite pins both behaviors as they work today (the maintainer
agreed to pin the current behavior): `route("")` returns the default agent,
and a context that differs only in case, like `Search-Relevance`, does not
match the agent registered for `search-relevance`, so it falls back to the
default agent too.

**Current Behavior**

The code already behaves that way, but nothing checks it. The four existing
tests only pass an exact lowercase context or `None`. When I made matching
case-insensitive in memory, all four tests still passed, and line coverage
of the router is already 100%, so coverage cannot show the gap.

**Affected Components**

- `src/orchestrator/router.py`: `PageContextRouter.route()`, whose
  `if page_context:` check (line 44) skips Tier 1 for an empty string.
- `src/orchestrator/registry.py`: `AgentRegistry.get_agent_for_context()`,
  an exact dictionary lookup (line 75).
- `tests/unit/orchestrator/test_router.py`: the four existing tests and their
  `_registry_with_default()` helper. No production code changes.

**Toolkit**

- Tool: Claude Code, reading only the router subsystem, and my
  `CLAUDE.local.md` memory file, which holds the distilled result.
- What it produced: the two source files, the exact lines that cause each
  behavior, and where their tests live.
- Difficulties: the issue names a test file (`tests/unit/test_router.py`)
  that does not exist, and the gap is invisible to coverage, which is
  already 100%.
- What changed: I wrote the file-and-line conclusions into
  `CLAUDE.local.md`, so each new session starts from them instead of
  re-reading the code.

---

## Reproduction Process

**Environment Setup**

Windows 10 Home (10.0.19045) with Git Bash. opensearch-agent-server `main`
at commit `9340178` (2026-09-01), cloned from upstream. uv 0.11.29 and
Python 3.12.9; pytest 9.0.3 and pytest-cov 7.0.0, installed with
`uv sync --all-extras` as the repo's DEVELOPER_GUIDE.md describes. Two
challenges: the project needs Python 3.12 or newer and my system Python is
3.11.3, so I let uv download 3.12 (`--python 3.12`); and my checkout sits
in a Google Drive folder, so I put the virtual environment outside it with
`UV_PROJECT_ENVIRONMENT` to stop Drive syncing thousands of package files.
CI also runs on Linux and macOS; I only ran on Windows.

**Steps to Reproduce**

1. Clone the repo and run `uv sync --all-extras --python 3.12`.
2. List the router tests:
   `uv run pytest tests/unit/orchestrator/test_router.py --collect-only -q`.
3. Build the same registry the existing tests use (`art` on
   `search-relevance`, default agent `olly`), then call
   `PageContextRouter.route()` with `search-relevance`, `Search-Relevance`,
   `SEARCH-RELEVANCE`, ` search-relevance` and `""`.
4. Check router coverage:
   `uv run pytest tests/unit/orchestrator/test_router.py -q --cov=orchestrator.router --cov-report=term-missing`.
5. Make matching case-insensitive in memory only (patch
   `AgentRegistry.get_agent_for_context`, no file changed), then run the
   same four tests.
6. Observed: four tests, none passing an empty string or a different-case
   context; every input except the exact `search-relevance` goes to the
   default agent; line coverage is already 100%; and all four tests still
   pass with case-insensitive matching.

**Reproduction Evidence**

- **Commit showing reproduction:** none. The issue asks for tests, so there
  is no failing behavior to commit yet, and I had not forked the repo when
  I reproduced it.
- **Screenshots/logs:**

  ```
  $ uv run pytest tests/unit/orchestrator/test_router.py --collect-only -q
  tests/unit/orchestrator/test_router.py::TestPageContextRouter::test_tier1_context_match
  tests/unit/orchestrator/test_router.py::TestPageContextRouter::test_falls_back_to_default_when_no_match
  tests/unit/orchestrator/test_router.py::TestPageContextRouter::test_falls_back_to_default_when_context_none
  tests/unit/orchestrator/test_router.py::TestPageContextRouter::test_raises_when_no_default_and_no_match
  4 tests collected in 0.03s

  # step 3: route() on each input
  'search-relevance' -> art
  'Search-Relevance' -> olly
  'SEARCH-RELEVANCE' -> olly
  ' search-relevance' -> olly
  '' -> olly

  # step 4: coverage
  src\orchestrator\router.py      18      0   100%
  4 passed in 0.11s

  # step 5: the same tests with case-insensitive matching
  4 passed in 0.04s
  ```

- **My findings:** The issue is real, as a test gap rather than a bug: both
  behaviors exist on main, and no test pins either one. Matching is an
  exact dictionary lookup (`self._page_context_map.get(page_context)`,
  `src/orchestrator/registry.py` line 75), so case and whitespace both
  matter. An empty string never reaches the registry, because `route()`
  checks `if page_context:` first and an empty string is falsy. Line
  coverage cannot show this gap; step 5 shows the current tests would not
  notice if case sensitivity changed. The empty-string test is the thinner
  half: I found no plausible code change that breaks it, so it mainly
  documents the contract.

**Toolkit**

- Tool: repro-check, run through Claude Code, first on my claim alone and
  then on the claim plus my repro report (`claim-draft.md` and
  `repro-draft.md`).
- What it produced: accept both times. In the full run, all seven required
  checks passed, and it re-ran every command in my report itself and got
  the same output.
- Difficulties: my claim was already posted before this week's tools
  existed, so the claim check was a look back, not a gate. It flagged that
  my claim broke my own new voice-guide rule (no update time), and that my
  follow-up comment on the thread mentioned a leading-space input my report
  had never run.
- What changed: I added ` search-relevance` to step 3 so the report backs
  everything I said on the thread, and my next comment on the issue will
  give a time for an update.

---

## Solution Approach

**Analysis**

The two untested behaviors come from two lines. For the empty string,
`PageContextRouter.route()` in `src/orchestrator/router.py` checks
`if page_context:` (line 44) before asking the registry; an empty string
is falsy, so Tier 1 is skipped and Tier 3 returns the default agent. For
case, `AgentRegistry.get_agent_for_context()` in
`src/orchestrator/registry.py` is `self._page_context_map.get(page_context)`
(line 75), an exact dictionary lookup, so any difference in case misses and
falls through to the default agent. The four tests in
`tests/unit/orchestrator/test_router.py` only pass an exact lowercase
context or `None`, so neither behavior is pinned. The cause is missing
tests, not a bug in the code.

**Proposed Solution**

Add two tests to the existing `TestPageContextRouter` class in
`tests/unit/orchestrator/test_router.py`, reusing `_registry_with_default()`
(agent `art` on `search-relevance`, default agent `olly`):

- `test_falls_back_to_default_when_context_empty`: `route("")` returns `olly`.
  This pins the fallback, not the `if page_context:` check itself: with that
  check removed, `""` would still reach the default agent, because no agent
  is registered for an empty string.
- `test_context_match_is_case_sensitive`: `route("Search-Relevance")` returns
  `olly`, not `art`, pinning the current case-sensitive matching that the
  maintainer agreed to in the issue.

What I am deliberately not changing: `router.py` and `registry.py` (no
production code), the matching behavior itself (no case-insensitive
matching), and the test path the issue suggests (`tests/unit/test_router.py`
does not exist, so the tests go in the existing file, as I said in my claim).
The router integration tests the maintainer also mentioned are a separate
follow-up PR. No CHANGELOG entry: the earlier test-only PR #164 skipped it
with the `skip-changelog` label, so the PR description asks for that label.
One open unknown: only a maintainer can apply that label, and the changelog
check fails the PR until then; if a maintainer prefers an entry, I will add
one line under `[Unreleased]` instead.

**Implementation Plan**

1. Make a branch from `main`, `test/9-router-empty-and-case-tests`, and set
   `user.name` to my real name in this clone for the DCO sign-off.
2. Add the two tests to `tests/unit/orchestrator/test_router.py`.
3. Show the case-sensitivity test catches a change: run it against
   matching made case-insensitive in memory and confirm it fails, then
   passes on the real code.
4. Run the three manifest checks: unit tests with the 90% coverage gate,
   `ruff check .`, and `ruff format --check .`.
5. Commit with `git commit -s` and a message that says what the commit does.
6. Save the before and after output in `test_evidence.md` (kept out of the
   commit) for the PR.
7. Write the PR description from the repo's template, with `Fixes #9`, the
   evidence, a request for the `skip-changelog` label, and a line disclosing
   that I used Claude Code, as the OpenSearch AI Usage Policy asks.

**Toolkit**

- Tool: plan-check, run through Claude Code on `plan.md` (this section)
  and a short plan comment, `plan-comment.md`, which I did not post.
- What it produced: accept, with all six required checks passing. The
  deciding check was that the cause in my plan (the `if page_context:`
  line and the exact dictionary lookup) matches what I reported on the
  thread.
- Difficulties: it accepted while still flagging real problems. My
  comment promised a PR "within a week", which breaks my own voice-guide
  rule against promising things I don't control. It quoted code but no
  run. My plan had no step to disclose AI use in the PR. And my
  empty-string test pins less than my analysis implied.
- What changed: the comment now gives an update time and pastes the run;
  the plan gained a disclosure step and names the changelog label as an
  open unknown with a fallback; and the empty-string bullet now says it
  pins the fallback, not the `if page_context:` check.

---

## Testing Strategy

Three observable checks, each run before and after the change:

1. **The router tests themselves.** On `main`, the router file collects
   4 tests, none passing an empty string or a case variant. On my branch it
   collects 6, and all 6 pass (`6 passed in 0.06s`).
2. **Does the new test catch a regression?** I made matching
   case-insensitive in memory, without changing any file, and ran the same
   tests. On `main`: `4 passed`, so nothing noticed. On my branch:
   `AssertionError: assert 'art' == 'olly'` in
   `test_context_match_is_case_sensitive`, `1 failed, 5 passed`. That is
   the proof the case test pins the behavior. The empty-string test pins
   only the fallback: removing the `if page_context:` check would not break
   it, because no agent is registered for an empty string, so I say so
   instead of claiming more.
3. **The repo's own checks**, from my ship manifest, run in a plain
   terminal the way CI runs them: the unit suite with the 90% coverage gate
   (`884 passed`, coverage 97.69%, unchanged from `main` because the router
   was already at 100%), `ruff check .` (all checks passed), and
   `ruff format --check .` (127 files already formatted).

All outputs are saved in `test_evidence.md` and quoted in the PR
description.

**Toolkit**

- Tool: the ship manifest (`ship-manifest.md`), which lists the repo's
  three pre-PR checks, plus a small in-memory patch I wrote to test the
  test.
- What it produced: the before and after outputs above, all passing on
  the branch, and a failing run that proves the case test catches a change
  to matching.
- Difficulties: line coverage could not measure this work (the router was
  already at 100%), so "tests pass" alone would have proved nothing; and
  my virtual environment lives outside the repo, so the manifest commands
  had to name the venv's own `python.exe` and `ruff.exe` to run in a plain
  terminal.
- What changed: I added the in-memory regression check to the plan and the
  evidence, and I state plainly that the empty-string test pins less than
  the case test.

---

## Implementation Notes

**2026-10-17**

Claimed #9 after noticing PR #164 had already covered four of its six
cases; asked whether the remaining two were still wanted.

**2026-10-18**

mingshl confirmed and assigned the issue to me. Set up the repo locally
with uv on Python 3.12 (my system Python is 3.11), with the virtual
environment outside my Google Drive folder. The four existing router tests
passed.

**2026-10-21**

Reproduced the gap: the router routes `""` and `Search-Relevance` to the
default agent, and the existing tests still pass when matching is made
case-insensitive. Read CONTRIBUTING.md: commits need a DCO sign-off with my
real name, while my global git name is a handle.

**2026-11-01**

- My terminal `claude` had been failing for days with "There's an issue
  with the selected model". The cause was the `claude/...` model names in my
  Claude Code settings, which my local proxy sent to a port where nothing
  was listening. Switching to plain model names fixed it, and the voice
  guard and ship-gate could finally run.
- The voice guard's draft check crashed with a `TypeError`: Windows read
  Claude's UTF-8 reply in the `cp936` encoding. Running it with
  `python3 -X utf8` fixed it, without editing the course script.
- Found that the repo's changelog check fails test-only PRs unless a
  maintainer adds `skip-changelog`, as #164 had; decided to ask for the
  label rather than add an entry.
- Forked the repo, set `user.name` to my real name in the clone, and made
  the branch `test/9-router-empty-and-case-tests`.
- Wrote the two tests in the existing file. A wrong turn I avoided: my
  first plan comment said the empty-string test pins the `if page_context:`
  check; plan-check showed it pins only the fallback, so I corrected the
  plan before building.
- Confirmed the case test fails against case-insensitive matching, ran the
  three manifest checks, and committed `2040b37` with `git commit -s`.

**2026-11-05**

- PR #177 has been quiet for four days. The only activity is Copilot's
  automatic review, which says it could not run for lack of quota.
- Measured the repo's review pace for my playbook: the five latest merged
  PRs from outside contributors (#163, #162, #161, #160, #156) waited 14.1,
  12.7, 18.4, 0.0 and 1.7 days for a maintainer's first comment, review or
  merge. Four quiet days is normal here, so I posted nothing.

**Toolkit**

- Tool: Claude Code for reading the router code and drafting the tests,
  my `CLAUDE.local.md` for the repo's rules, and plan-check for the plan.
- What it produced: two tests (11 added lines in one file) and a signed-off
  commit, kept inside the plan.
- Difficulties: most of the friction was my own tooling, not the code: a
  broken model route, an encoding crash in the guard, and a real-name rule
  my default git config would have broken.
- What changed: plain model names in my settings, `-X utf8` on the guard
  hook, and a per-clone `user.name` for the DCO.

---

## Pull Request

**PR Link:** https://github.com/opensearch-project/opensearch-agent-server/pull/177

**PR Description:** Title: "Add router tests for empty and case-mismatched
page contexts". It says the PR adds the two `PageContextRouter` tests #9
still needed after #164 (`route("")` and `route("Search-Relevance")` both
fall back to the default agent), in the existing test file, with no
production change. It shows why the case test matters (with matching made
case-insensitive in memory, `main`'s tests still pass but the new test
fails with `assert 'art' == 'olly'`), shows a run backing the claim that
the empty-string test pins only the fallback, and pastes the 6 passing
router tests and the repo checks (884 passed at 97.69% coverage, ruff check
and format clean). It asks a maintainer for the `skip-changelog` label as
#164 had, says the router integration tests are a separate follow-up,
includes `Fixes #9` and an AI disclosure in my words, and answers every
item of the repo's PR template.

**Maintainer Feedback:**

- 2026-11-05: none yet. The only review is Copilot's automatic notice
  that it could not review the PR (no quota), which needs no reply. The DCO
  check passed; the repo's CI, lint and changelog workflows are still
  waiting for a maintainer to approve the runs, which is usual for a
  first-time contributor.

**Status:** Awaiting review

**Toolkit**

- Tool: the ship-gate (my manifest's checks plus my pr-precheck) and the
  voice guard, both as Claude Code hooks on `gh pr create`.
- What it produced: `ship-gate: PASS` on my branch, with all three repo
  checks and all seven pr-precheck checks passing (plan-matched,
  description-true, evidence-decisive, diff-clean, standards-met,
  smallest-surface, tests-added). On my first attempt to open the PR, the
  voice guard stopped it.
- Difficulties: the guard caught a real claim without a run: my
  description said removing the `if page_context:` check would still send
  `""` to the default agent, with no output behind it. The manual
  ship-gate also crashed after printing PASS, because Windows' `gbk`
  console could not print an emoji in the model's reply; the pass had
  already been logged, so the hook still honored it.
- What changed: I ran that check, pasted its output into the description
  and `test_evidence.md`, and opened the PR on the second try with both
  hooks passing.

---

## Learnings and Reflections

**Technical Skills Gained**

- Proving that a test guards something when coverage can't show it.
  `router.py` was already at 100% line coverage, so I changed the code in
  memory (case-insensitive matching) and confirmed the new test fails while
  `main`'s tests still pass.
- Reading exactly what a test pins. The router skips its lookup with
  `if page_context:`, and the registry does an exact `dict.get`, so the
  empty-string test pins only the fallback, not that check; I said so in
  the PR instead of overclaiming.
- OpenSearch's contribution mechanics: a DCO sign-off with my real name
  (`git commit -s`, with `user.name` set per clone), a fork with an
  `upstream` remote and `gh repo set-default`, the `skip-changelog` label
  for test-only PRs, and maintainer approval for a first-time
  contributor's workflow runs.
- My own tooling: Claude Code hooks that check a command before it runs
  (the voice guard and the ship-gate), uv with its environment outside
  Google Drive, and Windows' `cp936`/`gbk` console encoding, worked around
  with `python3 -X utf8`.

**Challenges Overcome**

- The issue was mostly done: PR #164 had already covered four of its six
  cases. I asked on #9 whether the other two were still wanted before
  writing anything, and mingshl confirmed them and assigned me.
- The gap was invisible to coverage, so I had to show its value another
  way, which became the in-memory check pasted into the PR description.
- My tools broke more than the code did: a `claude/...` model name routed
  to a port with nothing listening, the guard crashed on Windows encoding,
  and Git Bash's `ln -s` silently copied files. I fixed each at the root
  (plain model names, `-X utf8`, native symlinks) and checked it worked.
- My voice guard stopped my first `gh pr create` because the description
  made a claim with no run behind it. I ran the check and pasted the output
  instead of softening the sentence.

**What I'd Do Differently Next Time**

- Read the whole issue thread and the repo's recent merged PRs before
  claiming. I would have seen #164 first, and my claim would have offered
  an update time, which my own claim rule now requires.
- Test my tooling end to end in the first week (`claude -p` in a plain
  terminal), not on the day a hook first needs it.
- Measure the repo's review pace before opening the PR, so I set my
  expectations, and my nudge date, on day one rather than four days in.

---

## Resources Used

- opensearch-agent-server's CONTRIBUTING.md, for the DCO real-name rule,
  the coverage gate and the review process:
  https://github.com/opensearch-project/opensearch-agent-server/blob/main/CONTRIBUTING.md
- The OpenSearch project's AI Usage Policy, which my PR's AI disclosure
  follows: https://github.com/opensearch-project/.github/blob/main/CONTRIBUTING.md
- PR #164, which showed where the router tests live, the
  `_registry_with_default()` helper, and the `skip-changelog` label for a
  test-only change:
  https://github.com/opensearch-project/opensearch-agent-server/pull/164
- mingshl's reply on issue #9, which confirmed the two missing cases and
  pinned the case test to current behavior:
  https://github.com/opensearch-project/opensearch-agent-server/issues/9
- The course's Unit 8 materials, for the PR monitor and the playbook
  template: https://github.com/codepath/ai301-unit8-starter
