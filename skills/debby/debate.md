# Debate — Cross-Critique & Synthesis

Make `agy-pro` (`antigravity` / `gemini-4-neon`) and `glm-pi` (`pi` / `zai-coding-cn/glm-5.4`) critique each other's answers across a configurable number of rounds (**default: 1 round**), then converge on an even-handed synthesis.

## Procedure

1. **Round 0 — Opening answers:**
   - If you already collected fresh `MODE: ANSWER` responses from both heads for this question, reuse them as Round 0.
   - Otherwise, run Debby's default `MODE: ANSWER` dispatch for both heads in parallel (`role: "design"`, `isolation: "shared"`, `mode: "async"`) and wait for both to finish.

2. **Rounds 1..N — Cross-critique (`delegate_task`):**
   - For each round `r` (default `N = 1`, or honor user's explicit count):
     - Launch a **new** `delegate_task` (`role: "design"`, `isolation: "shared"`, `mode: "async"`) for **both** heads in the same turn (never reuse `t3_thread_send` for critique rounds):
       - Title: `"[agy-pro] debate r<r> <topic>"` and `"[glm-pi] debate r<r> <topic>"`.
       - Because each fresh task has no prior memory, pass **all three** in `task`: (a) the original question, (b) **that head's own** Round `r-1` answer, and (c) **the other head's** Round `r-1` answer.
       - Prepend the **CRITIQUE** contract:
         - `MODE: CRITIQUE` — Do not edit or create files. Engage honestly with the other partner's answer: name what it gets right, where it is weak, wrong, or incomplete, and what you would change. Then give your own updated answer incorporating anything the critique convinced you of. Don't cave just to agree, and don't dig in out of pride.
     - End your turn and wait until both Round `r` results return.
   - **Early stop:** If two rounds produce no meaningful movement, note that and converge early rather than burning extra rounds.

3. **Converge — Moderator Synthesis:**
   - Act strictly as moderator (do not insert a third opinion). Present:
     - `## Gemini 4 Neon (agy-pro) — Final`
     - `## GLM-5.4 (glm-pi) — Final`
     - `## How the debate moved them` (2–4 bullets: concessions, held positions, remaining disagreements)
     - `## Synthesis` (strongest combined answer, explicitly flagging any genuine unresolved trade-offs)
