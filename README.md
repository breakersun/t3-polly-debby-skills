# T3 Code Multi-Agent Skills Pack (`polly` + `debby`)

Two lightweight multi-agent orchestration skills for **T3 Code** (`delegate_task` + `orchestrator_capabilities`), inspired by [omnigent-ai/omnigent](https://github.com/omnigent-ai/omnigent) (`examples/polly` and `examples/debby`).

## Included Skills

- **`polly`** — Tech-lead coding orchestrator with a 4-subagent roster across two vendors, plus 3 on-demand playbooks:
  - `investigate.md`: read-only dual-lens debugging, audits, and architecture exploration (`isolation: "shared"`)
  - `fanout.md`: parallel, file-disjoint implementation in isolated checkouts (`isolation: "worktree"`)
  - `cross-review.md`: deterministic test/lint gates + independent opposite-vendor code review loop
- **`debby`** — Two-headed brainstorming partner that fans questions out to two different-vendor models in parallel, displays both views side by side, and supports multi-round cross-critique (`debate.md`).

## Installation

Install both skills globally with the [Skills CLI](https://skills.sh/):

```bash
npx skills add breakersun/t3-polly-debby-skills -g -y
```

Or install a single skill:

```bash
npx skills add breakersun/t3-polly-debby-skills@polly -g -y
npx skills add breakersun/t3-polly-debby-skills@debby -g -y
```

Project-local install (without `-g`):

```bash
npx skills add breakersun/t3-polly-debby-skills
```

## Usage

- **Coding orchestration:** `$polly <task>` (e.g., `$polly add rate limiting and tests`, `$polly investigate why SSE drops chunks`, `$polly cross-review branch feat/x against main`)
- **Side-by-side brainstorming:** `$debby <question>`
- **Multi-round debate:** `$debby debate <question>` or `$debby debate for 2 rounds <question>`

## Default Roster & Customization

The default roster targets a 2-vendor setup (**Antigravity Gemini 4** + **Pi Zhipu GLM**, all thinking/effort set to `high`):

| Alias | `providerInstanceId` | `model` | `options` |
| --- | --- | --- | --- |
| `agy-pro` | `antigravity` | `gemini-4-neon` | `{"thought_level": "high"}` |
| `agy-flash` | `antigravity` | `gemini-4-hydrogen` | `{"thought_level": "high"}` |
| `glm-pi` | `pi` | `zai-coding-cn/glm-5.4` | `{"thinking": "high"}` |
| `glm-pi-flash` | `pi` | `zai-coding-cn/glm-5.3-flash` | `{"thinking": "high"}` |

Using different providers (such as `codex` + `claudeAgent`)? Run `orchestrator_capabilities` in T3 Code to inspect your active `providerInstanceId`, `model`, and `options` IDs, and update the roster table in `skills/polly/SKILL.md` and `skills/debby/SKILL.md`.
