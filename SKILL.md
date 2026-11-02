---
name: roadmap
description: Turn research into a phased roadmap of PR-sized work items, or pick up and work one item from an existing roadmap. Use "plan" mid-conversation when the user says there's enough to justify a roadmap ("write the roadmap", "let's turn this into a plan of PRs", "make a punchlist"). Use "work" at the start of a session to take one item from a roadmap doc ("pick up the next roadmap item", "work the roadmap at <path>"). Use "unattended" only when the user asks to run a whole roadmap without stopping ("let agents run the roadmap overnight").
argument-hint: "plan [topic] | work <roadmap path> | unattended <roadmap path>"
---

# Roadmap

A roadmap is a private planning doc for a multi-PR effort.
It records goals, a long-term target, and a punchlist of items that are each one standalone PR, plus the decisions, dependencies, and status needed for separate agents to work items one at a time, sometimes in parallel.
It sits on top of a research note that holds the evidence.
It deliberately has no implementation detail: each item gets its own plan when someone picks it up.

Three modes:

- **plan**: build a roadmap from research already under way in this conversation.
- **work**: in a fresh session, take one item from an existing roadmap through reproduction, issue, plan, and build, with the user at each checkpoint.
- **unattended**: work every available item in one session without stopping, for experiments the user reviews as a finished result.

Arguments: `$ARGUMENTS`

If the arguments don't say which mode, infer it: a roadmap path or "next item" means **work**; otherwise **plan**.
Never infer **unattended**; it runs only when the user names it.

## Where roadmaps live

Roadmaps and their research notes are private working docs, not project files.
Put them where the user's instructions say private notes go (for example a notes folder named in their global `CLAUDE.md`), and follow that folder's own rules for naming, opening lines, and committing.
If there's no such instruction, ask where to put them before writing anything.
For an experiment with nothing private in it, the user may choose to keep the roadmap in the repo instead; then the rest of this section doesn't apply.

Never put roadmap content, paths into the notes, roadmap item IDs, or customer or support-ticket details into commits, PRs, issues, release notes, or code comments.

## Plan mode

The user invokes this partway through a conversation, after some research and discussion.
Build on what's already established; don't restart the research.

1. **Take stock.**
   Summarize for yourself what the conversation has established: findings (and which you verified directly versus took from research agents), decisions the user made, preferences they stated (for example "incremental, for reviewers"), and open questions.
   Find any notes already written for this topic and read them.
   Check the other roadmaps in the notes folder for items that touch the same code; overlaps go in the new roadmap, with ownership as an open decision unless the user settles it.
2. **Find the gaps.**
   A roadmap needs: evidence the problems are real, how things work today, any docs or promises that describe intended behaviour, evidence about users (support tickets, issues) where it bears on priority or on a behaviour change, existing issues in the tracker, and existing good patterns in the code worth copying.
   **Checkpoint:** list what's missing, and how you'd fill it, before launching any research; keep it short.
   For broad gaps, fan out research agents; see `references/research-fanout.md`.
3. **Verify the headline claims yourself.**
   Anything that becomes a bug item or drives a decision gets checked directly in the code before it goes in the doc.
   Mark the rest as research output or inferred.
   Before proposing a behaviour change, check whether the current behaviour is deliberate: search the git history (`git log -S`) and PRs for whoever added it.
4. **Write or update the research note** if the evidence isn't already recorded.
   It holds what's true today, with symbols and file paths (not line numbers), each claim marked verified or inferred.
5. **Agree the shape before writing the roadmap.**
   Propose goals, a long-term target, phases, and rough items in the conversation, with your recommendation for ordering and for what must be agreed first (for example an ADR for team buy-in).
   Ask about anything that changes the ordering: priorities, how incremental to be, which decisions block which items.
6. **Write the roadmap** from `references/roadmap-template.md`.
   - Every item is one standalone PR that a reviewer can take in one sitting; split anything bigger, and say "several PRs" where an item is really a series.
   - Each item states the problem, what "done" means, and its dependencies, with enough pointers (symbols, files, ticket numbers for private context) that an agent with no conversation history can start.
   - Mark items blocked on a decision or on another item, so an agent choosing "the next item" can't pick them.
   - List which items touch the same code, for parallel work; see `references/parallel-work.md`.
   - Give every item its own status row; grouped rows can't be claimed one item at a time.
   - Record the date the notes were last checked against the code, and anything a fresh worktree needs before tests run (runtime binaries, images, test-runner quirks).
   - Mark the roadmap **draft** while writing; change it to **ready (<date>)** once the user agrees the shape and the first item's decisions are settled.
   - Record decisions with dates where they're made, and keep unresolved ones under "Open decisions".
   - Say which items need the strongest model for reproduction and planning and which can run on a building-tier model end to end, under "Roadmap-specific rules".
   - If the roadmap needs anything that overrides the generic process in work mode (a timebox, a different issue policy), put it in the item or in a "Roadmap-specific rules" section.
   - If the user means to run it unattended, settle every open decision now and write the "Unattended run" rules from `references/unattended.md`.
