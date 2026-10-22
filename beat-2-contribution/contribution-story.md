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
**Pull Request:** [GitHub PR link, once opened]

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

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

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

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

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

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
