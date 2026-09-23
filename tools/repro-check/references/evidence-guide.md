# Evidence guide: where proof lives in a reproduction package

The map the rubric's checks read. For each proof family: where to look,
and what good looks like when you get there.

A note that applies to every family: the package is what the comments
contain and quote. Files that exist on the author's disk, links to
private repos, and "I also tried X" without X shown are not evidence.
A stranger reading the issue thread sees only the posted text.

## Environment

**Where it lives.** In an eval bundle: the opening lines of the
"Candidate repro report", usually a single `Environment:` line, and
sometimes inside a fenced block (`pip list` output, `minikube version`,
a `uname -a`). The target it is read against lives in the "Issue"
section (the version and platform the reporter used) and in the
"Repo facts" block (`latest release`), and the thread highlights are
where a maintainer often names the axis that matters ("only on the
Windows driver", "only in release builds"). In live mode: the drafted
repro comment for the record, and the issue's own body plus its
template fields for the target.

**What good looks like.** The tool's version and the platform are both
named, in any format: `bat 0.24.0, Windows 11 22H2` is as good as a
ten-line block. When the issue or thread says the behavior turns on a
further axis - shell, terminal emulator, VM driver, GPU driver, build
profile (debug vs release), install method (pip vs conda vs distro
package) - that axis is named too. The test is placeability: could a
maintainer say which configuration this run was, and does it match the
one the issue is about? A report that lists twelve package versions but
never says which OS it ran on fails that test; a one-liner that names
version, OS, and the driver passes it.

## Steps

**Where it lives.** In an eval bundle: the "Steps" portion of the
candidate repro report, usually a numbered list or a fenced shell
transcript. Read it against the issue's own reproduction steps, which
sit in the "Issue" section. In live mode: the same part of the drafted
repro comment, read against the issue body on GitHub.

**What good looks like.** A stranger with a clean machine can start at
step 1 and arrive at the failure without asking a question. That means:
the starting state is reachable by anyone (clone this public repo,
`pip install x==1.2.3`, or `cat > config.yml` with the contents shown
inline); every command appears as it was typed, not described; and the
one step that actually triggers the behavior is present rather than
implied. Watch for two specific holes. First, private inputs: "in our
internal service", "using our config", a file named but never shown -
anything the reader cannot obtain makes the whole sequence
unrunnable. Second, summary steps: "set up the environment", "configure
as usual", "run the app normally" - each one hides the exact thing a
reader needs. Terseness is not a hole: four lines of real commands is a
complete set of steps.

## Behavior shown

**Where it lives.** In an eval bundle: the fenced blocks inside the
candidate repro report - command output, tracebacks, panics, exit
codes, log excerpts, measurements, or a described screenshot. Read them
against the issue's own quoted output and error text in the "Issue"
section. In live mode: the fenced blocks in the drafted repro comment,
against the issue thread on GitHub.

**What good looks like.** The artifact is something this run produced
(not a block copied back out of the issue), and it shows the issue's
own symptom, reached by the input form the issue names. The common
failure is an adjacent outcome narrated as the reported one, and it is
visible in the artifact itself if you read it rather than the prose
around it:

- The issue reports a panic or crash (exit 101, a stack trace) and the
  artifact shows a graceful error message with exit 1 - that is the
  program's validation working, not the bug.
- The issue names a specific syntax or input (an offset-from-the-end
  range, a particular expression) and the artifact uses a different
  form (a prefix range, an edited expression that no longer binds its
  variable). The error that comes back belongs to the author's edit.
- The artifact only proves the program starts: a version banner, a
  session list, a UI that renders. Running is not reproducing.
- The report's "expected" and "actual" are stated backwards from what
  the artifact shows.

For a report that concludes it could NOT reproduce, the artifact's job
changes: it must show the attempt - the commands run and what came back
instead of the failure. That is real evidence about the bug's
conditions, and it is the honest negative result the maintainers can
use.

## Honesty

**Where it lives.** At the seam between the report's prose and its own
blocks: the summary sentence above an artifact, the confidence words
("confirmed", "guaranteed", "definitely the cause"), the stated root
cause, and the environment line compared with the issue's target.

**What good looks like.** Every sentence in the report is cashable
against something in the package. Specifically:

- A stated root cause has a demonstration attached, not just a theory
  stated as fact ("I verified this race condition" with no transcript
  is an assertion).
- Deviations are named by the author, not discovered by the reader. If
  the run used an older version, a different OS, or a different input
  than the issue targets, the report says so in a clause of its own.
  One sentence - "note I'm on 1.5.3, not main" - is the whole
  requirement. Silence about a material deviation is the failure,
  because the reader would draw a conclusion the evidence cannot
  support.
- Confidence matches evidence. "Reproduced on two machines" needs both
  runs shown, or the claim drops to what was shown.
- "Cannot reproduce" is a first-class honest outcome when the attempt
  is shown and the differences are named ("my ARG_MAX is 2 MiB and my
  filenames are uniform length; a triggering setup probably needs X").
  It ranks with a successful reproduction, not below it.

## Comms

**Where it lives.** In an eval bundle: the "Candidate claim comment"
section, read against the "Issue" section and the "Repo facts" block -
particularly the `contribution policy` line and any bug-report template
or AI policy it names. In live mode: the drafted claim comment, the
repo's `CONTRIBUTING.md`, `.github/` templates, and any `AI_POLICY.md`
or `AGENTS.md`; plus `voice-guide.md` for the author's own rules.

**What good looks like.** The claim comment could only have been
written about this issue: it names the failing command, the error
string, the file, or the condition from the issue, and it says what the
author will do next in terms they control - investigate, attempt a
reproduction, report findings back. Boilerplate is the opposite and is
easy to spot: remove the issue number and it would fit any issue in any
repo ("I'd like to work on this, please assign me to it"). Promises are
the other failure: a fix, a date, or a guarantee is something no
first-time contributor controls, and the modest version says the same
thing honestly ("I'll report what I find either way").

On policy, read what the policy asks and check the comment's own text
against it:

- **Disclosure required** ("all AI usage must be disclosed"): the
  comment must say it, in the comment itself. Work done in this course
  is AI-assisted, so a comment that says nothing fails - however good
  the reproduction is.
- **Conditions other than disclosure** (understand the change, test it,
  a human reviews it): the comment must not contradict them.
- **Silent, or permissive-with-responsibility** ("AI tools welcome, you
  are responsible for your contributions"): nothing extra is needed,
  and adding a disclosure is harmless but not required.
- **Template expectations** (confirm you searched for duplicates, state
  your version): these are about the issue report, not the claim
  comment, but a claim that contradicts them reads badly.
