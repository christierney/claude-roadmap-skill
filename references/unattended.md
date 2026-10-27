# Unattended runs

An unattended run works every available item in one session without stopping to ask the user anything.
It suits experiments the user reviews as a finished result: no team review, no issue tracker, nothing pushed.
The design comes from a run that finished 14 items in under two hours with none needing attention; the per-item review was what caught real defects, so don't drop it to save time.

## The rules section

The roadmap needs an "Unattended run" subsection under "Roadmap-specific rules" before the run starts.
Fill this in and agree it with the user at setup; it replaces the checkpoints in work mode.

```markdown
### Unattended run

This roadmap runs **unattended**. <User> reviews the finished result, not each step.

- **No checkpoints.** Don't stop to ask anything. Where work mode says "checkpoint", write what you would have said in the run log and continue. Decisions the roadmap doesn't settle are made by the item's planner, recorded under "Open decisions" with the date, and logged.
- **Issue tracker:** <none: skip the issue step and record the branch in the status table.>
- **Remote:** never push, never add a remote, never open a PR.
- **Branches:** work lands on the integration branch `<integration branch>`, which starts from `<base branch>`. Each item is built on its own branch `<item branch pattern>` off the integration branch and merged back with `--no-ff`, so every item is one reviewable merge. `<default branch>` is never touched.
- **Plans:** `<plan location and naming>`, committed with the item. <Items that may skip a separate plan because their text is enough.>
- **Gates:** <the exact build, test, lint, and format commands that must pass before an item merges>. These are the only gates.
- **Who builds:** each item is planned by the orchestrator, built by a building-tier agent, and reviewed by a separate building-tier agent that reads the diff against the plan and the item's "done" criteria.
- **Order:** serial, one item at a time, in status-table order.
- **Per-item budget:** at most two build rounds (the build plus one fix round after review) and <90> minutes of wall-clock time. When the budget runs out, set the item to `Done: needs attention` with a paragraph on what's wrong and what was tried, commit whatever compiles on the item branch without merging it, and move on. Items that depend on it become `Blocked: <ID> needs attention`.
- **Global stop:** stop when no item is available, when <3> items have ended in `needs attention`, when the same failure repeats in two consecutive items (a toolchain or module problem, say), or when <8> hours have passed since kickoff.
- **Run log:** `<log path>` gets one entry per item as it finishes: status, branch, merge commit, the tail of the gate output, review findings and what was done about each, deviations from the plan, and anything <user> should look at. The run's summary goes at the top.
```

## Setup checklist

Before asking the user to approve the rules:

- Every open decision that blocks an item is settled or has a default the planner may take; list the defaults.
- Every item is either available or blocked only by another item in this roadmap.
- The gates run cleanly on the base branch now; a broken baseline would put every item in `needs attention`.
- Nothing in the run needs credentials, a network service, or a person.

## The loop

1. Create the integration branch and the run log with a kickoff timestamp; commit.
2. Re-read the status table and take the first available item; if none, go to step 9.
3. Claim it in the status table and commit the claim.
4. Plan it, unless the rules let it skip a plan; log any decision you made in place of a checkpoint.
5. Delegate the build to a building-tier agent on an item branch, with the kickoff contract from "Delegated build" in `SKILL.md`.
6. Have a separate building-tier agent review the diff against the plan and the item's "done" criteria. Send its findings to a build agent for one fix round at most.
7. Run the gates yourself. If they pass, merge the item branch into the integration branch with `--no-ff`; if the budget is spent, mark the item `Done: needs attention` instead.
8. Update the status table, append the log entry, commit, check the global stop rules, and go to step 2.
9. Write the summary at the top of the log: items done, items needing attention and why, items not reached, total wall-clock, why the run stopped, and the three things the user should look at first, including anything left unverified. Commit it and end.

## Keeping a long run on track

- The roadmap and the log hold the run's state; re-read them each iteration instead of relying on what you remember, so the run survives context compaction.
- Keep your own context lean: build and review agents return short reports, not diffs or test output.
- If the session is restarted on a roadmap whose log has no summary yet, resume from the status table: finish or reset any item still marked in progress, then continue the loop.
