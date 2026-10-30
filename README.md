# roadmap

A [Claude Code](https://claude.com/claude-code) skill for multi-PR efforts.
It turns research from a conversation into a phased roadmap of PR-sized work items, then lets separate agents pick up and work those items one at a time, sometimes in parallel.

A roadmap is a private planning doc.
It records goals, a long-term target, a punchlist of items that are each one standalone PR, and the decisions, dependencies, and status agents need to work items without the original conversation.
It deliberately has no implementation detail: each item gets its own plan when someone picks it up.

## Modes

- **`/roadmap plan [topic]`**: run partway through a conversation, once research has established enough to justify a roadmap. Fills evidence gaps, verifies headline claims in the code, agrees the shape with you, and writes the roadmap.
- **`/roadmap work <roadmap path>`**: run in a fresh session (usually a new worktree). Takes one item through reproduction, issue, plan, build, and independent review, stopping for you at each checkpoint.
- **`/roadmap unattended <roadmap path>`**: works every available item in one session without stopping, on an integration branch, for experiments you review as a finished result. Runs only when you name it.

## Install

Clone into your personal skills folder:

```sh
git clone <repo url> ~/.claude/skills/roadmap
```

Or into a project's `.claude/skills/roadmap` to share it with that project.

## Setup

Roadmaps, research notes, and item plans are private working docs, not project files.
The skill writes them wherever your instructions say private notes go, so add a line to your global `~/.claude/CLAUDE.md` naming a notes folder, for example:

```markdown
My private working notes live in `~/notes/`. Read `CLAUDE.md` in that folder before writing there.
```

If no notes folder is named, the skill asks where to put them.
A Git repository works well for the notes folder: the skill commits roadmap changes, and agents working in parallel claim items by committing to it.

The skill also follows your own rules for branch names, commits, pushing, and PRs.
By default it commits locally and leaves pushing and opening PRs to you.

## Files

- `SKILL.md`: the skill itself, with all three modes.
- `references/roadmap-template.md`: skeleton for a new roadmap, and the status values agents use to choose items.
- `references/parallel-work.md`: claim protocol and code-overlap rules for running items at the same time.
- `references/research-fanout.md`: how to brief research agents in plan mode.
- `references/unattended.md`: rules, setup checklist, and loop for unattended runs.

## License

[MIT](LICENSE)
