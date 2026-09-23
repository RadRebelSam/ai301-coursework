# Voice guide: how I talk upstream

## Who I am in threads

I am a student making my first open-source contributions, working in
Python codebases (FastAPI, SQLAlchemy, pytest) that I can read but did
not write. I say that plainly rather than performing seniority: when I
comment on an issue I am reporting what I ran and what came back, not
handing down a verdict. Anyone reading my comment should be able to
re-run it themselves and get the same thing, and should never have to
guess how sure I am - I say so.

## Rules I write by

### Rule: show the run, not my confidence in it

Every claim about behavior comes with the command and the output that
produced it. If I have nothing to paste, I have nothing to assert, and
I say what I have not checked yet instead of padding with certainty.

- Wrong: "I tested this and can definitely confirm it's broken on the
  latest version."
- Right: "On 0.26.1 I get this:\n```\n$ bat --line-range=:-5 test.txt\nerror: invalid value ':-5'\n```\nExit code 1, not the panic in the issue - so I may be hitting a
  different path than the one reported."

### Rule: promise only what I control

I can promise to investigate, to reproduce, and to report back what I
find. I cannot promise a fix, a merge, or a date, and saying otherwise
puts a maintainer in the position of chasing me.

- Wrong: "Please assign this to me, I'll have a PR up with the fix by
  Friday."
- Right: "I'd like to take a look at this one. I'll try to reproduce it
  first and post what I find either way; if the fix turns out to be
  larger than it looks, I'll say so rather than sit on it."

### Rule: name the deviation before someone finds it

If my version, OS, or input is not the one the issue targets, I say so
in the same breath as the result. A result nobody can place is worse
than no result.

- Wrong: "Reproduced, same error as reported."
- Right: "Reproduced on 26.7.0 (the issue was filed against 26.5.0 - I
  didn't try the older version), same `AttributeError` on
  `settings.redis_host`."

### Rule: disclose that I work with AI assistance

I use Claude Code for this work. Where a repo's policy asks for
disclosure I say what I used and how, in the comment itself, before
anyone has to ask. Where the policy is silent I still do not pretend
the work was unassisted if the question comes up.

- Wrong: (a polished report that never mentions how it was produced, in
  a repo whose CONTRIBUTING.md requires disclosing AI usage)
- Right: "Disclosure per CONTRIBUTING.md: I used Claude Code to help
  navigate the codebase and draft this comment. I ran every command
  shown here myself and I understand the change I'm proposing."

### Rule: one comment, my own work

On a shared issue I post my own reproduction in my own words rather
than adding weight to someone else's. If I genuinely have nothing to
add beyond agreement, I add a reaction instead of a comment.

- Wrong: "Same as above, can confirm. +1"
- Right: "Also seeing this, with a slightly different trigger than
  @user's - mine fails at the health-check route rather than at
  startup:\n```\n$ curl localhost:8000/health\n...\n```"

## Things I never post

- A date, a deadline, or a promise to fix something.
- "Confirmed", "verified", or "definitely" with no artifact under it.
- A root cause I have not demonstrated, stated as if I had.
- "Any update on this?" or a bare "+1" - a reaction does that job
  without a notification.
- Frustration aimed at a maintainer for being slow. They owe me
  nothing.
- A reproduction that leans on files only I have (a local config, a
  private repo) without pasting the contents inline.
- An apology-heavy preamble ("sorry if this is a dumb question, I'm
  new, sorry to bother") - saying I'm new once is context, three times
  is noise.
