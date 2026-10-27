# Roadmap template

Copy the skeleton below into the notes folder and fill it in.
Delete any section that has nothing in it, except "Status".
Follow the notes folder's conventions for file names and opening lines.

```markdown
# <Topic> roadmap: <one-line summary of the direction>

Roadmap and punchlist, status **draft** (<date>).
For <user> and for agents picking up one punchlist item at a time with `/roadmap work`.
Background and evidence are in `<research note>`; read it first.
Notes accuracy: checked against main on <date>.
This doc sets goals, order, and PR-sized items. It has no implementation detail: each item gets its own plan when someone picks it up.

## Goals

1. **<Goal>.** <What's true when it's met.>

## Long-term target

<The end state the items move toward, as a short numbered list. Items don't have to reach it; they have to move toward it.>

## Working rules

- **Incremental.** Every item is a standalone PR that a reviewer can take in one sitting.
- **Bugs get a failing test first.**
- **Refactors don't change behaviour.** A PR is either a behaviour change (with a release note when user-visible) or a refactor, not both.
- <Any other rules for this effort.>

## Evidence about users

<Optional. Support tickets, issues, or usage data that bear on priority or on a behaviour change. Private details are fine here and nowhere else.>

## Existing issues

<Optional. Open issues in the tracker that items will fix or must read first, mapped to item IDs.>

## Roadmap-specific rules

<Optional. Anything that overrides the `/roadmap work` process for this roadmap: item order and phase gates, issue tracker and conventions, plan file and branch names, project build rules, what must stay private.>

- **Who builds:** <which items start on the strongest model and delegate the build, and which run on a building-tier model end to end.>
- **Fresh worktree setup:** <what a new worktree needs before tests run: binaries, images, test-runner quirks.>

## Phases and punchlist

Phases are ordered by priority; items in different phases can run in parallel where dependencies allow.
IDs are stable; don't renumber.

### Phase 0: agreement

- **A1. <ADR or design agreement>.** <What it decides, and which phases wait for it.>

### Phase 1: <name>

#### Phase 1 order

<Which item goes first and the suggested order after it.>

- **<ID>. <Title>.**
  <The problem, with symbols and file paths.>
  Done when <observable outcome>.
  <Depends on / overlaps with / timebox / decided on <date>.>

### Phase 2: <name>

...

## Dependencies

- <ID> depends on <ID>.

## Code overlap

Items in the same group touch the same code; don't run them at the same time.

- <Area or files>: <IDs>.
- Parallel-safe with each other: <IDs>.
- Other roadmaps: <roadmap and item IDs that touch the same code, and which roadmap owns the change.>

## Open decisions

- **<Decision> (<who or where it's decided>):** <the question and what it blocks.>
- ~~**<Decided question>**~~ **Decided <date>:** <outcome; which items it unblocked.>

## Observations from finished items

<Findings from finished items that were deliberately left out of scope, and facts later items need. Keep long handoff notes here, not in the status table.>

## Status

One row per item; never group IDs in a row, since items are claimed one at a time.

| Item | Status | Issue / branch / plan |
|---|---|---|
| <ID> | Next up | |
| <ID> | Not started | |
| <ID> | Blocked: <reason> | |
```

## Status values

Use these so agents can choose items mechanically:

- **Next up**: the item to take first.
- **Not started**: available.
- **Blocked: <reason>**: not available until the reason is resolved.
- **In progress (<worktree or checkout>)**: claimed; nobody else takes it.
- **Built on `<branch>`, awaiting push**: work finished locally; the user pushes and opens the PR.
- **Done: merged <date> as #<PR>** / **Done: not reproduced**: finished; plans are usually deleted at this point, so the row keeps the PR number.
- **Done: needs attention**: an unattended run gave up on the item within its budget; see the run log. Items that depend on it become "Blocked: <ID> needs attention".
