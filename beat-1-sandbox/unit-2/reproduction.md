# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

Aqureshi106

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5853044222

I'd like to take #72 as my first Path Review contribution. A couple of classmates have claimed it as well; following the course house rules I'm doing my own setup and posting my own report rather than piggybacking on theirs.

What the issue describes: `verify_password()` in `core/security.py` passes the stored hash straight to `pwd_context.verify()`, so when the stored value is not a recognizable hash, passlib's `UnknownHashError` escapes instead of the function returning `False`. The covering test, `tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format`, carries a strict `xfail` marker for manifest id H-05. I have not run anything yet.

What I'll do next:

1. Fork the repo and set it up following `docs/SETUP.md`, recording my OS and my Python, passlib, and bcrypt versions along with the commit I test.
2. Run the H-05 test with `--runxfail` so the real exception is visible, alongside a valid bcrypt hash as a control, so I can tell a malformed-hash failure from a verification failure in general.
3. Call `verify_password()` directly with the test's `"not_a_valid_bcrypt_hash"` and with an empty string, to see whether both raise the same exception or different ones.

I'll post the commands and the output here whether it reproduces or not, and I'll say which environment the result came from. This is my first contribution to this repo, so if I have misread where this behavior belongs, I'd welcome a pointer before I take it further.

I used Claude Code to help draft this comment; the steps above are mine to run.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5853044919

Reproduction report for #72. **Result: reproduced** on `main` at commit `2f4e82f`. `verify_password()` raises `passlib.exc.UnknownHashError` for the test's malformed stored hash instead of returning `False`. Earlier reports on this issue were from macOS; this one adds Windows and Python 3.12.

**Environment**

- Windows 11 (10.0.26200, x64)
- Python 3.12.0 in a fresh `.venv`. Note: CI and the earlier reports here use Python 3.11; 3.12 is the closest version I had installed, and it is inside the project's `requires-python = ">=3.11"`.
- passlib 1.7.4, bcrypt 4.3.0, pytest 9.1.1, installed with `pip install -e ".[dev]"`
- Code: commit `2f4e82f52efbcfcc57d65b3fa5348672163ca088`, my fork of `main`, no local changes
- Setup followed `docs/SETUP.md` for the Python parts only: `cp .env.example .env`, venv, `pip install -e ".[dev]"`. I did not run `docker compose up -d` or `make setup`, so no Postgres, Redis, or migrations were involved — this test imports `core.security` and touches no service. Flagging that in case it matters to anyone comparing results.

**Steps**

```bash
git clone https://github.com/Aqureshi106/pathreview-ai301-fa26-s3.git
cd pathreview-ai301-fa26-s3 && git checkout 2f4e82f
cp .env.example .env
py -3.12 -m venv .venv && ./.venv/Scripts/python -m pip install -e ".[dev]"

# 1. As shipped, the strict xfail hides the error
./.venv/Scripts/python -m pytest \
  tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format \
  -v --disable-warnings
# -> 1 xfailed, 1 warning in 2.85s

# 2. The same test with the xfail marker ignored
./.venv/Scripts/python -m pytest \
  tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format \
  -v --runxfail --tb=short --disable-warnings
```

Output of step 2 (session header and one unrelated deprecation warning trimmed):

```
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format FAILED [100%]

================================== FAILURES ===================================
_______________ TestSecurity.test_verify_with_wrong_hash_format _______________
tests\unit\test_security.py:227: in test_verify_with_wrong_hash_format
    result = verify_password("password", wrong_hash)
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
core\security.py:37: in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.venv\Lib\site-packages\passlib\context.py:2343: in verify
    record = self._get_or_identify_record(hash, scheme, category)
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.venv\Lib\site-packages\passlib\context.py:2031: in _get_or_identify_record
    return self._identify_record(hash, category)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.venv\Lib\site-packages\passlib\context.py:1132: in identify_record
    raise exc.UnknownHashError("hash could not be identified")
E   passlib.exc.UnknownHashError: hash could not be identified
=========================== short test summary info ===========================
FAILED tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format
======================== 1 failed, 1 warning in 0.95s =========================
```