7. **Walk through decisions on request.**
   When the user asks for more detail on a decision, investigate (code, UI, docs, history, tickets) rather than restating the options; facts can change the recommendation, and you should say so plainly when they do.
   Record each decision in the roadmap when it's made and unblock the affected items.
8. **Finish**: commit the notes per their folder's rules, add or update a memory pointer to the roadmap, and tell the user how to start an item:
   in a fresh session (usually a new worktree), run `/roadmap work <absolute path to roadmap>`.
   If it's set up to run unattended, run `/roadmap unattended <absolute path to roadmap>` instead.

## Work mode

The user starts a fresh session, in a new worktree or a reused one, and invokes `/roadmap work <path>`.
Work exactly one item, in the current checkout or worktree; don't `cd` to another one.
Stop and wait for the user at each **checkpoint**.
Two checkpoints can share one message when the first needs no decision (for example a reproduction result and the issue draft), but don't skip any.
A stop condition in the plan or the roadmap is a checkpoint too: report what you found and wait, even if you think you know the fix.

1. **Read** the notes folder's rules, then the roadmap and its research note, in full.
   Notes go stale: check every claim your item relies on against the current code before acting on it, and tell the user when a note is wrong.
   Roadmap-specific rules and item text override the steps below where they differ.
2. **Choose** the first available item in the roadmap's stated order: status "Not started" or "Next up", not blocked, not claimed.
   If nothing is available, stop and say so.
   **Checkpoint:** tell the user which item and why, in two or three sentences.
   If the roadmap says the item should start on a different model from the one you're on, say so.
   Don't claim it until the user confirms.
3. **Claim** it: follow the claim protocol in `references/parallel-work.md`.
4. **Reproduce, or show the gap, with the user.**
   - Bug items: confirm the bug exists on current main before planning a fix. Propose a reproduction: a failing automated test at the cheapest layer that shows it, otherwise a manual repro on a running instance. Run it, or have the user run it.
   - Test-quality items: show that the current tests miss a failure, with a probe or a deliberate mutation they don't catch.
   - Refactors and other items with nothing to reproduce: skip this step and say so at the next checkpoint.

   Before proposing to change any behaviour, check whether it's deliberate: search the history (`git log -S`) and the PRs that added it, and report what you found.
   In a reused worktree, start from a detached `origin/main`; write the reproduction there and create the branch once the issue number is known.
   **Checkpoint:** agree on what was observed.
   If it doesn't reproduce within any timebox the roadmap sets, record that in the status table and the item, tell the user, and stop. Don't file an issue.
5. **File an issue, with the user's approval.**
   Skip this when the project has no issue tracker or the roadmap says the item needs none; for items that aren't bugs, ask whether an issue is wanted before drafting one.
   Search the project's issue tracker for an existing issue first (open and closed) and use it if one fits: show its assignee and labels, and propose assigning it to the user along with any comment you'd add.
   Otherwise draft one: title, behaviour, reproduction, expected versus actual; for items that aren't bugs, the goal and scope.
   Follow any project skill or convention for issues (labels, templates).
   Keep private context out (see "Where roadmaps live").
   **Checkpoint:** show the draft and wait for an explicit go-ahead before creating it.
   Record the issue number in the status table.
6. **Plan** the item in the notes folder and link it from the status table.
   Name it with the pattern in the roadmap's rules; without one, use `<topic>-<id>-<slug>-plan.md`, where `<topic>` is the roadmap's file name without `-roadmap.md`.
   Bug plans start from the reproduction, as a failing automated test.
   The plan says how the fix will be proven:
   - bug fixes: back the fix out and confirm the regression test fails, and add tests that pin the existing behaviour next to the fix (for example table rows for inputs that already worked);
   - test-quality fixes: mutations the new tests catch.

   The plan says who builds it (see "Choosing who builds" below) but doesn't set commit trailers; each commit's trailer names the model that wrote it.
   **Checkpoint:** the user approves the plan before you build.
7. **Build** on a feature branch named per the user's conventions, including the issue number, following the project's own rules for builds, tests, linting, release notes, and docs.
   Run every verification the plan names.
   Code comments describe the invariant, not the roadmap item, so they stay true after later items land.
   Keep to the item: record related problems you find in the roadmap or research note and tell the user, rather than fixing them on this branch.
   If the plan delegates the build, create the branch yourself, then follow "Delegated build" below.
