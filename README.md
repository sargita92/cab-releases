# CAB — Crew Agent Board

**English** · [Português](README.pt-BR.md)

**A local kanban board where AI agents do the work and you make the calls.**

You write a **request** ("email login", "fix the sales report"). CAB breaks it into **tasks** with dependencies; each
task runs on an agent (Claude or Codex) in an isolated copy of your repository, goes through a reviewer agent and
comes back for you to accept. **Nothing reaches your code without your approval.**

> This repository only hosts the app installers and updates. Download from
> **[Releases → latest](https://github.com/sargita92/cab-releases/releases/latest)**.
>
> The app's interface is in Portuguese; screen names below are quoted as they appear, with a translation.

## How it works

```
request ──► planner breaks it into tasks ──► you approve the definition
                                                  │
          ┌───────────────────────────────────────┘
          ▼
  agent works (own worktree) ──► reviewer checks ──► you accept, correct or reject
                                                              │
                                              delivery: none · branch · PR
```

- **One worktree per task** (branch `crew/<request>/<task>`): your checkout is never touched.
- **Questions (❓):** when an agent is unsure, triage answers what is already in the request or the code; the rest
  comes to you.
- **Stuck (⛔):** error, usage limit, conflict — shown on the card with the reason and a Resume button.
- **Parallel:** several tasks at once, respecting dependencies (🔗).
- **Transparent:** live log of every agent, tokens and tools used — plus a pixel-art office with the team at work.

## Install

| System | File | Notes |
|---|---|---|
| **Windows** | `CAB-x.y.z-setup.exe` | Per-user install, no admin. SmartScreen warns because the app is unsigned: **More info → Run anyway**. |
| **Linux / WSL** | `CAB-x.y.z.AppImage` | `chmod +x CAB-*.AppImage` and run it. This is the format that updates itself. |
| **Linux / WSL (.deb)** | `CAB-x.y.z.deb` | `sudo apt install ./CAB-*.deb`. On WSL, CAB shows up in the Windows Start menu. |
| **Mac (Apple Silicon)** | `CAB-x.y.z-arm64.dmg` | Not signed by Apple: after dragging it to Applications, run `xattr -cr /Applications/CAB.app` once (otherwise macOS says it "is damaged"). |

### Requirements

- **git** — projects are git repositories.
- **An AI account** — Claude ships inside the app:
  - **Claude:** subscription (Pro/Max) or API key. If you already use Claude Code on this machine, its login works.
    Otherwise set it up in **⚙ → Contas** (Accounts) — the app shows the steps and a ready-to-copy command.
  - **Codex** (optional): `codex login` or an API key, also under **Contas**.
- **GitHub CLI (`gh`), logged in** — only if you want deliveries as pull requests.

## Getting started

1. Open CAB → **📁 Projetos → + Adicionar projeto** (Projects → Add project) and pick your repository folder (or
   **Novo projeto** to create one).
2. Click **+ Pedido** (Request) and describe what you want, as you would to a person.
3. The planner returns the tasks under **Revisar definição** (Review definition): read, tweak if needed and
   **approve**.
4. Follow along on the board. When a task reaches **Revisar resultado** (Review result), check the diff and
   **accept**, **correct** (with a comment — it runs again) or **reject**.
5. In **⚙ → Projeto** (Project), choose the delivery: worktree only, a branch, or a **PR** (stacked, in dependency
   order).

## Chat — the assistant that knows your project

The **Conversa** (Chat) button opens a chat with an agent that runs **inside your repository** and **operates CAB**.
It knows everything about the project:

- **The code:** reads, searches, runs commands and tests, edits files — like Claude Code in a terminal.
- **The board:** requests, tasks, statuses, each agent's logs, pending questions, why something is stuck.
- **CAB itself:** it carries the built-in manual (formats, profiles, recipes) and the project's configuration.

So you can just ask:

- "create a request to add CSV export"
- "why doesn't task 01 start?" · "what did the executor do on task 03?"
- "unblock task 02" · "accept 01" (it only accepts when you explicitly ask)
- "always run the QA agent before the reviewer" — it edits the profile
- "set up crew for this project" — builds tailored agents and a flow for a new repository
- "create a Trello plugin"

Each message can pick the **model**, **effort**, **Plan** mode (proposes, doesn't edit) and **MCPs globais** (use the
MCP servers you installed in Claude Code). Attachments work too (📎, Ctrl+V, drag and drop). Coming from another tool
with its own agents (`.claude/agents/`)? **Portar para o CAB** (Port to CAB) opens a chat that converts agents, flow
and backlog.

## Agents

Every step of the flow is an agent — a file with instructions, tools, model and limits.

| Agent | What it does |
|---|---|
| `planner` | Breaks the request into tasks with dependencies |
| `executor` | Does the task in its worktree |
| `reviewer` | Reviews the result before it reaches you (rejects → back to the executor) |
| `triage` | Answers agent questions already covered by the request/code; decides whether a long task keeps going or is stuck |
| `fixer` | Tries to fix a ⛔ before calling you (optional) |
| `conflict_resolver` | Resolves merge conflicts between tasks |
| `pr_publisher` | Opens the PR when the task reaches review (optional) |
| `chat`, `telegram`, `porter` | The Chat, the Telegram bot and the project porter |
| `agent_writer`, `claudemd_writer` | Create agents and the repository's `CLAUDE.md` from plain text |

The built-ins are generic. The real power is **creating your own** — `backend-dev`, `qa`, `designer`, `rpa`… — and a
**profile** that chains them (e.g. `planner → backend-dev → qa → reviewer`). Each agent can use a different
provider: planner on Claude, executor on Codex.

On a task, the **Agentes** (Agents) tab lists every run (time, tokens, tools). The 🏢 button opens the **agents'
office**: pixel art with the team typing at their desks while they work and grabbing coffee when they don't.

## Telegram

Talk to CAB from your phone.

1. Create a bot with **@BotFather** and copy the token.
2. In **⚙ → Telegram**, paste the token, turn it on and save.
3. Send the bot a message: it shows your id. Fill it in and save — only you can talk to it.

From then on you get notified of **questions (❓)**, **stuck tasks (⛔)**, **definitions to approve** and **results
to accept**. Replying to a notification already tells it which task you mean: "use JWT", "approve", "correct: the
test is missing". It also understands plain language ("how's the board?", "create a request to…"). `/novo` starts a
new conversation.

## Settings (⚙)

Everything is a file, in layers — the most specific wins: **built-in** → **Meu** (Mine, all projects, `~/.crew/`) →
**Só este projeto** (This project only, `<repo>/.crew/`, can be committed for the team). Editing a built-in saves
your own copy; the original never changes.

| Tab | For |
|---|---|
| **Agentes** (Agents) | Create and edit agents by form or raw file; **Descrever com IA** (Describe with AI) creates one from text |
| **Perfis** (Profiles) | The flow: who plans, executes, reviews, triages, fixes and delivers |
| **Projeto** (Project) | Project profile, delivery (none/branch/PR), parallel tasks, execution mode, generate `CLAUDE.md` |
| **Ondas** (Waves) | Wave plan: the next wave starts only when the current one is accepted (shown in Waves mode) |
| **Plugins** | Integrations (**Jira** is built in: pulls cards in as requests and moves the card as the task progresses) |
| **Geral** (General) | Default provider (Claude/Codex), attachment limit, auto-archive, display options |
| **Contas** (Accounts) | Personal × work accounts per project; **backup accounts** take over when one hits its usage limit |
| **Telegram** | The bot (above) |

**Execution modes:** **classic** (each slot takes a task to the end), **assembly line** (each agent is a station with
a queue: the executor hands off to the reviewer and picks up the next one) and **Waves**. **⏸ Pausar tudo** (Pause
all) stops every agent at once; **▶ Retomar** (Resume) continues where they left off.

## Other ways to use it

- **Browser:** while the app is open, the board is also at `http://127.0.0.1:4747`.
- **Claude Code / VS Code (MCP):**
  `claude mcp add --transport http crew http://127.0.0.1:4747/mcp` — see the board, create requests, answer and
  approve without leaving the editor.

## Updates

The app checks for a new version on launch and every 6 hours.

- **Windows and AppImage:** downloads in the background and asks **Restart?**; if you postpone, it installs when you
  quit. Agents running at that moment become ⛔ interrupted and come back with **Resume**.
- **Mac and .deb:** tells you about the new version and opens this page for you to download.

## Troubleshooting

- **Port 4747 in use:** the app picks another free port by itself.
- **Windows app and CAB on WSL on the same machine:** whichever starts first gets `localhost:4747`. Use one at a time.
- **Emoji shown as boxes on Linux/WSL:** `sudo apt install fonts-noto-color-emoji`.
- **Running tasks on native Windows** is not validated yet; WSL and Linux are well tested.

---

© SArgita · [AGPL-3.0](https://www.gnu.org/licenses/agpl-3.0.html)