3. As a control, the neighbouring test — a wrong password against a valid bcrypt hash — passes:

```bash
./.venv/Scripts/python -m pytest \
  tests/unit/test_security.py::TestSecurity::test_verify_password_incorrect \
  -q --disable-warnings
# -> 1 passed, 1 warning in 1.44s
```

4. Direct calls in the same venv, with a real bcrypt hash as the control and the two malformed values from my claim, plus a truncated bcrypt-shaped hash:

```bash
./.venv/Scripts/python - <<'EOF'
from passlib.exc import UnknownHashError
from core.security import hash_password, verify_password

good = hash_password("password")
print("control, right password ->", verify_password("password", good))
print("control, wrong password ->", verify_password("nope", good))

for stored in ["not_a_valid_bcrypt_hash", "", "$2b$12$abc"]:
    try:
        print(f"{stored!r:27} -> {verify_password('password', stored)}")
    except Exception as e:
        print(f"{stored!r:27} -> {type(e).__module__}.{type(e).__name__}: {e}")

print("UnknownHashError is a ValueError:", issubclass(UnknownHashError, ValueError))
EOF
```

Output:

```
control, right password -> True
control, wrong password -> False
'not_a_valid_bcrypt_hash'   -> passlib.exc.UnknownHashError: hash could not be identified
''                          -> passlib.exc.UnknownHashError: hash could not be identified
'$2b$12$abc'                -> builtins.ValueError: salt too small (bcrypt requires exactly 22 chars)
UnknownHashError is a ValueError: True
```

**Expected:** `verify_password()` returns `False` when the stored value is not a usable bcrypt hash — the same answer it gives for a wrong password.

**Actual:** for the test's `"not_a_valid_bcrypt_hash"`, and for an empty string, passlib's `UnknownHashError` escapes from `core/security.py:37`. Valid hashes behave correctly (`True` for the right password, `False` for a wrong one), so the failure is specific to the unrecognizable stored hash rather than to verification in general.

One thing worth flagging for whoever writes the fix: a truncated but bcrypt-shaped value (`$2b$12$abc`) also escapes, but as a plain `ValueError` ("salt too small"), not `UnknownHashError`. So catching only `UnknownHashError` would still let that input raise. `UnknownHashError` is itself a subclass of `ValueError`. I have not decided which of those a fix should catch — that is the next thing I want to think about, not a conclusion.

Also not part of this bug, noted so nobody chases it: passlib 1.7.4 prints a trapped `AttributeError: module 'bcrypt' has no attribute '__about__'` traceback to stderr the first time it loads bcrypt 4.3.0. It does not affect the result above.

Next I'll work on a change to `verify_password()` that returns `False` for these inputs, remove the strict `xfail` marker, and link the PR here.

I used Claude Code to help run these steps and draft this comment. The commands ran on my machine, and the output above is copied from those runs.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

In order:

1. Full run, first attempt: `agreement: 14/17 scored items`. Three packages (`pkg-04`,
   `pkg-07`, `pkg-11`) errored rather than grading, so the run scored only 17. Three scored
   packages disagreed: `pkg-03`, `pkg-05`, and `pkg-09`, all gold `accept` and all rejected
   by my rubric.
2. Partial re-run after revising two checks:
   `--only pkg-03,pkg-05,pkg-09,pkg-04,pkg-07,pkg-11,pkg-15,pkg-18,pkg-20` →
   `agreement: 6/6 scored items`. The three revision targets flipped to `accept` and agreed,
   and the three canaries held (`pkg-15` for `no-evidence`, `pkg-18` for
   `unfollowable-comms`, `pkg-20` for the single-package `disclosure` category). The same
   three packages errored again.
3. Partial re-run of the errored three only: `--only pkg-04,pkg-07,pkg-11` →
   `agreement: 3/3 scored items`. The errors were never a rubric problem: those three bundles
   carry emoji and CJK text, and on Windows the harness's `text=True` encodes the prompt in
   cp1252, so the write failed and `claude` received no input. Forcing UTF-8 on the
   subprocess fixed all three.
