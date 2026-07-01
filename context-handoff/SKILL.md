---
name: context-handoff
description: Create a compact handoff document when a coding-agent session is large, slow, or expensive but unfinished work must continue in a fresh session. Captures the goal, completed work, decisions, touched files, exact next step, and gotchas. Triggers include "handoff", "baton pass", "continue in a fresh session", "context is too large", "write a session handoff".
---

# Context Handoff

Use this skill when the session is too heavy, but the work still needs to continue.

Write a small Markdown handoff. The next agent should be able to read it once and start working.

## Steps

1. Write to `HANDOFF.md` in the current working directory unless the user asks for another path.
2. Include only what the next session needs.
3. Keep exact paths, commands, errors, versions, and constraints.
4. End with the next concrete action.
5. After writing, report only the file path and the restart prompt.

Restart prompt:

```text
Read HANDOFF.md and continue from "Next Step".
```

## Template

```markdown
# Handoff: <task name>

Generated: <date>

## Goal
<What the work is trying to achieve. 1-2 sentences.>

## Done
- <Concrete progress, with files/functions/results.>

## Decisions
- <Important choices, constraints, and why they matter.>

## Files
- `path/to/file` - <why it matters>

## Next Step
1. <The exact next action.>
2. <The action after that, if obvious.>

## Gotchas
- <Known failures, environment notes, commands to run, tests still needed.>
```

## Quality Bar

- Short enough to paste into a new session.
- Specific enough that the next agent does not rediscover the same facts.
- No meeting notes. No vague summaries. No full transcript.
