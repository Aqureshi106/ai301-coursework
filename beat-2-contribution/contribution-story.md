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
**Pull Request:** https://github.com/Zwony/ecotrace/pull/229

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

**Unit 7: Wrote a ship manifest and ran the ship-gate on a real branch, and got around a flaky test and a Windows launch bug**

I wrote `ship-manifest.md` for Zwony/ecotrace with one check, the test suite, and moved lint to the excluded table because `flake8 ecotrace/` already fails on main (745 violations), then ran the ship-gate on my branch four times. Its last run said pass: the suite was ok (213 s) and all ten pr-precheck checks passed, including that my diff stays inside the plan's two files and that my new tests fail on main (8 failed, 3 passed), and the hook then let the PR open as #229. Three things stopped or confused me: a test in `tests/test_report.py` failed intermittently on matplotlib's Tk backend (on untouched main too, in 6 of 8 runs), `check_gate.py` could not launch `claude.CMD` on Windows, and my voice guard crashed on the PR description's emoji, so it never checked the description. I changed the manifest's `Tests` row to run with `set MPLBACKEND=Agg&&` (it passed 8 of 8 and no test was excluded), then patched both tools, so `check_gate.py` launches `shutil.which("claude")` and `check_voice.py` reads UTF-8, stops a post when it crashes, and waits up to 180 s, and the first time the patched guard really read my description it stopped on a sentence that put my own choice in the maintainer's mouth, which I reworded on the PR.

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

