# Toolkit Portfolio

Path: `beat-3-portfolio/toolkit-portfolio.md`

This file is Assignment 6, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. It has been waiting here since you created the repo in unit 1;
unit 9 is when you write it.

**Only this page is read.** Your `tools/` folders and your installed skill folders are never
opened, so every piece of evidence you want seen has to live inline here: an output excerpt,
a before/after pair, a dated commit reference.

**Write every sentence yourself.** AI may gather diffs, dates and excerpts for you; it does
not draft entries.

**Keep every section header below under its exact name** — Featured Tools, Inventory, The
Arc, and Limitations and What's Next. Feature three or four tools, each in the entry shape
shown below; give every other tool one inventory line.

**Some lines below are labelled, and the label is how they are read.** Three places on this
page use a `Label: your words` shape — the arc's beats, the tool-limits list, and nothing
else. Keep those labels exactly as written, one per line, and never delete a labelled line:
where you have nothing to put after a label, write `none` and move on. A deleted label reads
as a deleted answer.

**Before you finish**, replace every `[bracketed prompt]` with your own words, then delete
the unused `[Tool name]` entry stub, its copy-me sentence, and the prompt sentences under
each header. A leftover bracketed prompt reads as unfilled.

**Student:** Ashir Qureshi

## Featured Tools

I chose these four tools because each one visibly changed through real use between 2026-09-26 and 2026-11-05, and each has a dated before-and-after record below. Together they cover the workflow from finding an open issue and verifying a reproduction to gating a pull request before it opens and monitoring it afterward. My technical write-up already covers the voice guard in detail, so I placed the voice guard in the inventory rather than repeating its story here.

**issue-scout**

