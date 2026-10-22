# Contribution: feat(exporters): add Slack and Discord rich card formatters to WebhookExporter

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Ashir Qureshi
**GitHub Username:** Aqureshi106
**Issue:** https://github.com/Zwony/ecotrace/issues/194
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Built issue-scout and fixed a claim check that ignored linked PRs**

I grew my unit 1 issue-select skill into issue-scout: four `gh search issues` queries traced to my Python-first, small-and-testable fit profile, plus six ranking filters (drop assigned, stale, busy, or design-labeled issues; bugs and fewest comments first; at most two per repo), then one scan that graded the top five. It broke in one place: the scout accepted anthropics/claude-agent-sdk-python #1409 because `no_active_claim` quoted only assignees and comments, but when I checked with `gh` that issue already had an open PR (#1410) from its own author, opened two seconds after the issue. I rewrote that check in rubric.md to require the linked-PR count and to read it from `closedByPullRequestsReferences` and the timeline, and added `-linked:pr` to all four queries so such issues never take a grading slot; I confirmed the query change removes #1409 but have not re-run the rubric fix yet. The other four verdicts held up against the real repos, and the remaining limits (the language flag matches the repo, not the fix, and same-day batches crowd the top five) are logged in scan-notes.md.

**Unit 6: Installed the voice guard and a repo memory file, and patched the guard to run on Windows**

I installed the voice guard, a hook that checks every `gh` comment I post against my voice guide (which now has an Upstream section for claims, questions, and pings), and wrote a 41-line `CLAUDE.local.md` for ecotrace with its conventions, the exporters subsystem, the test commands, and its AI policy, kept out of the PR with `.git/info/exclude`. Three things fell short: on Windows the guard could not start `claude` (`claude.CMD` is not found by a bare `subprocess.run`), so every real check would have stopped, and symlinks needed admin, so the three skills got pointer files instead; my first claim draft promised to report back "within a week" and failed repro-check, which cited my own no-dates rule; and CONTRIBUTING's `pip install -e ".[dev]"` names an extra that does not exist. I patched the installed `check_voice.py` to find `claude` with `shutil.which`, reworded the claim to "I'll post what I find here before I open a PR" (repro-check then accepted it), and put the install commands CI actually uses in the memory file. I know the guard works because it stopped `gh issue comment --help` in the wiring test and logged a pass on the claim comment in `guard-log.md`, and the maintainer answered the claim and assigned me the same day.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I started from my scout's scan on 2026-10-18, which accepted four issues. When I came back on 2026-10-22 every one of them had been claimed within hours of being filed (a claim comment, an assignee, or an open PR; one of them, #1409, was already taken when the scan graded it), and re-grading three of them with the patched rubric rejected all three on `no_active_claim`. So I picked from the Issue Selection site's Python list instead. I read the full thread of about two dozen candidates from it, looking for claim comments and linked PRs, and graded the survivors with the scout in direct-URL mode. `Zwony/ecotrace` #194, "add Slack and Discord rich card formatters to WebhookExporter", was the one that held up: no comments, no PRs, no assignee, and the repo was pushed that day. The scout's verdict was accept with a difficulty note of **moderate**: the file is named exactly (`ecotrace/exporters/webhook.py`), but building the Slack Block Kit and Discord embed payloads means reading two outside specs. It ranked #194 above `ecotrace` #182 (TDP data entry, straightforward) because #194 has a written acceptance checklist and a named test file.

It fits me because it is Python-only, local to one exporter class, and checkable with tests. The issue's own acceptance criteria are backward compatibility for `format="json"`, a Discord embed, a Slack Block Kit payload, unit tests in `tests/test_exporters.py`, and no new dependencies. I read both copies of its `CONTRIBUTING.MD` and the PR template: they say nothing about AI, so AI-assisted work is not banned there. I hope to learn how to build payloads to an external spec and test them with mocks, how a small repo's CI (Python 3.8 to 3.14) limits what I can write, and how to settle the one open question, what "exceeding limits" means for the Discord colour, with the maintainer before building on a guess. As for the scout: its verdicts held up. Every `no_active_claim` grade I re-checked matched what I found reading the threads myself, and the difficulty note matched my read of the work. It fell short at the search stage. The ranking puts zero-comment issues updated that same day first, which are exactly the kind that got claimed within hours last time, and #194 survived because it was already 25 days old with zero comments. I would change the ranking to prefer that. My claim comment: https://github.com/Zwony/ecotrace/issues/194#issuecomment-6351982794

**Toolkit**

- Tool: issue-scout, with my own `search-strategy.md` and `rubric.md`, run through Claude Code. I used it for a scan on 2026-10-18, to re-grade that scan's accepts on 2026-10-22, and in direct-URL mode to grade the issues I took from the Issue Selection site. In unit 6 I patched the rubric's scope check (below).
- What it produced: scan 1 (2026-10-18) gave 4 accepts and 1 reject. The 2026-10-22 re-grade of three of those accepts rejected all three on `no_active_claim`, each with the PR number, claim comment, or assignee that did it. A second search's top five gave 4 accepts and 1 reject. For this issue it gave accept with a difficulty note of moderate, and accept with straightforward for the runner-up, `ecotrace` #182. Every read-out is saved in `scan-output.md`.
- Difficulties: all four scan-1 accepts had been claimed within hours of being filed, so the scan left me nothing I could still take. The scout's own scan mode could not run non-interactively because it was refused permission for `gh` commands; I ran the strategy's four queries and six ranking filters with a script instead and used the scout only to grade. The top five were all zero-comment issues last updated that same day. And the scout searches all of GitHub, while the Issue Selection site holds about 7,900 issues, so most of what a scan returns is not on the site. On one repo it flagged an instruction aimed at AI agents in `CONTRIBUTING.md` (star the repo) and did not act on it.
- What changed: I replaced `scope_is_reasonable` (a single concrete bug or narrow feature, no repeated abandoned attempts or open-ended discussion) with `scope-feasible`, which fails only when there is no single piece of work in the issue, and added a difficulty note section (straightforward, moderate, ambitious) that never changes the verdict. I logged it in `scan-notes.md` on 2026-10-22. It ran on ten issues and produced difficulty notes; none of them exercised the failing case, so rejecting an umbrella issue is untested, and I have not re-run the unit-1 harness against the patched rubric. I would also change the ranking to prefer zero-comment issues at least a few days old, which is what survived here; I have not made or tested that change.

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

**Environment Setup**

[What a stranger would need to stand this up: your operating system,
the relevant versions (language, runtime, package manager, the project
itself), and how you set the project up. Then the challenges you faced
and how you solved them, or that none came up.]

**Steps to Reproduce**

1. [Step 1]
2. [Step 2]
3. [Observed result]

**Reproduction Evidence**

- **Commit showing reproduction:** [link to commit in your fork]
- **Screenshots/logs:** [if applicable]
- **My findings:** [what you discovered during reproduction; an
  honest, evidenced cannot-reproduce that led you to re-pick or ask
  the maintainer belongs here and costs you nothing]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
