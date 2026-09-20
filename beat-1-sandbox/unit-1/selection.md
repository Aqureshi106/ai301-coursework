# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72

**Verdict output**

Live-mode run: `claude "issue-select: grade these candidate first issues: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72 https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68 https://github.com/codepath/pathreview-ai301-fa26-s3/issues/49"`

Scope confirmed: all three issues are in `codepath/pathreview-ai301-fa26-s3`, the repo named in `scope.md`.

Checks executed per `rubric.md`: `repo_is_active`, `no_active_claim`, `scope_is_reasonable`, `policy_allows_ai` (all required), `task_has_clear_success` (preferred).

Repo-level facts (shared by all three candidates): archived: false; last push 2026-09-16 (4 days before this run); no releases published; `docs/CONTRIBUTING.md` contains no generative-AI or AI-disclosure language, and no `AI_POLICY.md`/`AGENTS.md` file exists in the repo root.

Ranked read-out (all three accepted; ranked by the fit profile in `scope.md` — small, concrete, Python-verifiable work):

1. **#72** — `verify_password` raises `UnknownHashError` on malformed stored hashes instead of returning False. Fit: a single-file Python bug with a named regression test and an explicit fail-closed target, the smallest and most directly testable of the three.
2. **#68** — Keyword search raises `ZeroDivisionError` when the index is empty. Fit: same shape as #72 (single-file Python bug, named test) but a larger estimated effort (2-4h) and a classmate's claim comment already in the thread ("I'd like to take this one on") — not a blocker under the Path Review house rule, but it drops #68 below #72 on fit.
3. **#49** — Write a contributor onboarding guide covering the issue-to-PR lifecycle. Fit: accepted, but it is documentation work with no test to verify it and a partly open-ended acceptance criterion (only the `make test-integration` addition to CONTRIBUTING.md is concretely checkable), the weakest fit for a Python-heavy, test-verifiable first issue.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72",
    "checks": [
      {"name": "repo_is_active", "grade": "pass", "evidence": "archived: false, pushed_at 2026-09-16"},
      {"name": "no_active_claim", "grade": "pass", "evidence": "assignees: none, linked PRs: none, comments: 0"},
      {"name": "scope_is_reasonable", "grade": "pass", "evidence": "single fix in core/security.py with named test tests/unit/test_security.py, est. 1-2h"},
      {"name": "policy_allows_ai", "grade": "pass", "evidence": "CONTRIBUTING.md has no AI/generative-AI section; no AI_POLICY.md or AGENTS.md in repo root"},
      {"name": "task_has_clear_success", "grade": "pass", "evidence": "issue states the fix must fail closed (return False) and names the xfail test to un-mark (manifest id H-05)"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68",
    "checks": [
      {"name": "repo_is_active", "grade": "pass", "evidence": "archived: false, pushed_at 2026-09-16"},
      {"name": "no_active_claim", "grade": "pass", "evidence": "assignees: none, linked PRs: none; one claim comment (\"I'd like to take this one on\") — scope.md's Path Review house rule: classmates' claim comments do not block"},
      {"name": "scope_is_reasonable", "grade": "pass", "evidence": "single fix in rag/retriever/keyword_search.py with named test tests/unit/test_keyword_search.py, est. 2-4h"},
      {"name": "policy_allows_ai", "grade": "pass", "evidence": "CONTRIBUTING.md has no AI/generative-AI section; no AI_POLICY.md or AGENTS.md in repo root"},
      {"name": "task_has_clear_success", "grade": "pass", "evidence": "issue states index() shouldn't raise on an empty corpus and names the xfail test to un-mark (manifest id H-01)"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/49",
    "checks": [
      {"name": "repo_is_active", "grade": "pass", "evidence": "archived: false, pushed_at 2026-09-16"},
      {"name": "no_active_claim", "grade": "pass", "evidence": "assignees: none, linked PRs: none, comments: 0"},
      {"name": "scope_is_reasonable", "grade": "pass", "evidence": "two named targets (new docs/ONBOARDING.md, one CONTRIBUTING.md addition), est. 4-7h, no umbrella or tracking language"},
      {"name": "policy_allows_ai", "grade": "pass", "evidence": "CONTRIBUTING.md has no AI/generative-AI section; no AI_POLICY.md or AGENTS.md in repo root"},
      {"name": "task_has_clear_success", "grade": "unclear", "evidence": "the CONTRIBUTING.md line item is concrete, but 'write a walkthrough' names no acceptance criteria for the walkthrough's content"}
    ],
    "verdict": "accept"
  }
]
```

**The verdict must record `accept` for this issue.** Choose an issue your own skill accepts. If your skill rejects every candidate you try, that is a signal about your rubric rather than about the issues: revise it and re-run — retries are unlimited and a partial re-run costs about $0.20 — or run the skill on different candidates. Output recording `reject` for the issue you chose earns no credit for this field.

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

- Early canary: `--only issue-01` initially exposed a rubric mismatch.
- Issue validation: `--only issue-06` produced `agreement: 1/1 scored items`.
- Final full run: `agreement: 20/20 scored items  (bar: 18/20: PASS)`.
- Live mode: issues `#72`, `#68`, and `#49` were all accepted; `#72` ranked first for fit.

The final full-run score matches the agreement line in `eval-run.txt`; the saved full run is the submitted eval run.

**Issue analysis**

`issue-12` (source: `bookwyrm-social/bookwyrm#1133`, category `policy`). My rubric's verdict was `reject`, and the gold label is also `reject`. The issue passes every other required check: the repo is active (`last push to any branch: 2026-08-12`, recent merged commits), it has no assignee or open linked PR (the one comment on it, "I've started to look at this," is over a year stale and never became a claim), and it is a bounded UI/frontend feature (add the in-progress portion of the current book to the reading-goal progress bar). It fails on `policy_allows_ai`: the captured contribution policy states, "We do not accept AI-generated code or documentation," which is an outright ban rather than a disclosure condition, so the required check fails and the verdict rule (`accept` only if every required check passes) rejects it regardless of how clean the rest of the issue looks.

**Check rationale**

From the current `tools/issue-select/rubric.md`:

`| scope_is_reasonable | issue text, acceptance criteria, and comment history; look for umbrella/tracking language, years-long design debate, or feature requests without a grounded design target | Pass if the issue is a single concrete bug or narrow feature request with a defined target and no repeated abandoned attempts or open-ended tracking discussion | required |`

I kept this check narrow because a first contribution should be something that can be verified quickly without a large design discussion. This issue is a focused Python bug with explicit expected behavior and a named test, so it fits the single-concrete-bug case instead of the kind of tracker or broad feature request that often traps beginners.

**Trade-offs**

This check costs me some legitimate but slightly ambiguous issues that are still worth doing, especially cases where the surface area is small but the fix requires a design judgment. In live mode, #49 passed the required checks but ranked below #72 because its documentation success criteria were subjective, while #68 was a larger 2-4 hour bug and already had a student claim comment. I accepted that trade-off to keep the rubric focused on small, testable first-contribution scope.

---

## Selection rationale

**Selection rationale**

1. This issue fits my interests and the time I have because it is a small Python security-handling bug with a clear fail-closed behavior and a focused test path. It is not a large architecture task and should be verifiable with a targeted test.
2. The verdict identified the issue correctly because the repo is active, the task is bounded to one function and its test, there is no assignee or comment claim, and the expected outcome is explicit. Among the accepted live candidates, #72 fit me best: #68 was similar but estimated at 2-4 hours and had a claim comment, while #49 was documentation work with more subjective success criteria.
3. The expected difficulty in claiming it is low to moderate but manageable: I need to inspect the password hash verification path, handle the malformed-hash exception without weakening valid-password behavior, and remove or update the expected-failure marker in the named test. That is a contained Python change with a clear reproduction and verification path.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
