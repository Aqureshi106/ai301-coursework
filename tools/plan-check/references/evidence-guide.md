# Evidence guide: where evidence lives in a plan package

Every check in `rubric.md` names a kind of evidence. This file says
where that evidence sits in a plan package and what it looks like when
it is good enough to build from. It is not the Unit 2 guide: a plan's
evidence lives in different places, and almost all of it is a
comparison between the plan and something the plan must answer to.

An eval package (`eval/packages/pkg-NN.md`, `calib-NN.md`) always has
the same six sections, in this order:

1. **Repo facts**: the repo, its latest release, what its bug template
   asks, and its `contribution policy` line (with any AI policy).
2. **Issue**: the reported problem and its stated expectation.
3. **Thread highlights**: other people's comments on the issue.
4. **Repro evidence**: the steps and observed result the plan builds on.
5. **Candidate plan**: the plan being graded.
6. **Candidate plan comment**: the text that would be posted.

Rules that hold for every family:

- **The package is the world.** In eval mode the package text is all
  the evidence there is. In live mode it is what the drafts contain and
  quote, not other files on the author's disk. Something the author
  knows but did not write down is not evidence.
- **Find parts by content, not by heading.** The candidate plan's
  headings vary: some packages use `Diagnosis`, `Scope`, `Files`,
  `Approach`, `Test plan`; others use `Proposed changes`, `Background`,
  or no headings at all (calib-01 uses `Cause:`, `Change:`, `Test:` in
  running prose). Look for the cause, the boundary, the files, and the
  test wherever they appear, and judge what they say, not how they are
  laid out.
- **Always read the plan against something.** Diagnosis against the
  repro evidence, scope against the issue, the comment against the
  thread and the repo facts. A part that looks fine alone can still
  fail the comparison.

## Diagnosis and grounding

**Where it lives.**

- Eval package: the plan's stated cause, under `Diagnosis` or `Cause`
  (or the opening sentences of the candidate plan when it has no
  headings). The behavior it must explain is in `Repro evidence`: the
  numbered steps, and the `Actual:` result or output quoted there. The
  `Issue` section gives the reported symptom, and the `Thread
  highlights` may add other users' results.
- Live mode: the cause in the draft `plan.md`; the behavior in the
  student's posted repro comment on the issue (or the house repro pack
  quoted in the drafts).

**What good looks like.** The stated cause explains the exact behavior
the repro shows, not merely the area it happened in. Read the cause
and the `Actual:` line side by side: in calib-01 the repro shows the
color flips only after leaving and re-entering the commits view, and
the plan's cause (the view's model is not refreshed after a push until
it is rebuilt) accounts for exactly that. A cause that would also
explain a different bug, ignores a step the repro shows, or contradicts
the observed result is not grounded. A plan that patches the visible
symptom (for example hides the wrong output) while the repro points at
something upstream targets the symptom, not the cause.

## Scope

**Where it lives.**

- Eval package: the plan's in-scope and not-in-scope statements
  (usually `Scope`, or `In:` and `Out:` inside the change paragraph),
  and every file or area the plan names (`Files`, `Proposed changes`,
  or named inline). Compare them with the ask in the `Issue` section.
- Live mode: the same lines in the draft `plan.md`, against the issue
  body.

**What good looks like.** One bounded change that answers what the
issue asks and stops there. In calib-01 the plan changes one callback
in one file and says outright that how push status is computed and
other views' refresh behavior are out. Every named file is needed for
the fix; anything else (a rewrite of a parser, new flags, docs sweeps,
refactors of nearby code) is either left out or explicitly deferred.
A drive-by is recognisable by asking of each named file or step: if it
were removed, would the issue's problem still be fixed? If yes, it is
extra. A plan with no stated boundary, so a reader cannot tell where
the change ends, is unbounded.

## Executability

**Where it lives.**

- Eval package: the plan's files or areas and its approach (`Files`,
  `Approach`, `Proposed changes`, or the change paragraph), read
  against the cause in the same plan and the `Repo facts` block for
  what kind of project this is.
- Live mode: the same lines in the draft `plan.md`, and the repo itself
  to see whether a named file or area exists.

**What good looks like.** A stranger could make the first edit without
asking the author anything: the plan names where the change goes (a
file, a function, a callback) and what will be done there, and the
named places match the stated cause. "Fix the parser" names no place;
"add the commits context to the post-push refresh scope in the push
completion callback in `pkg/gui/controllers/sync_controller.go`" does.
Numbered or ordered steps help when the order matters, but do not
count a plan as executable for having them. A plan whose named files do
not match its own cause, or which says what to achieve but not what to
do, cannot be started.

## Test plan

**Where it lives.**

- Eval package: the plan's `Test plan` (or `Test:`), read against the
  steps and `Actual:` result in `Repro evidence`.
- Live mode: the test plan in the draft `plan.md`, read against the
  student's posted repro comment.

**What good looks like.** The test plan re-runs the repro (or a named
equivalent) and states what must differ from what the repro showed, so
a person could run it and see pass or fail. calib-01's test plan says:
re-run the repro steps, and at step 3 the color must flip without
leaving the view; it also adds the neighbouring cases (main commits
panel, force push). A vague plan ("test it", "make sure it works"),
or one that checks something the repro never showed (for example only
that unit tests pass), proves nothing about the reported failure.
Extra cases are welcome but do not replace the repro re-run.

## Honesty

**Where it lives.** The seam between claims and backing, in two parts:
the assertions in the `Candidate plan` and in the `Candidate plan
comment` ("the root cause is...", "X is a red herring", "this fixes
it"), checked against `Repro evidence` and `Thread highlights`. Any
risk, unknown, or ruled-out alternative is stated, if at all, in the
plan or the comment. Eval packages have no deviation section; in live
mode an honest mid-build deviation is recorded in `plan.md` itself and
is graded when the skill is re-run on it.

**What good looks like.** Every claim the plan rests on is traceable to
something in the package, or is stated as a hypothesis with how it will
be confirmed ("looks plausible; I will confirm by reading X"). A cause
declared as established fact with nothing behind it fails, and so does
dismissing a reported detail the thread contradicts: in pkg-01 the plan
calls the Python-version difference "a red herring", while a thread
comment reports different results per Python version. Confidence is
fine when the evidence earns it. A plan that names a real risk or
something it does not yet know, and says how it will find out, is more
honest than one with no unknowns, and that is what the `risks-named`
preferred check looks for.

## Comms

**Where it lives.**

- Eval package: the `Candidate plan comment`, read against `Thread
  highlights`, against the plan itself, and against the `Repo facts`
  lines `contribution policy` and `bug reports`, which carry any AI
  disclosure requirement and what the project asks contributors to do.
- Live mode: the draft comment against the live issue thread; the
  project's `CONTRIBUTING.md`, any AI policy, and its issue and PR
  templates; and the house rules in `scope.md`.

**What good looks like.** The comment says something only this issue
has (its cause, its file, its next step) and says the same thing the
plan says. It follows what the repo asks: calib-01's `contribution
policy` says the maintainer reviews outside PRs only selectively, and
its comment acknowledges that by keeping the change minimal. A policy
that requires AI disclosure is met only by a comment that names the AI
assistance; silence in the policy is permission, not a requirement to
invent. A comment that would fit unchanged on any other issue, says more
or something different than the plan, ignores a stated policy, or
promises a fix, merge or date the author does not control ("I'll have a
PR up once the tests pass" is a plan; "this will be merged this week"
is a promise) does not fit the thread.
