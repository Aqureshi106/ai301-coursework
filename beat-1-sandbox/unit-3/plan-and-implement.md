# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---


## Posted upstream

**GitHub username**

Aqureshi106

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5987082059

Text of the comment as posted:

```
Plan for #72, following from my reproduction report above (`main` at `2f4e82f`, Windows 11, Python 3.12.0).

**Cause.** `verify_password()` passes the stored value straight to `pwd_context.verify()` at `core/security.py:37`, with nothing handling a stored value passlib can't use. My report shows the traceback running from that line into passlib's `identify_record`, which raises `UnknownHashError`. A valid bcrypt hash verifies correctly (`True` for the right password, `False` for a wrong one), so the failure is specific to the stored value, not to verification in general.

**Change.** Only `verify_password()` in `core/security.py`: wrap the `verify` call and return `False` on `ValueError`. I'm proposing `ValueError` rather than only `UnknownHashError` because my report found that the truncated `$2b$12$abc` raises a plain `ValueError` ("salt too small"), and `UnknownHashError` is itself a `ValueError`. Catching only `UnknownHashError` would leave that input still raising.

**Tests.** In `tests/unit/test_security.py`, remove the strict `xfail` marker from `test_verify_with_wrong_hash_format`, and add one small test for the other two inputs my report found escaping (`""` and `$2b$12$abc`).

**Not touching.** `hash_password()`, the token code, `api/routes/auth.py`, logging, other xfail markers, or dependency versions.

**How I'll know it worked.** I'll re-run my report's steps: the test goes from `1 xfailed` (and `1 failed` with `--runxfail`) to `1 passed`; the direct calls for `'not_a_valid_bcrypt_hash'`, `''` and `'$2b$12$abc'` return `False` instead of raising; and the control tests for valid hashes still pass.

**What I'm unsure about.**
- Whether you'd rather catch only the two exception types than `ValueError`. I'll switch if so.
- Whether a `ValueError` caused by the password (not the hash) would also be turned into `False`. I haven't tested that yet and will try a NUL byte and an over-72-byte password against a valid hash during the build.
- I've only run this on Python 3.12 on Windows; CI's 3.11 run will be my check there.

Branch will be `fix/72-verify-password-malformed-hash` on my fork, and I'll open the PR from there. This plan is built from my own reproduction. If you'd like a different approach, tell me before I open it.

I used Claude Code to help draft this plan. I ran the reproduction steps myself, and I'll run the checks above myself.
```

---

## Your branch

**Branch**

fix/72-verify-password-malformed-hash

**Evidence**

My Unit 2 reproduction steps, re-run against the built change (fix commit `7c78b64` on the branch above, built on `main` at `2f4e82f`). The repro was never a stand-in script: it runs the project's own test and calls the real `core.security.verify_password()`, so the same steps run unchanged. Before and after below are the commands I ran and their output; each run prints which `core/security.py` it loaded.

#### Result at a glance

| Step | Before (`2f4e82f`) | After (`7c78b64`) |
|---|---|---|
| 1. test as shipped | `1 xfailed` | `1 passed` (the xfail marker is gone) |
| 2. same test, `--runxfail` | `1 failed`, `passlib.exc.UnknownHashError` | `1 passed` |
| 3. control: wrong password, valid hash | `1 passed` | `1 passed` |
| 4. `'not_a_valid_bcrypt_hash'` | raises `UnknownHashError` | `False` |
| 4. `''` | raises `UnknownHashError` | `False` |
| 4. `'$2b$12$abc'` | raises `ValueError: salt too small` | `False` |
| 4. valid hash, right / wrong password | `True` / `False` | `True` / `False` (unchanged) |

#### Before

##### As posted in Unit 2 (Windows 11, Python 3.12.0, `main` at `2f4e82f`)

From my reproduction comment on the issue
(https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5853044919):

