# Evidence guide: where proof lives in a reproduction package

Every check in `rubric.md` names a kind of proof. This file says where
that proof sits in a package and what it looks like when it is good
enough to post. Five families, in the order a reader meets them: the
environment, the steps, the behavior shown, the honesty of the claims,
and the words that carry them upstream.

Two reading rules hold across every family:

- **The package is the world.** In eval mode the bundle text is all the
  evidence there is; in live mode the package is what the drafts
  contain and quote, not other files on the author's disk. Something
  the author knows but did not write down is not evidence.
- **Read the candidate against the issue, always.** Almost every family
  is a comparison: the report's environment against the issue's, its
  artifact against the failure the issue reports, its claims against
  what it showed. A part that looks fine alone can still fail the
  comparison.

## Environment

**Where it lives.**

- Eval bundle: the `Environment:` line (or the opening sentence that
  serves as one) at the top of the *Candidate repro report*. Compare it
  with the environment stated inside the *Issue* section, and with the
  `bug reports:` line in the *Repo facts* block, which lists what this
  project's own template asks every reporter to name.
- Live mode: the environment block in the draft repro comment; the
  issue's own environment in the issue body or its filled template; the
  project's asks in its bug-report template under `.github/`, and the
  README for how the thing is built and run.

**What good looks like.** It names the version of the software under
test and the OS or platform it ran on, and it names whatever else this
particular failure turns on: the build profile when debug and release
fail differently, the install method when packaging is in play, the
shell, the driver, the browser. Where any of that differs from the
issue's environment, the report says so itself rather than leaving the
reader to notice; a stated difference is a fact a maintainer can use,
an unstated one silently changes what the run proves. A report with no
environment record at all cannot be placed by anyone, and no artifact
rescues it.

## Steps

**Where it lives.**

- Eval bundle: the steps or commands in the *Candidate repro report*,
  usually a numbered list or a fenced transcript, read against the
  reproduction given in the *Issue* section.
- Live mode: the same part of the draft repro comment, read against the
  issue's own steps and against the project's README for how a fresh
  install is made.

**What good looks like.** A stranger holding the stated environment
could get to the same point from what is written: the starting state is
named (a fresh clone, an empty directory, a checkout of a given tag),
the exact command or input is given rather than described, and every
file, config, or fixture the run depends on is either shown inline or
publicly fetchable. Input shown in full beats input summarised, because
the substitution is where reproductions quietly change target. Steps
that end at something only the author holds (a private repository, a
config not pasted, "set up the project as usual") are not followable,
and neither is a run whose trigger is skipped.

## Behavior shown

**Where it lives.**

- Eval bundle: the artifact inside the *Candidate repro report* (the
  fenced output excerpt, the log lines, the described screenshot),
  compared with the failure described in the *Issue* section, including
  any error text, exit code, or stack trace the issue quotes.
- Live mode: the artifact in the draft repro comment, compared with the
  issue body and with any output the thread's later comments quote.

**What good looks like.** The artifact shows the failure the issue
reports, not a failure in the same neighbourhood. Read the two side by
side and compare the specifics: a panic with exit 101 is not a handled
error with exit 1; a syntax error rejecting the input is not the crash
the parser was reported to hit; a silent no-op is shown by the state
afterwards (the stash list still empty, the file still untracked), not
by a sentence saying nothing happened. An artifact proving only that
the tool starts and runs shows nothing about the bug. An honest
cannot-reproduce belongs here too and reads the same way: it shows what
the run actually produced, and lets the reader see for themselves that
the reported failure did not appear.

## Honesty

**Where it lives.** The seam between claims and backing, in two places:
the concluding sentences of the *Candidate repro report* ("this
confirms...", "reproduced on both versions", "the cause is..."), and
the assertions in the *Candidate claim comment*. Both are checked
against the artifacts in the same package, never against the author's
confidence. Live mode reads the same two places in the drafts.

**What good looks like.** Every assertion of outcome is traceable to
something shown. A claim of a second run, a second version, or a second
machine needs that run in the package; ten identical runs asserted in
prose are one unshown run. A cause may be named as a hypothesis or a
next step ("my next step is reading X, which looks plausible"), but not
stated as an established fact without an artifact. The direction that
catches people out: a report can be scrupulous about its failure and
still overclaim in its last paragraph, and the last paragraph is what a
maintainer quotes. An evidenced cannot-reproduce is a pass in this
family, and often the most useful comment on the thread, because it
narrows where the bug lives.

## Comms

**Where it lives.**

- Eval bundle: the *Candidate claim comment*, read against the *Issue*
  section; and both comments read against the `contribution policy`
  line in the *Repo facts* block, which carries any AI policy and any
  disclosure requirement, plus the `bug reports:` line for what the
  project asks reporters to include.
- Live mode: the draft claim comment against the live issue thread; the
  project's `CONTRIBUTING.md`, its `AI_POLICY.md` or equivalent, and
  its issue and PR templates, for the policy the comments must satisfy.
  The house rules in `scope.md` apply here too: inside the course
  repository a classmate's claim does not block a claim, and
  piggybacking on a classmate's reproduction is never the answer.

**What good looks like.** The claim names something only this issue
has, usually its version and its behavior, and states a next action the
author controls: the artifact they will post, the code path they will
read. Boilerplate is recognisable by substitution: if the comment would
fit unchanged on any other issue in the tracker, it carries no
information. Promises of a merge, a fix, or a date are worse than
vague, because they are the part a maintainer can hold against the
author. Being new is fine to say plainly, once.

On AI policy, read the repo's stated rule and match it, treating the
package's comments as AI-assisted work, which is this course's
workflow:

- A policy requiring **disclosure** of AI use is satisfied only by a
  comment that names the assistance and its extent. Nothing else in the
  package substitutes for it, however strong the proof is.
- A policy requiring **understanding, responsibility, or human-written
  comments** is not a disclosure requirement. It is satisfied by
  comments that read as a person's own words about work they ran
  themselves.
- **Silence is permission.** Most projects say nothing about AI, and
  that is not a requirement to invent.
