# Procedure: how this skill grades a plan package

Follow these steps in order. They assume the rubric in `rubric.md` and
the map in `references/evidence-guide.md`.

## Read order

1. Read the **issue** section first (live: the issue body). Write down
   the reported defect in one line: the trigger, the symptom, and any
   error text or exit code it names. Everything later is graded against
   this line.
2. Read the **repro-evidence block** second, before the plan (live: the
   student's posted repro comment, or the house repro pack as quoted in
   the drafts). Write down: the steps run, each artifact's key output,
   and - separately, because it decides the hardest check - every
   control or comparison run and what it rules out. Read this before
   the plan on purpose: a plan read first is persuasive, and a cause
   read before its evidence gets believed instead of checked.
3. Read the **thread highlights** third (live: the issue thread). Write
   down any maintainer direction: a named culprit file, a posted patch,
   a request to test something, a settled approach, an open or merged
   PR on the same issue. If the thread is empty, record "no thread
   direction" - that is a pass condition later, not a gap.
4. Read the **repo-facts block** fourth. Write down one thing: what the
   contribution policy says about AI usage, in its own words, and
   whether it uses disclosure language.
5. Read the **candidate plan** fifth, in full, before grading anything.
   Write down: its stated cause, its scope statement and any
   not-in-scope line, the files or sites it names, its approach, its
   test plan, and its risks or deferrals.
6. Read the **candidate plan comment** last. Write down what it tells
   the thread, and whether it mentions AI assistance.

Do not grade while reading. Reading ends with six notes; grading starts
after.

## Evidence gathering

One gathering move per evidence family. Record the quote, not an
impression, because every grade has to cite a fact.

1. **Grounding.** Put the plan's stated cause (step 5) next to the
   control runs (step 2). For each control, ask one question: does this
   result remain possible if the plan's cause is true? Record the
   answer and the control's output line. In live mode the repro
   evidence comes only from the posted repro comment or the quoted
   house pack; do not use the student's local files.
2. **Scope.** List every distinct kind of work the plan describes, one
   line each (fix the subtraction; migrate the HTTP client; add a
   setting). Mark each as either required by the reported defect or
   not. Record the not-required ones verbatim, and record any
   not-in-scope or deferral line as its counterweight.
3. **Executability.** Extract two things: the chosen approach, and
   every file, function, or code site named. Record them verbatim. If
   the approach is a menu of options or an investigation, record the
   phrase that shows it.
4. **Test.** Extract the test plan's stated observable: the command or
   input, and what the plan says the result will be after the fix.
   Put it beside the repro's own steps and outputs from step 2 and
   record whether the two line up.
5. **Comms.** Put the comment (step 6) next to the thread direction
   (step 3). Record which piece of direction the comment engages, and
   how - follows it, argues with it, or is silent about it.
6. **Policy.** Put the comment's text next to the policy note (step 4).
   Record whether the policy uses disclosure language, and whether the
   comment discloses AI assistance.

## Check execution

1. Grade the required checks in this order: cause-grounded,
   bounded-scope, executable, decisive-test, thread-engaged,
   policy-disclosed. Then the preferred ones: unknowns-named,
   repro-quoted. Cause first because a plan built on a ruled-out cause
   makes its other virtues irrelevant, and it is the check most easily
   talked out of by a confident write-up.
2. Grade each check only from the notes gathered for its family above.
   Do not re-read the whole package for a check; if a note is missing,
   go back for that one fact and add it to the notes.
3. Apply the rubric's pass condition literally. Where it names a
   failure shape, the package must show that shape for a fail. A check
   that feels wrong but passes by its stated condition passes; say so
   in the summary instead of adjusting the grade.
4. Every grade gets one line of evidence: a quote from the package or a
   fact from it, never "looks fine". For a fail, quote the thing that
   fails, not the rule it breaks.
5. Grade `unclear` only when the package genuinely does not contain the
   evidence the check names - not when the evidence is present and
   weak. Weak evidence is a fail with the weak thing quoted.
6. A check never reads another check's verdict. A plan with a ruled-out
   cause can still have a decisive test, and it gets a pass there.

## Verdict assembly

1. Collect the six required grades. If every one is `pass`, the verdict
   is `accept`. If any is `fail`, the verdict is `reject`.
2. Convert every `unclear` on a required check to `fail` before step 1,
   per the rubric's verdict rule.
3. Preferred grades never enter the verdict. Report them, and on an
   accepted package mention them in the summary as what makes the plan
   strong or bare.
4. On a reject, the deciding check is the first required check that
   failed in the execution order above. Quote its evidence line in the
   summary, so the author knows which single thing to fix first. On an
   accept, quote the cause-grounded evidence: it is the grade the rest
   of the plan rests on.
5. Emit the JSON block last, with one entry per check in execution
   order and the verdict from step 1.
