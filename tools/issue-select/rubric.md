# Rubric: is this a good first issue?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| repo_is_active | repo-facts block: `archived`, `last push to any branch`, `latest release`, and the recent default-branch commits | Pass if the repo is not archived and has current maintenance signal in the capture: a last push or default-branch commit within 365 days, or a release within 365 days | required |
| no_active_claim | issue sidebar and comments: `assignees`, `linked PRs`, and claim comments such as "I'll take this" or "working on this" | Pass if there is no assignee, no open linked PR, and no evidence of active or repeated claim activity in the thread; stale claim comments alone are not a blocker | required |
| scope_is_reasonable | issue text, acceptance criteria, and comment history; look for umbrella/tracking language, years-long design debate, or feature requests without a grounded design target | Pass if the issue is a single concrete bug or narrow feature request with a defined target and no repeated abandoned attempts or open-ended tracking discussion | required |
| policy_allows_ai | contribution policy and any explicit AI policy or contributor docs from repo facts | Pass if the repo either says nothing about AI contributions or explicitly allows AI-assisted work; fail if it expressly bans AI-generated code or docs | required |
| task_has_clear_success | issue body: checklist, reproduction details, expected behavior, or maintainer-defined acceptance criteria | Pass if the issue states a concrete, testable outcome or a specific bug to fix; missing this is a nice-to-have signal, not a veto | preferred |

## Verdict rule

Accept if all required checks pass. Preferred checks never change the verdict and only rank accepted issues. If any required check fails, reject. If evidence is genuinely absent, grade `unclear` and treat it as fail for the verdict.
