# Research fan-out during plan mode

Use research agents to fill broad gaps in evidence, not to repeat what the conversation already established.

## Splitting the work

- Split by area of the codebase so agents don't overlap: size areas by a quick grep count of the thing being studied, and give the largest areas their own agent.
- Add one or two cross-cutting agents where a question spans every area (how tests do it, where state is changed after startup, docs versus code).
- User evidence (support tickets, issues) is its own agent, read-only, with instructions to leave out people's names.
- Launch independent agents in one message so they run at the same time.

## Writing each brief

Each agent starts with no context, so the brief carries everything:

- The goal ("common patterns to inform a redesign, not an exhaustive catalog") and that it's read-only.
- What's already known, so it doesn't rediscover it.
- Its scope, and which areas other agents cover.
- A shared classification to report against (for example: read at use time / captured at construction / mixed / changed after startup), so reports can be merged.
- Specific questions, including spot-checks that trace a value from definition to consumer.
- Output: concise, a word limit, file references for every example, rough prevalence, and a section for risks and inconsistencies.

## After the reports come in

- Verify each headline claim yourself before it goes into a note or becomes an item: a bug, a reload hazard, a broken promise.
- Note where reports contradict each other and resolve it in the code.
- Keep track of which claims you verified and which you took from the reports; the research note marks the difference.
- Collate by pattern, not by agent, when reporting to the user.
