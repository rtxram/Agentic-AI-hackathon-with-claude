# /generate — AgentForge Entry Point

## Purpose
This is the **single activation command** for the entire AgentForge multi-agent system.
One prompt. One command. A complete application.

## Usage
```
/generate "<high-level product description>"
```

## Arguments
- `$ARGUMENTS` — The full natural language product description from the user

---

## Execution Protocol

You are the **Orchestrator Agent**. Execute the following pipeline exactly.

### STEP 1 — Requirement Extraction

Analyze `$ARGUMENTS` and extract:

1. **Core purpose** — What problem does this solve?
2. **User types** — Who are the actors? (admin, user, guest, etc.)
3. **Feature list** — All explicit and implied features
4. **Data entities** — All nouns that need to be persisted
5. **Integration needs** — Payments, email, real-time, AI, etc.
6. **Scale hints** — Is this a solo tool, team product, or marketplace?

Use `mcp__sequential-thinking__sequentialthinking` to reason through ambiguous requirements before proceeding.

### STEP 2 — Tech Stack Selection

Apply the Stack Decision Matrix from `CLAUDE.md` to select:
- Frontend framework
- Backend framework  
- Database + ORM
- Auth provider
- Any additional services

Document your reasoning briefly in `AGENT_LOG.md`.

### STEP 3 — Generate PROJECT_MANIFEST

Create `./output/[project-slug]/PROJECT_MANIFEST.json` following the schema in `CLAUDE.md` exactly.

This manifest is the **shared contract** between all agents. It must be complete before any agent is spawned.

Validate that:
- Every feature maps to at least one agent
- Every entity has complete field definitions
- Every page has its API calls listed
- Every API route has request/response schemas

### STEP 4 — Create Output Directory Structure

```bash
mkdir -p ./output/[project-slug]/{frontend,backend,database,docs}
```

Use `mcp__filesystem__create_directory` for each required path.

### STEP 5 — Spawn Sub-Agents in Parallel

Launch ALL five agents simultaneously using the **Task tool**. Pass each agent:
1. The full contents of `PROJECT_MANIFEST.json`
2. Their specific role instructions (from `.claude/commands/[agent].md`)
3. The output path they should write to

**Spawn these Tasks concurrently:**

```
Task 1: Architect Agent    → .claude/commands/architect.md
Task 2: Frontend Agent     → .claude/commands/frontend.md  
Task 3: Backend Agent      → .claude/commands/backend.md
Task 4: Database Agent     → .claude/commands/database.md
Task 5: DevOps Agent       → .claude/commands/devops.md
```

Wait for ALL tasks to complete before proceeding.

### STEP 6 — Integration Resolution

After all agents complete, perform cross-agent validation:

1. **API Contract Check** — Compare frontend API calls against backend routes
   - Any mismatch → fix the backend route to match the contract
2. **Type Sync** — Ensure TypeScript types are consistent across frontend/backend
   - Generate `./output/[project-slug]/shared/types.ts` with all shared types
3. **Env Var Audit** — Collect all env vars referenced by all agents
   - Merge into a single `.env.example` at the root

### STEP 7 — QA Pass

Spawn the **QA Agent** as a Task:
```
Task: QA Agent → .claude/commands/qa.md
```

The QA agent will review all generated code and return a list of issues.
Apply all P0 (critical) fixes before proceeding.

### STEP 8 — Final Assembly

Generate these root-level files:

**`README.md`** — Must include:
- Project overview
- Architecture diagram (ASCII)
- Prerequisites
- Setup instructions (`git clone`, `npm install`, env setup)
- Development commands
- Deployment guide
- Agent generation log summary

**`AGENT_LOG.md`** — Complete trace of:
- Orchestrator decisions
- Each agent's output summary
- QA findings and resolutions
- Total files generated

### STEP 9 — GitHub Push (if GITHUB_TOKEN is set)

```
mcp__github__create_repository → create [project-slug] repo
mcp__github__push_files → push entire ./output/[project-slug]/ 
mcp__github__create_pull_request → open PR titled "feat: initial generation by AgentForge"
```

### STEP 10 — Summary Report

Output a final summary to the user:

```
✅ AgentForge Generation Complete
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Project:     [name]
Stack:       [frontend] + [backend] + [database]
Features:    [count] implemented
Files:       [count] generated
Agents:      5 specialized + 1 QA
Duration:    [time]

📁 Output:   ./output/[project-slug]/
🐙 GitHub:   [repo URL or "not configured"]

Key files:
  → README.md (setup instructions)
  → AGENT_LOG.md (full agent trace)
  → PROJECT_MANIFEST.json (system contract)
```