4. Confirming full run, with `--save-run`: `agreement: 20/20 scored items  (bar: 18/20: PASS)`,
   with every category matched — `clear-accept 8/8  disclosure 1/1  no-evidence 4/4
   unfollowable-comms 3/3  wrong-target 4/4`.

The last score matches the agreement line in the committed `eval-run.txt`.

**Package analysis**

`pkg-05` (source: `conda/conda#16543`, category `clear-accept`). The gold label is `accept`,
and my rubric now decides `accept` — but it rejected the package on my first full run, and
the reason is the useful part.

The package reproduces a `conda env update --json` failure where a human-readable
`EnvironmentSectionNotValid` message is emitted onto stdout ahead of the JSON, which then
fails to parse. The report's environment line is complete, the command is exact, and the
artifact shows the parse failure the issue describes. What it does not do is paste the
`env.yml` it used: it describes it, as "a valid `dependencies:` list plus a `category:`
section (the section conda does not recognize)".

My original `steps-rerunnable` check required that "every file, config, or fixture the run
depends on is shown inline or is publicly fetchable", so the described-but-not-pasted fixture
was a fail, and one required fail rejects the package. Reading the bundle against the gold
label, my check was asking for the wrong thing. The description names every property of the
file that matters to the failure — a valid dependencies list, plus an unrecognized
`category:` section — so a stranger can rebuild it without guessing. The fixture's exact bytes
are not what triggers the bug; the presence of an unknown section is. My check was grading the
transcription, not whether the run could be re-done.

**Check rationale**

From the current `tools/repro-check/rubric.md`:

`| claim-matches-evidence | every assertion of outcome in the repro report's conclusion and in the claim comment ("reproduced", "confirmed", "on both versions", "ran it ten times", a named cause), checked against the artifacts the package actually shows | Pass if the assertion the package rests on — that the issue's failure did or did not occur here — is backed by an artifact shown in the package, and no assertion overstates what that artifact shows. Supporting observations (a repeat count, a described control, a variant run) may stand in prose when the outcome does not rest on them. An evidenced cannot-reproduce passes. A "reproduced" or "verified" with no artifact behind it, or a root cause stated as established fact without one, fails | required |`

It started out stricter: "Pass if each such assertion is backed by an artifact in the package,
or is marked as a hypothesis or a next step... A second version, machine, or run that is
asserted but not shown... fails." That wording rejected `pkg-03` and `pkg-09`, both gold
`accept`. `pkg-03` shows its failing ripgrep run in full and then mentions, in prose, that the
same command without `-r` reports the line numbers correctly; `pkg-09` is an honest
cannot-reproduce that shows one attempt's log and mentions having run it several times. In
both, the claim the package actually rests on — the issue's failure did happen here, or did
not — is backed by a shown artifact. Only the corroborating detail is unshown.

So I rewrote the check around the deciding assertion rather than around every assertion. What I
rejected was the alternative fix of demoting the check to `preferred`: that would have bought
back `pkg-03` and `pkg-09` at the cost of `pkg-15`, where a confident root cause ("I verified
this race condition") has no artifact anywhere behind it and the package should be held.

**Trade-offs**

The revision is a loosening, so I ran canaries before trusting it, per the eval README's rule:
one already-agreeing package from each category the change could touch — `pkg-15`
(`no-evidence`, the category most exposed to a looser honesty check), `pkg-18`
(`unfollowable-comms`, exposed to the parallel loosening of `steps-rerunnable`), and `pkg-20`,
the set's only `disclosure` package, where a single flip would break the category floor no
volume of agreement elsewhere can buy back. All three held at $0.20 each rather than on a $4
full run.

What the looser check gives up: a package can now describe a control run that it never
actually performed, and my rubric will not catch it, because prose about a corroborating run
is exactly what I stopped requiring proof for. I accept that. The check still holds the line
where it decides the outcome — a bare "reproduced" with no artifact fails, and a root cause
asserted as fact fails — and the alternative cost more: under the strict wording, every honest
cannot-reproduce that mentions more than one attempt was a reject, which would have made the
rubric blind to the exact reporting this unit says earns full marks.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