```
# step 1 -> 1 xfailed, 1 warning in 2.85s

# step 2 (--runxfail)
core\security.py:37: in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
...
E   passlib.exc.UnknownHashError: hash could not be identified
======================== 1 failed, 1 warning in 0.95s =========================

# step 3 (control) -> 1 passed, 1 warning in 1.44s

# step 4 (direct calls)
control, right password -> True
control, wrong password -> False
'not_a_valid_bcrypt_hash'   -> passlib.exc.UnknownHashError: hash could not be identified
''                          -> passlib.exc.UnknownHashError: hash could not be identified
'$2b$12$abc'                -> builtins.ValueError: salt too small (bcrypt requires exactly 22 chars)
UnknownHashError is a ValueError: True
```

##### Re-run today in the same environment as the "after" (Python 3.14.5, clean checkout of `2f4e82f`)

```
# Python 3.14.5, commit 2f4e82f, no local changes
# core.security loaded from: <base checkout>/core/security.py

$ python -m pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --disable-warnings -p no:cacheprovider

tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format XFAIL [100%]

======================== 1 xfailed, 1 warning in 1.20s ========================

$ python -m pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --runxfail --tb=short --disable-warnings -p no:cacheprovider

tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format FAILED [100%]

================================== FAILURES ===================================
_______________ TestSecurity.test_verify_with_wrong_hash_format _______________
tests\unit\test_security.py:227: in test_verify_with_wrong_hash_format
    result = verify_password("password", wrong_hash)
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
core\security.py:37: in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
<venv>/Lib/site-packages/passlib\context.py:2343: in verify
    record = self._get_or_identify_record(hash, scheme, category)
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
<venv>/Lib/site-packages/passlib\context.py:2031: in _get_or_identify_record
    return self._identify_record(hash, category)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
<venv>/Lib/site-packages/passlib\context.py:1132: in identify_record
    raise exc.UnknownHashError("hash could not be identified")
E   passlib.exc.UnknownHashError: hash could not be identified
=========================== short test summary info ===========================
FAILED tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format
======================== 1 failed, 1 warning in 1.16s =========================

$ python -m pytest tests/unit/test_security.py::TestSecurity::test_verify_password_incorrect -q --disable-warnings -p no:cacheprovider
.                                                                        [100%]
1 passed, 1 warning in 1.62s

$ python direct.py   # direct calls into core.security
control, right password -> True
control, wrong password -> False
'not_a_valid_bcrypt_hash'   -> passlib.exc.UnknownHashError: hash could not be identified
''                          -> passlib.exc.UnknownHashError: hash could not be identified
'$2b$12$abc'                -> builtins.ValueError: salt too small (bcrypt requires exactly 22 chars)
UnknownHashError is a ValueError: True
core.security loaded from: <base checkout>/core/security.py
```

#### After

##### Python 3.14.5 (fix branch, commit `7c78b64`)

```
# Python 3.14.5, commit 7c78b64, no local changes
# core.security loaded from: <fork clone>/core/security.py

$ python -m pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --disable-warnings -p no:cacheprovider

tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format PASSED [100%]

======================== 1 passed, 1 warning in 0.82s =========================

$ python -m pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --runxfail --tb=short --disable-warnings -p no:cacheprovider

tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format PASSED [100%]

======================== 1 passed, 1 warning in 0.98s =========================

$ python -m pytest tests/unit/test_security.py::TestSecurity::test_verify_password_incorrect -q --disable-warnings -p no:cacheprovider
.                                                                        [100%]
1 passed, 1 warning in 1.64s

$ python direct.py   # direct calls into core.security
control, right password -> True
control, wrong password -> False
'not_a_valid_bcrypt_hash'   -> False
''                          -> False
'$2b$12$abc'                -> False
UnknownHashError is a ValueError: True
core.security loaded from: <fork clone>/core/security.py
```

##### Python 3.12.0 (the venv from my Unit 2 run, fix branch, commit `7c78b64`)

