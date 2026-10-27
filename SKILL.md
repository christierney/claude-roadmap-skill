---
name: roadmap
description: Turn research into a phased roadmap of PR-sized work items, or pick up and work one item from an existing roadmap. Use "plan" mid-conversation when the user says there's enough to justify a roadmap ("write the roadmap", "let's turn this into a plan of PRs", "make a punchlist"). Use "work" at the start of a session to take one item from a roadmap doc ("pick up the next roadmap item", "work the roadmap at <path>").
argument-hint: "plan [topic] | work <roadmap path>"
---

# Roadmap

A roadmap is a private planning doc for a multi-PR effort.
It records goals, a long-term target, and a punchlist of items that are each one standalone PR, plus the decisions, dependencies, and status needed for separate agents to work items one at a time, sometimes in parallel.
It sits on top of a research note that holds the evidence.
It deliberately has no implementation detail: each item gets its own plan when someone picks it up.

Two modes:

- **plan**: build a roadmap from research already under way in this conversation.
- **work**: in a fresh session, take one item from an existing roadmap through reproduction, issue, plan, and build, with the user at each checkpoint.

Arguments: `$ARGUMENTS`

If the arguments don't say which mode, infer it: a roadmap path or "next item" means **work**; otherwise **plan**.

## Where roadmaps live

Roadmaps and their research notes are private working docs, not project files.
Put them where the user's instructions say private notes go (for example a notes folder named in their global `CLAUDE.md`), and follow that folder's own rules for naming, opening lines, and committing.
If there's no such instruction, ask where to put them before writing anything.

Never put roadmap content, paths into the notes, roadmap item IDs, or customer or support-ticket details into commits, PRs, issues, release notes, or code comments.

## Plan mode

The user invokes this partway through a conversation, after some research and discussion.
Build on what's already established; don't restart the research.

1. **Take stock.**
   Summarize for yourself what the conversation has established: findings (and which you verified directly versus took from research agents), decisions the user made, preferences they stated (for example "incremental, for reviewers"), and open questions.
   Find any notes already written for this topic and read them.
2. **Find the gaps.**
   A roadmap needs: evidence the problems are real, how things work today, any docs or promises that describe intended behaviour, evidence about users (support tickets, issues) where it bears on priority or on a behaviour change, and existing good patterns in the code worth copying.
   List what's missing and tell the user before filling it.
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
   - Record decisions with dates where they're made, and keep unresolved ones under "Open decisions".
   - Say which items need the strongest model for reproduction and planning and which can run on a building-tier model end to end, under "Roadmap-specific rules".
   - If the roadmap needs anything that overrides the generic process in work mode (a timebox, a different issue policy), put it in the item or in a "Roadmap-specific rules" section.
7. **Walk through decisions on request.**
   When the user asks for more detail on a decision, investigate (code, UI, docs, history, tickets) rather than restating the options; facts can change the recommendation, and you should say so plainly when they do.
   Record each decision in the roadmap when it's made and unblock the affected items.
8. **Finish**: commit the notes per their folder's rules, add or update a memory pointer to the roadmap, and tell the user how to start an item:
   in a fresh session (usually a new worktree), run `/roadmap work <absolute path to roadmap>`.

## Work mode

The user starts a fresh session, usually in a new worktree, and invokes `/roadmap work <path>`.
Work exactly one item, in the current checkout or worktree; don't `cd` to another one.
Stop and wait for the user at each **checkpoint**.

1. **Read** the notes folder's rules, then the roadmap and its research note, in full.
   Notes go stale: check every claim your item relies on against the current code before acting on it, and tell the user when a note is wrong.
   Roadmap-specific rules and item text override the steps below where they differ.
2. **Choose** the first available item in the roadmap's stated order: status "Not started" or "Next up", not blocked, not claimed.
   If nothing is available, stop and say so.
   **Checkpoint:** tell the user which item and why, in two or three sentences.
3. **Claim** it: follow the claim protocol in `references/parallel-work.md`.
4. **Reproduce, with the user (bug items).**
   Confirm the bug exists on current main before planning a fix.
   Propose a reproduction: a failing automated test at the cheapest layer that shows it, otherwise a manual repro on a running instance.
   Run it, or have the user run it.
   **Checkpoint:** agree on what was observed.
   If it doesn't reproduce within any timebox the roadmap sets, record that in the status table and the item, tell the user, and stop. Don't file an issue.
5. **File an issue, with the user's approval.**
   Search the project's issue tracker for an existing issue first (open and closed) and use it if one fits.
   Otherwise draft one: title, behaviour, reproduction, expected versus actual; for items that aren't bugs, the goal and scope.
   Follow any project skill or convention for issues (labels, templates).
   Keep private context out (see "Where roadmaps live").
   **Checkpoint:** show the draft and wait for an explicit go-ahead before creating it.
   Record the issue number in the status table.
6. **Plan** the item in the notes folder, named after the roadmap and the item (for example `<roadmap-name>-<id>-<slug>-plan.md`), and link it from the status table.
   Bug plans start from the reproduction, as a failing automated test.
   The plan says who builds it (see "Choosing who builds" below).
   **Checkpoint:** the user approves the plan before you build.
7. **Build** on a feature branch named per the user's conventions, including the issue number, following the project's own rules for builds, tests, linting, release notes, and docs.
   Keep to the item: record related problems you find in the roadmap or research note and tell the user, rather than fixing them on this branch.
   If the plan delegates the build, create the branch yourself, then follow "Delegated build" below.
8. **Finish**: rebase on main if parallel items may have merged, rerun the affected tests, commit locally, and follow the user's rules about pushing and PRs (by default: don't).
   Update the item's status-table row with the outcome and anything later items need to know, fix any note facts the work changed, commit the notes, and tell the user.

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
   - If the plan contradicts the code, stop and report rather than improvising.
   - Done means the plan's tests pass and the project's lint and format steps are clean; commit locally, don't push.
   - Report what changed, test results, and anything left undone.
2. After each phase, review the diff before the next one starts, using a code-review skill or a separate review agent on the building-tier model, rather than reviewing every phase yourself.
   Send fixes back to a build agent; don't patch the branch yourself unless the fix is trivial.
3. When all phases are done, review the whole branch yourself against the plan and the item's "done" criteria, then go to Finish.

## References

- `references/roadmap-template.md`: skeleton for a new roadmap.
- `references/parallel-work.md`: claim protocol and overlap rules for running items at the same time.
- `references/research-fanout.md`: how to brief research agents during plan mode.
