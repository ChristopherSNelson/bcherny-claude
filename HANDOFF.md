# HANDOFF.md

## What was done this session

- Added `/shutdown` slash command (`shutdown.md`) — end-of-session procedure covering HANDOFF.md, Mistakes Log, memory update, commit, and push
- Added `/push` slash command (`push.md`) — push current branch to origin without opening a PR
- Added co-author trailer convention to all commit commands (`quick-commit.md`, `commit-push-pr.md`, `shutdown.md`) — both user and Claude listed
- Added git identity fallback to commit commands — if `user.name`/`user.email` unset, silently use `whoami@hostname`
- Added "Presentation style" section to `CLAUDE.md` — no mid-sentence bold in written artifacts
- Made Nextflow pipeline-run check conditional on `*.nf` files existing in the project
- Registered `/shutdown` and `/push` in the CLAUDE.md command table
- Updated Claude co-author trailer from `Claude Sonnet 4.6` to `Claude` (model-agnostic)

## What's left / next steps

- Consider setting `git config --global user.name` and `user.email` explicitly on this machine to get a clean identity in commits (currently falling back to auto-detect)
- No other open tasks from this session

## Key decisions

- Co-author trailer uses `Claude <noreply@anthropic.com>` (no version) — won't go stale when model changes
- Git identity fallback uses `whoami@hostname` silently rather than warning the user
- Nextflow checks are conditional throughout to avoid noise on non-pipeline projects

## Current blockers

None.

## Active pipeline runs

No `*.nf` files in this project — no pipeline check needed.