```
# Python 3.12.0, commit 7c78b64, no local changes
# core.security loaded from: <fork clone>/core/security.py

$ python -m pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --disable-warnings -p no:cacheprovider

tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format PASSED [100%]

======================== 1 passed, 1 warning in 1.31s =========================

$ python -m pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --runxfail --tb=short --disable-warnings -p no:cacheprovider

tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format PASSED [100%]

======================== 1 passed, 1 warning in 0.87s =========================

$ python -m pytest tests/unit/test_security.py::TestSecurity::test_verify_password_incorrect -q --disable-warnings -p no:cacheprovider
.                                                                        [100%]
1 passed, 1 warning in 1.64s

$ python direct.py   # direct calls into core.security
control, right password -> True
control, wrong password -> False
'not_a_valid_bcrypt_hash'   -> False
''                          -> False
'$2b$12$abc'                -> False
UnknownHashError is a ValueError: True
core.security loaded from: <fork clone>/core/security.py
```

#### Notes

- Step 1 after the fix prints `1 passed`, not `1 xfailed`, because the fix removes the
  strict xfail marker (the issue and `docs/CONTRIBUTING.md` ask for that). With the marker
  left in, the strict xfail would fail with `XPASS(strict)`.
- `direct.py` is the Unit 2 step 4 script, unchanged, plus one added last line that
  prints which `core/security.py` it loaded. It is run with `PYTHONPATH` set to the
  checkout so it cannot import the other one. My first "before" run of it imported the
  fixed code by mistake, through the editable install, and printed `False`; I discarded
  that output and the one above replaces it.
- Separate from the repro: with the fix, a password containing a NUL byte against a valid
  hash also returns `False`, because passlib raises `PasswordValueError` (a `ValueError`)
  for it. This is noted under Deviations in `plan.md`.

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

In order:

1. The first launch of the full run crashed before grading anything (`FileNotFoundError`: the harness could not start `claude`, which is `claude.cmd` on Windows). No score. I launched the harness through a small wrapper that resolves the executable, without editing `run_eval.py`.
2. Smoke run, `--only pkg-01`: `agreement: 1/1 scored items`.
3. First full run: `agreement: 18/19 scored items`. One item errored rather than grading (`pkg-02`), so the run scored 19, and the harness refused to write `eval-run.txt` ("partial run"). One scored package disagreed: `pkg-14` (gold `accept`, my rubric `reject`, failed `claims-match-evidence`).
4. After narrowing `claims-match-evidence`, `--only pkg-14,pkg-02,pkg-01,pkg-11,pkg-15`: `agreement: 4/4 scored items`. `pkg-14` flipped to `accept` and agreed; the canaries `pkg-01` and `pkg-11` (category `wrong-cause`) and `pkg-15` (`scope-creep`) held at `reject`. `pkg-02` errored again.
5. `pkg-02` errored the same way each time: the harness's `text=True` encodes the prompt in the Windows code page, so `claude` received no input. Forcing UTF-8 in the wrapper fixed it. `--only pkg-02`: `agreement: 1/1 scored items`.
6. Confirming full run, with `--save-run eval-run.txt`: `agreement: 18/20 scored items  (bar: 18/20: PASS)`, with every category matched: `clear-accept 6/7  scope-creep 4/4  thread-convention 1/2  unbuildable 3/3  wrong-cause 4/4`. Two packages disagree in that run: `pkg-14` (gold `accept`, graded `reject`) and `pkg-20` (gold `reject`, graded `accept`).

The last score matches the agreement line in the committed `eval-run.txt`.

**Package analysis**

`pkg-14` (source: Zellij 0.44.2, "OSC color sequences leak into terminal on session reattach via SSH", category `clear-accept`). The gold label is `accept`; my rubric decided `reject` in the saved run, failing only `claims-match-evidence`. This is one of the two packages I still disagree on.

What the package shows: the repro evidence has the control runs "Same host and terminal on zellij 0.44.1: 5 reattach cycles, no leak." and "the leak appeared on every re-attach, never on a fresh session create." The plan's cause is grounded in exactly those: its diagnosis ends "Grounded in the repro: fresh attach (which performs the same queries behind the loading screen) is clean, the leak starts exactly at reattach, and 0.44.1, which predates the reattach-path change in 0.44.2, is clean on the same setup." The other six checks passed.

