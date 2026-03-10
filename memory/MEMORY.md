# MEMORY.md

## Project

This repo is a personal Claude Code configuration kit adapted from Boris Cherny's setup for a bioinformatics/ML biopharma workflow on an M1 Pro Mac (16 GB RAM).

Remote: https://github.com/ChristopherSNelson/bcherny-claude.git

## Slash commands available

| Command | Purpose |
|---|---|
| `/quick-commit` | Stage all and commit |
| `/push` | Push to origin, no PR |
| `/commit-push-pr` | Commit, push, open PR |
| `/shutdown` | End-of-session procedure |
| `/review-changes` | Review uncommitted changes |
| `/test-and-fix` | Run tests and fix failures |
| `/grill` | Adversarial code review |
| `/techdebt` | Find and kill dead code |
| `/worktree` | Set up parallel git worktree |

## Conventions

- Co-author trailers on all commits: user (`git config user.name/email`) + `Claude <noreply@anthropic.com>`
- Git identity fallback: if unset, use `whoami@hostname` silently
- No mid-sentence bold in written artifacts (README, comments, CLAUDE.md prose)
- Nextflow checks are conditional on `*.nf` files existing
- Conventional commit prefixes: `feat:`, `fix:`, `refactor:`, `data:`, `pipeline:`, `chore:`, `docs:`

## User preferences

- Prefers concise, direct responses
- No emojis
- Corrections should be applied immediately to CLAUDE.md/memory
