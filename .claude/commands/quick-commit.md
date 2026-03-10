---
description: "Stage all changes and commit with a descriptive message"
---

1. Run `git status` to see the current state
2. Run `git diff` to understand the changes
3. Stage all changes with `git add -A`
4. Create a commit with a clear message that:
   - Starts with a type prefix (feat:, fix:, refactor:, docs:, test:, chore:)
   - Briefly describes what changed
   - Uses imperative mood ("Add feature" not "Added feature")

5. Include co-author trailers in the commit body. Resolve the user's identity from `git config user.name` and `git config user.email`:
   ```
   Co-Authored-By: <git config user.name> <git config user.email>
   Co-Authored-By: Claude <noreply@anthropic.com>
   ```

Example: `feat: add product search functionality`