Why my rubric read it as a reject: the grader's evidence line for the failed check was "Plan states as fact that 0.44.1 'predates the reattach-path change in 0.44.2' and that fresh attach 'performs the same queries behind the loading screen' — neither claim is backed by any observation in the package (no changelog/diff/trace shown), and unlike the main mechanism claim, neither is framed as a hypothesis the plan's first step will confirm." That is my check doing what it says: the observations (fresh attach clean, 0.44.1 clean) support the pattern, but the two reasons the plan gives for the pattern are asserted flat. I think the gold label is right that the plan is ready, because the diagnosis stands on the observations, and the reasons are background the plan says it will trace. So this is a real gap in my check, not a grader accident: it still lets a stray explanatory clause fail an otherwise sound plan. It agreed in my partial re-run after I narrowed the check and reverted in the full run, which tells me the wording is not firm enough for this case.

**Check rationale**

From the `rubric.md` I uploaded to `tools/plan-check/`:

| claims-match-evidence | The plan's and the plan comment's assertions of cause and effect (for example "the root cause is...", "X is a red herring", "this fixes it"), checked against the repro evidence and the thread highlights. | Pass if the claim the plan rests on (its stated cause, or its dismissal of a reported detail) is consistent with everything the repro evidence and thread show, and is either backed by an observation in the package or framed as the author's explanation that the plan's own first step will confirm (for example by tracing or reading the code). An account of code internals the package cannot show is a hypothesis, not a fail, when it fits every observation and nothing contradicts it. Fail if the plan states a cause as established fact with no observation in the package that supports it, if it dismisses or explains away a detail that the thread or repro evidence reports (for example another user's results that differ), or if it contradicts what the package shows. | required |

It reads this way because of `pkg-14`. My first version was: "Pass if every assertion the plan rests on is backed by something in the repro evidence or thread, or is stated as a hypothesis with what will confirm it. Supporting claims may stand in prose when the plan does not depend on them. Fail if a cause, or the dismissal of a reported detail, is stated as established fact with nothing in the package behind it, or if it contradicts what the thread reports (for example another user's results)." On the first full run that failed `pkg-14` because the grader read the plan's account of code internals (what the client does on reattach) as fact. I rewrote it around "the claim the plan rests on" (its cause, or its dismissal of a reported detail) and said outright that an account of code internals the package cannot show is a hypothesis, not a fail, when it fits every observation and nothing contradicts it. I kept the two fail conditions that carry the check's value: a cause stated as fact with nothing supporting it, and a dismissal of a detail the thread reports, such as `pkg-01` calling the Python-version difference "a red herring" while a thread comment reports different results per Python version.

What I rejected: demoting the check to `preferred`. I did not test that, and the first run shows why it would be tempting: nine of the ten packages it failed also failed another required check, so it rarely decides a verdict by itself in this set, and `pkg-14` was the one exception. I kept it required anyway because it is the only check that reads the plan's claims against the thread, which is the live case on a crowded issue.

**Trade-offs**

The narrowing changed one package and I checked what else it could touch before trusting it: `pkg-14` went from `reject` to `accept` in the partial re-run, and I re-ran canaries `pkg-01`, `pkg-11` and `pkg-15` (all gold `reject`, all still `reject`) with `--only` for $0.20 a package instead of on a full run.

What the looser check gives up: a plan can now state an unsupported mechanism confidently and pass, as long as the claim it rests on is supported and nothing in the package contradicts it. I accept that, because the alternative, failing every plan with an unhedged explanatory clause, rejected a package the gold label accepts.

What I did not fix: the final full run flipped `pkg-14` back to `reject` and `pkg-20` (category `thread-convention`, gold `reject`) to `accept`, although `pkg-20` agreed in the first full run, so the grader is not fully deterministic on these two. The saved run is at the bar with no margin, and `thread-convention` holds its floor only through `pkg-04` (1/2). I stopped there because the run met 18/20 with every category matched, and re-running to get a better draw would have replaced the file I am submitting.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
