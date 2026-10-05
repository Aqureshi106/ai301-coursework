# Procedure: how this skill grades a plan package

<!--
THIS IS THE PART YOU WRITE, and it is a new kind of part. Weeks 1 and
2, SKILL.md carried a numbered workflow and you only wrote judgment
files. This week the workflow is gone from the frame: SKILL.md says
"execute procedure.md", and these are the operating steps you author.
The machinery is in your hands now.

Your operator swap is the design brief. When your executor stalled
because your rubric said WHAT to decide but not HOW to find the
evidence, that was a procedure gap. This file is where those gaps get
closed: a complete procedure lets someone who has never seen a plan
package before (a groupmate, or the skill itself) grade one exactly the
way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
repro evidence before the plan, and note what behavior it pins down"
is a step; "understand the context" is a wish.
-->

## Read order

1. Read the issue first: the title, the body, any thread highlights, and
   the repo-facts block (eval) or the repo's contribution docs (live).
   Note in one line what the issue asks to be fixed, and note any
   contribution or AI policy the repo states. The scope and
   comment-fits-thread checks are judged against these.
2. Read the repro evidence next (eval: the repro-evidence block; live:
   the student's posted repro comment, or the house repro pack quoted in
   the drafts). Note the steps, the observed failing behavior, and any
   output or artifact quoted. The diagnosis and test plan checks are
   judged against this.
3. Read the candidate plan. Note the stated cause, the in-scope and
   not-in-scope statements, the files or areas named, and the test plan.
4. Read the candidate plan comment last. Note anything it claims that
   the plan does not say, and anything it promises.
5. Grade nothing until all four are read. In eval mode, use only the
   bundle text. In live mode, read `scope.md` before step 1.

## Evidence gathering

1. Diagnosis: copy the plan's stated cause in one line. Copy, from the
   repro evidence, the observed failing behavior it must explain. Record
   both side by side. If the plan states no cause, record "no cause
   stated".
2. Scope: copy the issue's ask (from Read order step 1). Copy the plan's
   in-scope statement, its not-in-scope line, and every file or area it
   names. If any of these is missing, record "missing" for that item.
3. Test plan: copy the plan's test plan. Copy the repro steps and the
   observed result from the repro evidence. Record whether the test plan
   names the observable result that would change.
4. Executable: copy the files or areas the plan names and its approach
   steps. Copy the cause the plan states. Record whether the named files
   match that cause. If no file or approach is named, record "missing".
5. Claims: list each assertion of cause or effect in the plan and the
   plan comment ("the root cause is...", "X is a red herring", "this
   fixes it"). Next to each, copy the repro or thread line that backs it,
   or record "no backing" if there is none. Also record any thread
   line that contradicts it.
6. Comment: copy the one or two sentences in the plan comment that are
   specific to this issue, any promise of a fix, merge or date, and the
   repo's stated policy line. Record "none" for any that are absent.
7. Risks (preferred): copy any risk, unknown, or ruled-out alternative
   the plan names and how it would be checked, or record "none".
8. Every recorded item must be a quote or a "missing" note taken from
   the package. Do not record anything from memory or outside the
   package (in eval mode, never fetch anything).

## Check execution

1. Run the checks in this order: diagnosis, scope, test plan,
   executable, claims-match-evidence, comment-fits-thread, risks-named.
   Grade each
   one separately, using only that check's recorded evidence from
   Evidence gathering and the pass condition in `rubric.md`.
2. Grade `pass` only if the pass condition is met by a fact you can
   quote. Grade `fail` if the evidence shows the condition is not met.
3. If the evidence for a check is genuinely absent (for example the plan
   states no cause, no test plan, or no scope line), do not guess and do
   not fill it in from the comment. Grade the check `unclear`. If the
   evidence is present but does not meet the pass condition, grade
   `fail`, not `unclear`.
4. For each check, write one line naming the fact or quote that decided
   it. A grade with no quoted fact is not allowed.
5. A check may be graded from its recorded evidence alone, without
   re-reading the whole package. Re-read the package only if the
   recorded evidence is missing or contradicts itself.
6. If a passing grade feels wrong, keep the grade and note the tension
   in the summary. Do not change a grade because of polish or length.

## Verdict assembly

1. List every check's grade, required and preferred.
2. Apply the verdict rule in `rubric.md`: `accept` only if every
   required check is `pass`. If any required check is `fail`, the
   verdict is `reject`.
3. Count each `unclear` as `fail`. If any required check is `unclear`,
   the verdict is `reject`.
4. Preferred checks never change the verdict.
5. Quote in the output the fact that decided the verdict: if `reject`,
   the evidence line for the first failing or unclear check in the check
   order; if `accept`, the evidence line for each passing check.
6. Emit the JSON block with every check's grade and evidence line and
   the verdict, as the last thing in the output.
