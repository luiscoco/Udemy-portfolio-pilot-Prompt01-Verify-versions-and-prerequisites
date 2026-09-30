# PortfolioPilot — Prompt 00: Establish the Project Contract

PortfolioPilot is a stock portfolio manager with a live news feed, portfolio-aware AI chat, cited
news analysis, research recommendations, watchlists, and alerts. Its AI features run on the
**Claude Agent SDK**. During this course you build the application step by step, pasting one prompt
at a time into a coding assistant in VS Code.

This README covers the **first learning activity, Prompt 00**.

> **Current status:** Prompt 00 is complete. The repository contains documentation only: the
> project contract, plan, progress tracker, and decision records. **There is no application code
> yet**, so there is nothing to install or run. Milestone 36 will replace this README with the full
> application guide.

---

## 1. Purpose

### What this prompt asks the coding agent to do

Prompt 00 asks the coding agent to **write down the rules and the plan before writing any code**.
It must:

1. Describe the **product**: what PortfolioPilot does and what is out of scope. For example, there
   are no real broker trades, no short selling, and no currency conversion.
2. Record the **required architecture**: which folder holds which part of the system (explained
   below).
3. Record **11 implementation rules** that every later prompt must follow.
4. Create a **36-milestone plan**, one milestone for each later prompt.
5. Create a **state file** that tracks what is actually finished and verified.
6. **Stop** without scaffolding any application code.

### Why it matters

A coding assistant only remembers the current conversation. When you open a new chat, switch
between Claude and GPT, or come back the next day, it has forgotten your decisions. Without written
rules, assistants tend to:

- invent package versions or SDK methods that do not exist,
- put secret keys where the browser can see them,
- build a second frontend when you wanted only one,
- mark work "done" without testing it.

Prompt 00 solves this by storing the rules **in the repository**, where every assistant and every
future session reads them.

> **Rule of thumb:** the repository is the memory, not the chat.

### What you will learn

- How to give an AI coding assistant a **project contract**: one written set of rules it must follow.
- Why you should keep **one** rule file and point other assistant files to it instead of copying it.
- How to split a large project into **small, verifiable milestones**.
- How to keep an honest **state file** that records real test results, not claims.
- How to record important choices as **Architecture Decision Records (ADRs)**.

### Key terms

| Term | Meaning |
| --- | --- |
| **Project contract** | A file of rules the coding assistant must follow on every task. Here it is `AGENTS.md`. |
| **Milestone** | A small, finished piece of work with clear acceptance criteria (conditions that must be true before it counts as done). |
| **ADR (Architecture Decision Record)** | A short document explaining one important technical decision: the context, the choice, and the alternatives. |
| **Monorepo / npm workspaces** | One repository holding several apps and packages, managed together by npm. |
| **SPA (Single-Page Application)** | A browser app, here React + Vite, that loads once and then talks to an API. |
| **Route Handler** | A Next.js file that answers HTTP requests such as `GET /api/health/live`. We use Next.js only for these, with no pages. |
| **SSE (Server-Sent Events)** | A simple way for the server to push live updates, such as new news, to the browser over HTTP. |
| **Mock mode** | Running with fake, predictable (deterministic) data so the app works without paid API keys. |

### The architecture being described

The contract describes this planned structure. **None of these folders exist yet**; they are created
from Milestone 02 onward.

```text
apps/web               React 19.3+ / Vite / TypeScript — the ONLY frontend
apps/api               Next.js Route Handlers (Node.js runtime) — backend API only
apps/worker            Background jobs: news ingestion, event delivery, AI agent runs
packages/contracts     Shared Zod schemas and browser-safe data types
packages/domain        Pure portfolio math (cost basis, gains, allocation)
packages/db            Prisma schema, migrations, database access
packages/providers     Stock quote and news adapters, plus mock versions
packages/agent         Claude Agent SDK integration (server-only)
packages/config        Validated configuration
packages/observability Logging, tracing, metrics
```

PostgreSQL is the **source of truth** for all data. Redis is used only for caching and live event
streams. The app will be deployed to **Azure Kubernetes Service (AKS)**.

---

## 2. Steps performed

These are the steps the coding agent actually carried out, in order.

### Step 1 — Inspect the workspace

The contract forbids overwriting existing instruction files, so the agent checked the workspace and
the installed tools first:

```bash
ls -la
node -v
npm -v
git --version
```

**Observed result:** the `portfolio-pilot` folder was empty, and it was not yet a git repository.

```text
v24.21.0      # Node.js
11.19.0       # npm
git version 2.52.0.windows.1
```

### Step 2 — Look for instructions in the parent folder

