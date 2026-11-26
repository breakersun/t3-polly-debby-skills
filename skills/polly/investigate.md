# Investigate — Delegated Read-Only Work

Use for debugging, codebase audits, architecture questions, failure analysis, or pre-implementation scoping.

## Roster Selection

| Task Type | Preferred Dispatch |
| --- | --- |
| Quick/targeted lookup | `agy-flash` (`antigravity` / `gemini-4-hydrogen`, `thought_level: "high"`) or `glm-pi-flash` (`pi` / `zai-coding-cn/glm-5.3-flash`, `thinking: "high"`) |
| Ambiguous or high-stakes question | **Two independent lenses in parallel:** `agy-flash` + `glm-pi-flash` (or `agy-pro` + `glm-pi` for deep architectural/UI analysis) |
| Visual / screenshot / UI trace | `glm-pi` (`zai-coding-cn/glm-5.4` supports text + image) or `agy-pro` |

## Procedure

1. **Decompose** the question into narrow, bounded `EXPLORE` tasks. Split broad investigations into parallel subtasks.
2. **Dispatch in the same turn** via `delegate_task` with:
   - `role: "research"`
   - `isolation: "shared"`
   - `mode: "async"`
   - `title: "explore-<task_slug>"`
   - `task`: prepend `PURPOSE: EXPLORE`, state the exact question, target directories/files, and require `edit nothing` + exact `file:line`, symbol, and command evidence.
3. **End your turn** once dispatches are in flight (or continue only independent orchestration work). Never poll `task_status` in a loop, and avoid duplicating the workers' deep reads yourself while they run.
4. **Synthesize strictly from worker reports:**
   - Reconcile answers using their cited `file:line` and command evidence.
   - If two lenses conflict or leave a critical gap, dispatch a targeted follow-up `EXPLORE` task rather than guessing.
5. **Transition if changes are needed:** Switch to [fanout.md](fanout.md) for implementation and [cross-review.md](cross-review.md) for verification.
