# Plan for #72: `verify_password` raises `UnknownHashError` on a malformed stored hash

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72
Branch (on my fork): `fix/72-verify-password-malformed-hash`

## The ask

The issue asks that verification against a malformed stored hash "fail
closed (return `False`), not raise", and says the covering test is marked
`@pytest.mark.xfail` for manifest id H-05 and the marker should be removed
"as part of the fix". Relevant files named by the issue:
`core/security.py` and `tests/unit/test_security.py`.

## Repro evidence I am building on

From my reproduction report on the issue (posted 2026-09-27, `main` at
`2f4e82f`, Windows 11, Python 3.12.0, passlib 1.7.4, bcrypt 4.3.0,
pytest 9.1.1). Quoted, not paraphrased:

The test with the strict xfail marker ignored
(`pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --runxfail --tb=short`):

```
core\security.py:37: in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
...
.venv\Lib\site-packages\passlib\context.py:1132: in identify_record
    raise exc.UnknownHashError("hash could not be identified")
E   passlib.exc.UnknownHashError: hash could not be identified
======================== 1 failed, 1 warning in 0.95s =========================
```

As shipped, the same test without `--runxfail`: `1 xfailed, 1 warning in 2.85s`.

Direct calls in the same venv, with a real bcrypt hash as the control:

```
control, right password -> True
control, wrong password -> False
'not_a_valid_bcrypt_hash'   -> passlib.exc.UnknownHashError: hash could not be identified
''                          -> passlib.exc.UnknownHashError: hash could not be identified
'$2b$12$abc'                -> builtins.ValueError: salt too small (bcrypt requires exactly 22 chars)
UnknownHashError is a ValueError: True
```

Control test `test_verify_password_incorrect` (a wrong password against a
valid bcrypt hash) passes: `1 passed, 1 warning in 1.44s`.

My report's own conclusion: expected `verify_password()` to return `False`
when the stored value is not a usable bcrypt hash; actual, an exception
escapes from `core/security.py:37`; valid hashes behave correctly, so the
failure is specific to an unusable stored hash, not verification in
general. I also flagged there that the truncated bcrypt-shaped hash raises a
plain `ValueError`, not `UnknownHashError`.

## Diagnosis

`verify_password()` in `core/security.py` hands the stored value straight to
`pwd_context.verify()` (line 37) with no handling of a stored value that
passlib cannot use, so whatever passlib raises for that value reaches the
caller. The repro shows this directly: the traceback goes from
`core\security.py:37` into passlib's `identify_record`, which raises
`UnknownHashError`, and nothing in between catches it.

This is the cause, not just the symptom, because the repro shows the failure
is tied to the stored value and not to verifying passwords in general: the
right and wrong password against a real bcrypt hash return `True` and
`False` as they should. Two kinds of unusable value escape, and the fix
has to account for both: unrecognisable values (`"not_a_valid_bcrypt_hash"`
and `""`) raise `UnknownHashError`, and a truncated bcrypt-shaped value
(`$2b$12$abc`) raises a plain `ValueError` instead. The repro also shows
`UnknownHashError` is a `ValueError`.

One thing I am inferring, not observing: the caller in
`api/routes/auth.py:80` (`if not user or not verify_password(...)`) turns a
`False` into the 401 "Invalid email or password" response, so returning
`False` is the behavior the login route already expects. I read that from the
code; I have not run the login route.

## Scope

What will change (one bounded change):

- `core/security.py`: `verify_password()` returns `False` when passlib raises
  `ValueError` for the stored hash (this covers `UnknownHashError`, which the
  repro shows is a `ValueError`, and the truncated-hash `ValueError`).
- `tests/unit/test_security.py`: remove the strict `@pytest.mark.xfail`
  marker from `test_verify_with_wrong_hash_format` (the issue and
  `docs/CONTRIBUTING.md` both say to), and add one small test covering the
  two other inputs my repro found, the empty string and `$2b$12$abc`.

What will not change:

- `hash_password()`, the token functions, and anything else in
  `core/security.py`.
- `api/routes/auth.py` and the login flow. It already treats `False` as a
  failed login.
- Adding logging when a malformed hash is seen. It would be useful, but
  `core/security.py` has no logger today, so it is a separate change.
- Other xfail markers, the `pyproject.toml` baseline suppressions, and any
  lint or type cleanup, which `docs/CONTRIBUTING.md` says to keep out of a bug fix.
- Dependency versions (passlib, bcrypt).

## Files I will touch

1. `core/security.py`, the `verify_password()` function only (line 37).
2. `tests/unit/test_security.py`, around `test_verify_with_wrong_hash_format`
   (the xfail marker, lines 218-221) plus one new test next to it.

## Approach

1. Create `fix/72-verify-password-malformed-hash` from `main`
   (`docs/CONTRIBUTING.md` branch convention `<type>/<issue-number>-<description>`).
