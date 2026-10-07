# Procedure: how this tool grades a PR package

Follow these steps in order. They assume the checks in `rubric.md` and
the map in `references/evidence-guide.md`.

## Read order

Most steps here compare two things side by side, so the order decides
which one is read first - and the one read first is the one that
frames the other. Read the references before the candidate, always.

1. **The issue.** (Live: fetch it. Eval: the bundle's issue section.)
   Write down the reported defect in one line - trigger, symptom, and
   the error text or exit code it names - and any maintainer direction
   in the thread.
2. **The plan, including its `## Deviations` section.** Write down:
   the work it promises, the files it names, what it says is out of
   scope, and every deviation it records. Deviations are part of the
   plan from this moment on; do not grade them as drift later.
3. **The repo's standards.** (Live: the PR template and the
   contributing or policy docs. Eval: the repo-facts block.) Write
   down the template's required sections and whether the stated policy
   demands AI disclosure, in its own words.
4. **The diff, before the description.** Walk it hunk by hunk and
   write one line per hunk: file, what it does, and whether it traces
   to something in the plan note from step 2. This order matters: a
   description read first tells you what the diff means, and you stop
   seeing what it actually contains.
5. **The test evidence.** Write down: which commands were run, what
   output is shown, and whether the issue's own repro appears with a
   before and an after.
6. **The PR title and description, last.** Write down every claim it
   makes about the change, especially fidelity claims ("only the plan",
   "docs updated", "no other changes").

Reading ends with six notes. Grading starts after; do not grade while
reading.

## Evidence gathering

One gathering move per family. Record quotes and facts, never
impressions - every grade has to cite one.

1. **Plan fidelity.** Put the hunk list (step 4) beside the plan note
   (step 2) and sort every hunk into one of three piles: planned,
   deviation-recorded, or unaccounted. Then run the comparison the
   other way: every promise in the plan, matched to a hunk or marked
   missing. Record the unaccounted hunks verbatim and the missing
   promises by name, plus whether anything disclosed covers them.
2. **Description truth.** Take each claim from step 6 and name the
   hunk that backs it, or record that none does. A fidelity claim is
   graded against the unaccounted pile from move 1.
3. **Test evidence.** Put the evidence (step 5) beside the plan's test
   plan and the issue's repro (steps 2 and 1). Record three things:
   whether the before and after are shown rather than asserted,
   whether the command exercises the path the diff changes, and which
   of the repo's stated checks were run with their results.
4. **Diff quality.** Walk the hunk list once more, this time ignoring
   the plan, and record every line that is not part of the change or
   its tests: debug prints, commented-out code, dead declarations,
   reformatted untouched lines, unrelated files.
5. **Standards.** Put the description beside the template's required
   sections (step 3) and record, section by section, whether it is
   filled with real content, left as placeholder text, or absent. Then
   record whether the policy demands disclosure and whether the
   description discloses.

Live mode gathers steps 1, 3, and 5's repo-side facts from the live
repo and the issue thread; eval mode takes every one of them from the
bundle text and fetches nothing.

## Check execution

1. Grade the required checks in this order: plan-matched,
   description-true, evidence-decisive, diff-clean, standards-met.
   Then the preferred ones: smallest-surface, tests-added. Plan
   fidelity goes first because it is the comparison a confident
   description most easily talks you out of, and it is the one whose
   outcome the author cannot see for themselves.
2. Grade each check only from the notes gathered for its family. Do
   not re-read the whole package for one check; if a note is missing,
   go back for that one fact and add it.
3. Apply each pass condition literally. Where it names a failure
   shape, the package must show that shape to fail. A check that feels
   wrong but passes by its stated condition passes - say so in the
   summary instead of bending the grade.
4. Before failing plan-matched or description-true, check the plan's
   deviation section and the description one more time for a
   disclosure covering the thing you are about to fail. Disclosed is
   not drift; that is the rule this set is built to test.
5. Every grade carries one line of evidence: a quote from the package
   or a fact from the diff. For a fail, quote the failing thing - the
   debris line, the unaccounted hunk, the asserted-not-shown claim.
6. Grade `unclear` only when the package genuinely lacks the evidence
   a check names. Present-but-weak evidence is a `fail` with the weak
   thing quoted.
7. No check reads another check's grade. A PR with an unaccounted hunk
   can still have decisive evidence, and it gets a pass there.

## Verdict assembly

1. Convert every `unclear` on a required check to `fail`, per the
   rubric's verdict rule.
2. If all five required checks pass, the verdict is `accept`. If any
   fails, it is `reject`.
3. Preferred grades never enter the verdict. Report them; on an accept,
   name them in the summary as what makes the PR strong or bare.
4. On a reject, the deciding check is the first required check that
   failed in the execution order above. Quote its evidence line in the
   summary so the author knows the one thing to fix first. On an
   accept, quote plan-matched's evidence: it is the claim the rest of
   the PR rests on.
5. Live mode only: after the summary's check lines, report any
   voice-guide rule the title or description breaks, quoting rule and
   line, and state that it does not change the verdict unless a rubric
   check read it.
6. Emit the JSON block last, one entry per check in execution order,
   with the verdict from step 2 and nothing after it.
