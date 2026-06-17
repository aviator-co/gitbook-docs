# Fixing verification failures

A failed verification means either your code, your intent, your invariants, or your preview needs attention. This guide covers the common failure shapes and how to resolve each.

### Reading the failure

Open the review document for the run. Every failed check shows:

* **Which verifier handled it** (Code-scan, Scenario, Invariant, or LLM fallback).
* **The criterion text** (or invariant rule name).
* **Evidence** — the snippet, request/response, or matched rule that produced the verdict.
* **A suggested direction** when the verifier can offer one.

Most failures fall into one of the patterns below.

### Criterion failed on Scenario

The most common failure. The verifier ran a scenario against your preview and the assertion didn't hold.

```
✗ Returns 429 when rate exceeded
  Verifier: Scenario
  Evidence: POST /api/v1/public/users → 200 (expected 429)
```

**Cause:** the implementation doesn't behave as the criterion asserts.

**Fix:** make the code do what the criterion says. Push the fix; verification re-runs.

If you're confident the code is right and the scenario is wrong, see *When the failure is the verifier* below.

### Criterion failed on Code-scan

The verifier inspected the diff or AST and the assertion didn't hold.

```
✗ No new direct dependencies
  Verifier: Code-scan
  Evidence: package.json:42 — added "got@^12.0.0"
```

**Cause:** the assertion is structural and the diff violates it.

**Fix:** either remove the offending change or update the intent if the dependency is intentional and the next submission should re-generate the criterion to permit it.

### Invariant violation

A team-defined rule flagged the change.

```
✗ security-baseline (org invariant)
  Evidence: src/handlers/admin.go:23 — Handler AdminUsers does not call AuthMiddleware
```

**Cause:** the code breaks a rule that applies across changes.

**Fix paths, in order of preference:**

1. **Fix the code.** Most of the time the rule is right and the change just missed it.
2. **Add an exception to the invariant.** If this case is legitimate and recurs (e.g. a new webhook endpoint that validates by signature), add it to the invariant's exception list. See [Concepts: Invariants — Exceptions](../concepts/invariants.md#exceptions).
3. **Waive the verdict.** For one-off cases the invariant didn't anticipate, the reviewer can waive with a reason. The waiver is recorded in the audit trail.

Avoid disabling the invariant globally. If you're tempted to, the rule is probably wrong — tighten its scope or rewrite the assertion.

### Scope drift

The diff grew after submission. The agent submitted with one set of files; the PR now contains more.

```
✗ Scope drift
  Submitted: 3 files
  Current PR: 5 files
  Added: src/auth/middleware.go, src/db/migrations/0042.sql
```

**Cause:** the change expanded after the MCP submission — usually from later commits, a merge from main, or a tool refactor that touched unrelated files.

**Fix paths:**

* **Re-submit the intent.** If the new files are intentional, the agent should call `submit-spec` again. The fresh submission carries the current diff and a re-generated criterion set.
* **Trim the diff.** If the new files snuck in unintentionally (formatter, IDE refactor), back them out and verification will accept the original submission.

### Preview boot failure

Verification couldn't bring up the preview, so no scenarios ran.

```
✗ Preview did not become ready
  Phase: setup script
  Exit code: 1
  Last 50 lines of container output: [...]
```

**Cause:** something is wrong with the preview itself — secret missing, setup script broken, image incompatible.

**Fix:** start with the container output. The common shapes are listed under [Creating a preview — Common boot failures](creating-a-preview.md#common-boot-failures). For ongoing flakiness, see [Managing previews — When the preview is wrong](managing-previews.md#when-the-preview-is-wrong).

### Scenario timeout

The preview booted, but a scenario didn't finish in the allowed time.

```
✗ P99 latency under 200ms
  Verifier: Scenario
  Reason: scenario exceeded 60s timeout
```

**Cause:** either the criterion is too expensive to verify, the preview is slow, or the code under test has a real performance regression.

**Fix:**

* If the code is slow on purpose (legitimate trade-off), update the intent so the next submission doesn't generate this criterion.
* If the preview is slow because of heavy setup, move work into the image — see [Managing previews — Bake vs setup](managing-previews.md#bake-into-the-image-or-run-at-setup-time).
* If it's a real performance regression, fix the code.

### LLM fallback ambiguity

The classifier routed the criterion to the LLM fallback verifier and it returned a low-confidence verdict.

```
⚠ Behaviour matches the SOC 2 logging requirement
  Verifier: LLM fallback (low confidence)
  Reason: criterion is too abstract to pin to a specific check
```

**Cause:** the criterion is vague enough that no deterministic verifier could handle it.

**Fix:** rewrite the criterion to be more specific. See [Writing effective acceptance criteria](writing-effective-acceptance-criteria.md). The next submission will route it back to a deterministic verifier.

### When the failure is the verifier, not the code

Occasionally a verdict is wrong. Before assuming it's a bug:

* **Re-read the evidence.** Does it actually contradict the criterion, or are you and the verifier interpreting the criterion differently?
* **Check the criterion phrasing.** "Requires authentication" might be read as "calls AuthMiddleware" or "rejects unauthenticated callers." Tighten the phrasing on the next submission.
* **Check the preview.** If a scenario verdict is wrong, the preview's state at run time might be wrong — wrong fixtures, stale seed. See [Seed data for previews](seed-data-for-previews.md).

If you've ruled all that out and the verdict still seems wrong, click **Report verdict** in the review document. Include the run ID; engineering uses these to improve the classifier and verifier accuracy.

### Re-running verification

After fixing issues, verification re-runs automatically on the next push.

You can also trigger it manually:

* Click **Re-run** in the review document.
* Or comment `/aviator verify` on the PR.

Re-running is safe — verification is deterministic for everything but LLM-fallback verdicts, and even those are cached on the criterion + change set.

### Getting help

If you can't resolve a failure:

* Check the run's debug log from the review document.
* Ask on Discord: [discord.gg/aviator](https://discord.gg/MmQWrY9xrA).
* Email support: [support@aviator.co](mailto:support@aviator.co).

Include the verification run ID when asking. It's in the URL of the review document.

### See also

* [How verification works](../concepts/how-verification-works.md) — the verifier pipeline
* [Concepts: Invariants](../concepts/invariants.md) — exceptions and waivers
* [Managing previews](managing-previews.md) — preview reliability
* [Writing effective acceptance criteria](writing-effective-acceptance-criteria.md)
