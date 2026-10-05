# Rubric: is this plan ready to post and build from?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here (via your procedure.md). It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the plan's scope statement, the test plan read against
     the repro evidence's steps, the plan comment read against the
     thread highlights, the repo-facts block) or a location from your
     references/evidence-guide.md. "The plan" is not a source; "the
     plan's stated cause read against what the repro evidence shows"
     is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (is
     this one bounded change? could a stranger start executing it?),
     never the write-up's shape (how many sections it has, how long it
     is, whether it uses headings). Structure-shaped checks are what
     make graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad plans posted. The lecture named the
failure families: the diagnosis ignores or contradicts the reproduced
evidence, the change is unbounded (scope creep), the plan targets the
symptom while the evidence points at the cause, a stranger could not
start executing it, the test plan proves nothing observable, the
unknowns are dressed up as certainty, and the comment ignores what the
thread or the repo's stated conventions ask. A rubric that ignores a
family will fail eval packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diagnosis | The plan's stated cause, read against the behavior the repro evidence actually shows (the repro steps, the observed output, and any artifacts the repro quotes). | Pass if the stated cause explains the observed failure in the repro evidence: it names the behavior the evidence shows, does not contradict it, and targets the cause rather than only the symptom. Fail if the cause ignores or contradicts the evidence, or if the fix would hide the symptom while the evidence points at a different underlying cause. Unclear if the plan states no cause or the repro evidence is too thin to tell. | required |
| scope | The plan's in-scope statement and not-in-scope line, and the files or areas it says it will touch, read against the issue's ask. | Pass if the plan describes one bounded change that resolves what the issue asks and nothing more: every file or area named is needed for that fix, and unrelated cleanup, refactors, or extra features are excluded or explicitly deferred. Fail if the change grows beyond the issue's ask (drive-by rewrites, bundled fixes) or does not say where the change stops. | required |
| test plan | The plan's test plan, read against the repro evidence's steps and observed result. | Pass if the test plan names an observable result that would show the fix worked: it re-runs the repro steps (or a named equivalent) and states what output or behavior changes from what the repro showed, so someone could run it and see a clear pass or fail. Fail if it only says to "test it" or "verify it works", or checks something the repro never showed. | required |
| executable | The plan's files or areas and its approach, read against the repo-facts block and the issue (live: the repo itself). | Pass if a stranger could start the work from the plan alone: it names where in the repo the change goes and what will be done there, in an order that makes sense, so no question to the author is needed before the first edit. Fail if the plan names no place to change or no approach, or if the files it names do not match the cause it states. | required |
| claims-match-evidence | The plan's and the plan comment's assertions of cause and effect (for example "the root cause is...", "X is a red herring", "this fixes it"), checked against the repro evidence and the thread highlights. | Pass if the claim the plan rests on (its stated cause, or its dismissal of a reported detail) is consistent with everything the repro evidence and thread show, and is either backed by an observation in the package or framed as the author's explanation that the plan's own first step will confirm (for example by tracing or reading the code). An account of code internals the package cannot show is a hypothesis, not a fail, when it fits every observation and nothing contradicts it. Fail if the plan states a cause as established fact with no observation in the package that supports it, if it dismisses or explains away a detail that the thread or repro evidence reports (for example another user's results that differ), or if it contradicts what the package shows. | required |
| comment-fits-thread | The plan comment, read against the issue thread highlights and the repo-facts block (its `contribution policy` and `bug reports` lines). | Pass if the comment says something only this issue has (its cause, its files, its next step), matches what the plan says, respects any policy the repo-facts block states (including a disclosure requirement for AI help, if one is stated), and makes no promise of a fix, merge or date that the author does not control. Fail if it would fit unchanged on any other issue, says more or something different than the plan, ignores a stated policy, or promises an outcome or date. | required |
| risks-named | The plan's stated risks, unknowns, or alternatives it ruled out. | Pass if the plan names at least one thing that could go wrong or that it does not yet know, and says how it would find out. | preferred |

## Verdict rule

Accept (ready to post and build from) only if every `required` check
passes. A `fail` on any `required` check rejects (holds) the package.
`preferred` checks are recorded but never change the verdict; they only
separate a strong plan from a sufficient one. Grade a check `unclear`
only when the evidence it names is genuinely absent from the package.
A `?` (`unclear`) counts as a `fail`: if the package does not let a
reader verify a required check, the plan is not ready to build from, so
a required check graded `unclear` also rejects.