`WebhookExporter` can only send one fixed JSON shape (`function`, `carbon_gco2`, `duration_s`, `region`, `run_id`, `run_label`). The issue (#194) says teams get their alerts in Slack and Discord through incoming webhooks and want EcoTrace to format for those, so they don't have to write their own middleware. Right now the exporter has no way to choose a format at all.

**Expected Behavior**

`WebhookExporter(eco, url=..., format="discord")` sends a Discord embed showing function name, duration and gCO2, coloured green when emissions are under `carbon_limit` (or no limit is set) and red/orange when they are over it, which the maintainer confirmed on the thread. `format="slack"` sends a Slack Block Kit payload. `format="json"`, the default, keeps sending the existing payload. No new third-party dependencies, and tests in `tests/test_exporters.py`.

**Current Behavior**

Without `format`, the exporter works and POSTs only the flat dict, with no `embeds` or `blocks`. Passing `format="json"`, `"discord"` or `"slack"` raises `TypeError: WebhookExporter.__init__() got an unexpected keyword argument 'format'` (ecotrace 1.7.1 at commit 413685b, Python 3.14.3, Windows 11).

**Affected Components**

`ecotrace/exporters/webhook.py` (`WebhookExporter.__init__` and `export`) is where the change goes. It reads `carbon_limit` from the `EcoTrace` instance in `ecotrace/core.py`, which calls the exporter from `_accumulate_carbon`. The tests go in `tests/test_exporters.py`.

**Toolkit**

- Tool: `CLAUDE.local.md`, the repo-memory file I made in Unit 6 for this repo, and Claude Code reading the issue (`gh issue view`) and the exporter code with me.
- What it produced: notes on the repo's conventions from CONTRIBUTING.MD, the issue's own checklist as the scope, a walk through the subsystem (`webhook.py`, how `core.py` calls exporters, the existing webhook tests), and the build and test commands.
- Difficulties: CONTRIBUTING.MD says to install with `pip install -e ".[dev]"` and lint with `flake8 ecotrace/`, but `pyproject.toml` has no `dev` extra and CI never runs flake8. On untouched main, flake8 reports 745 violations.
- What changed: `CLAUDE.local.md` now says not to copy that install line, and my Unit 7 ship manifest runs the test command CI uses and lists flake8 as excluded, with the date and the reason.

---

## Reproduction Process

**Environment Setup**

I ran this on Windows 11 (`Windows-11-10.0.26300-SP0`) with Python 3.14.3 in a fresh `.venv`, against ecotrace 1.7.1 installed editable from commit `413685b4c82cbcfcb436ba6276dda99b62b0a29e` (`v1.7.1-6-g413685b`, main on 2026-10-22), with no local changes. `requests` 2.34.2 comes in through ecotrace's own dependencies, and the install is `pip install -e .`. The issue names no environment, and CI tests Python 3.8 to 3.14-dev, so 3.14.3 is one point in that range. No network is used: the script patches `requests.post`, the way the repo's own tests do. For the baseline I also installed pytest 8.3.4, requests-mock 1.12.1 and pytest-cov 5.0.0, which is CI's install.

Challenges: CONTRIBUTING says `pip install -e ".[dev]"`, but `pyproject.toml` has no `dev` extra, so I copied CI's install lines from `.github/workflows/ci.yml` instead. Nothing else came up: `tests/test_exporters.py` gave 9 passed and the full suite 239 passed, 2 skipped, before I changed anything.

**Steps to Reproduce**

1. Clone and pin the commit: `git clone https://github.com/Zwony/ecotrace.git`, then `cd ecotrace && git checkout 413685b`.
2. Create the environment and install: `python -m venv .venv`, then `.venv\Scripts\python.exe -m pip install -e .`.
3. Save this as `repro_194.py` (anywhere; it imports the installed package):

```python
import inspect
from unittest.mock import patch

from ecotrace.core import EcoTrace
from ecotrace.exporters.webhook import WebhookExporter

URL = "http://example.com/webhook"

print("signature:", inspect.signature(WebhookExporter.__init__))

# Control: the call the code supports today works, and sends the raw JSON dict.
with patch("requests.post") as post:
    post.return_value.status_code = 200
    eco = EcoTrace(quiet=True, check_updates=False)
    WebhookExporter(ecotrace_instance=eco, url=URL)
    eco._accumulate_carbon(carbon_emitted=0.005, func_name="test_function", duration=1.2)
    eco._exporter_pool.shutdown(wait=True)
    payload = post.call_args.kwargs["json"]

print("control, no format argument -> POST json:", payload)
print("  has Discord 'embeds':", "embeds" in payload, "| has Slack 'blocks':", "blocks" in payload)

# The calls the issue proposes, including the backward-compatible default.
for fmt in ("json", "discord", "slack"):
    try:
        WebhookExporter(ecotrace_instance=eco, url=URL, format=fmt)
        print(f"format={fmt!r}: accepted")
    except TypeError as e:
        print(f"format={fmt!r}: TypeError: {e}")
```

4. Run it: `.venv\Scripts\python.exe repro_194.py`.
5. Observed result: with no `format` argument the exporter works and POSTs the flat dict with no `embeds` or `blocks` key; with `format` set to `"json"`, `"discord"` or `"slack"`, the constructor raises `TypeError: WebhookExporter.__init__() got an unexpected keyword argument 'format'`. I ran it twice with the same output (only the generated `run_id` differs).

**Reproduction Evidence**

- **Commit showing reproduction:** none. I have not forked the repo, and the script is not committed; it is in the steps above.
- **Screenshots/logs:** output of step 4:

```
signature: (self, ecotrace_instance: ecotrace.core.EcoTrace, url: str, headers: Dict[str, str] | None = None)
control, no format argument -> POST json: {'function': 'test_function', 'carbon_gco2': 0.005, 'duration_s': 1.2, 'region': 'GLOBAL', 'run_id': 'f4bf68575d01', 'run_label': ''}
  has Discord 'embeds': False | has Slack 'blocks': False
format='json': TypeError: WebhookExporter.__init__() got an unexpected keyword argument 'format'
format='discord': TypeError: WebhookExporter.__init__() got an unexpected keyword argument 'format'
format='slack': TypeError: WebhookExporter.__init__() got an unexpected keyword argument 'format'
```

- **My findings:** reproduced. #194 is a feature request, so what reproduces is the missing behavior. `WebhookExporter.__init__` takes only `ecotrace_instance`, `url` and `headers`, so the exporter can send only the raw JSON dict and rejects the `format` argument the issue proposes, including the `"json"` default it wants to keep. The control run shows the exporter works without `format`, so the failure is specific to the missing argument and not to my setup. I have not changed any code and have not yet decided how the Discord and Slack payloads should be built.

**Toolkit**

- Tool: repro-check, my unit-2 skill with this week's real-repo `scope.md` and my voice guide, run in live mode through Claude Code on `claim-draft.md` and `repro-draft.md` together.
- What it produced: on the claim alone it first failed the draft because of "within a week" (`claim-is-specific`, and it quoted my own no-dates rule), then accepted the reworded claim. On the full package it accepted with all seven checks passing, including the preferred `control-run`, and found no voice-guide rule broken.
- Difficulties: the rubric is written around failures with error text and exit codes, and #194 is a feature request, so `artifact-matches-issue` took a judgment: the grader read "the artifact shows the requested behavior missing" as the failure, which was the right reading, but nothing in my rubric says that. `claim-matches-evidence` also let the pytest counts in my claim stand as prose with no artifact behind them, which my unit-2 loosening allows; I know they are true because I ran them, not because the check proved it. The package passed on its first grading, so I cannot say yet what it would catch in a weaker report.
- What changed: nothing in the rubric. The claim wording changed, from a dated promise to "I'll post what I find here before I open a PR". I would add one line to `artifact-matches-issue` saying that for a feature request the artifact should show the requested behavior absent, and I have not made that change.

---

## Solution Approach

**Analysis**

The cause is a missing capability, in two places in `ecotrace/exporters/webhook.py` (read on main at 413685b):

- `WebhookExporter.__init__` (line 24) takes only `ecotrace_instance`, `url` and `headers`, so there is nowhere to say which format to send. I observed this: `format="json"`, `"discord"` and `"slack"` all raise `TypeError: WebhookExporter.__init__() got an unexpected keyword argument 'format'` (my `repro_194.py`, Windows 11, Python 3.14.3).
- `WebhookExporter.export` (line 44) always builds the same flat dict (`function`, `carbon_gco2`, `duration_s`, `region`, `run_id`, `run_label`) and POSTs it with `requests.post(..., json=payload)`. The control run in my repro shows that payload has no `embeds` key and no `blocks` key. The issue's premise is that Slack and Discord incoming webhooks need their own payload shapes (Block Kit, embeds). I have not posted to either service, so that part comes from the issue, not from a run of mine.

Nothing upstream of the exporter has to change. `EcoTrace._accumulate_carbon` (`ecotrace/core.py`, line 394) already calls `exporter.export(carbon_emitted=..., func_name=..., duration=..., region=..., run_id=..., run_label=...)` on a thread pool, so everything a card needs is already passed in. The colour limit is reachable too: `EcoTrace.__init__` validates `carbon_limit` (a positive number or None, lines 108-113) and keeps it as `self.carbon_limit`, and the exporter already holds the instance as `self.ecotrace`.

One thing to keep straight: core uses `carbon_limit` as a running budget (`_enforce_carbon_budget`, line 468, compares the cumulative `total_carbon`), but an exporter only receives one measurement at a time. The maintainer confirmed on the issue thread (2026-10-22) that the Discord colour should compare the measurement against `carbon_limit`: green when below it or when it is unset, red/orange when above it. So the colour is per measurement. It is not a "budget used up" signal.

**Proposed Solution**

Add an optional `format` argument to `WebhookExporter` and build the payload in `export()` by format, all inside `webhook.py`, with pure dict formatting and no new dependencies (the issue requires that).

- `format="json"` is the default and sends exactly the dict it sends today. A test pins that.
- `format="discord"` sends `{"embeds": [...]}` with one embed: the function name as the title, gCO2 and duration as fields, and a colour. The colour is red when `carbon_limit` is a number and the measurement is at or above it, and green otherwise (including when `carbon_limit` is None). I will read `self.ecotrace.carbon_limit` rather than change core to pass it into `export()`, which would touch the contract every exporter shares.
- `format="slack"` sends Block Kit `blocks` (a section with the function name, and a section with gCO2 and duration as fields) plus a plain `text` fallback.
- An unknown `format` logs a warning and falls back to "json", the way `EcoTrace.__init__` treats a bad `gpu_index` or `carbon_limit`.

What I am deliberately not changing: `core.py` (dispatch, `carbon_limit` validation, the budget logic, the "reserved for future budget alert" docstring), the cloud and OTel exporters, the default JSON payload and the existing webhook tests, `region` / `run_id` / `run_label` on the cards (the issue names only function name, duration and gCO2), other chat services such as Teams, retries, and docs. `docs/USAGE.md` still says each event sends the JSON payload, which stays true by default; I will ask in the PR whether a format example belongs there, instead of adding it unasked.

What I am least sure of, and how I will find out:

- Whether `>=` is the right edge for red. The maintainer's words cover "below" and "exceed", not "equal". I will use `>=` to match core's budget check, test the equal case, and say so in the PR.
- The card shapes are only checked as dicts, and I have not sent one to real Slack or Discord. I will check them against Discord's embed docs and Slack's Block Kit docs as I build, and send one of each to a throwaway webhook of my own if I can set one up.
- `tests/test_v151_validation.py` builds `WebhookExporter` around a bare `MagicMock()`, where `carbon_limit` is a mock and not a number. The default JSON path never reads it, and the Discord path will compare only when it is an int or float. The full suite will show whether I got that right.
- CI runs Python 3.8 to 3.14-dev, so the new code keeps to `typing.Dict` / `Optional` (no `X | Y`, no `match`).

**Implementation Plan**

1. In `ecotrace/exporters/webhook.py`, add `format: str = "json"` after `headers` (so existing positional calls still work), document it in the class docstring, validate it (an unknown value logs a warning and falls back to "json"), and store it as `self.format`. In `tests/test_exporters.py`, add the first test: the default payload and `format="json"` both send the same six-key dict as today. It follows `test_webhook_exporter_registration_and_export`: patch `requests.post`, drive a real `EcoTrace` through `eco._accumulate_carbon(...)`, and call `eco._exporter_pool.shutdown(wait=True)` before asserting. Run it before going further; my claim on the issue said this comes first.
2. In the same file, add a Discord payload builder and branch `export()` on `self.format`, leaving the json branch's dict and its `requests.post` call as they are. Tests in `tests/test_exporters.py`: the payload has one embed with the function name, gCO2 and duration; the colour is green when `carbon_limit` is None and when the measurement is under it, and red when it is over it and when it is equal to it.
3. In the same file, add the Slack payload builder. Test: `blocks` and `text` are present and carry the function name, gCO2 and duration.
4. Add a test that an unknown `format` logs a warning and still sends the json payload.
5. Check the result. Re-run the script from my repro report (`repro_194.py`). Before: the three `format` calls raise TypeError. After: they are accepted, the control (no `format`) prints the same six keys as before, and the POSTed payload has `embeds` only for "discord" and `blocks` only for "slack". Then run `.venv\Scripts\python.exe -m pytest tests/ -q`. On main that was 239 passed, 2 skipped (`tests/test_exporters.py` alone: 9 passed). After the change I expect the same, plus the new tests passing, with no existing test edited.

Deviations from this plan: none yet.

**Toolkit**

- Tool: plan-check (my Unit 3 skill, with the scope for real repos), run with `claude -p` from my clone on `plan.md` and a draft plan comment, `plan-comment.md`, against issue #194. The comment stays local; I did not post it.
- What it produced: accept on the first graded run, with all seven checks passing (diagnosis, scope, test plan, executable, claims-match-evidence, comment-fits-thread, risks-named). Each grade quoted its evidence from the live code and thread, for example that `__init__` takes only `ecotrace_instance`, `url` and `headers`, that the maintainer's own words back the per-measurement colour rule, and that the `>=` edge is hedged as unsure rather than asserted.
- Difficulties: the first run didn't grade anything. In print mode it could not read the skill's own rubric and procedure files and asked me to approve access. I re-ran it with read access to the skill folder and read-only `gh` and `git` commands. My repro report isn't posted on the issue yet, so there was no posted repro comment for plan-check to read; the plan and the comment quote the TypeError and the control payload themselves, and it passed on that.
- What changed: nothing in the plan or the comment, because the grade was accept, so `plan.md` and this section are still the same text. For next time I know the permissions that print mode needs for this skill, and I want to post the repro report before relying on the grade.

---

## Testing Strategy

I check the fix three ways, all on Windows 11 with Python 3.14.3, and each one is a command whose output I can compare before and after. The complete output is in `test_evidence.md`, which I keep out of my commits and plan to use for the PR description.

**1. My reproduction, re-run.** I ran `repro_194.py` from Steps to Reproduce against main at 413685b, then against the branch (HEAD b25d3e2). Because the editable install points at the branch, I ran the "before" against an export of main and printed which `webhook.py` each run imported.

```
before (main):  signature: (self, ecotrace_instance, url, headers=None)
                format='json':    TypeError: WebhookExporter.__init__() got an unexpected keyword argument 'format'
                format='discord': TypeError: ... unexpected keyword argument 'format'
                format='slack':   TypeError: ... unexpected keyword argument 'format'

after (branch): signature: (self, ecotrace_instance, url, headers=None, format: str = 'json')
                format='json':    accepted
                format='discord': accepted
                format='slack':   accepted
```

The control call with no `format` still POSTs the same six keys (`function`, `carbon_gco2`, `duration_s`, `region`, `run_id`, `run_label`) with no `embeds` or `blocks`. That script only constructs exporters, so I added a companion that sends one measurement per format and prints the top-level keys of what is POSTed: the six JSON keys for no format and for `"json"`, `['embeds']` for `"discord"`, and `['blocks', 'text']` for `"slack"`.

**2. New tests that fail without the fix.** I added 9 tests to `tests/test_exporters.py`: the default payload is unchanged; `format="json"` sends the same payload; the Discord embed carries the function name, gCO2 and duration; the Discord colour with no `carbon_limit`, below it, equal to it and above it; the Slack blocks and `text`; and an unknown format warns and falls back to JSON. I ran them against main's code: 8 fail and 3 pass (the two existing webhook tests, and the default-payload pin, which has to pass either way). On the branch they all pass.

**3. The repo's own suite, through my ship manifest.** The manifest's one check is `.venv\Scripts\python.exe -m pytest tests/ -q`, the test command CI runs (without CI's coverage flags, so nothing is written). Through the ship-gate's loader, in a plain `cmd` at the clone root: exit code 0, 248 passed, 2 skipped, in 211 s. On main it was 239 passed, 2 skipped, so the difference is the 9 new tests and I edited no existing test.

What I have not run: a card sent to a real Slack or Discord webhook (the tests check payload dicts, which I compared with both platforms' docs); any Python other than 3.14.3 or any OS other than Windows (CI runs 3.8 to 3.14-dev on Linux); and the whole ship-gate, since it also needs a PR title and description. flake8 is excluded in my manifest because it already fails on main; I ran it by hand for information: 745 violations on main, 760 on the branch, and the 15 new ones are all line length on lines I added.

**Toolkit**

- Tool: my ship manifest, `ship-manifest.md`. I loaded it with the ship-gate's own loader and ran its one check the way the gate runs it (`cmd.exe`, top of the clone, no virtual environment active). Alongside it I used `repro_194.py` and pytest. I did not run the whole gate, which also grades a PR title and description I don't have yet.
- What it produced: a pass: exit code 0, 248 passed and 2 skipped in 211 s. I put that, my reproduction before and after, and the new tests failing on main into `test_evidence.md`.
- Difficulties: the manifest holds only one check, because lint already fails on main, so the gate cannot see the 15 new line-length hits I added; I counted them by hand (flake8: 745 on main, 760 on the branch). My repro script only constructs exporters, so it cannot show what is sent. And the editable install points at the branch, so a naive "before" run would have imported the fix; I had to run main from a scratch export and print which file each run imported.
- What changed: I wrote the companion script that prints what is POSTed, and I now run "before" from an export of main. The manifest itself did not change. What I would update, and have not: a lint row that counts only the lines a PR adds, so the gate could catch new violations without failing on old ones.

---

## Implementation Notes

**2026-10-29 (branch and plan step 1)**

I branched `feature/194-webhook-format` off main at 413685b; CONTRIBUTING's only example name is `feature/your-feature-name`, and this is a feature. Upstream main had moved 8 commits since 2026-10-22, all docs and examples, none in `webhook.py` or `tests/test_exporters.py`, and no PR for #194 exists, so I kept 413685b as the base and will rebase onto origin/main before I open the PR.

For step 1 I wrote the tests first. Against main's `webhook.py`, the `format="json"` test failed with the same `TypeError: ... unexpected keyword argument 'format'` as my repro, and the default-payload test passed, which is its job: it pins the payload that must not change. Then I added `format: str = "json"` after `headers`, with the warning and fallback for an unknown value. A wrong turn: my first version listed `"discord"` and `"slack"` as valid and documented them, though they would still have sent JSON, so I cut the commit down to `"json"` only; each later commit adds its own format and its docs. Commit 1b7bf97. `tests/test_exporters.py` and `tests/test_v151_validation.py`: 24 passed.

**2026-10-29 (plan step 2, Discord)**

Tests first again: the five Discord tests all failed against commit 1, because `"discord"` was not yet a known format and fell back to the JSON payload. Then I added `_discord_payload` to `webhook.py`: one embed with the function name as the title, gCO2 and duration as fields, and a colour that is red when `carbon_limit` is a number and the measurement is at or above it, green otherwise. I chose `>=` so the edge matches core's own budget check (`total_carbon >= carbon_limit`), and the maintainer's words ("below" and "exceed") do not cover "equal"; the colour test pins the equal case, and I plan to say so in the PR. The plan's `MagicMock` risk was real in principle: `tests/test_v151_validation.py` builds the exporter around a bare `MagicMock()`, so `carbon_limit` there is a mock, not a number. The suite never runs a Discord exporter against a mock, so I checked it with a throwaway script (not committed): the embed posts and comes out green, no exception. The colour values are Discord's usual green (0x2ECC71) and red (0xE74C3C); I picked red over orange. Commit 2. `tests/test_exporters.py` and `tests/test_v151_validation.py`: 29 passed.

**2026-10-29 (plan step 3, Slack)**

The Slack test failed first for the right reason: `"slack"` was not yet a known format, so it fell back to the JSON payload. I added `_slack_payload`: a plain `text` summary, a section block with the function name, and a section block with gCO2 and duration as two fields. Two small choices the plan left open. The function name goes in a `plain_text` object, not `mrkdwn`, because Slack treats `_`, `*` and `<` in mrkdwn as formatting and Python function names are full of underscores. And the card shows only the three values the issue lists, not region or run_id, as the plan says.

A wrong turn: my first test insert went through the shell with the `\n` inside the Slack field text, and the shell turned it into a real line break, which made `tests/test_exporters.py` fail to import (a `SyntaxError` at collection). I caught it on the first run, and rewrote the edit as a script file so the backslash-n stayed two characters.

The plan said I would check the payload shapes against the platforms' docs while building, so I read them. Slack's section-block page confirms a section needs `text` or `fields`, up to 10 fields of up to 2000 characters, and that `{"text": ..., "blocks": [...]}` is a valid incoming-webhook body. It does not say the top-level `text` is a notification fallback, and it does not list which text types a section's `text` takes, so I have not confirmed those two points. Discord's pages confirm `embeds` is a content field of Execute Webhook, an embed `title` is up to 256 characters, `fields` up to 25 with name up to 256 and value up to 1024, and `color` is an integer (the page does not give the encoding; I used the usual decimal RGB value of 0x2ECC71 and 0xE74C3C). A very long function name could pass Discord's 256-character title limit; I did not handle that, because it is outside the issue. I still have not sent a card to a real Slack or Discord webhook. Commit 3. `tests/test_exporters.py` and `tests/test_v151_validation.py`: 30 passed.

**2026-10-29 (plan step 4, unknown format)**

The last test covers the fallback that commit 1 added: a `format="teams"` exporter logs `Invalid format='teams', defaulting to 'json'.` and sends the same six-key JSON payload as the default. I used "teams" as the unknown value because the repo's docs already say Teams is a webhook target, and the issue does not ask for it, so it is a realistic thing for a user to try. It passed on the first run, because the validation was already in commit 1; it fails on main for the same reason as the others, since main does not accept `format` at all. Commit 4, a test-only commit.

**2026-10-29 (plan step 5, checking the result)**

I re-ran `repro_194.py` on the branch. Before, `WebhookExporter.__init__`'s signature ended at `headers` and `format='json'`, `'discord'` and `'slack'` each raised `TypeError`; after, the signature ends `format: str = 'json'`, all three are accepted, and the control call with no `format` still sends the same six keys. A companion script that sends one measurement per format shows what is POSTed: the six JSON keys for the default and for `"json"`, only `embeds` for `"discord"`, and `blocks` and `text` for `"slack"`.

To show the tests fail without the fix, I checked out untouched main into a scratch worktree, copied in the new `tests/test_exporters.py`, and ran the webhook tests: 8 of the new tests failed, and 3 passed (the two existing webhook tests and the default-payload pin, which is meant to pass either way). The full suite on the branch, from a plain `cmd` with nothing activated, was 248 passed, 2 skipped in 228 s; main was 239 passed, 2 skipped, so the 9 new tests are the whole difference and I edited no existing test.

The plan did not change: the diff against main is `ecotrace/exporters/webhook.py` and `tests/test_exporters.py` and nothing else, so `plan.md` and the Implementation Plan above are still the text plan-check accepted. Things I know about for the PR description: `>=` as the red edge (the maintainer's words do not cover "equal"); 15 new `E501` line-length hits in the lines I added, at flake8's default 79 columns, which the repo already breaks 513 times with no flake8 config and which CI does not run (I left the lines as they are to match the surrounding code); and I have not sent a card to a real Slack or Discord webhook. Still to do before a PR: rebase onto origin/main (8 commits ahead, none touching these files) and re-run the suite.

**Toolkit**

- Tool: Claude Code, working from `plan.md`: tests first, then the code, one commit per plan step. I also used WebFetch to read Slack's and Discord's docs while building, as the plan says.
- What it produced: four commits (`1b7bf97`, `35f5ae1`, `de8b833`, `b25d3e2`), 145 lines added and 2 removed across `ecotrace/exporters/webhook.py` and `tests/test_exporters.py`, 9 new tests, and the dated entries above.
- Difficulties: a shell command turned the `\n` inside a Slack test string into a real line break, and `tests/test_exporters.py` stopped importing with a `SyntaxError`; I saw it on the next run. The clone's files are CRLF while git stores LF (`core.autocrlf` is on), so a plain edit risked mixed line endings. My first commit accepted and documented `"discord"` and `"slack"` before either was built. And cleaning up a scratch `git worktree` hit a permission lock, probably from OneDrive syncing the folder, and left a stray folder in `.git/worktrees`, which I removed by hand.
- What changed: I made the edits with script files and a small helper that keeps CRLF, so the diff stayed small. I cut commit 1 down to `"json"` only so each commit is true on its own. And I use `git archive`, not `git worktree`, for scratch copies of main.

---

## Pull Request

**PR Link:** https://github.com/Zwony/ecotrace/pull/229

**PR Description:** the title is `feat(exporters): add Slack and Discord payload formats to WebhookExporter`, and the description follows the repo's PR template. In short:

- *What it changes and why:* `WebhookExporter` takes an optional `format` argument. `"json"` is the default and sends the same payload as before; `"discord"` sends an embed, green below `carbon_limit` (or with none set) and red at or above it (the maintainer wrote red/orange when emissions exceed it, and the equal case is my choice); `"slack"` sends Block Kit blocks plus a `text` summary; an unknown format logs a warning and falls back to JSON. It changes only `ecotrace/exporters/webhook.py` and `tests/test_exporters.py` (4 commits, +145 -2), and it says `Fixes #194`.
- *Evidence:* my reproduction before (main: `TypeError: ... unexpected keyword argument 'format'` for all three formats) and after (all three accepted, and what is POSTed for each); the 9 new tests failing against main's code (8 failed, 3 passed); and the suite, 248 passed and 2 skipped against main's 239 passed and 2 skipped.
- *Limits, stated as facts:* a measurement exactly equal to `carbon_limit` is red (`>=`, matching core's budget check) and I ask whether the maintainer wants it strictly above; `docs/USAGE.md` is unchanged; Teams and function names past Discord's 256-character title limit are not handled; I have not sent a card to a real Slack or Discord webhook, or run anything but Python 3.14.3 on Windows. The PEP 8 checklist box is unticked with the count (flake8 745 on main, 760 here, the 15 new ones all line length), and so is the README box, with the reason.
- *AI use:* in my own words, because the repo states no AI policy: Claude Code helped read the code, draft the plan, write the tests and then the code, run the checks, and draft the description, and the commits carry a Co-Authored-By trailer. The description ends with the Claude Code attribution line.

**Maintainer Feedback:**

- 2026-10-29: none yet from the maintainer. I opened the PR the same evening. SonarCloud's bot commented that its Quality Gate passed with 0 new issues.

**Status:** Awaiting review

**Toolkit**

- Tool: the ship-gate: my `ship-manifest.md` (one repo check, `Tests`) and my pr-precheck, run by hand on my branch, plan, test evidence and description with `check_gate.py --branch --title ... --body-file pr_draft.md`, and then as the hook that guards `gh pr create` when Claude Code opened the PR.
- What it produced: four manual runs. The last one passed: `Tests` ok in 213 s, all ten pr-precheck checks passing, and no voice-guide rule broken. It graded real things: that the diff stays inside the plan's two files, that my test evidence shows the new tests failing on main (8 failed, 3 passed), and that my PEP 8 box was unticked with a count and not blank. The hook then honored that manual pass and the PR opened as #229 on `Zwony/ecotrace`, from my fork.
- Difficulties: run 1 stopped on a repo check: `tests/test_report.py::test_create_gpu_usage_chart_valid` failed with a Tcl/Tk error from matplotlib, intermittently, on untouched main too (6 of 8 runs; 8 of 8 passed with matplotlib's Agg backend). Run 2 stopped with "the claude CLI is not on PATH": `check_gate.py` starts `claude` by bare name, which cannot launch `claude.CMD` on Windows, the same bug I patched in the voice guard in unit 6. Run 3 passed and then crashed printing its summary into a redirected file (cp1252 cannot encode an arrow); the verdict was already logged. My first try at opening the PR stopped when the session asked to edit my description; I added the Claude Code attribution line, which voided the pass, so I ran the gate again (run 4). And my voice guard did not check the PR description: it crashed reading `pr_draft.md` (it reads with the Windows default encoding, which cannot decode the PR template's emoji), and a crashed hook is a non-blocking error, so the PR went out unchecked by the guard. Run by hand, the guard timed out at its own 80 s limit; its prompt with a longer limit returned pass in 69 s, and the ship-gate's pr-precheck had already found no voice-guide violation.
- What changed: in `ship-manifest.md` I changed the `Tests` row to run with `set MPLBACKEND=Agg&&` and wrote the reason in its Source cell, instead of excluding the check. I did not change my rubric, because no pr-precheck check asked for something this repo doesn't. After the PR opened I patched both tools: `check_gate.py` launches `shutil.which("claude")` and prints UTF-8, and `check_voice.py` reads files as UTF-8, stops a post if the check itself crashes (it used to let it through), and waits up to 180 s. A gate run with no workaround then passed. The first time the patched guard really read my description it stopped on one line, the colour rule, where I had written that red at or above the limit was what the maintainer confirmed; he wrote red when emissions exceed it, and the equal case is my choice. I reworded that line on the PR, and the guard passed the new text.

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
