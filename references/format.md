# Roadmap format v1

Agents aren't the only readers of a roadmap: tools list roadmaps, count what's done, and show what's next by parsing the file.
The structure below is what they rely on, so keep to it exactly; where a roadmap drifts from it, a tool shows a warning or the wrong status.

## Frontmatter

A roadmap starts with this block, before the H1:

```yaml
---
roadmap: 1
title: <Topic>
summary: <one-line summary of the direction>
status: draft
repo: <owner/name>
updated: <YYYY-MM-DD>
---
```

- `roadmap`: the format version, always `1`. Its presence is what marks the file as a roadmap.
- `title`: a short name for the effort, without the word "roadmap".
- `summary`: one line on the direction, the same as the H1's second half.
- `status`: the roadmap's own lifecycle, one of `draft`, `ready`, `done`, `archived`. Item statuses live only in the Status table.
- `repo`: the GitHub repository (`owner/name`) that a bare `#1234` in the roadmap refers to. Leave the key out when the project has no GitHub tracker.
- `updated`: the date of the last edit to the roadmap.

Write one `key: value` per line, with `---` alone on the first line of the file and again on the closing line.
Put a value in double quotes if it contains `: ` or ` #`, and never put a double quote inside a value.

## Keeping the frontmatter current

- **`updated`**: whenever you edit the roadmap, set `updated` to today's date in the same edit. That includes claiming an item and changing a status row.
- **`status`**:
  - `draft` while the roadmap is being written;
  - `ready` once the user agrees the shape and the first item's decisions are settled;
  - `done` when every row in the Status table starts with `Done`, and none is `Done: needs attention`;
  - `archived` only when the user says the roadmap is abandoned or superseded.
- The opening line (`Roadmap and punchlist, status **ready** (<date>).`) repeats the status for people reading the file. Change it in the same edit as the frontmatter, so the two never disagree.
- A roadmap with no frontmatter predates this format. Work it as it is: skip the frontmatter updates, and don't add frontmatter unless the user asks.

## Structure rules

- **Opening.** Keep the H1 (`# <Topic> roadmap: <summary>`) and the opening line that starts `Roadmap and punchlist, status`. A notes folder's own rules for opening lines apply after the frontmatter and alongside these, never in place of them.
- **Section headings.** `## Phases and punchlist`, `## Dependencies`, and `## Status` keep exactly those names.
- **Items.** An item is a top-level bullet `- **<ID>. <Title>.**` under a `###` or `####` heading inside `## Phases and punchlist`. Its body is everything up to the next item or the next `###` or `####` heading.
- **IDs.** One to three capital letters, a number, and an optional lowercase letter (`C8`, `PR12`, `R1a`). An ID is unique within the roadmap and never renumbered or reused.
- **Phase headings.** A heading named `Order`, or ending in ` order` (`#### Phase 1 order`), holds ordering prose and is never a phase. Don't give a phase such a name. Every other `###` or `####` heading inside `## Phases and punchlist` starts a phase, and the items after it belong to it, so add no other headings at those levels; inside an item, use `#####` or bold text.
- **Status table.** Columns `Item | Status | Issue / branch / plan`. Exactly one row per item, and a row for every item; the Item cell holds one ID and nothing else, never a range or a list.
- **Status cells.** A cell starts with one of the status values listed in `roadmap-template.md`, spelled as written there. Detail follows after `:`, `;`, `,`, or in parentheses (`Blocked: waiting on C2`, `In progress (worktree-3)`). Don't invent a new leading value.
- **Dependencies.** Write `<ID> depends on <ID>, <ID>.` under `## Dependencies`, or a sentence starting `Depends on <ID>` in the item's body. Name only this roadmap's items that way. A dependency on anything else (another roadmap's item, a decision, a release) goes in parentheses: `C3 depends on C2 (and on the storage ADR)`.
- **Mentions.** When Open decisions, Code overlap, or Observations refer to an item, write its ID as a separate word (`C8`, or `C2–C5` for a run of them), so the note can be found from the item.
