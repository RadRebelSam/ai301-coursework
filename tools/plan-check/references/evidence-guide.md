# Evidence guide: where evidence lives in a plan package

The map the checks read. Each family says where to look and what good
looks like. The package is what the plan and the comment contain and
quote; a file on the author's disk is not evidence.

## Diagnosis and grounding

**Where it lives.** The plan's cause sits in its opening lines or under
a "diagnosis" or "root cause" heading in the `## Candidate plan`
section. The behavior it must explain sits in `## Repro evidence`: the
steps, the fenced outputs, the exit codes, and - the part that decides
this family - any control or comparison run (the same command without
the flag, the prior version, the feature disabled, a top-level case).
Live: the plan's own text against the student's posted repro comment on
the issue.

**What good looks like.** The cause explains the artifact that was
actually produced, and survives every control in the package. Read each
control as an elimination: if the same items parse fine without `-v`,
the item tokenizer is not the cause; if the instance method prints a
friendly error in the same 2.x build, the module was not tree-shaken
out; if the timing matrix shows the 26-second cost with no pager in the
loop, the pager bindings are not the cause. A plan that names a culprit
some control has already eliminated is ungrounded no matter how
confident it reads, and a cause borrowed from a commenter is graded the
same way as one the author invented. Watch for the subtler version too:
a cause about a stage the evidence never exercised (blaming a
post-read cast when the evidence shows the data already wrong before
that cast runs).

## Scope

**Where it lives.** The plan's scope statement and its not-in-scope or
deferral line, plus the list of files or areas it plans to touch and
the work its approach describes step by step. Read against the issue's
reported defect in the `## Issue` section.

**What good looks like.** Every piece of work the plan describes traces
back to the reported defect, and the plan says out loud what it is
leaving alone. The failure is easy to see once the work is listed one
line per item: the one-constant fix that also brings a dependency
migration, a settings panel, and a retry framework; the empty-tar fix
that also regenerates preloads, upgrades containerd, and adds a CI
matrix; the missing-class removal that also rewrites a state machine
and ports a component. A correct core fix inside that list does not
rescue it. The opposite - "I am not touching the tombstone rework, here
is why" - is exactly what a bounded plan looks like, and a plan that
scopes itself down to less than the issue asks, with its reasons, is
still bounded.

## Executability

**Where it lives.** The plan's approach section and any list of files,
functions, branches, or code sites. Live: the same parts of the draft
`plan.md`.

**What good looks like.** A stranger reads it and knows what to change
and where, without asking the author anything. Two concrete signals:
the approach is one decided change rather than a menu ("clamp the
subtraction at both sites" beats "recover() somewhere, upstream or
vendored, whichever is easier"), and at least one real location is
named (`src/printer.rs` line 934, the erase-scrollback branch, the
grouping site the thread isolated). Naming the site the repro evidence
already isolated counts. The failure reads as a plan to make a plan:
profile first, investigate the input stack, pick the layer later -
every real decision deferred to build time.

## Test plan

**Where it lives.** The plan's test or verification section, read
against the `## Repro evidence` steps and their outputs.

**What good looks like.** The test names an observable that changes
when the fix lands: re-run the repro's own command and state the
expected result (exit 0 where the repro showed 101, the highlight
colored where it printed plain, the JSON parsing where it errored), or
add a fixture or regression case over the issue's inputs with the
expected output written down. The failure is an outcome nobody can
check: "should feel fast", "nothing else should feel broken", or "run
the full test suite" with nothing said about what in that suite would
catch this fix. A suite run is fine as an addition, never as the whole
test.

## Honesty

**Where it lives.** The plan's risks, unknowns, open questions, and
deferral lines; after a build, the `## Deviations` section.

**What good looks like.** The plan distinguishes what it knows from
what it is betting on: an unknown named as an unknown ("tool-by-tool
`--` support may differ; I will check each"), a deferral with its
reason, a risk with what would trigger it. Confidence about something
the package cannot support is the failure this family watches for, but
note that it is graded by cause-grounded and decisive-test, not here;
this family is about whether the uncertainty is stated at all. Post
build, an honest deviation is written into the plan with what changed
and why; a deviation visible only in the diff is not recorded.

## Comms

**Where it lives.** The `## Candidate plan comment` section, read
against two things: `## Thread highlights` (live: the issue thread) and
the `## Repo facts` block's contribution-policy line. Live: also the
repo's `CONTRIBUTING.md`, `.github/` templates, and any `AI_POLICY.md`.

**What good looks like.** The comment answers the thread it is joining.
If a maintainer isolated the culprit to a file, posted a patched
binary, asked for a test, or settled an approach, the comment either
follows that or says why not; if another PR is open on the same issue,
the comment acknowledges it instead of silently racing. An empty thread
asks nothing, so a specific comment about this issue is enough. The
failure is a comment that would read the same if the thread did not
exist.

On policy, only explicit disclosure language creates an obligation:

- **Disclosure required** ("all AI usage must be disclosed"): the
  comment must say so in its own text. Course work is AI-assisted, so
  silence fails - however good the plan is.
- **Human-voice or responsibility conditions**: met by a comment that
  reads as a specific human account of this issue; no disclosure
  sentence required.
- **Silent or permissive policy**: nothing extra needed.
