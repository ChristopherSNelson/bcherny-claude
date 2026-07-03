# /shutdown

End-of-session procedure. Run this before closing Claude Code.

## Steps

1. Update `HANDOFF.md` in the project root. **Do not overwrite blindly. The main risk is that unfinished items silently disappear.**

   **Step 1a - unfinished-items audit (do this BEFORE writing anything).**
   - Read the existing `HANDOFF.md` end-to-end.
   - Enumerate every item that represents unfinished state. Signals to look for:
     - Explicit `TODO(...)` markers
     - "User must do X" / "manual actions still needed" checklists
     - Roadmap items without a completion marker
     - Table rows marked "pending", "waiting", "TBD", "unverified", or with an unresolved access status
     - "Current blockers" list entries
     - "Explicitly cut for MVP" lists (deferred, not deleted)
     - `# verified` comments that are missing where an accession or number is asserted
   - For each enumerated item, decide: **(a)** resolved this session (mark done in-place with today's date), **(b)** still open (carry forward verbatim into the new HANDOFF), or **(c)** obsoleted by a decision this session (mark obsolete and cite the decision, do not silently delete).
   - If you cannot classify an item confidently, default to (b). Silent loss of an open TODO is the worst outcome; a stale item preserved is recoverable.

   **Step 1b - section classification.**
   - **Durable (preserve, targeted-edit only):** roadmaps, deadline/scope plans, substrate/data verification tables, load-bearing rules, manual-actions checklists the user still owes, "MVP explicitly cut" lists, "Sonnet must not do X" guardrails, prior-session archive blocks. Anything a next-session reader needs regardless of which day they open the file.
   - **Session-scoped (rotate):** "Current state (YYYY-MM-DD)" blocks, "Session deliverables" blocks, "This session's key decisions" blocks, "Current blockers" that only made sense this session.

   **Step 1c - write / rotate.**
   - Migrate durable content forward with targeted edits (fill in a verified accession, tick off a completed manual action, update a roadmap day). Do not delete durable sections.
   - Rotate the previous session's "Current state" / "Session deliverables" blocks into a **"Prior session (YYYY-MM-DD) notes preserved"** section at the bottom, keeping only the items still relevant. If a prior-session archive block already exists, append the new prior-session entry below it, or fold in items that have since been superseded.
   - Write a fresh **"Current state (today's date)"** block up top with:
     - What was done this session (bullet list)
     - What's left / next steps (in priority order)
     - Key decisions made this session and why
     - Current blockers (fresh list; do NOT delete unresolved blockers from prior sessions - carry them forward)
   - When in doubt whether a section is durable, preserve it. It is cheaper to leave a stale section than to erase substrate the user spent hours verifying.

   **Step 1d - verification pass before moving to Step 2.**
   - Diff the new HANDOFF against the old one mentally: for every open item you enumerated in Step 1a, confirm it appears somewhere in the new file (either resolved-with-date, still-open-carried-forward, or explicitly-obsoleted). If any open item is missing from the new HANDOFF without a resolution note, stop and add it back.
   - Report to the user in the Step 6 summary: "N unfinished items carried forward, M resolved this session, K obsoleted."

2. Propose any additions to the Mistakes Log in `CLAUDE.md` from lessons learned this session.

3. Update `memory/MEMORY.md` with anything that should persist across sessions.

4. Stage and commit all modified files (excluding ignored paths). Use the standard co-author commit format.

5. Push to origin.

6. Print a one-line summary of the session for the user.
