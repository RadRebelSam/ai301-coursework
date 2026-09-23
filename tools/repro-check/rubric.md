# Rubric: is this reproduction package ready to post?

Every check reads the package against the issue it belongs to. Judge the
outcome, never the write-up's shape: a three-line report with a real
artifact beats a templated page with none.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| env-recorded | The repro report's environment record, read against the issue's stated target (issue body, repo-facts "latest release", and any environment the thread names as load-bearing) | Pass if the report names the tool's version AND the platform it ran on (OS or distro, or the equivalent runtime: browser, Python version, container driver), plus any additional axis this issue's behavior turns on where the issue or thread says it matters (shell, terminal emulator, GPU driver, build profile, install method, VM driver). Fail if there is no environment record at all, or if the load-bearing axis is missing on an issue that is specific to it (a Windows-only bug with no driver named, a build-profile-dependent panic with no profile named). A single dense line is enough; prose length is not the test. | required |
| steps-rerunnable | The repro report's steps, read as a stranger with a clean machine and no access to the author's files | Pass if a stranger could arrive at the same run: the starting state is obtainable by anyone (a public repo, a package install, or an input file that is either pasted or described precisely enough to recreate - "an env.yml with a valid dependencies list plus a category section" is recreatable, and passes), the commands are given verbatim, and the step that triggers the issue is present. Fail only when the reader is actually blocked: a required input exists only on the author's machine or behind a private door (an internal monorepo, "our config", a file referenced by name with nothing said about its contents), a step is a summary that hides the action ("set up the project", "configure as needed"), or the trigger step the issue names is missing from the sequence. | required |
| artifact-present | The repro report's artifacts: command output excerpts, tracebacks, logs, measurements, screenshots described in text | Pass if the report contains at least one artifact the author's own run produced, showing the state of the thing after the steps were followed. Fail if the report asserts an outcome with nothing shown ("I can confirm this", "reproduced on two machines", "I verified the race condition"), or if the only quoted material is copied from the issue rather than produced by this run. | required |
| artifact-shows-the-issue | The artifacts read line by line against the specific behavior the issue reports (its error text, exit code, symptom, and the syntax or input it names) | Pass if the artifact shows the issue's own reported behavior: the same error or symptom class, produced by the input form the issue names. Fail if the artifact shows an ADJACENT outcome presented as the reported one - a graceful validation or syntax error where the issue reports a panic or crash, a compile error caused by the author's own edit to the input, output from a different syntax form than the issue names, or a run that only proves the program starts. When the report's conclusion is "cannot reproduce", this check passes if the artifacts show the actual attempt (the commands run and what came back), because an honest negative result is proof of what was tried. | required |
| claims-match-evidence | The report's stated conclusion and confidence wording, set beside its own artifacts and environment record | Pass if the report's MAIN claim - the reported behavior it says it saw, or did not see - is backed by a shown artifact, and any deviation from the issue's target is stated in the report itself (a version delta acknowledged in one clause is enough; an honest "could not reproduce, here is what differed" passes in full). A secondary observation reported in passing without its own transcript (a control run summarized as "the no-replace form printed 1, 4, 7, 10 correctly") does not fail this check; it costs the preferred control-run check instead. Fail if the report narrates its main outcome over an artifact that does not show it, asserts a root cause it never demonstrates, uses certainty its evidence does not carry ("guaranteed reproducible", "this is definitely the cause"), or tests a materially different version or platform than the issue targets without saying so. | required |
| claim-specific-and-honest | The claim comment, read against the issue body and thread | Pass if the claim comment does two things: it shows the author read THIS issue (it names the issue's own specifics - the failing command, the error text, the file, the condition - rather than words that would fit any issue), and it states what the author will actually do next in a way they control (investigate, attempt a reproduction, report back). Fail if it is interchangeable boilerplate ("I'd like to work on this, please assign me"), a bare "+1" or "same here" with no stated intent, or if it promises an outcome or a date the author cannot guarantee ("I'll have a fix by Friday", "I'll definitely fix this"). | required |
| policy-respected | The repo-facts "contribution policy" line (and any AI policy it names), read against what the claim and repro comments actually say | Only an explicit DISCLOSURE requirement creates an obligation to write something. Fail only when the policy says AI use must be disclosed (wording like "all AI usage must be disclosed", "state the tool and the extent of the assistance") and neither comment discloses it; course packages are AI-assisted work, so silence there is a fail. Every other policy shape passes unless a comment visibly contradicts it: a policy asking that comments be written by a human in their own words is met by a comment that reads as a specific human account of this issue (no disclosure sentence required), and policies asking the contributor to understand, test, or human-review the work are met unless the package says otherwise. A silent or permissive-with-responsibility policy always passes. | required |
| control-run | The repro report's artifacts, looking for a second run alongside the failing one | The report also shows a run that does NOT exhibit the issue (the neighbouring input, the prior version, the documented-good path), so the difference isolates the trigger. | preferred |
| next-step-concrete | The closing lines of the claim comment or repro report | The package names a specific next action tied to this issue (a file, function, or hypothesis to check), rather than "I'll keep looking". | preferred |

## Verdict rule

Accept if and only if every required check passes: env-recorded,
steps-rerunnable, artifact-present, artifact-shows-the-issue,
claims-match-evidence, claim-specific-and-honest, and
policy-respected. Any required `fail` rejects the package.

`unclear` on a required check counts as `fail`: proof that cannot be
verified from the package is proof that is not ready to post. The one
exception is the claim-only draft state defined in SKILL.md, where the
checks that need the repro report (env-recorded, steps-rerunnable,
artifact-present, artifact-shows-the-issue, claims-match-evidence,
control-run) are reported `unclear` with evidence `not yet applicable:
claim-only draft` and left out of the verdict; the verdict then rests
on claim-specific-and-honest and policy-respected alone.

The preferred checks (control-run, next-step-concrete) never change a
verdict. Report their grades and mention them in the summary as what
makes an accepted package stronger than the bar.
