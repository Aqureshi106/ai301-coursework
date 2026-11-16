# Format Option Addition

## Overview

I added a format option to ecotrace's webhook exporter, so emissions alerts can go out as Slack or Discord rich cards. Claude Code carried out the diagnosis, the patch, and gh pr edit with both my approval and review. Plain JSON remains the default in order for nothing to break for existing users. The maintainer approved my pull request and merged it upon review and asked for no alterations.
https://github.com/Zwony/ecotrace/pull/229

## What happened

The voice guard is a hook that checks pull request drafts before gh pr create runs. The crash happened at 22:55 on 10-29, when I ran the guard by hand on the real pull request command and real description. The voice guard threw a UnicodeDecodeError as Windows read the draft with the wrong encoding. A crash exits with code 1, which Claude Code lets through, so a broken guard would have let the post go out unchecked. 

*2026-10-29 22:55 EDT*

```
exit code: 1
stdout: (empty)
stderr: Traceback (most recent call last):
  ...
    return (Path(cwd or ".") / path).read_text(), None
           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^^
  ...
    return codecs.charmap_decode(input,self.errors,decoding_table)[0]
           ~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
UnicodeDecodeError: 'charmap' codec can't decode byte 0x90 in position 278: character maps to <undefined>
--- guard-log lines after this run:
- 2026-10-29 20:45 [hook] stop: no body the guard can read (interactive and --help forms are stopped on purpose) | target: gh pr create --help
- 2026-10-29 20:46 [hook] stop: no body the guard can read (interactive and --help forms are stopped on purpose) | target: gh pr create --help
```

The two --help log lines show that a crashed hook wrote nothing, because the guard-log hadn't changed.

The patch wraps the check in try/except in order for a crash to stop the post and reads drafts as UTF-8. Per the record, Claude Code proposed this at 23:14 and made it after I provided approval at 23:27. 

*2026-10-29 23:28 to 23:33 EDT*

```diff
@@ -258,14 +261,23 @@
         return 0
     target = " ".join(tokens[start - 3:start + 2])
-    body, problem = extract_body(tokens, start, payload.get("cwd", "."))
-    if body is None:
-        return stop(problem, "hook", target,
-                    f"stop: {problem.split('.', 1)[0]}")
-    return check_draft(body, "hook", target)
+    try:
+        body, problem = extract_body(tokens, start, payload.get("cwd", "."))
+        if body is None:
+            return stop(problem, "hook", target,
+                        f"stop: {problem.split('.', 1)[0]}")
+        return check_draft(body, "hook", target)
+    except Exception as e:
+        # A crash exits 1, which Claude Code lets through. A guard that
+        # cannot run must stop the post, not wave it out unchecked.
+        kind = type(e).__name__
+        return stop(f"the guard itself crashed ({kind}: {e}), so the "
+                    "check did not run and nothing posts. Fix the guard "
+                    "or flag a TF.", "hook", target,
+                    f"stop: guard crashed ({kind})")
 
 
 def manual_main(draft_path: str) -> int:
     try:
-        draft = Path(draft_path).read_text()
+        draft = Path(draft_path).read_text(encoding="utf-8")
     except OSError as e:
         print(f"voice-guard: cannot read {draft_path}: {e}",
```

The guard flagged the line that starts "its colour is green when..." under the rule "separate what I saw from what I think." The sentence presented the colour rule as confirmed by the maintainer. He had only written green below carbon_limit and red/orange above it. The equal case and choosing red over orange were my choices. The patched guard stopped the real description at 23:31.

*2026-10-29 23:31 EDT*

```
exit 2 in 119s: voice-guard: STOP. your own rule: "Rule: separate what I saw from what I think". Draft line: "Its colour is green when t
```

*The sentence as the PR opened*

```
- Same file: `format="discord"` sends one embed with the function name as the title and gCO2 and duration as fields. Its colour is green when the measurement is below the EcoTrace instance's `carbon_limit` or no limit is set, and red at or above it, which is the rule confirmed on [the issue thread](https://github.com/Zwony/ecotrace/issues/194#issuecomment-6352132885). It compares one measurement with `carbon_limit`. It is not a "budget used up" signal; `core.py` tracks that separately, with the running total.
```

Claude Code had reworded the sentence and ran gh pr edit after my approval to execute at 00:06. The new version said the maintainer wrote green below carbon_limit, and red/orange above it. The guard passed the new sentence at 00:08.

*2026-10-30 00:08 EDT*

```
- Same file: `format="discord"` sends one embed with the function name as the title and gCO2 and duration as fields. The colour is green when the measurement is below the EcoTrace instance's `carbon_limit` or no limit is set, and red at or above it; the tests cover no limit, below, equal and above. On [the issue thread](https://github.com/Zwony/ecotrace/issues/194#issuecomment-6352132885) the maintainer wrote green below `carbon_limit` (or unset) and red/orange when emissions exceed it. The equal case, and red over orange, are my choices. The colour compares one measurement with `carbon_limit`. It is not a "budget used up" signal; `core.py` tracks that separately, with the running total (I read that in `_enforce_carbon_budget`).
```

The pull request opened on the evening of 10-29. The maintainer approved it on 10-30, 13.7 hours after it opened, and merged it 15 seconds later.

The guard stopped the overclaiming sentence at 23:31 on 10-29, 37 minutes after the pull request opened. The pull request opened around 22:54. I corrected the sentence, and the guard passed it at 00:08. That was 12.4 hours before the maintainer approved. The maintainer's review doesn't mention the guard.

*2026-10-30 12:33 EDT*

> Thank you for the thorough work, detailed PR description, and comprehensive test suite. The implementation is clean, backward-compatible, and adheres strictly to the project requirements. Merging now.

Release v1.8.0 on 2026-11-01 credits #229.

## Limitations and what's next

Limitations are that the hook's command list has comment and create for issues and pull requests, but no edit. The pull request fix went through gh pr edit after a manual check, so the hook didn't gate it. The next step would be to add ("pr", "edit") and ("issue", "edit") to that list and to check whether extract_body can find the body in an edit command before a one-line change. 

On the same unchanged description, the guard passed it at 23:00, then stopped it at 23:31, and passed it at 23:32. The log excerpt shows the stop and the pass. The 23:00 pass isn't in the excerpt as it is a direct run on the guard's prompt and not the hook. For a guard whose job is to be trusted, this is the more serious limit. The next step would be to run the check more than once, or to make the verdict depend less on a single model call.

```
- 2026-10-29 23:31 [hook] stop: rule broken: "Rule: separate what I saw from what I think" | target: gh pr create --title feat(exporters): add Slack and Discord payload formats to WebhookExporter
- 2026-10-29 23:32 [manual] pass | target: pr_draft.md
```

```
_OUTGOING = {("issue", "comment"), ("pr", "comment"),
             ("issue", "create"), ("pr", "create")}
```
