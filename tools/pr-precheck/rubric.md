# Rubric: is this PR ready to submit?

Every check reads the package against the plan it claims to implement,
the issue that plan belongs to, and the repo's stated standards. Judge
the artifact, not the writing: a terse PR with a matched diff and a
re-run repro is ready, and a polished one that quietly grew is not.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| plan-matched | Every hunk of the diff, listed as a unit of work, set against the plan's scope and files, including anything under the plan's `## Deviations` heading | Pass if every change in the diff is work the plan asked for, or work a deviation note records; and if the plan's promised changes are all present, or the ones missing are disclosed. Fail if the diff carries work the plan never named and no note records it - a rename or refactor pass over the touched module, a new user-facing option, flag, or setting, a second behavior fixed along the way, a dependency bump - or if the plan promised two things and the diff delivers one with no disclosure. A disclosed shortfall passes: "the docs update is deferred, here is why" is a matched plan, not a missing one. | required |
| description-true | The PR description's claims about the change, read line by line against the diff | Pass if the description describes the diff that exists: its summary, its change bullets, and any fidelity claim it makes ("no changes beyond the plan", "docs now document the limitation") are each borne out by a hunk in the diff. Fail if any claim is contradicted by the diff - a documented behavior with no docs hunk, a "plan only" claim over a diff with unplanned hunks, a bullet describing work that is not there. Silence about something the diff does is graded by plan-matched; this check grades what the description asserts. | required |
| evidence-decisive | The test evidence, read against the plan's test plan and the issue's own reproduction | Pass if the evidence shows the issue's own failure path exercised before and after the change, with the output that distinguishes them, AND the repo's stated checks (its template's test boxes, its documented commands) were actually run with their results shown - a failure reported honestly still counts as run. Fail if the before/after is missing or asserted rather than shown ("tested locally, works now", "verified on my machine", "tests pass"), if the evidence exercises a path the change does not affect while the issue's repro is never re-run, or if the plan's test plan named two failure modes and only one was re-run with no note about the other. | required |
| diff-clean | The diff itself, hunk by hunk, ignoring the description | Pass if every hunk is part of the change or its tests. Fail on debris: a debug print or logging line left in, commented-out code or a commented-out earlier attempt, a dead variable, function, or buffer the change does not use, formatting or import-reordering churn over lines the change does not touch, or a hunk in a file unrelated to the fix. Size alone is not debris: a large diff made entirely of the planned change passes, and a one-line diff with a stray `eprintln!` fails. | required |
| standards-met | The repo's PR template and its stated contribution and AI policy, read against the PR title and description | Pass if the description fills the template's required sections with real content and meets the repo's stated asks: the issue linked in the form the template asks for (`Closes #123`), the type-of-change or checklist marked, the testing section filled with what was actually run, and - when the repo's stated policy requires disclosing AI assistance - a disclosure in the description's own words. Fail if a required section is visibly ignored, left as the template's placeholder text, or ticked with nothing behind it, or if the policy requires disclosure and the description carries none. All work graded here is AI-assisted, so silence under a disclosure requirement is a fail, however good the change is. A repo with no template and no policy passes this check. | required |
| smallest-surface | The diff's chosen approach, read against the plan's stated options or alternatives | The change takes the narrowest route the plan identified (a guard at the shared call site rather than at each caller, the plan's "smallest surface" option rather than a rework). | preferred |
| tests-added | The diff's test files | The change adds or extends a test that fails without the fix, so the behavior cannot silently regress. | preferred |

## Verdict rule

Accept if and only if every required check passes: plan-matched,
description-true, evidence-decisive, diff-clean, and standards-met.
Any required `fail` rejects the package.

`unclear` on a required check counts as `fail`: a claim the package
cannot verify is a claim that is not ready to go out. Grade `unclear`
only when the package genuinely lacks the evidence a check names - a
package whose evidence is present and weak is a `fail` with the weak
thing quoted.

The preferred checks (smallest-surface, tests-added) never change a
verdict. Report their grades and cite them in the summary as what
separates a strong accepted PR from a bare one.