The agent listed the parent folder and found the course prompt pack,
`PortfolioPilot_Complete_VSCode_Prompt_Pack.docx`. A `.docx` file is a ZIP archive, so the agent
copied it to a temporary folder, unzipped it, and pulled out the text with a short Python script.
That gave it the full list of Prompts 01–36.

**Key decision:** the plan's milestone numbers and titles were matched exactly to the prompt pack.
When you paste "Prompt 12" later, it lines up with "Milestone 12" in the plan.

The temporary copy was deleted afterwards. The original `.docx` was not modified.

### Step 3 — Write the project contract

The agent created two files:

- **`AGENTS.md`**: the full contract, covering product scope, the architecture table, the 11 rules,
  documentation conventions, and the completion report format.
- **`CLAUDE.md`**: only two lines, telling Claude to read `AGENTS.md` and `docs/project-state.md`.

**Key decision:** the rules were written **once**. If `CLAUDE.md` had a copy, the two versions would
drift apart over time and the assistant would get conflicting instructions.

### Step 4 — Write the plan, state, and decision records

| File | What it contains |
| --- | --- |
| `docs/project-plan.md` | 36 milestones in nine phases (A–I). Each lists its scope and an **Accept** line. |
| `docs/project-state.md` | A status table for all milestones, the tools found on this machine, and the latest completion report. |
| `docs/decisions/README.md` | How to write ADRs, a template, an index, and a list of decisions expected later. |
| `docs/decisions/0001-architecture-baseline.md` | The first ADR: why there is one React frontend, a separate worker, PostgreSQL as the source of truth, and SSE rather than WebSockets. |
| `docs/lessons/00-project-contract.md` | Teaching notes: key ideas, an instructor demo, a student exercise, and a common mistake. |

**Key decision:** the ADR and lesson notes were not explicitly requested. They were added because
later prompts say "update the architecture ADR" and "update the lesson notes", so they need to exist
from the start.

### Step 5 — Verify the result

The agent listed the created files and counted the milestone headings in the plan:

```bash
find . -type f | sort
grep -c '^### [0-9][0-9] ·' docs/project-plan.md
```

**Observed output:**

```text
./AGENTS.md
./CLAUDE.md
./docs/decisions/0001-architecture-baseline.md
./docs/decisions/README.md
./docs/lessons/00-project-contract.md
./docs/project-plan.md
./docs/project-state.md
36
```

### Step 6 — Report and stop

The agent wrote the five-part **completion report** (see Section 3) and did **not** start Milestone
01. Stopping after one slice is part of the contract.

### Step 7 — Follow-up requests (after the prompt)

After Prompt 00 finished, two more requests were made in the same session:

1. **Add this README** to explain the activity to students.
2. **Commit and push.** The instructor had already created the git repository, made a first commit,
   and connected it to GitHub. The agent corrected two outdated "not a git repository" notes in the
   docs, then ran:

```bash
git add README.md docs/project-state.md docs/lessons/00-project-contract.md
git commit -m "Add student README for Prompt 00 and update project state"
git push origin main
```

**Observed output:**

```text
To https://github.com/luiscoco/Udemy-portfolio-pilot-Prompt-00-Establish-the-project-contract.git
   9949499..f1bd27d  main -> main
```

---

## 3. Results achieved

### Files in the repository

```text
portfolio-pilot/
├── AGENTS.md                              ← project contract (the only rule set)
├── CLAUDE.md                              ← pointer: read AGENTS.md + project-state.md
├── README.md                              ← this file
└── docs/
    ├── project-plan.md                    ← 36 milestones with acceptance criteria
    ├── project-state.md                   ← what is done and verified, and what comes next
    ├── decisions/
    │   ├── README.md                      ← ADR template + index
    │   └── 0001-architecture-baseline.md  ← first architecture decision
    └── lessons/
        └── 00-project-contract.md         ← teaching notes for this lesson
```

### What the "application" does now

There is **no running application yet**. What you have is a set of instructions that changes how
your coding assistant behaves:

- **Expected behavior:** any assistant opened in this folder reads `CLAUDE.md` or `AGENTS.md`,
  learns the rules, checks `docs/project-state.md`, and knows the next task is **Milestone 01 —
  Verify versions and prerequisites**.
- **Observed:** this expected behavior has **not** been formally tested in a fresh session yet. Try
  it yourself using Section 4.

### Example: one of the 11 rules

From `AGENTS.md`:

```text
5. Ownership. Enforce ownership in repositories, APIs, streams, and agent tools. Derive the user
   from the authenticated session — never from model-supplied or browser-supplied user IDs.
```

This means the AI assistant inside PortfolioPilot will never be trusted to say whose portfolio it is
reading. The server decides that from the login session.

