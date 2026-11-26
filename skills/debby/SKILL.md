---
name: debby
description: Two-headed brainstorming partner that fans questions out in parallel to Gemini 4 Neon (agy-pro) and Zhipu GLM-5.4 (glm-pi), shows both views side by side, and optionally runs multi-round cross-critique and synthesis. Use when the user invokes /debby or $debby, asks for two perspectives, wants models to brainstorm or debate, or says "ask both".
---

# Debby — Two-Headed Brainstorming Partner

Never answer a substantive question from a single model's point of view. Fan every question out to **both** independent heads via `delegate_task`, present both answers side by side, and highlight agreements and disagreements.

## 1. The Two Heads (Thinking = `high`)

Call `orchestrator_capabilities` once per thread before the first `delegate_task`. Dispatch both heads in parallel (if a listed provider or model is unavailable in `orchestrator_capabilities`, report it and ask the user which two-vendor models to substitute rather than switching unasked):

| Head | `providerInstanceId` | `model` | `options` |
| --- | --- | --- | --- |
| **`agy-pro`** | `antigravity` | `gemini-4-neon` | `{"thought_level": "high"}` |
| **`glm-pi`** | `pi` | `zai-coding-cn/glm-5.4` | `{"thinking": "high"}` |

## 2. Default Workflow (Side-by-Side Brainstorming)

1. **Dispatch both in the same turn** via `delegate_task`:
   - `role: "design"`
   - `isolation: "shared"`
   - `mode: "async"`
   - Distinct titles: `"[agy-pro] <topic>"` and `"[glm-pi] <topic>"`.
   - Pass the user's question faithfully (plus any needed repo/context pointers) without biasing toward an answer, and prepend the **ANSWER** contract:
     - `MODE: ANSWER` — You are a thinking-and-writing responder. You may read files for reference, but **do not edit or create files** unless explicitly asked. Give a concrete, well-reasoned, self-contained answer; offer options with trade-offs where helpful.
2. **End your turn** once both dispatches are in flight and wait for reactive wakeups (never poll `task_status` in a loop). Wait until **both** answers are back before presenting.
3. **Present both views evenly** (lightly trimmed at most, never silently merged or dropped):

```markdown
## Gemini 4 Neon (`agy-pro`)
<agy-pro's answer>

## GLM-5.4 (`glm-pi`)
<glm-pi's answer>

## Where they agree / differ
- <2–4 bullets: shared ground, then real disagreements>
```

## 3. Debate Mode (Critique & Synthesis)

When the user asks the two heads to **debate, argue, critique, stress-test, or converge** (or specifies rounds like `"debate for 2 rounds"`), read and follow [debate.md](debate.md).
