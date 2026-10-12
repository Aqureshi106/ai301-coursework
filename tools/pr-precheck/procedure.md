# Procedure: how this tool grades a PR package

<!--
Most steps here are a side-by-side: the diff against the plan, the test
evidence against the plan's test plan, the description against the
diff. That is why the plan is read before the diff and the description
is read last: nothing you read earlier should be allowed to tell you
what a later part contains.
-->

## Read order

1. In live mode, read `scope.md` before anything else, then
   `voice-guide.md` once the scope check passes. In eval mode, skip
   both, and use only the bundle text for everything below.
2. Read the issue first: the title, the body, and the thread
   highlights (live: the issue and its comments from the scoped repo).
   Note in one line what the issue asks to be fixed, and note any
   explicit direction or question from a maintainer or member.
3. Read the repo's stated asks next: the Repo facts block (eval) or the
   repo's PR template and `docs/CONTRIBUTING.md` (live). Note each
   template section, checklist item, required artifact (tests,
   changelog or whatsnew entry, closing line such as "fixes #N"),
   title convention, the test command, and any AI-use policy. If the
   repo states none of these, note "none stated".
4. Read the plan before the diff: the Plan context block (eval) or
   `plan.md` with its deviation notes (live; the house plan for a
   house-chain student). The plan is the yardstick for every diff
   check, so read it first: a diff read first sets your expectation of
   what the plan said. Note the stated scope, the not-in-scope line,
   the Files list, each promised deliverable, any known limit,
   deferral, or deviation note, and the test plan. Note the repro
   evidence's steps and recorded before-result too.
5. Read the diff third: the Candidate PR's Diff (eval) or the output of
   `git diff main...HEAD` (live). Go through it in file order and list
   every changed file with a one-line note of what changed. Note, do
   not judge yet. Read the commit list after it (live: `git log
   main..HEAD --oneline`).
6. Read the test evidence fourth, next to the plan's test plan: the
   Candidate PR's Test evidence (eval) or `test_evidence.md`
   (live).
7. Read the PR title and description last (live: `pr_draft.md`). Note every claim it makes
   about what the PR contains or leaves out (fidelity phrases, named
   deliverables, behavior changes, checklist ticks), any disclosure
   sentence, and any limit it states. It comes last so the description
   cannot tell you what the diff holds.
8. Grade nothing until all of it is read.

## Evidence gathering

Record each item as a quote or a "missing" note taken from the
package. In live mode, name the command or file each item came from.

1. diff-within-plan: build two columns. Left: every changed file from
   Read order step 5, with its one-line note. Right: the plan item that
   licenses it (a Files-line entry, a line of the scope statement, a
   deviation note, or an artifact the plan or the repo's asks call
   for), or "unlicensed". Then check each hunk for behavior the plan
   never named (a new flag, option, or setting; a rename or rewrite;
   reworded unrelated text) and set it against the not-in-scope line.
   If the plan has no scope or Files line, record "missing".
