# Evidence guide: where evidence lives in a PR package

The map each check reads. One heading per evidence family, matching
the harness's reject categories. For each: where the evidence sits in
an eval bundle, where it sits in a live submission, and what good
looks like.

A rule that holds across all four: the package is the title, the
description, the diff, and the evidence. A file on the author's disk,
a fix "already done locally", a test run nobody pasted - none of these
are in the package, and a check that cannot find its evidence in the
package grades what is there.

## Plan fidelity (harness category: silent-drift)

**Where it lives.** In an eval bundle: `## Plan context` carries the
plan - its scope, its named files, its out-of-scope line, and any
deviation note - and `## Candidate PR` carries the `### Diff` it is
compared against. Live: `plan.md` in the working directory, including
everything under `## Deviations`, against the output of
`git diff main...HEAD` run in the working copy.

**What good looks like.** Every hunk traces to something the plan
asked for, and every promise the plan made is either in the diff or
disclosed as not. Build the two lists and compare them in both
directions; the failure is always visible once the hunks are listed
one line each:

- A one-line fallback fix that arrives with a `truncate()` rewrite and
  a new `resolve_symlinks` config option.
- A theming fix bundled with a member-renaming pass and a new
  experimental setting.
- An error-surfacing fix that also rewords unrelated flag help text
  and adds a `--time-format` flag the plan explicitly scoped out.
- A plan promising a warning AND a docs update, with only the warning
  in the diff.

The opposite of drift is not completeness - it is disclosure. A
caret-escaping fix that defers `%VAR%` expansion, with the deferral in
the plan's deviation note and in the description, is a matched plan.
Read the deviation section before calling anything drift; that is
where honest work gets recorded, and grading it as drift punishes the
behavior the course is teaching.

## Test evidence (harness category: not-tested)

**Where it lives.** In an eval bundle: `### Test evidence` under
`## Candidate PR`, read against the plan's test plan in
`## Plan context` and the issue's own reproduction in `## Issue`.
Live: `test_evidence.md`, plus the Testing section of `pr_draft.md`,
against the repro steps in the issue thread and the plan's test plan.

**What good looks like.** Two things are shown, not claimed. First,
the issue's own failure path, before and after, with the output that
distinguishes them - the exception then `parsed OK`, exit 101 then
exit 0, the garbled render then the correct one. Second, the repo's
own checks, run with their results pasted; a failing check reported
honestly still counts as run, and hiding one does not.

The failures in this family all look like confidence:

- "Tested locally, colors work now, cargo test passes" with no
  before, no after, and no command.
- "Verified working on my machine for a full day" where the plan's
  delayed-connect before/after is never shown.
- Evidence that exercises the path the change does not touch: a GET
  with header casing when the issue reports a single-header POST, or
  the single-file control when the bug needs two files.
- A plan whose test plan names two failure modes, with only one
  re-run and nothing said about the other.

"Tests pass" names nothing. A decisive artifact names the command, the
input, and what changed in the output.

## Diff quality (harness category: unreviewable)

**Where it lives.** In an eval bundle: the `### Diff` block, read on
its own, with the description set aside. Live: `git diff main...HEAD`,
and `git diff main...HEAD --stat` for the file list.

**What good looks like.** Every hunk is the change or its tests. Read
the diff for what a reviewer would have to skip past:

- A debug print or `eprintln!` left in a loop.
- Commented-out code, including a commented-out first attempt kept
  "for reference".
- A dead variable, buffer, counter, or function the change never uses.
- Formatting or import-reordering churn over lines the change does not
  touch, and blocks of identical lines re-printed with no semantic
  difference.
- A hunk in a file unrelated to the fix.

Size is not debris. A long diff that is entirely the planned change is
reviewable; a three-line diff with a stray debug line is not. Note the
difference from plan fidelity: an unplanned *feature* is drift, while
an unplanned *artifact of working* - a print, a dead counter, churn -
is debris. Both fail, by different checks, and the distinction is what
tells the author which fix to make.

## Standards and comms (harness category: standards-wall)

**Where it lives.** In an eval bundle: the `## Repo facts` block names
the PR template's asks and quotes the contribution policy, read
against `### Title` and `### Description`. Live: the repo's
`.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` (plus
any `AI_POLICY.md` or `AGENTS.md`), read against `pr_draft.md`.

**What good looks like.** The description does what the repo asks, in
the form it asks for:

- The issue linked the way the template words it - `Closes #2211`, not
  a bare mention in prose, when the template asks for the keyword.
- The checklist or type-of-change boxes marked, and marked truthfully:
  a ticked box with nothing behind it reads worse than an unticked
  one.
- Every required section filled with real content. Template
  placeholder text left in place is the same as an empty section.
- Where the repo's stated policy requires disclosing AI assistance, a
  disclosure in the author's own words, in the description. All work
  graded here is AI-assisted, so silence under a disclosure
  requirement fails - including on an otherwise excellent PR
  implementing a maintainer-authored diagnosis with decisive evidence.
  That package exists in the set precisely because the fix is good.

Two shapes pass that look like they should not. A repo with no
template and no stated policy asks nothing, so a terse description
that names the issue satisfies this family. And a policy that asks
only for human-written prose or contributor responsibility - not for
disclosure - is met by a description that reads as a specific human
account of this change.
