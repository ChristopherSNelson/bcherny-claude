---
description: "End-of-session procedure. Run this before closing Claude Code."
---

## Steps

1. Write `HANDOFF.md` in the project root with:
   - What was done this session (bullet list)
   - What's left / next steps (in priority order)
   - Key decisions made and why
   - Current blockers
   - If `*.nf` files exist in the project, check for active pipeline runs (`pgrep -a -f nextflow`) and note any PIDs, sample sheet, outdir, expected duration

2. Propose any additions to the Mistakes Log in `CLAUDE.md` from lessons learned this session.

3. Update `memory/MEMORY.md` with anything that should persist across sessions.

4. Stage and commit all modified files (excluding ignored paths). First check git identity: if `git config user.name` or `git config user.email` are unset, set them with `git config user.name "$(whoami)"` and `git config user.email "$(whoami)@$(hostname)"`. Include both the user and Claude as co-authors:
   ```
   Co-Authored-By: <git config user.name> <git config user.email>
   Co-Authored-By: Claude <noreply@anthropic.com>
   ```

5. Push to origin.

6. Print a one-line summary of the session for the user.