2. plan-delivered: list each deliverable the plan promises (every
   Files-line entry and each named or numbered change). Next to each,
   copy the hunk that delivers it, or record "absent". For each absent
   one, copy the disclosure (the plan's deviation or known-limit note,
   or the description's sentence on what the PR leaves out), or record
   "undisclosed".
3. description-matches-diff: list each claim from Read order step 7.
   Next to each, copy the hunk or evidence line that backs it, or
   record "no backing", or "contradicted by" with the hunk. Treat
   "exactly as planned", "in full", and "no changes beyond the plan"
   as claims about the diff, and mark them with the outcome of steps 1
   and 2.
4. decisive-test-evidence: copy the plan's test plan item by item: the
   repro, the expected after-result, each failure mode named, each
   control. Next to each, copy the test-evidence line that shows it run
   on the branch with an observed result, or record "not shown". Copy
   the before-result (shown, or the repro's recorded one). For each
   test the diff adds, record what it asserts: the fix, or only the
   unchanged path.
5. repo-checks-run: copy the test command the plan's test plan names
   (or, if none, the repo's stated test command) and the evidence line
   that states its outcome on the branch, or record "not shown". Record
   whether the command includes the code the diff changed.
6. diff-reviewable: scan every hunk for debug prints, commented-out
   code, dead or unused functions or imports the change added, TODO or
   WIP leftovers, formatting, re-indent, or import-reorder churn,
   identical lines printed twice, and unrelated hunks. For each found,
   copy the file and the line. Record "none found" if the scan is
   clean. Copy any commit message that admits debris ("wip", "cleanups
   while debugging") as corroboration only.
7. template-and-asks: take each ask from Read order step 3 and, next to
   it, copy the place the description or diff meets it, or record
   "missing", or "empty or boilerplate". Record "none stated" if the
   repo states no PR asks.
8. disclosure-policy: copy the repo's AI-use policy line (or "none
   stated") and the description's disclosure sentence (or "none").
9. thread-direction: copy any explicit direction or question from a
   maintainer or member (or "none"), and the plan or description line
   that engages it (or "not engaged").
10. limits-disclosed: copy each limit or deferral the plan records (or
    "none") and the description line that restates it (or "not
    restated").
11. Never record anything from memory or from outside the package. In
    eval mode, fetch nothing.

## Check execution

1. Run the checks in rubric order: diff-within-plan, plan-delivered,
   description-matches-diff, decisive-test-evidence, repo-checks-run,
   diff-reviewable, template-and-asks, disclosure-policy,
   thread-direction, limits-disclosed. Grade each one separately,
   using only that check's recorded evidence and the pass condition in
   `rubric.md`.
2. Grade `pass` only if the pass condition is met by a fact you can
   quote. Grade `fail` if the evidence shows the condition is not met.
3. If the evidence a check names is genuinely absent (no plan scope
   line, no test-evidence section, no description), do not guess and do
   not fill it in from another part of the package. Grade the check
   `unclear`. If the evidence is present but does not meet the pass
   condition, grade `fail`, not `unclear`.
4. Grade description-matches-diff after diff-within-plan and
   plan-delivered: a fidelity claim in the description is false if
   either of those failed, so quote the claim and the unplanned hunk or
   the missing deliverable.
5. Do not skip a check because another one already failed. A hunk that
   is both unplanned and debris fails both diff-within-plan and
   diff-reviewable, each with its own evidence line.
6. An omission the plan or the description honestly discloses is a
   `pass` for plan-delivered; record the disclosure as its evidence. An
   omission nothing discloses is a `fail`.
7. For each check, write one line naming the fact or quote that decided
   it. A grade with no quoted fact is not allowed.
8. A check may be graded from its recorded evidence alone, without
   re-reading the whole package. Re-read the package only if the
   recorded evidence is missing or contradicts itself.
9. Grade the PR, not the plan. If the plan itself looks weak, say so in
   the summary and still hold the PR to it.
10. If a passing grade feels wrong, keep the grade and note the tension
    in the summary. Do not change a grade because of polish or length.

## Verdict assembly

1. List every check's grade, required and preferred.
2. Apply the verdict rule in `rubric.md`: `accept` only if every
   required check is `pass`. If any required check is `fail`, the
   verdict is `reject`.
3. Count each `unclear` as `fail`. If any required check is `unclear`,
   the verdict is `reject`.
4. Preferred checks (thread-direction, limits-disclosed) never change
   the verdict.
5. Quote in the output the fact that decided the verdict. If `reject`,
   quote the evidence line of the first failing or unclear required
   check in rubric order, and list the other failing checks by name in
   the summary. If `accept`, quote the evidence line of each required
   check.
6. In live mode, add any voice-guide notes and any procedure gaps to
   the summary, before the JSON block. They never change the verdict.
7. Emit the JSON block with every check's grade and evidence line, in
   rubric order, and the verdict, as the last thing in the output.
