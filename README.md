# PortfolioPilot — Learning activities 00 and 01

PortfolioPilot is a planned stock portfolio manager with holdings, live news, watchlists, alerts, and portfolio-aware AI chat. Its application runtime will use the Claude Agent SDK. The course builds it one milestone at a time. **There is no runnable application yet.**

Activity 00 established the [project contract](AGENTS.md), [36-milestone plan](docs/project-plan.md), [progress record](docs/project-state.md), and [first architecture decision](docs/decisions/0001-architecture-baseline.md). Activity 01, covered below, established the toolchain before application code is generated. The current next milestone is **02 — Scaffold the monorepo and shared contracts**.

## 1. Purpose: choose tools before writing code

Milestone 01 asks the coding agent to inspect the computer and workspace, confirm that the requested libraries have real stable releases, and select versions that can work together. It also asks for files that record the chosen Node.js version and keep secrets and generated files out of Git.

This matters because a package can exist but require a newer Node.js release, or reject another package's version. Installing whatever is newest can also select a prerelease. Students learn to check **engine requirements** (the Node.js versions a package supports), **peer dependencies** (versions of another package it expects the project to supply), and official release status before installing anything. A **lockfile** is the npm file that later records exact installed packages, including indirect dependencies.

Activity 00 introduced a second lesson: store project rules in [AGENTS.md](AGENTS.md) so a new coding-assistant session can read them. [CLAUDE.md](CLAUDE.md) points to that one rule set. The planned architecture has one React/Vite frontend in `apps/web`, a Next.js API in `apps/api`, a background worker, and shared packages. PostgreSQL will hold durable data; Redis will support caching and event streams. These folders and services have **not** been built yet.

## 2. Steps performed

The agent performed the following steps for Milestone 01, in order:

1. Read `AGENTS.md`, `docs/project-state.md`, and the Milestone 01 acceptance criteria in `docs/project-plan.md`.
2. Checked the workspace and local tools with `node --version`, `npm --version`, `git --version`, `docker --version`, `docker compose version`, `docker info`, `nvm list`, and `git status --short`. At that time the workspace contained documentation but no application packages.
3. Checked npm package listings and official Node.js, React, Next.js, Vite, Prisma, Anthropic, Vitest, and Playwright references. React 19.3.0 was confirmed as stable. Prisma 8 was a release candidate at the time, so the agent selected stable Prisma 7.10.0. It paired matching React/React DOM and Prisma CLI/client versions, and chose TypeScript 5.9.3 following Prisma 7 guidance.
4. Created [docs/versions.md](docs/versions.md) with the exact version set, compatibility reasons, verification date, and source links.
5. Created `.nvmrc` for Node 24.21.0, `.npmrc` for strict engine checks and exact saved versions, and `package.json` with Node/npm engine and package-manager metadata. Created `.gitignore` for `.env` secrets, SDK transcripts, dependencies, generated output, and local database files.
6. Added [lesson notes](docs/lessons/01-toolchain.md) and updated [project state](docs/project-state.md) with the checks that passed, failed, or were skipped. Stopped before scaffolding the application.

No global tools or project dependencies were installed. No package lockfile was generated.

## 3. Results achieved

The selected direct versions are recorded below. These are **documented selections**, not a locally installed or tested dependency graph.

| Tool or package | Selected version |
| --- | --- |
| Node.js LTS / npm | 24.21.0 / 11.19.0 |
| React / React DOM | 19.3.0 / 19.3.0 |
| Vite / TypeScript / Next.js | 8.3.1 / 5.9.3 / 16.3.8 |
| Prisma / `@prisma/client` | 7.10.0 / 7.10.0 |
| Claude Agent SDK / Zod | 0.3.276 / 4.6.4 |
| Vitest / Playwright Test | 5.0.0 / 1.63.0 |

For example, `package.json` now declares the required Node and npm ranges:

```json
{
  "engines": {
    "node": ">=24.21.0 <25",
    "npm": ">=11.19.0 <12"
  },
  "packageManager": "npm@11.19.0"
}
```

