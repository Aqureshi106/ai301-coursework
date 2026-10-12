# Evidence guide: where evidence lives in a PR package

<!--
The four headings below are the harness's failure categories under the
names the eval README uses: plan fidelity = silent-drift, test evidence
= not-tested, diff quality = unreviewable, standards and comms =
standards-wall. A package that fails none of them is a clear accept.
-->

Every check in `rubric.md` names a kind of evidence. This file says
where that evidence sits, in a practice submission (an eval bundle) and
in your own PR (live mode), and what it looks like when it is good
enough to submit. Almost all of it is a side-by-side: the PR on one
side, and something it must answer to (the plan, the test plan, the
repo's stated asks) on the other.

**A practice submission** (`eval/packages/pkg-NN.md`, `calib-NN.md`)
always has the same sections, in this order:

1. **Repo facts**: the repo, its latest release, a `pull requests` line
   (the PR template's asks, the contributing docs' asks) and a
   `contribution policy` line (with any AI policy).
2. **Issue**: the reported problem and its stated expectation.
3. **Thread highlights**: other people's comments on the issue.
4. **Plan context**: the repro evidence the plan built on, then the
   accepted plan itself, in running prose: its cause, `Scope`, `Not in
   scope`, `Files`, any known limit or deferral, and `Test plan`.
5. **Candidate PR**, with five parts: `Title`, `Description`,
   `Commits`, `Diff` (a unified diff), and `Test evidence`.

**Your own PR** is the same five parts, kept in different places:

- The plan, with its deviation notes: your `plan.md`.
- The diff: run `git diff main...HEAD` (three dots) from your working
  copy. The commit list: `git log main..HEAD --oneline`.
- The test evidence: your `test_evidence.md` (step 8).
- The title and description: your `pr_draft.md` (step 9).
- The issue and its thread: `gh issue view <number> --repo <scoped
  repo> --comments`, the issue number from `plan.md`.
- The repo's asks: its PR template (usually
  `.github/PULL_REQUEST_TEMPLATE.md`) and `docs/CONTRIBUTING.md`, plus
  the house rules in `scope.md`.

Rules that hold for every family:

- **The package is the world.** In eval mode the bundle text is all the
  evidence there is. In live mode it is what the named files, the
  branch diff, and the repo's own pages contain, not other files on
  your disk and not what you remember doing. Something you know but
  did not write down is not evidence.
- **Find parts by content, not by heading.** Plans use `Scope`,
  `Files`, `Test plan`, or run the same things together in prose
  (calib-01's plan reads "Plan: two steps ... Not in scope: any other
  theme. Files: `themes.gitconfig`. Test plan: ..."). Look for the
  scope, the files, and the test wherever they sit, and judge what they
  say, not how they are laid out.
- **Always read one thing against another.** The diff against the plan,
  the test evidence against the test plan, the description against the
  diff, the description against the repo's asks. A part that looks fine
  alone can still fail the comparison.
- **The description is a claim, not evidence.** What the PR says about
  itself is checked against the diff; it never stands in for it.

## Plan fidelity (harness category: silent-drift)

**Where it lives.**

- Practice submission: the plan's `Scope`, `Not in scope`, and `Files`
  statements, and any known-limit or deferral note, in the **Plan
  context** block. What actually changed is in the **Diff**: the
  `--- a/` and `+++ b/` lines name each changed file and each `@@` hunk
  shows what moved. The description's claims about the PR (including
  fidelity phrases such as "exactly as planned") are in the
  **Description**.
- Your own PR: the same statements in `plan.md`, and the deviation
  notes you recorded in it; the changed files and hunks in `git diff
  main...HEAD`; the claims in `pr_draft.md`.

