# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62

**Verdict output**

````
Repo `codepath/pathreview-ai301-fa26-s1` matches scope, house rule noted (claim comments don't block here — moot, none present anyway).

Checks:
- repo-alive: pass — last commits by Andrew Burke (human), 2026-09-16, within 180 days, not archived.
- unclaimed: pass — no assignees, no comments, no linked/mentioned PRs found.
- bounded-scope: pass — single named bug, expected vs actual stated, files named (`api/routes/health.py`, `core/config.py`).
- ai-policy-ok: pass — CONTRIBUTING.md silent on AI; repo commits show AI co-authorship in normal use.
- maintainer-responsive (preferred): unclear — small comment sample, no clear signal.
- newcomer-label (preferred): pass — labeled `good first issue`, `tier-1`.

Verdict: all required checks pass → accept.

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62",
  "checks": [
    {"name": "repo-alive", "grade": "pass", "evidence": "Last default-branch commit 2026-09-16 by Aburke225 (human), not archived"},
    {"name": "unclaimed", "grade": "pass", "evidence": "assignees: [], comments: 0, no linked or mentioned PRs found"},
    {"name": "bounded-scope", "grade": "pass", "evidence": "Body names exact fields/files (settings.redis_host vs redis_url in core/config.py) with reproduction steps and expected/actual behavior"},
    {"name": "ai-policy-ok", "grade": "pass", "evidence": "docs/CONTRIBUTING.md has no AI ban; repo's own commits use AI co-authorship"},
    {"name": "maintainer-responsive", "grade": "unclear", "evidence": "Sampled recent issues show 0-1 comments each, no clear maintainer-response timing signal"},
    {"name": "newcomer-label", "grade": "pass", "evidence": "Labels include good first issue and tier-1"}
  ],
  "verdict": "accept"
}
```
````

---

## Eval iterations

**Run history**

1. Smoke run, `--limit 3` (issue-01, issue-02, issue-03): 3/3 agreement. Partial run, so
   no file was written; I used it only to confirm the harness and my Claude CLI login
   worked after two failed attempts (the first died on a Windows GBK encoding error,
   fixed with `PYTHONUTF8=1`; the second on an expired CLI login).
2. Full run, all 20 scored issues: **18/20 agreement (bar: 18/20: PASS)**, categories
   `claimed 4/4  clear-accept 6/8  dead-repo 3/3  policy 1/1  scope 4/4`.

That second run is the one saved in `eval-run.txt` with `--save-run`, and 18/20 is the
agreement line in that file.

**Issue analysis**

`issue-04` (zxcalc/zxlive#555, "Missing several basic rule previews"). My rubric said
**reject**; the gold label is **accept**. Every check passed except `bounded-scope`,
which failed with the evidence: "Body: 'Including remove identity, fuse spiders, remove
self loops, etc.' — open-ended list of missing previews with no stated expected result,
not a single concrete change".

My check requires "one concrete change whose expected result is stated", and the entire
body of this issue is a single sentence with an "etc." in it. The grader had nothing to
match the "expected result is stated" wording against, so it fell through to my umbrella
clause. What the check could not see is that the issue was opened by a COLLABORATOR
(`RazinShaikh`, who is also the author of the repo's last five commits), carries a `good
first issue` label, and names one feature area (rule previews in Proof mode). The work is
small and known to the maintainer who filed it; only the *writing* is terse. My check
graded the polish of the writeup rather than the size of the work, which is exactly the
failure mode the evidence guide warns about: "Short is not the same as unscoped." The
same mistake cost me `issue-01`, where the check counted the five named docs pages of a
single documentation task as five separate changes.

**Check rationale**

The check that produced both misses, quoted as it currently stands in
`tools/issue-select/rubric.md`:

> | bounded-scope | Issue body, labels, opener's author_association, the comment thread, and closed linked PRs | Pass only if the issue asks for one concrete change whose expected result is stated (a bug with expected vs actual, a regression back to prior behavior, or a docs/cleanup task with named files or pages). Fail if ANY: (1) it is an umbrella, tracking, or "megaissue" list, or invites many PRs across the codebase; (2) it proposes a NEW feature, option, flag, or UI element and no OWNER/MEMBER/COLLABORATOR has opened or endorsed it with a settled design; (3) the thread shows design still being debated (competing proposals, "confirm the design first") with no maintainer decision; (4) 2 or more closed unmerged PRs have already attempted it; (5) it is a usage/support question. A short body or a missing good-first-issue label does not fail this check. | required |

It is written as one pass condition plus five named disqualifiers because each
disqualifier came from a specific way a friendly-looking issue wastes a newcomer's time,
and I wanted a grader to be able to point at the clause that fired rather than at a
feeling. Clause (1) is the megaissue pattern (`issue-10` is literally titled
"Documentation request megaissue"; `issue-05` invites "PRs both big and small" across the
whole codebase). Clause (2) exists because `issue-20` is a feature wish opened by a bot
with a product decision hiding inside it. Clause (3) and clause (4) both come from
`issue-15`, where a `good first issue` label sits on top of years of design debate and
two closed, unmerged attempts — the abandoned-PR count is the measurable half of "this is
harder than it looks". I kept the checks required-only for verdicts and left the two
soft signals (`maintainer-responsive`, `newcomer-label`) as preferred, so a quiet repo
like `issue-14`'s is ranked lower without being rejected.

**Trade-offs**

This check's strictness is what cost me both of my disagreements, so the trade-off is
concrete rather than hypothetical: it turns two accepts into rejects (`issue-01` and
`issue-04`), which is the difference between 18/20 and 20/20. The clause that does it is
the pass condition's demand that the expected result be *stated*. That demand is what
catches `issue-20` — a one-line feature wish with no spec — but it cannot tell that wish
apart from `issue-04`, a one-line bug report filed by the maintainer of the repo. In
other words, the check currently reads terseness as a risk signal, when terseness is only
a risk signal in the hands of someone who is not a maintainer. A newcomer using my rubric
loses two perfectly good first issues and is never told why; they simply see two rejects.

I would rather make that error than the reverse. A false reject costs you one candidate
out of the many a repo offers; a false accept costs you a weekend on a five-year design
argument. The fix I would make next is to add a clause that a bug or cleanup opened by an
OWNER/MEMBER/COLLABORATOR, with no umbrella and no open design debate, passes even when
the body is one sentence — which is the maintainer-endorsement idea already living in
clause (2), applied in the opposite direction. I did not make that change before saving
this run, because `eval-run.txt` fingerprints the exact `rubric.md` that produced it, and
the committed run and the committed rubric have to be the same file.

---

## Selection rationale

**Selection rationale**

1. **Fit and time.** Issue #62 is a FastAPI health-check route reading
   `settings.redis_host`, a field that does not exist on `Settings` (the real one is
   `redis_url`). It is the part of a Python codebase I have actually worked in — route
   handlers, a settings object, a Redis client — so my time goes into confirming the fix
   and its test rather than into learning the stack. It is a one-file change I can
   reproduce by starting the app and calling `GET /health`, which fits the hours I have
   before Unit 2 rather than a whole weekend. I ran the skill against three candidates
   (#62, #72, #64); it accepted all three, and the fit profile ranked #62 first, with #72
   (a `verify_password` bug that raises `UnknownHashError` instead of returning `False`)
   as the backup if #62 gets taken.

2. **What the verdict got right, and what I weighed myself.** The rubric correctly
   established the things I would otherwise have had to check by hand: the repo is alive
   (human commits the same week), nobody holds the issue (no assignee, no comments, no
   linked PRs), the contribution policy does not ban AI-assisted work, and the issue
   names its own files and its expected versus actual behavior. What it could not weigh
   is that an `AttributeError` on a config field is the kind of bug where the fix is
   obvious but the *scope of the fix* is a judgment call — whether to just rename the
   field access, or to also add the missing Redis probe the health check implies. It also
   could not tell me that this is a course repo where my classmates are looking at the
   same `tier-1` labels I am, which makes speed matter more than the rubric's claim check
   can express.

3. **Anticipated difficulty in claiming it.** The main risk is a race: #62 is labeled
   `good first issue` and `tier-1`, it has no comments yet, and there are about twenty
   classmates in this section reading the same list. By the time I post the claim comment
   in Unit 2, someone may have commented first. The Path Review house rule says that is
   survivable — credit attaches to the pull request I open, not to whether it merges, and
   a shared issue costs nobody anything — so the plan is to claim #62 anyway and keep #72
   as a second option if the thread is already crowded. The smaller risk is that the
   maintainer-response signal came back `unclear` on this repo, so I should not count on
   a fast reply if I need a question answered mid-fix.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
