# How to use Cursor effectively

Cursor is a coding agent for understanding codebases, planning and building features, fixing bugs, and reviewing changes. The core loop:

**pick the right mode → give clear intent and context → let the agent search/edit/verify → review diffs → iterate**

Official entry points: [Docs](https://cursor.com/docs) · [Quickstart](https://cursor.com/docs/get-started/quickstart)

---

## 1. Pick the right surface

| Surface | Shortcut | Role | Use when |
| --- | --- | --- | --- |
| **Agent** | `Cmd/Ctrl+I` | Edits files, runs terminal, tools | Most coding work |
| **Ask** | Cycle with `Shift+Tab` | Read-only Q&A | Understanding code with no edits |
| **Plan** | `Shift+Tab` | Research + plan, then Build | Multi-file / unclear / architectural work |
| **Debug** | Mode picker | Instrument → you reproduce → evidence-based fix | Tricky or hard-to-repro bugs |
| **Inline Edit** | Select + `Cmd/Ctrl+K` | Edit selection in place | Small, targeted changes |
| **Tab** | Accept with `Tab` | Autocomplete from context | Continuous typing |

**Mode rules that matter:**

- **Agent** builds; **Ask** never edits; **Plan** codes only after you approve; **Debug** uses runtime evidence, not guess-and-patch.
- Switching modes starts a fresh context window — prefer a **new chat per task**.
- Checkpoints undo agent file state (not Git). Use Git for lasting history.
- For multi-file work from a selection: `Cmd/Ctrl+L` sends it into Agent. For Inline Edit Q&A: `Opt/Alt+Return`.

Docs: [Agent](https://cursor.com/docs/agent/overview) · [Plan](https://cursor.com/docs/agent/plan-mode) · [Debug](https://cursor.com/docs/agent/debug-mode) · [Ask](https://cursor.com/help/ai-features/ask-mode) · [Inline Edit](https://cursor.com/help/ai-features/inline-edit) · [Tab](https://cursor.com/help/ai-features/tab)

---

## 2. Prompt well

- State the **outcome**, **constraints**, and what “done” means (e.g. which tests must pass).
- `@` attach files when you already know them; otherwise let Agent search.
- Attach screenshots for UI/bugs; switch models mid-chat (faster for routine, stronger for hard multi-file work).
- Watch the **context ring** — older turns get summarized when the window fills.
- Queue follow-ups while Agent works (`Enter` queues; `Cmd+Enter` sends now).

Docs: [Prompting](https://cursor.com/docs/agent/prompting)

---

## 3. Encode habits as rules and skills

**Rules** (persistent instructions for chat modes — not Tab or Inline Edit):

1. **Project** — `.cursor/rules/*.mdc` (versioned; frontmatter required)
2. **User** — Customize → Rules (global)
3. **Team** — dashboard (Team/Enterprise; wins on conflicts)
4. **AGENTS.md** — plain markdown in root or nested dirs

Apply: Always / Intelligently (`description`) / globs / Manual (`@rule`).

**Rule tips:** keep under ~500 lines; be concrete; `@` reference files instead of pasting; don’t dump full style guides (use linters); add a rule when the agent repeats a mistake; commit project rules.

**Skills** (`.cursor/skills/`, etc.) package repeatable workflows; invoke with `/`. Use a **Custom Mode** (`Opt/Alt+Enter`) to keep a playbook active for a session (e.g. TDD).

Docs: [Rules](https://cursor.com/docs/rules) · [Skills](https://cursor.com/docs/skills)

---

## 4. Manage context and indexing

Useful `@` mentions: files/folders, `@Terminals`, `@Chats`, `@Commit`, `@Branch`, `@Browser`.

- **Do `@`** when you know the relevant files (component + tests).
- **Don’t over-attach** when unsure — Agent can search.
- Respect `.gitignore`; add `.cursorignore` for generated noise and secrets beyond defaults.
- Ignored files are blocked from Agent file tools (terminal/MCP may still reach them).

Docs: [Context](https://cursor.com/help/customization/context) · [Ignore files](https://cursor.com/help/customization/ignore-files)

---

## 5. Local Agent vs Cloud Agents

| | Local | Cloud |
| --- | --- | --- |
| Runs on | Your machine | Isolated VMs |
| You online? | Required | Async / parallel |
| Output | Local edits + checkpoints | Branch + PR (+ screenshots/logs) |
| Env | Your laptop | Configured deps, secrets, start cmds |

Start Cloud Agents from Desktop (Cloud in dropdown), [cursor.com/agents](https://cursor.com/agents), Slack/GitHub/Linear `@cursor`, or API.

**“Move to Cloud”** transfers conversation context, **not** dirty uncommitted files — commit or stash first.

Cloud quality depends as much on **environment setup** as on the prompt: runnable tests, secrets, `AGENTS.md`/skills for how to run services, and rules for conventions.

Docs: [Cloud Agents](https://cursor.com/docs/cloud-agent) · [Best practices](https://cursor.com/docs/cloud-agent/best-practices)

---

## 6. Review: Bugbot and Agent Review

- **Bugbot** — automated PR review (bugs/security/quality). Configure with `.cursor/BUGBOT.md` (not `.cursor/rules`). Optional Autofix spawns a Cloud Agent.
- **Agent Review** — local change review (`/agent-review` or Source Control); Quick vs Deep; can auto-run after agent tasks.

Docs: [Bugbot](https://cursor.com/docs/bugbot) · [Agent Review](https://cursor.com/docs/agent/agent-review)

---

## 7. CLI (any terminal)

```bash
curl https://cursor.com/install -fsS | bash
agent                    # interactive
agent "your task"
agent -p "..." --output-format text   # headless / CI
```

Same Agent / Plan / Ask modes and rules/`AGENTS.md`. Handoff to cloud with `& message`. Resume with `agent resume` / `agent ls`.

Docs: [CLI](https://cursor.com/docs/cli/overview)

---

## 8. Recommended workflows

**Feature:** Ask (orient) → Plan (multi-file) → Build → review diffs → run real checks. Small known changes can stay in Agent.

**Debug:** paste error + repro + expected vs actual in Agent; use **Debug Mode** for racey/perf/hard-to-repro issues.

**Refactor:** Plan if cross-cutting; `@` modules; verify tests after each slice; use checkpoints.

**PR:** Agent Review or `/review-bugbot` before push; Bugbot on the remote PR.

---

## 9. Practices that improve results

1. Be specific about outcome, constraints, and done criteria.
2. `@` files when you know them; don’t spam context when you don’t.
3. Match mode to job (Ask / Plan / Agent / Debug / Inline / Tab).
4. Iterate: change → run checks → follow up.
5. For large work, invest in Plan; restart from a better plan instead of fighting a bad build.
6. Encode repeated mistakes as rules or skills.
7. For Cloud, treat environment setup as part of the prompt.
8. Checkpoints for undo; Git for history.
9. New chat per task (and when switching modes).
10. Verify with your real tooling — don’t merge on agent confidence alone.

---

## Quickstart path

1. Open a folder → `Cmd/Ctrl+I` → ask it to explain the codebase.
2. Make one small safe change → review the diff → run checks.
3. Use **Plan** for the next larger feature.
4. Add `.cursor/rules` or `AGENTS.md` as patterns stabilize.
5. Enable Bugbot for PRs; try Cloud Agents once the environment is solid.
