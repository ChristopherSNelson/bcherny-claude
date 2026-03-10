---
description: "Commit, push, and open a PR"
---

Follow these steps in order:

1. Check git identity: if `git config user.name` or `git config user.email` are unset, configure them using `git config user.name "$(whoami)"` and `git config user.email "$(whoami)@$(hostname)"`.
2. Run `git status` to see what files have changed
3. Run `git diff` to review the changes
4. Stage the appropriate files with `git add`
5. Create a commit with a clear, descriptive message following conventional commits format. Include co-author trailers in the commit body — resolve the user's identity from `git config user.name` and `git config user.email`:
   ```
   Co-Authored-By: <git config user.name> <git config user.email>
   Co-Authored-By: Claude <noreply@anthropic.com>
   ```
6. Push to the remote branch (create remote branch if needed with `-u origin <branch>`)
7. Create a Pull Request using `gh pr create` with:
   - A clear title summarizing the changes
   - A description with:
     - Summary of what changed and why
     - Any testing done
     - Any notes for reviewers

If there are any issues at any step, stop and report them.
