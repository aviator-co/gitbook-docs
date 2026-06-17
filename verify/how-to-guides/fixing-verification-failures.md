# Fixing verification failures

A failed verification means either your code, your criteria, an invariant, or your preview needs attention. This guide covers the common failure shapes and how to resolve each.

### Reading the failure

Open the review document for the run. Every failed verdict shows:

* **The criterion text** (or invariant title).
* **Which verifier path handled it** (Code-scan or Runtime).
* **Evidence** — the snippet, request/response, screenshot, or other captured artifact that produced the verdict.
* **A reason** describing why the verdict went the way it did.
* **A location** — file + line range, when the verifier could attribute one.

Most failures fall into one of the patterns below.

### Criterion failed on Runtime

The most common failure. The runtime runner drove your preview and the assertion didn't hold.

```
✗ Returns 429 when rate exceeded
  Verifier: Runtime
  Evidence: POST /api/v1/public/users → 200 (expected 429)
```

**Cause:** the implementation doesn't behave as the criterion asserts.

**Fix:** make the code do what the criterion says. Push the fix; verification re-runs.

If you're confident the code is right and the runtime check is wrong, see *When the failure is the verifier* below.

### Criterion failed on Code-scan

The verifier inspected the diff or AST and the assertion didn't hold.

```
✗ No new direct dependencies
  Verifier: Code-scan
  Evidence: package.json:42 — added "got@^12.0.0"
```

**Cause:** the assertion is structural and the diff violates it.

**Fix:** either remove the offending change or update the criterion if the change is intentional. Edit the criteria via [`editRunbook`](../reference/mcp-tools.md#editrunbook) (for user criteria) or work with the reviewer to waive (for invariant criteria).

### Invariant violation

A team-defined rule flagged the change.

```
✗ auth-required-on-handlers (invariant)
  Verifier: Code-scan
  Evidence: src/handlers/admin.go:23 — Handler AdminUsers does not call
  an authentication middleware before responding.
```

**Cause:** the code breaks a rule that applies across changes.

**Fix paths, in order of preference:**

1. **Fix the code.** Most of the time the rule is right and the change just missed it.
2. **Waive the verdict with a category.** For legitimate cases the invariant didn't anticipate, the reviewer waives from the review document. Pick the right category:
   * `false_positive` — the rule fired but misjudged this case.
   * `doesnt_apply` — the rule is valid in general but isn't relevant to this PR.
   * `accepted_risk` — the failure is real but the author accepts it.
   * `fix_in_followup` — will be addressed in a separate PR.
3. **Fix the invariant.** If you're waiving the same invariant repeatedly across changes, the rule is wrong. Tighten its conditions or rewrite the body. See [Concepts: Invariants](../concepts/invariants.md).

### Preview boot failure

Verification couldn't bring up the preview, so no runtime checks ran.

```
✗ Preview did not become ready
  Phase: setup script
  Exit code: 1
  Container output (last lines): [...]
```

**Cause:** something is wrong with the preview itself — secret missing, setup script broken, image incompatible.

**Fix:** start with the container output. The common shapes are listed under [Creating a preview — Common boot failures](creating-a-preview.md#common-boot-failures). For ongoing flakiness, see [Managing previews — When the preview is wrong](managing-previews.md#when-the-preview-is-wrong).

### Runtime run terminated

The preview booted, but the runtime runner stopped without producing a clean verdict. Common termination reasons:

| Reason            | What it means                                                                |
| ----------------- | ---------------------------------------------------------------------------- |
| `caps_exceeded`   | Hit a hard cap on tool calls, wall time, or per-scenario cost.               |
| `loop_detected`   | The runner got stuck repeating the same action or page state.                |
| `stuck`           | A periodic check determined the runner wasn't making progress.               |
| `give_up`         | The runner explicitly decided it couldn't verify the criterion.              |
| `unhandled_error` | The runner raised an unclassified exception.                                 |

**Fix:**

* `caps_exceeded` or `give_up`: often the criterion is too expensive to verify or the preview is too slow. Move setup work into the image, or rewrite the criterion to be tighter.
* `loop_detected` or `stuck`: usually the preview's UI/state is non-deterministic between runs. See [Seed data for previews](seed-data-for-previews.md).
* `unhandled_error`: file as a bug from the review document.

### When the failure is the verifier, not the code

Occasionally a verdict is wrong. Before assuming it's a bug:

* **Re-read the evidence.** Does it actually contradict the criterion, or are you and the verifier interpreting the criterion differently?
* **Check the criterion phrasing.** "Requires authentication" might be read as "calls AuthMiddleware" or "rejects unauthenticated callers." Tighten the phrasing — for user criteria, via `editRunbook`; for invariants, by editing the catalog entry.
* **Check the preview.** If a runtime verdict is wrong, the preview's state at run time might be wrong — wrong fixtures, stale seed. See [Seed data for previews](seed-data-for-previews.md).

If you've ruled all that out and the verdict still seems wrong, click **Report verdict** in the review document. Include the run ID; the team uses these to improve the classifier and verifier accuracy.

### Re-running verification

After fixing issues, verification re-runs automatically on the next push.

You can also trigger it manually from the runbook UI. Re-running is safe — verifier results are stable on identical input, and the system caches runtime evidence per criterion + change set.

### Getting help

If you can't resolve a failure:

* Check the run timeline and container output from the review document.
* Ask on Discord: [discord.gg/aviator](https://discord.gg/MmQWrY9xrA).
* Email support: [support@aviator.co](mailto:support@aviator.co).

Include the runbook number when asking. It's in the URL of the review document (`r/{number}`).

### See also

* [How verification works](../concepts/how-verification-works.md) — the verifier pipeline
* [Concepts: Invariants](../concepts/invariants.md) — waivers and the catalog
* [Managing previews](managing-previews.md) — preview reliability
* [Writing effective acceptance criteria](writing-effective-acceptance-criteria.md)