**Observed behavior:** `package.json` parsed as valid JSON, the metadata matched the selected versions, and the expected ignore patterns were present. `git --version`, `docker --version`, and `docker compose version` returned versions. `nvm list` showed Node 24.21.0 installed. There is no UI, API endpoint, database, or test suite to run yet.

**Expected later behavior:** once Milestone 02 creates workspace package manifests and npm can reach the registry, npm should resolve these pinned dependencies, write `package-lock.json`, and report their installed versions. That has not been verified.

## 4. How to run and verify

### Prerequisites

To review this activity, you only need the files. To repeat local tool checks, use PowerShell on Windows or a shell in Linux/WSL2. Node.js 24.21.0 is the selected LTS release; this Windows environment listed it under `nvm` but could not activate it during Milestone 01. Docker and API credentials are **not required** to review the toolchain work.

### Check the files on Windows PowerShell

Run these commands from the PortfolioPilot folder:

```powershell
Get-Content .nvmrc
Get-Content .npmrc
Get-Content package.json -Raw | ConvertFrom-Json | Select-Object name,packageManager,engines
Get-Content docs/versions.md -TotalCount 35
Get-Content .gitignore
```

Expected values include `24.21.0` in `.nvmrc`, `npm@11.19.0` in `package.json`, and `.env` plus `sdk-transcripts/` in `.gitignore`. The agent observed the JSON parse and the ignore-pattern presence check passing.

Try activating the already listed Node version and checking the tools:

```powershell
nvm use 24.21.0
node --version
npm --version
git --version
docker --version
docker compose version
docker info --format '{{.ServerVersion}}'
```

During Milestone 01, `node --version` and `npm --version` failed with “No active Node.js version is configured.” The Docker CLI and Compose checks succeeded, while `docker info` failed with access denied to the Docker configuration or engine pipe. These commands are a **retry procedure**, not a claim that activation or Docker now works.

### Check the files on Linux or WSL2

With `nvm-sh` already installed, run:

```bash
cat .nvmrc .npmrc
cat package.json
sed -n '1,35p' docs/versions.md
cat .gitignore
nvm install 24.21.0
nvm use 24.21.0
node --version
npm --version
```

The Linux/WSL2 path was documented but **not executed** in Milestone 01. If you do not have `nvm-sh`, use the [official Node.js 24.21.0 download](https://nodejs.org/en/download/archive/v24.21.0). Do not install a global tool solely for this lesson.

### Verify dependency resolution later

These commands are for **after Milestone 02** creates the app and package manifests and Node/npm can reach the npm registry:

```powershell
npm install --package-lock-only --ignore-scripts
npm install
npm ls react react-dom vite typescript next prisma @prisma/client @anthropic-ai/claude-agent-sdk zod vitest @playwright/test
```

They were **not run** in Milestone 01. Inspect the installed Claude Agent SDK type definitions before using its APIs. If npm reports a peer conflict, resolve it and update `docs/versions.md`; do not hide it with `--legacy-peer-deps`.

## 5. Limitations and unfinished work

- Node/npm were inactive in the Milestone 01 PowerShell session, and its direct request to the npm registry failed. No local dependency resolution or lockfile exists yet.
- Docker CLI and Compose were present, but access to the Docker engine was denied. This did not block toolchain documentation.
- The Milestone 01 `git status --short` check reported that the directory was not a Git repository. During this README task, `.git` is present and Git reports that the previously tracked README had been deleted; this file restores the useful project introduction and updates it for Milestone 01. No commit or push is part of this activity.
- There is no application code or runtime behavior yet. Build, lint, and application tests were not available to run. SDK installed types and the complete installed peer-dependency graph remain unverified.
- A fresh-session check that another coding assistant reads the contract has not been formally run. The old Prompt 00 README proposed it as an exercise, not an observed result.

For the full check record and exact next step, read [docs/project-state.md](docs/project-state.md). For version evidence, read [docs/versions.md](docs/versions.md).
