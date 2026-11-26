# Fanout — Safe Parallel Implementation

Use for feature work, refactors, or bug fixes — especially when splitting independent subtasks across parallel workers.

## Parallel-Safety Gate

Only parallelize tasks that are **file-disjoint and order-independent**. If task B depends on task A's API or edits the same files, run A first (or keep them in one scoped task).

## Procedure

1. **Choose worker per task:**
   - Heavy/complex changes → `agy-pro` (`antigravity` / `gemini-4-neon`) or `glm-pi` (`pi` / `zai-coding-cn/glm-5.4`)
   - Small, well-bounded changes → `agy-flash` (`antigravity` / `gemini-4-hydrogen`) or `glm-pi-flash` (`pi` / `zai-coding-cn/glm-5.3-flash`)
   - Always pass `{"thought_level": "high"}` (Antigravity) or `{"thinking": "high"}` (Pi).
2. **Dispatch via `delegate_task` in the same turn:**
   - `role: "implementation"`
   - `isolation: "worktree"` (never create a child worktree in the shell; T3 Code isolates each worker automatically)
   - `mode: "async"`
   - `title: "impl-<task_slug>"`
   - `task`: prepend `PURPOSE: IMPLEMENT`, include full context (workers see no parent history and no uncommitted parent edits), exact file scope, acceptance criteria, and required test/lint/typecheck gates. Instruct the worker to commit in its worktree branch and **never merge or push** unless explicitly requested.
3. **Batch large fanouts in waves** if there are many tasks; wait for the active wave to wake you before dispatching the next.
4. **Handle runaway or failed workers cleanly:**
   - If a running worker is off-track, superseded, or stuck, stop it with `task_cancel` (never `t3_thread_interrupt` on a child thread) before launching a replacement.
   - If a worker fails or returns an unusable result, don't nag it in a loop — re-dispatch a fresh `delegate_task` in a clean worktree with a tighter brief, or escalate to the user.
5. **Verify before handoff:** Once workers finish with green gates and commit SHAs, run [cross-review.md](cross-review.md) for every implementation branch/diff. Never merge into the user's branch unless explicitly asked.
