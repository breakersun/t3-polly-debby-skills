# Cross-Review — Independent Verification & Fix Loop

Every `IMPLEMENT` deliverable must be verified by an **independent, opposite-vendor** reviewer before Polly considers it done.

## Cross-Vendor Pairing

- **If `agy-*` (`antigravity`) implemented** → **`glm-pi`** (`pi` / `zai-coding-cn/glm-5.4`) reviews *(or `glm-pi-flash` for tiny, low-risk diffs)*.
- **If `glm-pi*` (`pi`) implemented** → **`agy-pro`** (`antigravity` / `gemini-4-neon`) reviews *(or `agy-flash` for tiny, low-risk diffs)*.

## Procedure

1. **Check deterministic gates first:** Confirm the implementer's tests, lint, and typechecks actually passed (re-run or verify exact command output if uncertain). If gates are red, fix or re-dispatch implementation to drive green **before** involving the reviewer.
2. **Isolate the review input:** Identify the exact diff range (`<base-sha>..<head-sha>`, branch, or PR) and acceptance contract.
   - Pass **only** the diff scope/commit range and acceptance contract.
   - **Never** pass the implementer's reasoning, chat transcript, or internal notes — independence is the point.
3. **Dispatch reviewer via `delegate_task` in the same turn:**
   - `role: "review"`
   - `isolation: "shared"`
   - `mode: "async"`
   - `title: "review-<task_slug>"`
   - `task`: prepend `PURPOSE: REVIEW`, state the exact `<base-sha>..<head-sha>` (or PR/diff path), acceptance contract, `edit nothing`, and require:
     - `VERDICT: APPROVE | REQUEST_CHANGES`
     - Structured `blocking`, `non-blocking`, and `suggestions` lists with `file:line` and concrete fixes.
4. **Handle findings:**
   - **Zero `blocking` issues + green gates** → Done. Present the verified commit/branch/PR and any `non-blocking` / `suggestions` follow-ups to the user (do not merge unless asked).
   - **Small `blocking` findings** → Fix them directly yourself (do not spawn a new implementer delegate for trivial fixes), re-run affected checks, then launch a **new** `delegate_task` for re-review.
   - **Substantive `blocking` findings** → Dispatch a fresh `IMPLEMENT` `delegate_task` scoped to the concrete blockers and target branch/commit, verify green gates, then launch a **new** `delegate_task` for re-review (never `t3_thread_send` for re-reviews).
5. **Escalation cap:** If the contract is still not satisfied after **2–3 review-fix loops**, stop and escalate to the user with the remaining blockers and commit/diff details.