### Example: one milestone from the plan

From `docs/project-plan.md`:

```text
### 09 · Implement valuation and performance calculations
- Reference case: buy 10 @ $100 fee $2; buy 5 @ $120 fee $1; sell 6 @ $130 fee $3
  → basis $1,603 / 15 sh before sale; sold basis $641.20; realized $135.80; ...
- Accept: tests for the reference case, full liquidation, fractional shares, missing quotes, ...
```

Every milestone has a clear **Accept** line, so you know exactly when it is finished.

### The completion report for Prompt 00

Every milestone ends with the same five sections. Prompt 00's report was:

| Section | Content |
| --- | --- |
| What works | Contract, plan, state tracker, ADR index, and lesson notes exist. No application code, as intended. |
| Changed files | The seven documentation files listed above (the README was added afterwards). |
| Check results | Workspace confirmed empty beforehand; plan confirmed to have 36 milestones. No build or tests exist yet. |
| How to demonstrate | Ask a fresh assistant session for the next milestone (see Section 4). |
| Remaining limitations | Not a git repository at the time (since resolved); no versions pinned yet. |

---

## 4. How to run and verify

### Prerequisites

- **Git**, to clone the repository.
- **VS Code** with a coding assistant extension (Claude Code or another).
- Node.js, Docker, and API keys are **not** needed for this activity.

### Get the files

```bash
git clone https://github.com/luiscoco/Udemy-portfolio-pilot-Prompt-00-Establish-the-project-contract.git portfolio-pilot
cd portfolio-pilot
```

### Check that all files exist

Linux, macOS, or Git Bash:

```bash
find . -type f -not -path './.git/*' | sort
```

Windows PowerShell:

```powershell
Get-ChildItem -Recurse -File | Where-Object FullName -notmatch '\\.git\\' | Select-Object -ExpandProperty FullName
```

**Expected:** the eight files shown in Section 3.

### Check that the plan has 36 milestones

Linux, macOS, or Git Bash:

```bash
grep -c '^### [0-9][0-9] ·' docs/project-plan.md
```

Windows PowerShell:

```powershell
(Select-String -Path docs/project-plan.md -Pattern '^### \d\d ·').Count
```

**Expected output:** `36`

### Check that the contract works (the real test)

1. Open the `portfolio-pilot` folder in VS Code.
2. Start a **new** chat with your coding assistant. Do not explain anything about the project.
3. Ask:

   ```text
   What is the next milestone, and what must be true for it to be accepted?
   ```

4. **Expected answer:** the assistant reads `CLAUDE.md` → `AGENTS.md` → `docs/project-state.md` and
   replies **"01 — Verify versions and prerequisites"**, followed by that milestone's acceptance
   criteria (a documented compatible version set, no invented versions, secrets excluded).

If it answers correctly without your help, the contract is working.

### Exercise

Find the rule in `AGENTS.md` that says never to trust a user ID supplied by the model. Then find
Milestones 07, 17, 25, and 29 in `docs/project-plan.md` and explain how each one enforces it.

### Common mistakes

- **Pasting the whole prompt pack at once.** Run one prompt per milestone and verify it before
  continuing.
- **Adding rules to `CLAUDE.md`.** Project rules belong only in `AGENTS.md`.
- **Trusting "done" without evidence.** `project-state.md` should contain the commands actually run
  and their real results.

---

## 5. Limitations and unfinished work

| Item | Status |
| --- | --- |
| Application code | **Not started.** Intentional: Prompt 00 creates documentation only. Scaffolding begins in Milestone 02. |
| Package versions | **Not chosen yet.** No versions are pinned; that is Milestone 01. |
| `.gitignore` | **Missing.** Planned for Milestone 01. Safe for now because the repository contains only documentation and no secrets. |
| Docker | **Not checked.** Checked in Milestone 01. |
| Node.js version suitability | v24.21.0 was recorded but **not yet confirmed** as the project's supported LTS version (Milestone 01). |
| Fresh-session contract test (Section 4) | **Not yet performed.** The expected behavior is described, not observed. |
| Line endings | On `git add`, Git warned that it will convert files from LF (Linux-style) to CRLF (Windows-style) line endings. Harmless, but a `.gitattributes` file should standardize line endings (suggested for Milestone 01). |
| Build, lint, tests | **None exist yet**, so none were run. |

---

## What comes next

Paste **Prompt 01 — Verify versions and prerequisites**. The assistant will choose a compatible,
pinned set of versions (React 19.3+, Vite, Next.js, Prisma, Claude Agent SDK, and others). It will
document them in `docs/versions.md` and add a `.gitignore`, still without writing any application
code.