8. **Review independently.**
   Every build gets this unless the user waives it.
   Launch a separate agent on the building-tier model (set `model` explicitly) that runs a code-review skill such as `/code-review` on the branch.
   Brief it with the diff, the item's goal, and the project's rules only: not the plan, your conclusions, or this conversation, so its read isn't anchored on yours.
   Check each finding against the code before passing it on.
   **Checkpoint:** go through the findings with the user and agree which to fix.
   Fixes go to a build agent if the build was delegated; record findings you deliberately skip, and why, in the plan.
9. **Finish**: rebase on main if parallel items may have merged, rerun the affected tests, commit locally, and follow the user's rules about pushing and PRs (by default: don't).
   Update the item's status-table row (for example "Built on `<branch>`, awaiting push"), record anything later items need to know, fix any note facts the work changed, update any memory pointer to the roadmap, and commit the notes.
   Then tell the user, with:
   - a draft PR description following the project's PR template: what changed, exactly what was verified and in what scope, and what was left out, with nothing from the notes;
   - for behaviour users can see, an offer to check it by hand on a running instance when that's cheap.
10. **After merge**, when the user says the PR merged or asks for cleanup:
    - Confirm it merged (for example `gh pr view`) and that the merged diff matches the local branch.
    - Set the row to "Done: merged <date> as #<PR>" and mark the next item "Next up".
    - Update the text, dependencies, and code-overlap entries of later items the work changed, and record findings deliberately left unfixed under "Observations from finished items".
    - Decide for yourself whether the plan can go; don't ask.
      It can go once everything still needed from it is somewhere else: in the roadmap, the research note, an issue, or the code.
      That includes decisions and their reasons, follow-ups, facts later items rely on, and why review findings were skipped.
      Move those first, then delete the plan with `git rm` and commit.
      Keep the plan only if something in it has no other home yet, and say what and why.
      In your report, say what you moved and where, so the user can check.
    - Update any memory pointer, and commit the notes.
    - Reset the worktree for the next item: detach at `origin/main` and delete the local branch (`git branch -D`, since squash merges leave it unmerged). Never touch the remote.
    - Name the next item and the command to start it.

Between Finish and merge the user may bring the session back for follow-ups:

- **Human review comments:** fix what the PR introduced; route problems that predate it to the roadmap item that owns them.
- **CI failures:** compare with main before assuming the PR caused it.

### Choosing who builds

If the user's instructions or memory define a model split for planning, building, and review, follow it.
Otherwise:

- **Build it yourself** when you're already on a building-tier model, or the item is small and mechanical.
- **Delegate the build** when you're on the strongest model and the item is more than a small, mechanical change: the strong model's value was in the reproduction and the plan, and a building-tier agent (for example `model: "opus"` on the Agent tool) can carry out an approved plan at lower cost.
- A roadmap's item text or roadmap-specific rules can set the choice for an item; that wins.
- The user can override either way at the plan checkpoint.

### Delegated build

1. Launch one build agent per plan phase (most items have one phase), with a kickoff contract:
   - Read the plan in full and do only the named phase, on the existing branch, in this worktree.
   - Treat every "verify" step in the plan as mandatory.
   - If the plan contradicts the code, or one of its stop conditions is hit, stop and report rather than improvising.
   - Done means the plan's tests and verifications pass and the project's lint and format steps are clean; commit locally, don't push.
   - Each commit's trailer names the agent's own model.
   - Report what changed, test results, and anything left undone.
2. For plans with several phases, review each phase's diff before the next one starts, with a separate review agent on the building-tier model (set `model` explicitly); this review may see the plan.
   Send fixes back to a build agent; don't patch the branch yourself unless the fix is trivial.
3. When all phases are done, review the whole branch yourself against the plan and the item's "done" criteria, then go to step 8, the independent review, which is separate from these.

## Unattended mode

The user invokes `/roadmap unattended <path>`, usually in a fresh session.
Every rule of work mode applies except the checkpoints, the issue step, pushing, and "exactly one item"; the roadmap's "Unattended run" rules replace them.
Follow `references/unattended.md`.

1. **Read** the notes rules (if the roadmap is in a notes folder), the roadmap, and its research note, in full.
2. **Set up.**
   If the roadmap has no "Unattended run" rules, draft them from the reference and work through its setup checklist.
   **Checkpoint**, the only one: show the rules, the defaults you'll take for open decisions, and the items in run order, and wait for the user to approve the start.
   Commit the rules.
   If the roadmap already has approved rules and a run log with no summary, the run was interrupted: resume without asking.
3. **Run the loop** in the reference until a stop rule holds.
4. **Summarize** at the top of the run log, commit, and tell the user where to start reviewing.

## References

- `references/roadmap-template.md`: skeleton for a new roadmap.
- `references/parallel-work.md`: claim protocol and overlap rules for running items at the same time.
- `references/research-fanout.md`: how to brief research agents during plan mode.
- `references/unattended.md`: rules section, setup checklist, and loop for unattended runs.
