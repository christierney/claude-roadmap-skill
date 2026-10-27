# Running roadmap items in parallel

Several items can be in progress at once, each in its own worktree and branch, with agents sharing one roadmap file.

## Claim protocol

1. Re-read the roadmap's status table immediately before claiming.
2. Set the item's row to "In progress (<worktree or checkout>)".
3. Commit that change on its own, right away, so other agents see it.
4. Do the rest of the work only after the claim is committed.

If the notes folder is a Git repository that agents share:

- Stage only the files you changed (`git add <file>`), never everything.
- If a commit fails on `index.lock`, another agent is committing; wait a moment and retry.
- Re-read the roadmap before every edit, since another agent may have changed it since you last read it.
- Make small, targeted edits to the roadmap rather than rewriting it.

## Avoiding collisions

- Check the roadmap's "Code overlap" section. Don't take an item whose group already has one in progress.
- If your item's cause turns out to be in code another in-progress item owns, record that in the roadmap and tell the user instead of changing that code.
- Before declaring an item finished, rebase on main in case a parallel item merged first, and rerun the tests that cover the changed code.
- Heavy test suites (database, API, end-to-end) are best run in one worktree at a time so they don't starve shared resources such as Docker.

## When writing a roadmap

Group items by the files or subsystems they change, and record the groups under "Code overlap".
Items in the same group run one after another; say which items are safe to run alongside anything.
Later-phase items whose overlap isn't clear yet can note "list overlaps in the item's plan".