2. In `verify_password()`, wrap the `pwd_context.verify(...)` call in
   `try` and `except ValueError: return False`, keeping the existing
   `bool(...)` on the success path. Update the docstring to say a stored
   value that is not a usable hash returns `False`.
3. Delete the `@pytest.mark.xfail(...)` decorator from
   `test_verify_with_wrong_hash_format`. The marker is `strict=True`, so
   leaving it in would make CI fail with `XPASS(strict)` once the fix works.
4. Add one test next to it asserting `verify_password("password", stored)
   is False` for `""` and `"$2b$12$abc"`, so the two other escaping inputs
   from my repro stay covered.
5. Run the checks in the test plan below, then `ruff`, `black`, and `mypy`
   on the two files, as `docs/CONTRIBUTING.md` asks, and open the PR using the
   PR template.

## Test plan

I re-run my Unit 2 repro steps against the fix and compare with the outputs
quoted above. After the fix I expect:

1. `pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v`
   (no `--runxfail` needed now that the marker is gone): `1 passed`. Before the
   fix this printed `1 xfailed`, and `1 failed` with `UnknownHashError` under
   `--runxfail`.
2. The direct-call script from my report, unchanged: `control, right password
   -> True`, `control, wrong password -> False`, and `False` (not an exception)
   for each of `'not_a_valid_bcrypt_hash'`, `''` and `'$2b$12$abc'`.
3. The control `test_verify_password_incorrect` still passes, and the whole
   `tests/unit/test_security.py` file passes with no `XPASS(strict)` and no
   other test changing result. That shows valid hashes still verify as before.
4. My new test for the empty string and `$2b$12$abc` passes, and fails if I
   temporarily revert the `except`. That check shows the test can actually fail.

I will run these on the same Windows 11, Python 3.12.0 setup as my repro. CI
uses Python 3.11, so I will rely on CI for the 3.11 result.

## Risks and unknowns

- **Catching `ValueError` is broader than the issue strictly asks.** I chose
  it because my repro shows the truncated hash escapes as a plain `ValueError`
  and `UnknownHashError` is a subclass, so catching only `UnknownHashError`
  would leave `$2b$12$abc` still raising. I do not know whether a maintainer
  would rather catch only the two named exception types; I will say so in the
  PR and switch if asked.
- **A `ValueError` that is about the password, not the hash, would also be
  turned into `False`.** I have not shown whether passlib raises `ValueError`
  for any plain-password input with a valid hash (for example one with a NUL
  byte, or over 72 bytes). During the build I will try those against a valid
  hash. If one raises, `False` is still the safe answer for authentication,
  but it would mean my `except` hides a different case, and I will report it
  rather than leave it unmentioned.
- **I only reproduced on Windows, Python 3.12.0.** The earlier reports on the
  issue used 3.11 on macOS and Linux, and CI uses 3.11. I expect the same
  behavior but have not shown it on 3.11, so the CI run is my check.
- **I have not run the login route.** My claim that `api/routes/auth.py:80`
  returns the 401 for a `False` comes from reading the code, not from a run,
  so I will not claim any login-flow behavior in the PR.
- **The issue already has several classmates' plans and reports.** This plan
  is built from my own repro. If a maintainer says which exception to catch,
  I will follow that.

## Deviations

The plan held: nothing about the change itself moved. I edited the same two
files (`core/security.py` and `tests/unit/test_security.py`), made the same
`try`/`except ValueError: return False` change in `verify_password()`, removed
the strict xfail marker, and added the one small test for `""` and `$2b$12$abc`.
Nothing was added to or dropped from the scope, so the plan comment I posted is
still true.

Three things I found while building that the plan had left open or got slightly
different:

- **The password-side `ValueError` unknown is answered.** Against a valid hash,
  a password containing a NUL byte makes passlib raise `PasswordValueError`,
  which is a `ValueError`, so `verify_password()` now returns `False` for it
  where it used to raise. A 100-byte password and one with accented characters
  and an emoji did not raise and simply returned `False` against the wrong
  hash. I kept the `except ValueError`, because `False` is the safe answer for
  authentication, and I will say in the PR that the catch also covers that
  password-side case.
- **I ran on Python 3.14.5, not 3.12.0 as the plan said.** The bug reproduced
  identically there (same `UnknownHashError` and `ValueError` results as in my
  report). After the fix the target test and the two new cases pass, the whole
  `tests/unit/test_security.py` file passes (27 tests), and the unit suite shows
  378 passed and 52 xfailed with no strict-xfail failures. CI's Python 3.11
  result is still the one I will rely on CI for.
- **The commit scope is `api`,** because `docs/CONTRIBUTING.md` lists only
  `ingestion`, `rag`, `agent`, `safety`, `api` and `frontend` as scopes and the
  issue is labelled `api`. The plan did not say.

I also confirmed the new tests fail without the fix: with the change stashed,
`test_verify_with_wrong_hash_format` and both new cases fail with the original
exceptions. `ruff`, `black --check` and `mypy` are clean on the changed files.
