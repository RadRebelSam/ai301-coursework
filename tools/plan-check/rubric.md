# Rubric: is this plan ready to post and build from?

Every check reads the plan and its comment against the issue and the
repro evidence in the same package. Judge the plan, not the prose: a
five-line plan with a named file and a decisive test is ready, and a
polished page that defers every real decision is not.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| cause-grounded | The plan's stated cause, read against the repro-evidence block: its steps, its outputs, and especially any control run or comparison it contains | Pass if the stated cause explains the behavior the repro evidence actually shows, and nothing in that evidence rules it out. Fail if a control run in the package already eliminates the named culprit (the same input without the flag behaves correctly, the named module prints fine in the same build, the symptom persists with the accused feature disabled), if the cause contradicts what an artifact shows, or if the cause is asserted about a layer the evidence never exercised. A cause adopted from the thread is graded against the evidence too: agreement with a confident commenter does not make it grounded. | required |
| bounded-scope | The plan's scope statement, its list of files or areas, and the work it describes, read against what the issue reports | Pass if the plan changes what the reported defect requires and says what it is leaving alone. Fail if the fix is bundled with work the issue never asked for: a rewrite or restructure of the surrounding module, a dependency migration, a new user-facing option or setting, a cross-cutting abstraction, a CI or test-harness overhaul, or a second symptom's fix folded in. The presence of a correct core fix does not rescue a plan that also does four other things. Explicitly deferring adjacent work, with or without reasons, is the opposite of this failure and passes. | required |
| executable | The plan's approach and the files or areas it names | Pass if a stranger could start work from the plan without asking the author a question: the change is decided (not a menu), and the place it lands is named (a file, a function, a named site or branch in the code). Fail if the layer or approach is still open ("gocui or tcell, not sure", "upstream or vendored, whichever is easier"), if the first step is to investigate or profile with no chosen change behind it, or if no file, function, or code site is named anywhere. Naming a site the repro evidence already points at counts as named. | required |
| decisive-test | The plan's test plan, read against the repro evidence's steps and artifacts | Pass if the test names something observable that separates fixed from not fixed: re-running the repro's own steps with the expected output stated (exit 0 instead of 101, the flipped color, the parsed value), a fixture or regression test over the issue's inputs, or an equivalent check whose result a reader could verify. Fail if the outcome is subjective ("should feel fast", "nothing else should feel broken"), or if the only test is running an existing suite with no statement of what about the fix that suite would catch. | required |
| thread-engaged | The plan comment, read against the "Thread highlights" (live: the issue thread): maintainer direction, an isolated culprit, an open or prior-art pull request, a stated preference for an approach | Pass if the comment engages the direction the thread actually contains: it follows the maintainer's isolation or preference, or says why it is doing something else, and it acknowledges an existing PR on the same issue instead of silently racing it. Fail if the thread carries explicit maintainer direction (a named culprit file, a posted patch, a request to test something, a settled approach) and the comment neither follows nor addresses it. An empty thread passes this check. | required |
| policy-disclosed | The repo-facts "contribution policy" line, read against the plan comment's own text | Only an explicit disclosure requirement creates an obligation. Fail when the stated policy requires disclosing AI usage (wording like "all AI usage must be disclosed") and the comment does not disclose it; every package here is AI-assisted work, so silence is a fail. Every other policy shape passes unless the comment visibly contradicts it: a policy asking for human-written comments is met by a comment that reads as a specific human account of this issue, and a silent or permissive policy always passes. | required |
| unknowns-named | The plan's risks, unknowns, or deferral lines | The plan names at least one thing it is not sure of, or a deferral with its reason, rather than presenting every step as certain. | preferred |
| repro-quoted | The plan's own text, read against the repro-evidence block | The plan quotes or cites the specific evidence it relies on (an output line, an exit code, a control result) rather than referring to the reproduction in general. | preferred |

## Verdict rule

Accept if and only if every required check passes: cause-grounded,
bounded-scope, executable, decisive-test, thread-engaged, and
policy-disclosed. Any required `fail` rejects the package.

`unclear` on a required check counts as `fail`: a plan whose grounding,
bounds, approach, test, or policy compliance cannot be established from
the package is not ready to build from. The preferred checks
(unknowns-named, repro-quoted) never change a verdict; report their
grades and cite them in the summary as what separates a strong accepted
plan from a bare one.
