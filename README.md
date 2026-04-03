# Claude Code Setup for Bioinformatics & ML/AI in Biopharma

> Forked from [0xquinto/bcherny-claude](https://github.com/0xquinto/bcherny-claude), which reconstructed
> [Boris Cherny's](https://x.com/bcherny/status/2007179832300581177) Claude Code configuration.
> Boris is the creator of Claude Code at Anthropic.

This fork adapts the setup for computational biology and ML/AI work in biopharma,
running on an Apple Silicon MacBook with a Claude Pro subscription.

---

## What changed from the original

| Area | Original (Boris) | This fork |
|------|-------------------|-----------|
| **Target domain** | General TypeScript/web dev | Bioinformatics pipelines, ML for life sciences |
| **Hardware** | Unlimited (Anthropic internal) | M1 Pro, 16 GB RAM — memory-aware rules |
| **Account** | Internal/unlimited | Claude Pro — usage economy section, Opus/Sonnet routing |
| **CLAUDE.md voice** | Mixed human/agent | Split: `[AGENT RULES]` (for Claude) vs `[OPERATOR NOTES]` (for you) |
| **Slash commands** | Documented as Claude-callable | Correctly marked as user-invoked only |
| **MCP servers** | Not addressed | On-demand creation via `mcp-maker` skill, no preloaded MCPs |
| **Verification loop** | `npm test / typecheck / lint` | `pytest / ruff / mypy / nextflow -profile test` |
| **Guardrails** | Not present | Claude proactively warns about context bloat, scope creep, memory limits |

## What's included

### CLAUDE.md

The main config file Claude reads on startup. Sections cover:

- **Environment constraints** — ARM/Apple Silicon awareness, 16 GB memory discipline, Docker rules
- **`.claude/` directory manifest** — Documents everything in the config directory so Claude knows what's available
- **Domain rules** — Bioinformatics (file formats, genome builds, tool preferences) and ML/AI (PyTorch/MPS, model size limits, data leakage prevention)
- **Coding standards** — Python, R, Nextflow/Snakemake conventions
- **Verification loops** — What Claude must run before declaring a task complete
- **Workflow philosophy** — Plan-first approach, handoff discipline, subagent guidelines
- **Biopharma context** — Audience calibration, therapeutic mechanism precision, HIPAA/GxP defaults
- **MCP server policy** — On-demand creation, no preloaded overhead
- **Usage economy** — Token-saving rules, Opus/Sonnet model routing, session management
- **Guardrail alerts** — Claude proactively flags context bloat, complexity, resource limits, diminishing returns
- **Mistakes log** — Append-only log of corrections (Claude proposes entries)
- **Project-specific overrides** — Fill in per-project (genome build, workflow engine, primary language)

### Slash commands (`.claude/commands/`)

User-invoked shortcuts. Claude cannot call these — it suggests them by name when relevant.

| Command | Description |
|---------|-------------|
| `/commit-push-pr` | Commit, push, and open a PR |
| `/quick-commit` | Stage all changes and commit with a descriptive message |
| `/test-and-fix` | Run tests and fix any failures |
| `/review-changes` | Review uncommitted changes and suggest improvements |
| `/grill` | Adversarial code review |
| `/biogrill` | Adversarial biological code review - challenges every biological assumption (magic numbers, coordinate systems, assay artifacts, normalization, filtering logic, identifier consistency). Rates each assumption VALID/QUESTIONABLE/WRONG. |
| `/techdebt` | Codebase cleanup |
| `/worktree` | Set up git worktrees for parallel sessions |

### Subagents (`.claude/agents/`)

Claude can delegate to these. Limited to 1 concurrent on this machine.

| Agent | Purpose |
|-------|---------|
| `code-simplifier` | Simplify and clean up code after implementation |
| `code-architect` | Design reviews and architectural decisions |
| `verify-app` | Thorough end-to-end testing |
| `build-validator` | Ensure project builds for deployment |
| `oncall-guide` | Diagnose and resolve production issues |

### Skills (`.claude/skills/`)

| Skill | Purpose |
|-------|---------|
| `mcp-maker` | Scaffold, register, and test a new MCP server on demand. Includes a FastMCP template and step-by-step workflow. |

### MCP servers (`.claude/mcp-servers/`)

Empty on clone. This is where `mcp-maker` puts the servers it creates. Commit reusable ones, delete disposable ones.

### Settings (`.claude/settings.json`)

Pre-allowed permissions for the bioinformatics CLI stack (`git`, `conda`, `samtools`, `bcftools`, `nextflow`, `docker`, etc.) and a PostToolUse hook that runs `ruff format` on any Python file Claude writes.

---

## Prerequisites

Install these before using:

```bash
# Required
brew install uv               # Python package runner (needed for mcp-maker)
brew install ruff              # Python linter/formatter (used by settings.json hook)
brew install gh                # GitHub CLI (used by /commit-push-pr)

# Your bioinformatics stack (install as needed)
# conda/mamba, nextflow, docker, samtools, bcftools, bedtools, etc.
```

## Quick start

### One-time setup: clone the fork as a template source

```bash
git clone https://github.com/YOUR_USER/bcherny-claude.git ~/claude-template
```

Keep this as a read-only source. Never `cd` into it and work directly — never `rm -rf .git` inside it. You always copy *from* it into new projects.

### Start a new project

```bash
mkdir ~/my-new-project
cd ~/my-new-project
git init
git commit --allow-empty -m "init"
cp -r ~/claude-template/.claude .
cp ~/claude-template/CLAUDE.md .
git add -A
git commit -m "feat: add claude code config"
# Edit the "Project-specific overrides" section at the bottom of CLAUDE.md
claude
```

The empty initial commit matters — Claude Code expects a valid git HEAD. Without it you can get cryptic startup errors.

### Add to an existing project

```bash
cd ~/my-existing-project
cp -r ~/claude-template/.claude .
cp ~/claude-template/CLAUDE.md .
git add -A
git commit -m "feat: add claude code config"
# Edit project-specific overrides
claude
```

### Shell alias (optional)

Add to `~/.zshrc`:

```bash
new-claude-project() {
  mkdir -p "$1" && cd "$1" && git init && git commit --allow-empty -m "init" \
    && cp -r ~/claude-template/.claude . && cp ~/claude-template/CLAUDE.md . \
    && git add -A && git commit -m "feat: add claude code config" \
    && echo "Ready. Edit CLAUDE.md project overrides, then run: claude"
}
```

Then: `new-claude-project my-methylation-pipeline`

## Tips for daily use

**You don't need to explain the setup to Claude.** It reads `CLAUDE.md` automatically on startup. Just describe your task.

**Use Opus for planning, Sonnet for execution.** Start in plan mode (shift+tab twice), iterate on the plan, then switch to Sonnet (`/model sonnet`) for auto-accept execution. This stretches your Pro account 3–5x.

**Start fresh often.** After ~15 turns, or when you finish a unit of work, ask Claude to write a `HANDOFF.md` and start a new session. Claude picks up from the handoff without re-paying for old context.

**Let Claude warn you.** The guardrail alerts are there so Claude flags problems proactively — context bloat, memory pressure, scope creep, debug loops going nowhere. Trust the alerts.

**Install the life sciences plugins if you want them:**

```bash
# In Claude Code
/plugin marketplace add anthropics/life-sciences
/plugin install pubmed@life-sciences
/plugin install nextflow-development@life-sciences
```

**Update CLAUDE.md when Claude makes mistakes.** End corrections with: "Now update CLAUDE.md so you don't make that mistake again." This is the single highest-leverage habit.

---

## Original source

Based on: [Boris Cherny's X thread](https://x.com/bcherny/status/2007179832300581177) via [0xquinto/bcherny-claude](https://github.com/0xquinto/bcherny-claude)

Anthropic life sciences marketplace: [anthropics/life-sciences](https://github.com/anthropics/life-sciences)
