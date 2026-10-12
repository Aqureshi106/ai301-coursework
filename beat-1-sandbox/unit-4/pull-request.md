# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/161

**Branch**

fix/72-verify-password-malformed-hash

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. First launch of the harness: no score. It crashed at process creation on Windows (`FileNotFoundError`, because `claude` here is an npm `.cmd` shim that Python's `subprocess` cannot start), so nothing was graded and nothing was spent. I fixed it by putting the real `claude.exe` on PATH, not by editing the harness.
2. Smoke run, `--only pkg-02`: 1/1 (a partial run, so no bar).
3. Full run, saved with `--save-run`: 20/20.

The last score matches the agreement line in the `eval-run.txt` I committed:

> agreement: 20/20 scored items  (bar: 18/20: PASS)
> categories: clear-accept 7/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3

I never revised after a scored run. The one rubric edit (below) came before the first scored run.

**Package analysis**

**pkg-17** (`vuejs/core#14893`, category silent-drift). My rubric graded it `reject` and the gold label is `reject`.

The plan promises two deliverables: a dev-mode warning in `reactive.ts` and an update to the reactivity-core documentation page. The diff contains only the warning, while the description says the docs now document the limitation. My rubric read it as a failure on three checks, with these evidence lines from the saved run:

- `plan-delivered`: "Plan deliverable (2), 'update the reactivity-core documentation page,' has no corresponding hunk in the diff and no deviation/deferral note discloses it."
- `description-matches-diff`: "Description states 'the reactivity-core documentation now documents the limitation ... in full,' but the diff contains no docs file change at all."
- `decisive-test-evidence`: "Test plan names 'docs render the new note' as a check; test evidence never shows or discloses this as not run."

It reads this way because this is drift in the less-than-plan direction: `plan-delivered` lays each promised deliverable against the diff's hunks, finds none for the docs, and finds no deviation note or description sentence that discloses the omission. The false claim is graded separately by `description-matches-diff`, as a claim the diff contradicts, not as a communication problem. Had the description said plainly that the docs update was left out and why, `plan-delivered` would have passed, because a disclosed shortfall is honest work.

**Check rationale**

The `repo-checks-run` row of the `rubric.md` in `tools/pr-precheck/`, exactly as it reads now:

> | repo-checks-run | The test evidence's account of the repo's own checks, read against the test command or smoke check the plan's test plan names (or, if it names none, the test command the repo's PR template or contributing docs name). | Pass if every repo test command or smoke check the plan's test plan names (or, if it names none, the repo's stated test command) is shown run on the branch with its outcome stated (passes, a pass count, or the specific failures), and that run includes the code the diff changed. If neither the plan nor the repo names a check beyond the repro itself (for example a one-line config change), pass when the checks the plan does name are shown run with their outcome, or when there is no repo check to run; the repro itself is graded by decisive-test-evidence. Fail if a named command or suite is never shown as run, if a one-off script stands in for it, or if its outcome is missing or failing. Unclear only if the package has no test evidence at all. | required |

Why it reads that way: my first version ended its pass condition with "Fail if no repo test command is shown as run, if only a one-off script is shown, or if the outcome is missing or failing." When I read `calib-01` (a one-line config fix; the plan's test plan names a `configparser` repro and a `delta --show-syntax-themes` smoke check, and neither the plan nor the repo names a test suite), I saw that wording would have failed a ready PR for not running a command nobody asked for. So I rewrote it around what the plan's test plan and the repo actually name, and added the clause for when neither names a check: the repro itself is then graded by `decisive-test-evidence`. I did not delete the check, because in my full run it failed `pkg-07`, `pkg-10`, `pkg-14`, and `pkg-10`'s gold note is exactly "yarn test never shown".

**Trade-offs**

A case it will miss: because of the clause I added for `calib-01`, `repo-checks-run` passes when neither the plan's test plan nor the repo names a check beyond the repro. A PR in that position can skip the repo's whole test suite and still pass this check; only `decisive-test-evidence` still demands the repro, and a test the PR adds has to exercise the fix. I accept that because, as I read it, the stricter wording would have rejected a PR shaped like `calib-01`, which the gold labels call ready (I read that package; I did not run it through the harness).

Nothing else changed around it: the edit came before my only full run, so all 20 packages, including the two-package `standards-wall` category, were graded with it in place. I did not loosen any check after a scored run, so there was no flip to catch with canaries.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
