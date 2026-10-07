---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer one question about one PR package: **is this ready to submit?**

A PR package is a candidate pull request - its title, description,
commits, diff, and test evidence - read against two fixed references:
the plan it claims to implement, and the issue that plan belongs to.
Those references are what make the question answerable. A diff is not
"too big" in the abstract; it is bigger than the plan it claims to
follow. Evidence is not "thin" in the abstract; it fails to re-run the
repro the plan named.

Do not answer any other question. Do not grade whether the fix is the
best possible fix, whether the issue was worth fixing, or whether the
code is elegant. Grade one package per run, and grade it by executing
the components in this directory - never from impression.

## Inputs and modes

**Live mode.** The student's own submission, checked before it goes
out. Read exactly these inputs:

- `plan.md` in the working directory, including everything under its
  `## Deviations` heading. This is the reference the diff is graded
  against, deviations included: a change recorded there is part of the
  plan, not drift.
- The diff on the branch: every change the branch makes relative to
  the repo's default branch. Produce it with
  `git diff main...HEAD` (three dots), run from the working copy, and
  grade what that command prints. Uncommitted working-tree changes are
  not part of the package; if `git status` shows the student's own
  draft files (`plan.md`, `comment.md`, `pr_draft.md`,
  `test_evidence.md`) as untracked, that is correct and not a finding.
- The draft PR title and description, in `pr_draft.md` (title on the
  first line, description below it).
- The test evidence, in `test_evidence.md`.
- The issue named in the request, read live: its body, its thread, and
  the direction it carries.
- The repo's stated standards, read live: its PR template
  (`.github/PULL_REQUEST_TEMPLATE.md`) and its contributing and policy
  docs (`docs/CONTRIBUTING.md`, any `AI_POLICY.md` or `AGENTS.md`).

A house-chain student has no plan of their own: read the house plan
and the house repro pack as quoted in their files, and grade the same
checks against those. If the package quotes no plan at all, that
absence is what the plan-fidelity checks grade; do not substitute your
own idea of what the change should have been.

Gather issue-side and repo-side facts with `gh`, the GitHub API, or
the web. Read the drafts the way a maintainer will read the opened PR:
the package is what the title, description, diff, and evidence
contain, not what sits elsewhere in the student's working directory.

**Eval mode.** A package bundle is the whole world. Every fact comes
from the bundle text; fetch nothing, read nothing else, and never
reason from what the real upstream repo does today. Eval mode always
grades a complete package: every check runs and the full verdict rule
applies.

## The scope seam (live mode only)

Read `scope.md` before anything else, before the plan and before the
diff.

- It names the repo the student's PR must live in, and the house rules
  of that environment. Apply a house rule wherever a check reads
  evidence it touches.
- If the package's issue or PR is outside the scoped repo, refuse to
  grade and say which repo is in scope.
- If the `Repo:` line still carries an unfilled placeholder (anything
  in angle brackets), stop without grading and tell the student to put
  their section's Path Review repo on that line. Never guess a scope,
  and never infer one from the working directory's git remote.

Eval mode ignores `scope.md` entirely.

## The voice seam (live mode only)

Read `voice-guide.md` after the rubric and the procedure, before
writing the summary. It gates exactly two pieces of outgoing text: the
PR title and the PR description.

Hold both against the guide's rules. For each rule the draft breaks,
quote the rule and quote the line that breaks it, in the summary. The
voice guide never changes the verdict on its own; only a rubric check
that reads it can do that. Say so when reporting a break, so the
student knows whether they are looking at a blocker or a note.

Eval mode ignores `voice-guide.md` entirely.

## Component reads

Three files decide everything. Read all three before grading:

1. `rubric.md` - the checks table and the verdict rule. Each row names
   the check, the evidence it reads, its pass condition, and its
   weight: `required` gates the verdict, `preferred` never does.
2. `references/evidence-guide.md` - the map. For each evidence family
   it says where that evidence lives in a package (and, live, where on
   GitHub) and what good looks like there. Use it to find what a check
   names; do not go looking elsewhere.
3. `procedure.md` - the operating steps: read order, how each evidence
   family is gathered, how a check executes, and how grades become a
   verdict. Follow it as written, exactly, the way an executor follows
   a rubric.

Where the procedure is silent, note the gap in the summary rather than
inventing a step: a procedure gap is feedback the student needs, and
inventing around it makes two runs disagree.

Refuse to grade if `rubric.md` has no checks or `procedure.md` has no
steps. Say which file is empty and stop. The instruction comments that
ship inside the templates are not content; only written checks and
written steps count. A tool that invents checks at runtime produces
noise that looks like judgment, which is worse than no tool.

## Verdict and output

Apply the rubric's verdict rule to the check grades and emit one of
two verdicts: `accept` (ready to submit) or `reject` (hold). There is
no third verdict and no score. A reservation goes in a check's
evidence line, never in the verdict.

Treat `unclear` as the rubric's verdict rule directs. Where that rule
is silent, `unclear` on a required check counts as `fail`: a claim the
package cannot verify is a claim that is not ready to go out.

Before the JSON block, print a short readable summary: one line per
check with its grade and the deciding fact, any voice-guide breaks
(live mode), and any procedure gap hit. Then emit the fenced JSON
block below, valid and last, with nothing after it. The schema is
CONTRACT.md's, verbatim; do not edit it.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- **Evidence first.** Never grade a check without naming the fact or
  quote that decided it. "Looks fine" is not evidence. For a fail,
  quote the thing that fails, not the rule it breaks.
- **Grade the thing, not the polish.** A terse complete PR can be
  ready; a beautiful confident one can be hiding drift. Read the diff,
  the evidence, and the description against the plan and the stated
  standards, never against how well they are written.
- **An honest shortfall can still be ready.** A PR that does less than
  the plan and says so - in the description, in the plan's deviation
  note, or both - is disclosed work, not drift. What fails is silence:
  a diff that quietly does more or less than the plan, or a
  description claiming a fidelity the diff contradicts.
- **The rubric decides, not the run.** If a check passes by its stated
  condition but feels wrong, it passes. Note the tension in the
  summary; the fix belongs in the rubric.
- **The procedure decides how, not the run.** Follow `procedure.md`
  and report its gaps.
- **One package per run.** Never fold a second package, a sibling PR,
  or an earlier version of the same branch into the grade.