**What it is, and the judgment hole I wrote:** issue-scout searches GitHub for candidate issues and grades whether each one is open, unclaimed, and ready for a contribution. The judgment hole I wrote is rubric.md and search-strategy.md—specifically my no_active_claim check and -linked:pr query filter (the scope-feasible row and difficulty note come from the course's rubric-scope-patch.md, not from me). Together, those files encode my judgment on where to search for viable issues and what counts as an active claim or disqualifying blocker before I start work.

**How it evolved, dated:** My initial no_active_claim check in rubric.md required "no open linked PR" but did not say where to read linked pull requests in live mode, so the scout checked only the two easy fields and passed issues whenever assignees and comments were empty. On 2026-10-18, it accepted anthropics/claude-agent-sdk-python#1409 on "assignees: [], comments: []" while PR #1410—opened by the issue's own author two seconds after the issue—was already open. That same day, I rewrote no_active_claim to require checking closedByPullRequestsReferences in gh issue view and timeline cross-references, set any grade whose evidence quotes only assignees and comments to unclear, and added -linked:pr to my queries in search-strategy.md. On 2026-10-22, re-grading the earlier candidates failed three issues that had previously passed (repowise#4092, repowise#4091, and Sentient#459). Two open limits remain: the language filter reads only the repository's primary language, and all five graded issues came from a single query.

**Evidence:** The excerpt below shows scan 1 on 2026-10-18 versus the re-grade on 2026-10-22 on the same two issues. Naming the exact live fields turned false passes into concrete failures backed by linked PR numbers, merge timestamps, and claim comments, preventing wasted work on issues someone else had already claimed or closed.

```
https://github.com/repowise-dev/repowise/issues/4092
  2026-10-18, scan 1 (line 24):  {"name": "no_active_claim", "grade": "pass", "evidence": "assignees: [], comments: []"},
  2026-10-22, re-grade (line 115): {"name": "no_active_claim", "grade": "fail", "evidence": "1 linked PR (#4096), state=open, merged=false, opened 2026-10-18 by aazminu; plus unanswered claim comment from parth33320 on 2026-10-18"},
```

```
https://github.com/repowise-dev/repowise/issues/4091
  2026-10-18, scan 1 (line 35):  {"name": "no_active_claim", "grade": "pass", "evidence": "assignees: [], comments: []"},
  2026-10-22, re-grade (line 126): {"name": "no_active_claim", "grade": "fail", "evidence": "issue state=CLOSED; 1 linked PR (#4099) merged 2026-10-19T07:08:43Z closes this issue; also had a claim comment from dhvani7014 on 2026-10-18"},
```

**repro-check**

**What it is, and the judgment hole I wrote:** repro-check evaluates bug-reproduction packages to verify that their claims rest on shown artifacts, followable commands, and appropriate scope. The judgment hole I wrote is the Unit 2 grading rubric (rubric.md) and the real-repository scope.md I added on 2026-10-22. Those files encode my judgment on what evidence a reproduction package must show to be accepted and when a package must fail for missing evidence, unfollowable communication, wrong target, or missing disclosure.

**How it evolved, dated:** My initial rubric.md required an artifact for every single assertion in a package, which rejected valid packages whenever the author mentioned a secondary corroborating detail in prose without attaching a separate log (pkg-03 mentioned running ripgrep without -r, and pkg-09 mentioned running a cannot-reproduce attempt several times). On 2026-09-26, my first full evaluation run scored 14/17: three packages (pkg-04, pkg-07, pkg-11) errored before grading and three gold-accept packages (pkg-03, pkg-05, pkg-09) were rejected. That same day, I revised two rubric checks so unshown corroborating mentions do not fail a package whose primary claim is backed by an artifact, and the three errors were traced to a Windows cp1252 subprocess encoding failure on emoji and CJK characters; forcing UTF-8 on the subprocess brought the confirming full run to 20/20 against an 18/20 bar. On 2026-10-22, I added scope.md for live repository work, and in Unit 6 live use the tool failed my initial claim over the phrase "within a week" before passing the reworded claim.

**Evidence:** The run history excerpt from reproduction.md below traces the four runs on 2026-09-26 from 14/17 to 20/20. Revising the two overly strict checks flipped all three gold-accept targets (pkg-03, pkg-05, and pkg-09) while the three canaries (pkg-15, pkg-18, and pkg-20) held, and fixing the Windows UTF-8 subprocess encoding resolved the three errored bundles to reach 20/20.

```
1. Full run, first attempt: `agreement: 14/17 scored items`. Three packages (`pkg-04`,
   `pkg-07`, `pkg-11`) errored rather than grading, so the run scored only 17. Three scored
   packages disagreed: `pkg-03`, `pkg-05`, and `pkg-09`, all gold `accept` and all rejected
   by my rubric.
[...]
4. Confirming full run, with `--save-run`: `agreement: 20/20 scored items  (bar: 18/20: PASS)`,
   with every category matched — `clear-accept 8/8  disclosure 1/1  no-evidence 4/4
   unfollowable-comms 3/3  wrong-target 4/4`.
```

**ship-gate and my ship manifest**

**What it is, and the judgment hole I wrote:** ship-gate is a pre-ship hook and runner from the original install that blocks pull request creation until the repository checks in the manifest and pr-precheck pass. The judgment hole I wrote is ship-manifest.md (kept local and out of the pull request), which defines one required check (Tests) and one dated exclusion (Lint). It encodes my judgment on what must pass before shipping a change to Zwony/ecotrace and why flake8—which is not run in CI and already reports 745 violations on untouched main—should not block a webhook exporter pull request.

**How it evolved, dated:** My initial manifest ran the repository's plain pytest command without accounting for matplotlib's default Tk backend failing on Windows. On 2026-10-29, the gate returned BLOCK at 21:58 when a flaky GPU-chart test (test_create_gpu_usage_chart_valid) failed under Tk with invalid command name "tcl_findLibrary" (1 failed, 247 passed) and BLOCK again at 22:07 because claude was not on PATH for pr-precheck. The Tests row in ship-manifest.md gained set MPLBACKEND=Agg&& with the explanation recorded in its Source cell and the PATH issue was resolved, producing PASS runs at 22:14 and 22:53 that the hook honored at 22:54 when PR #229 opened. After the pull request opened, the gate script was also patched for the launch and UTF-8 handling; however, because the manifest runs only Tests, new lint hits remain invisible (flake8 reports 745 violations on main and 760 on my branch).

**Evidence:** The run table from gate-log.md and the two rows from ship-manifest.md below show the progression from blocked runs at 21:58 and 22:07 to verified passes at 22:14 and 22:53. They show that the gate stopped my pull request until both the test environment and precheck runner worked, and that ship-manifest.md records the rationale for adding MPLBACKEND=Agg to Tests and the dated reason for excluding Lint.

`gate-log.md`, every run:

| local time | mode | verdict | Tests check |
|---|---|---|---|
| 2026-10-29 20:45 | hook | BLOCK (setup) | none ran |
| 2026-10-29 20:46 | hook | BLOCK (setup) | none ran |
| 2026-10-29 21:58 | manual | BLOCK (block) | FAILED (exit 1, 204s) |
| 2026-10-29 22:07 | manual | BLOCK (block) | ok (214s) |
| 2026-10-29 22:14 | manual | PASS | ok (208s) |
| 2026-10-29 22:53 | manual | PASS | ok (213s) |
| 2026-10-29 22:54 | hook | PASS (honored the manual pass at 2026-10-29 22:53) | none ran |
| 2026-10-29 23:40 | manual | PASS | ok (215s) |

`gate-log.md`, the 21:58 entry (selected lines):

```
## 2026-10-29 21:58 [manual] Zwony/ecotrace @ feature/194-webhook-format (base main)
- verdict: BLOCK (block)
  - Tests: `.venv\Scripts\python.exe -m pytest tests/ -q` -> FAILED (exit 1, 204s)
    [EcoTrace] ERROR: [ERROR] GPU utilization chart generation failure: invalid command name "tcl_findLibrary"
    ERROR    ecotrace:report.py:94 [ERROR] GPU utilization chart generation failure: invalid command name "tcl_findLibrary"
    FAILED tests/test_report.py::test_create_gpu_usage_chart_valid - assert None ...
    1 failed, 247 passed, 2 skipped in 202.60s (0:03:22)
```

`ship-manifest.md`, the Tests row and the Lint exclusion:

```
| Check | Command | Source |
|---|---|---|
| Tests | `set MPLBACKEND=Agg&& .venv\Scripts\python.exe -m pytest tests/ -q` | CONTRIBUTING.MD (Development Setup: `pytest`), .github/workflows/ci.yml (`python -m pytest tests/`), and the Build and test commands in CLAUDE.local.md. I added `set MPLBACKEND=Agg&&` (matplotlib's non-GUI backend, which a headless runner like CI's ubuntu one uses): on this Windows machine matplotlib's default Tk backend fails at startup in most runs of `tests/test_report.py::test_create_gpu_usage_chart_valid`, on untouched main too (`invalid command name "tcl_findLibrary"`), and passes 8 of 8 runs with Agg. |

| Check | Command | Date | Why it doesn't count against this PR |
|---|---|---|---|
| Lint | `flake8 ecotrace/` | 2026-10-29 | CONTRIBUTING.MD names it, but CI doesn't run it and the repo has no flake8 config. On untouched main (413685b) it already reports 745 violations across the package, mostly E501 line length and W293 whitespace, so a fix to the webhook exporter can't make it pass. flake8 isn't installed in the project's .venv either. |
```

**PR monitor and my playbook**

**What it is, and the judgment hole I wrote:** The PR monitor is a script that checks the state of an open or recently reviewed pull request against a repository playbook and drafts any needed follow-up. The judgment hole I wrote is playbook.md and its staleness thresholds, which encode my judgment on how long to wait for a maintainer's first review or follow-up reply in a given repository before nudging, and how to respond when review activity occurs.

**How it evolved, dated:** Until 2026-11-05, playbook.md still held unmeasured 14-day and 7-day thresholds written for an earlier repository (pathreview) that had zero merged pull requests. On 2026-11-05, I counted five recent merged pull requests from outside contributors in Zwony/ecotrace (#233, #228, #220, #219, and #218, which waited 0.9, 0.1, 7.1, 9.3, and 0.4 hours for the maintainer's first reply) and rebuilt playbook.md with measured thresholds of 4 days and 3 days; the monitor script was also patched from its pre-patch state for launch and UTF-8 encoding. When I ran the updated monitor on my merged PR #229, it drafted a thank-you comment that included a claim the maintainer's review did not make, so I did not send it.

**Evidence:** The before-and-after playbook.md excerpt below shows how the staleness rules moved from an unmeasured two-week threshold on a zero-merge repository to empirical thresholds based on five outside pull requests in ecotrace. Because the longest observed wait among those five pull requests was 9.3 hours, a 4-day threshold is roughly ten times the usual wait, reserving a nudge for a pull request that was likely missed rather than merely slow.

```markdown
BEFORE (playbook.pathreview-161.bak.md):
## Staleness thresholds

[...]
- I couldn't count five merged PRs. On 2026-11-05 the repo had 202 PRs
  and 0 merged. No maintainer has commented or reviewed on any of
  them; the only first replies came from a Copilot review bot (same
  day) and one outside reviewer (under a day). 180 open PRs have no
  reply, median age 23 days, oldest 42.
- So the number is chosen, not measured: nudge after 14 quiet days on
  a first review, 7 on a stalled one (a maintainer replied, then went
  quiet). 14 is far past every reply this repo has shown, and still
  under the 23 days the unanswered PRs already sit at. Recount if a
  maintainer ever replies to anyone.

AFTER (playbook.md):
## Staleness thresholds

[...]
- On 2026-11-05 I counted five recent merged PRs from outside
  contributors (skipping Zwony's own and Dependabot's): #233, #228,
  #220, #219 and #218 waited 0.9, 0.1, 7.1, 9.3 and 0.4 hours for the
  maintainer's first reply, which was also the merge each time. My own
  #229 waited 13.6 hours.
- So nudge after 4 quiet days on a first review, and after 3 on a
  stalled one (a maintainer replied, then went quiet). The longest wait
  was 9.3 hours, so 4 days is about ten times the usual, which means a
  PR here that quiet was probably missed, not just slow.
```

## Inventory

- plan-check: My Unit 3 skill for grading implementation plans against codebase evidence; it reached a saved run of 18/20 on 2026-10-04 (where pkg-14 and pkg-20 flipped in the final run and I stopped at the bar) and accepted my plan for ecotrace issue #194 on its first live grading.
- pr-precheck: My Unit 4 skill that grades pull request readiness across ten checks; after one rubric edit made before the first scored run, it scored 20/20 on 2026-10-12 and passed all ten checks inside ship-gate on 2026-10-29.
- voice guard and voice guide: My outgoing draft checker where the rules in the voice guide are mine and Claude Code made the 2026-10-29 fail-closed and UTF-8 patch after my approval; the hook's command list covers comment and create for issues and pull requests, but not edit (see the technical write-up).
- CLAUDE.local.md: The repository-memory file I built in Unit 6 for ecotrace to record working environment and test commands; it now warns not to copy CONTRIBUTING.MD's install line, which pyproject.toml cannot satisfy.

## The Arc

**Beat 1**
Date: 2026-09-26
Friction: repro-check's first full run scored 14/17 because three packages errored on Windows cp1252 encoding and three gold-accept packages (pkg-03, pkg-05, and pkg-09) were rejected by overly strict check wording.
Change: Two rubric checks were revised so unshown corroborating details in prose do not fail a package whose core claim has an artifact, and the three errored packages were rerun after forcing UTF-8 on the subprocess.
Result: The confirming full run scored 20/20 against the 18/20 bar, matching every category.

**Beat 2**
Date: 2026-10-04
Friction: plan-check's first launch crashed because claude.cmd was not found, and the rubric rejected pkg-14, a gold-accept package.
Change: The claims-match-evidence check was narrowed and the harness was launched through a wrapper that resolves the executable and forces UTF-8.
Result: The saved run scored 18/20 to meet the bar, though pkg-14 and pkg-20 flipped in the final run and I stopped there without fixing that flip.

**Beat 3**
Date: 2026-10-12
Friction: Calibration on calib-01 showed that pr-precheck's first rubric would have failed a ready pull request for not running a command nobody had asked for.
Change: Before the first scored run, I rewrote the test-evidence check rather than deleting it, because the check was still needed to fail pkg-07, pkg-10, and pkg-14.
Result: The full scored run hit 20/20 with no rubric revision needed after a scored run.

**Beat 4**
Date: 2026-10-18
Friction: issue-scout accepted #1409 on "assignees: [], comments: []" while PR #1410 was already open, created by the issue's own author two seconds after the issue.
Change: I rewrote no_active_claim to name the live linked-PR sources and treat a grade quoting only assignees and comments as unclear, and I added -linked:pr to all four search queries.
Result: On 2026-10-22, the re-grade failed three earlier passes (#4092, #4091, and Sentient #459), citing the specific linked PR, merge, or assignee/claim that disqualified each one.

**Beat 5**
Date: 2026-10-29
Friction: ship-gate returned BLOCK at 21:58 on a flaky GPU-chart test under matplotlib's Tk backend and BLOCK again at 22:07 because claude was not on PATH.
Change: The Tests row in ship-manifest.md gained set MPLBACKEND=Agg&& with the reason documented in its Source cell, and the launch problem was resolved.
Result: Manual runs passed at 22:14 and 22:53, the hook honored the 22:53 pass at 22:54, and PR #229 opened.

**Beat 6**
Date: 2026-10-29
Friction: At 22:55, the voice guard crashed on my real pull request description due to a Windows cp1252 error on byte 0x90 at position 278, exiting with code 1, which Claude Code lets through.
Change: Claude Code proposed the fail-closed and UTF-8 patch at 23:14 and made it after my "Go ahead" at 23:27, then edited the flagged description sentence via gh pr edit after my "Go on" at 00:06.
Result: The patched guard stopped the real description at 23:31 on the colour-rule sentence and passed the edit at 00:08; the pull request was approved and merged on 2026-10-30, and the maintainer's review does not mention the guard (see the technical write-up).

**Beat 7**
Date: 2026-11-05
Friction: The PR monitor's playbook still held pathreview's chosen staleness thresholds of 14 and 7 days from a repository with zero merged pull requests.
Change: I rebuilt playbook.md from five counted ecotrace pull requests to set thresholds of 4 and 3 days, and the monitor's launch and UTF-8 encoding were patched.
Result: The run on #229 drafted a thank-you comment containing a claim the maintainer's review does not make, so I did not send it.

## Limitations and What's Next

As a whole, the toolkit still has three gaps: the outgoing hook covers create and comment commands (gh pr create, gh pr comment, gh issue create, and gh issue comment) but not edits (gh pr edit and gh issue edit), its single-call LLM checks can flip verdicts across repeated runs on identical text or invent unsupported details when drafting replies, and its pre-ship gate checks only full-repo commands rather than diff-scoped lint on changed lines. Next, I would add ("pr", "edit") and ("issue", "edit") to the voice guard's command list, require multiple passes or deterministic pre-checks before trusting a single model call, add a diff-only flake8 check to ship-manifest.md so new line-length violations on touched files are caught even when legacy files fail, and tighten the PR monitor's drafting rules so it cannot attribute unstated claims to a reviewer.

issue-scout: The language filter matches only a repository's primary language rather than the fix itself, same-day zero-comment batches can fill the top five slots from a single query, rejecting an umbrella issue remains untested, and the Unit 1 harness was not re-run on the patched rubric.
repro-check: The rubric is written around error text and exit codes rather than requiring a feature request's artifact to show the target behavior absent, and because my live package passed on its first run after rewording one phrase, what the rubric catches in a weaker live report is still unknown.
ship-gate and my ship manifest: The manifest runs only one required check (Tests), leaving the gate blind to the 15 new flake8 violations on my branch (760 vs. 745 on main), and my notes cite the flaky GPU test failing "6 of 8" runs in prose versus "passes 8 of 8 runs with Agg" in the manifest with no saved run log for those counts.
PR monitor and my playbook: The staleness thresholds are derived from only five merged pull requests in a single repository, and the monitor's drafted thank-you move for #229 contained a claim the maintainer's review does not make.