**What good looks like.** Read the plan's `Files` line and the diff's
file list side by side. Every changed file and hunk falls inside what
the plan names, or inside a deviation note recorded in the plan, or is
an artifact the plan or the repo's asks call for (a test for the fix, a
changelog or whatsnew entry); and every deliverable the plan promises
has a hunk. calib-01 is the model: the plan names one file and two
steps, the diff changes only `themes.gitconfig` (one deleted line, the
plan's step 1), and the plan's step 2 (parse with a strict parser) is
shown in the test evidence.

Drift runs in both directions, and a description can lie about either:

- More than the plan: calib-03's diff fixes the else-hugging in
  `statement.js` as planned, but a second hunk rewrites
  `print/property.js`, which the plan never names, under a description
  that says "no changes beyond" the plan. pkg-03 adds a `truncate()`
  rewrite and a new config option; pkg-06 adds a renaming pass and a new
  setting. A new flag, option, or setting, a rewrite, or a file the plan
  does not name is drift unless a deviation note in the plan covers it;
  mentioning it only in the description does not.
- Less than the plan: pkg-17's plan promises a dev warning and a docs
  update; the diff has only the warning, while the description says the
  docs "now document the limitation". The missing piece, and the false
  claim, are both drift.

An honest deviation re-ties the mismatch: if the plan records the
change (or, for a deferral, the plan or the description states what is
left out and why), the diff matches. pkg-13 defers `%VAR%` expansion,
says so in the plan's known-limit note, and restates it in the
description: that is a shortfall disclosed, not drift. The test is
whether a reviewer holding only the plan would be surprised by the
diff, or by what the description says about it.

## Test evidence (harness category: not-tested)

**Where it lives.**

- Practice submission: the **Test evidence** section, read against the
  plan's `Test plan` and the repro evidence (its steps and its recorded
  result) in the **Plan context** block. Also the tests the **Diff**
  adds (test files, new cases), and the test command's outcome stated
  in the evidence.
- Your own PR: `test_evidence.md`, read against the test plan in
  `plan.md` and your posted repro; the tests added in `git diff
  main...HEAD`; and the repo's test command from `docs/CONTRIBUTING.md`
  or the PR template.

**What good looks like.** The evidence re-runs the plan's repro on the
branch and shows what changed: the command or steps, the before-result,
and the observed after-result for the behavior the plan said would
change. calib-01's test plan says to re-run the configparser repro and
expect a clean parse, and to smoke-check the theme still renders; the
evidence shows `DuplicateOptionError` before, `parsed OK` after, the
effective value unchanged, and the render unchanged. Every failure mode
the test plan names by name is shown or disclosed as not run, and any
test the PR adds would fail without the fix. A control the plan lists
only needs to be reported unchanged. The repo's own checks are named
with their outcome: "`cargo test -p ignore` passes (214 tests)", not
"tests pass". When neither the plan nor the repo names a test command
(a config-only change like calib-01), the plan's own checks are the
repo checks.

What it looks like when it is not decisive:

- Nothing observable named: pkg-04's evidence is "tested locally,
  colors work now, cargo test passes", and the plan's repro is never
  shown before or after; pkg-10's is "verified working on my machine for
  a full day".
- The unchanged path exercised: pkg-07 runs only the single-file
  control, the path that never broke; pkg-14 runs a GET with header
  casing while the issue's POST Content-Type repro is never re-run, and
  its added test covers casing, not the fix.
- A named failure mode skipped: calib-04's test plan names two failure
  modes, and the evidence re-runs the first only.

## Diff quality (harness category: unreviewable)

**Where it lives.**

- Practice submission: the **Diff** (every hunk) and the **Commits**
  list. The commit list corroborates what the diff shows; it does not
  decide.
- Your own PR: `git diff main...HEAD`, and `git log main..HEAD
  --oneline`.

**What good looks like.** A reviewer can see the change without digging
past noise: every hunk is the fix, a test for it, or an artifact the
plan or repo asks for. calib-01's whole diff is one removed line with
its context. Size is not the test; a long diff can be clean, and a
short one can bury the change.

