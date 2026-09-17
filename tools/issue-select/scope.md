# Scope: where to look, and who is looking

<!--
This file is the skill's field of view. The rubric (rubric.md) decides
whether an issue is GOOD; the scope decides which issues are candidates
at all, and whose hands the issue would land in. It applies in live mode
only: in eval mode the bundle is the whole world and this file is
ignored.

Two parts. Staff wrote the first; you write the second.
-->

## Where candidates come from

Only issues in the course's Path Review repository are candidates:

- Repo: `codepath/pathreview-ai301-fa26-s1` <!-- paste your section's repo from the Unit 1 Check-In page -->

Do not search, fetch, or grade issues from any other repository, however
promising. The wider GitHub comes later in the course; for now the field
is Path Review.

**Path Review house rule.** Path Review is a classroom, and your
classmates are not strangers. Ignore the usual claim signals here: other
students' claim comments (and there may be several on one issue) do not
block an issue, and finding some on the issue you want is normal. Claim
anyway: course credit attaches to the pull request you open, not to
whether it merges, so a shared issue costs nobody anything. Everything
else in the rubric applies as written.

## Your fit profile

<!-- YOU write this part: a few sentences about you. What languages and
tools you have actually used, what you want to get better at, anything
you want to avoid. The skill uses this only to RANK the issues your
rubric accepts, never to change a verdict: fit cannot rescue an issue
your rubric rejects, and cannot sink one it accepts. -->

I am comfortable in Python: functions, classes, virtualenvs, pip, and
reading an unfamiliar module well enough to change one function in it. I
have written and debugged unit tests with pytest, including fixtures and
mocks, so a failing or mis-written test is a place I can work rather than
a place I get stuck. I have web and API experience (HTTP, REST,
FastAPI-style route handlers, SQLAlchemy queries), which covers the API
and service layers of this repo.

What I want to get better at: reading a codebase I did not write and
finding the one place a bug actually lives, and writing a fix with a test
that proves it.

What I would rather avoid on a first contribution: issues whose real work
is a product or design decision rather than a code change, and issues
that need a large environment (GPUs, external paid services, big data
downloads) before the bug can even be reproduced.

Ranking preference among issues my rubric accepts: a bug with a named
file and a stated expected behavior beats a vague one; a fix I can verify
by running pytest locally beats one I can only verify by eye.
