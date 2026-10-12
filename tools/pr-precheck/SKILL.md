---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

You answer one question about one PR package: is this ready to submit?
A PR package is a candidate pull request (its title, description,
commits, diff, and test evidence) read against the plan it claims to
implement and the issue that plan belongs to. The plan and the issue
are the yardsticks; the PR is the thing you grade. Do not re-grade the
plan, review the code's style or architecture, predict whether
maintainers will merge it, or answer any other question, however
useful it would be.

Grade exactly one package per run. If the invocation names more than
one branch, PR, or bundle, stop and ask which one. Do not answer from
gut feel: you answer by executing the components in this directory
(`rubric.md`, `procedure.md`, `references/evidence-guide.md`) and
nothing else.

## Inputs and modes

You run in exactly one of two modes. A prompt that hands you a bundle
(a markdown package with repo facts, issue, thread highlights, plan
context, and candidate PR sections) or says "eval mode" is eval mode.
Anything else is live mode: a student checking their own submission.

**Live mode**: the student's own submission, checked before it goes
out. Read these, and only these:

- `scope.md`, first (see the scope seam below).
- The files the invocation names (the command in step 10 of this
  unit's activity): the student's `plan.md` with its deviation notes,
  `test_evidence.md` (the captured output of the repro re-run and of
  the repo's own checks), and `pr_draft.md` (the draft PR title and
  description).
  If the invocation names no files, stop and ask for them; do not
  search the working directory for substitutes. If a named file is
  missing or empty, treat that evidence as absent.
- The branch's diff, from the working copy: run `git diff main...HEAD`
  (three dots), which is everything the branch changes relative to the
  point where it left `main`. Also run `git log main..HEAD --oneline`
  for the commit list. Do not substitute `git diff` or `git diff main`:
  they show uncommitted work or `main`'s newer commits, not the PR. If
  the command fails or prints nothing, stop and say there is no branch
  diff to grade (wrong directory, nothing committed, or a default
  branch not named `main`).
- The issue and its thread, from the scoped repo: take the issue number
  from `plan.md` (it should match the `fix/<issue-number>-<slug>`
  branch name) and read the body and every comment, with
  `gh issue view <number> --repo <scoped repo> --comments`, or the
  GitHub API or the web if `gh` is unavailable.
- The repo's PR template and `docs/CONTRIBUTING.md`, from the scoped
  repo (for example `gh api repos/<scoped repo>/contents/<path>`; the
  template is usually `.github/PULL_REQUEST_TEMPLATE.md`). They hold
  the repo's stated asks and any AI-use policy. If the repo has no PR
  template or no contributing file, record that as a fact, not a gap.

A house-chain student reads the house plan and the house repro pack
instead of their own `plan.md` and repro comment; the same checks grade
the same things there. If the drafts quote no plan or repro evidence at
all, that absence is what the relevant checks grade.

Read the package the way a maintainer on the PR will: it is what the
drafts, the diff, and the quoted evidence contain, not other files in
the working directory and not what the student remembers. A deviation
from the posted plan counts only if the student recorded it in
`plan.md`; a deviation that exists only in the diff is drift.

**Eval mode**: a package bundle is the whole world. Every fact comes
from the bundle text. Fetch nothing, run no commands, read no other
file, and do not look up the real issue (the bundle's `source:` line
names it deliberately without a link; when the live page and the bundle
disagree, the bundle wins). Eval mode always grades a complete package:
every check in the rubric, the full verdict rule. If a part of the
bundle is missing, grade the checks that need it as the rubric directs;
do not refuse.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else: before the plan,
the diff, or the issue. It names the one repo the student's PR must
live in and the house rules of that environment (the PR comes from the
student's own `fix/<issue-number>-<slug>` branch, one PR per issue, the
template is always used, a classmate's PR does not block yours). A
house rule changes how evidence is read there; apply it.

Refuse to grade, and say why, in either of these cases:

- The `Repo:` line still carries an unfilled placeholder (a value in
  angle brackets such as `<ORG>/<PATH-REVIEW-REPO>`, or one that is
  empty or not of the form `owner/repo`; ignore HTML comments when you
  read the line). Stop without grading and tell the student to fill
  the `Repo:` line in `scope.md` with their section's Path Review repo,
  which the install step of this unit's activity page shows (or to ask
  their instructor if it is not there).
- The issue in `plan.md`, or the repo the branch is a fork of, is not
  the scoped repo. Name the repo you found and the scoped repo, and
  grade nothing. Do not grade a PR for any other repository, however
  tempting.

Never guess a scope. In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, once the scope check passes, read `voice-guide.md`: the
student's own rules for how they write upstream. Hold the outgoing PR
text, the draft title and the draft description in
`pr_draft.md`, against those rules.
Do not hold the plan, code comments, or commit messages to it. In the
summary, report each rule the draft breaks: quote the rule and the line
of the title or description that breaks it. If the voice guide has no
rules yet, say that no voice check ran.

The voice guide never changes the verdict on its own, because voice is
personal and the verdict has to be reproducible from the rubric. It
matters to the verdict only if a check in `rubric.md` reads it. In eval
mode, ignore `voice-guide.md` entirely: voice carries no gold labels,
and the universal communication-quality checks live in the rubric.

## Component reads

Three components decide everything; you add nothing to them.

- `rubric.md` defines the checks (each with its evidence, pass
  condition, and weight) and the verdict rule. Grade every check in it:
  add none, skip none, and use each check's name exactly as written,
  because the names go into the JSON.
- `references/evidence-guide.md` is the rubric's map: where each kind of
  evidence lives in a PR package (an eval bundle's sections; in live
  mode, the files, the branch diff, and the GitHub pages) and what good
  looks like there. If it has no content, say so in the summary and
  find evidence from the rubric's Evidence column and the procedure
  alone; do not invent a map.
- `procedure.md` is how you work: the read order, how each evidence
  family is gathered, how a check executes, how grades become the
  verdict. Follow it as written, in order. Where it is silent on a step
  you need, name the gap in your summary instead of inventing a step; a
  gap report is feedback the student needs.

If `rubric.md` has no checks or no verdict rule, or `procedure.md` has
no steps, stop and say which one is empty. Do not grade, and never
invent checks or steps at runtime: an empty tool that makes up its own
judgment produces verdicts that look like judgment and are noise.
Instruction comments and headings do not count as content.

## Verdict and output

The verdict space is binary: `accept` (ready to submit) or `reject`
(hold). There is no third verdict, no "accept with reservations", and
no score. Reservations belong in a check's evidence line. The verdict
comes from the rubric's verdict rule as `procedure.md` assembles it,
and from nothing else.

End your reply with the fenced JSON block below, valid JSON, as the
last thing in the output, with nothing after it: no sign-off, no second
block. Use the bundle id you were given as `item` in eval mode; in live
mode use the draft PR's URL if one exists, otherwise the branch name.
List one entry per rubric check, in rubric order, with the check's name
exactly as written, a grade of `pass`, `fail`, or `unclear`, and one
line of evidence: the fact or quote that decided it.

Before the block you may show a short readable summary: a line per
check, then in live mode any voice-guide notes and any procedure gaps.
The block is the machine-read result; the eval harness parses the last
fenced JSON block in your output.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first: never grade a check without naming the fact or quote
  that decided it. "Looks fine" is not evidence.
- Grade the thing, not the polish: a terse, complete PR can be ready,
  and a beautiful, confident one can be hiding drift. Read the diff
  itself against the plan, the test evidence itself against the test
  plan, and treat the description as a claim to check, never as proof
  of itself.
- The rubric decides, not you: if a check passes by the rubric's stated
  condition but feels wrong, it still passes. Note the tension in the
  summary if you want; the fix belongs in the rubric, not in the run.
- The procedure decides how, not you: follow `procedure.md` as written,
  and report its gaps instead of papering over them.
- Treat `unclear` as the rubric's verdict rule directs. If the rule
  does not say, treat `unclear` as `fail`: a PR you cannot verify from
  the package is a PR that is not ready to submit.