The debris tells, each of which fails the check when present: debug
prints or logging left in (pkg-15's `DEBUG` eprintln in the accept
loop); commented-out code (pkg-12's commented-out debug line and
commented-out first attempt; pkg-18's commented-out debug eprintln);
dead or unused functions or imports the change added (pkg-12's
`_unused_buffer_probe`, pkg-15's `count_open_fds` behind
`allow(dead_code)`); TODO or WIP leftovers; formatting, re-indent, or
import-reorder churn and identical lines printed twice (pkg-12, pkg-15,
pkg-18); and an unrelated hunk folded in. A comment that explains what
the fix does or why is not debris: pkg-13 and pkg-19 comment their
changes and are clean. Commit messages like "wip", "fmt + cleanup", or
"misc cleanups while debugging" corroborate debris you can see in the
diff, but a clean diff with a poor history still passes, and a tidy
history never excuses debris in the diff.

## Standards and comms (harness category: standards-wall)

**Where it lives.**

- Practice submission: the **Repo facts** block's `pull requests` line
  (the template's sections and checklist, required entries, the
  issue-closing line, the test command) and `contribution policy` line
  (including any AI-use policy); read against the **Description** and
  the **Diff** (where required entries such as a changelog or whatsnew
  file would appear). The **Thread highlights** hold any explicit
  maintainer direction.
- Your own PR: the repo's PR template and `docs/CONTRIBUTING.md`,
  fetched with `gh api` or the web; the house rules in `scope.md` (the
  template is always used); your `pr_draft.md`; the issue's thread.

**What good looks like.** Every ask the repo states is met with
content that belongs to this PR. pkg-02 (rich) asks for a type-of-
changes checklist and a changelog entry: the description says "Type of
changes: Bug fix." and the diff adds the `CHANGELOG.md` entry, which is
short and complete. pkg-08 (lazygit) answers each checklist line (
cheatsheets, formatting, integration test, i18n, no UserConfig change).
A copied template with empty or unticked sections, "n/a" with no
reason, or a required entry absent from the diff is a visibly ignored
ask. pkg-01 (pandas) has a solid fix and decisive ASAN evidence, but the
repo's required checklist is ignored: no `closes #xxxx`, no checklist,
and the whatsnew entry the template asks for is not in the diff. When
the repo states no PR asks (calib-01: "no PR template"; the docs ask
that PRs stay small and reference the issue), there is nothing to
ignore, and the description citing the issue is the whole bar.

AI-use policy: treat every package as AI-assisted work. Silence is
permission, not a requirement to invent a disclosure. Where the policy
asks for something, the description itself provides it. pkg-20 (ghostty)
states that all AI usage must be disclosed, naming the tool and the
extent of the help, and its description has no disclosure at all: that
fails, however good the fix. pkg-11 (ripgrep) welcomes AI-assisted
coding with a human in the loop and asks for maintainer-facing text in
the author's own words; its description says the change was
AI-assisted, that the author reviewed and understands every line, and
that the text is in their own words: that meets it. A policy that only
welcomes AI help asks for nothing.

Two preferred checks read here too. Explicit maintainer direction in the
thread is engaged, not stepped around: pkg-05's description answers
fdncred's uniqueness question and the caveat that followed it. And a
limit the plan records is restated in the description, so a reviewer can
weigh it: pkg-13 and pkg-16 both do. (Whether the description's claims
match the diff is plan fidelity, above.)

## Where each check looks

| Check | Family heading |
|---|---|
| diff-within-plan | Plan fidelity |
| plan-delivered | Plan fidelity |
| description-matches-diff | Plan fidelity |
| decisive-test-evidence | Test evidence |
| repo-checks-run | Test evidence |
| diff-reviewable | Diff quality |
| template-and-asks | Standards and comms |
| disclosure-policy | Standards and comms |
| thread-direction | Standards and comms |
| limits-disclosed | Standards and comms |
