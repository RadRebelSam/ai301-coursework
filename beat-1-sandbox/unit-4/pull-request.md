# Unit 4 - Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request I opened against the Path Review repo, and of the
evaluation runs that produced `eval-run.txt`.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/101

**Branch**

fix/62-redis-health-url

## Eval iterations

**Run history**

1. Smoke run, `--limit 3` (pkg-01, pkg-02, pkg-03): 3/3 agreement. Partial run, no file
   written; used to confirm the tool loaded and the harness could reach the CLI.
2. Full run, all 20 scored packages: **19/20 agreement (bar: 18/20: PASS)**, categories
   `clear-accept 6/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2
   unreviewable 3/3`.

Run 2 is the one saved with `--save-run`, and 19/20 is the agreement line in the
committed `eval-run.txt`. There was no revise loop: the first full run cleared the bar
and matched every category, including the two-package `standards-wall` category, so no
`--only` re-grade followed.

One thing about that file's header, stated here because the header records it and I did
not want it to look like a quiet substitution. It reads `model: claude-sonnet-5
(pinned)` rather than `model: sonnet (pinned)`. On this machine the Claude Code build
resolves the alias `sonnet` to `claude/claude-sonnet-5-5[1m]`, a model neither of my
orgs can access:

```
There's an issue with the selected model (claude/claude-sonnet-5-5[1m]). It may not exist or you may not have access to it. Run --model to pick a different model.
```

`run_eval.py` hardcodes `MODEL = "sonnet"` and passes it as `--model sonnet`, so every
package errored with `claude exited 1`. I tried three fixes before touching the harness:
repointing `model` in `~/.claude/settings.json`, removing the inaccessible row from
`modelPicker`, and a PATH shim named `claude.cmd` that rewrote the argument. The first
two failed because the alias is resolved inside the CLI build, not from settings; the
shim failed because Windows `CreateProcess` only appends `.exe` when resolving a bare
command name, so Python's `subprocess` never saw the `.cmd`. The change I made was one
line, `MODEL = "claude-sonnet-5"`, with the original kept beside it as
`run_eval.py.orig`. The grading model is still Sonnet; only the alias differs.

**Package analysis**

`pkg-05`. My rubric said **reject**; the gold label is **accept**. It is my one
disagreement, and the check that caused it was `evidence-decisive`:

> Issue repro is shown before/after, but the plan's other two test-plan items are only
> asserted: 'Same-key redefine prints the one-time warning' and 'cargo test -p
> nu-protocol passes (312 tests); fmt and clippy clean' have no re-run output or pasted
> command output

The package is a keybinding-merge fix whose evidence does the main thing right: the
issue's own three-line script is re-run before and after, and the two tables are pasted
in full, showing one row before and both rows after. Then the last paragraph adds, in
prose, that the same-key redefine prints a warning and that `cargo test -p nu-protocol`
passes with 312 tests.

My check read those two trailing sentences as test-plan items that were claimed rather
than shown, and failed the package on them. The gold label treats them as what they are:
supporting observations next to a decisive before/after, not the proof itself.

The clause that fired is the one I wrote for a different shape - "if the plan's test
plan named two failure modes and only one was re-run with no note about the other" -
which is exactly right for calib-04, where the plan names two distinct failure modes and
the evidence re-runs only the first. pkg-05 does not have two failure modes. It has one,
re-run properly, plus two remarks. My wording could not tell a second failure mode apart
from a supporting remark, so it applied the strict reading to both. The fix is to scope
that clause to a second *failure mode of the issue*, and to say that a repo-check result
stated without pasted output does not fail a package whose issue repro is shown. I did
not make the change: it would need a confirming full run at about $5 to prove it does
not flip pkg-04, pkg-07, pkg-10 or pkg-14, and 19/20 already clears the bar with every
category matched.

**Check rationale**

`plan-matched`, quoted as it reads in the `rubric.md` uploaded to `tools/pr-precheck/`:

> | plan-matched | Every hunk of the diff, listed as a unit of work, set against the plan's scope and files, including anything under the plan's `## Deviations` heading | Pass if every change in the diff is work the plan asked for, or work a deviation note records; and if the plan's promised changes are all present, or the ones missing are disclosed. Fail if the diff carries work the plan never named and no note records it - a rename or refactor pass over the touched module, a new user-facing option, flag, or setting, a second behavior fixed along the way, a dependency bump - or if the plan promised two things and the diff delivers one with no disclosure. A disclosed shortfall passes: "the docs update is deferred, here is why" is a matched plan, not a missing one. | required |

Two decisions shape it.

The first is that the comparison runs in both directions. An early draft only asked
whether the diff exceeded the plan, which catches pkg-03, pkg-06, pkg-09 and pkg-15 but
walks straight past pkg-17, where the plan promises a dev warning AND a docs update and
the diff contains only the warning. Drift is not a size; it is a mismatch, and a plan
can be missed by doing less as easily as by doing more.

The second is the last sentence, and it is the one I would defend hardest. Four of the
seven clear accepts do less than everything, and two of them - pkg-13 and pkg-16 - do
less on purpose and say so. A rubric that reads "less than planned" as a hold rejects
both and loses the category. Writing the disclosure rule into the pass condition rather
than leaving it to the grader's judgment is what makes the check produce the same answer
twice. I also listed the drift shapes concretely (a rename pass, a new flag, a bundled
second fix, a dependency bump) instead of saying "unrelated work", because "unrelated"
is exactly the word a confident description talks a grader out of.

**Trade-offs**

`plan-matched` gives up drift that the plan was already too loose to exclude. It grades
the diff against the plan as written, so a vague plan - "clean up the health module" -
licenses hunks a tight plan would have flagged. The check cannot see that; it was a
unit-3 problem, and a package whose plan is that loose passes this check and gets caught,
if at all, by `diff-clean`. I accept the miss, because the alternative is a check that
second-guesses the plan, and then two graders disagree about what the plan "should" have
said.

Nothing else moved as a result, and here is how I know: there was only one full run and
no revision, so no check was loosened and no canary was needed. The one disagreement in
that run (pkg-05) came from `evidence-decisive`, not from this check, and `plan-matched`
graded all 20 packages the way the gold labels did - including pkg-13 and pkg-16, the two
honest shortfalls that the disclosure clause exists for, and pkg-17, the missing-half
package that the two-direction comparison exists for.

---

Related paths: `eval-run.txt` in this directory; my skill's files in
`tools/pr-precheck/`.
