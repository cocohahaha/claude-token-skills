---
name: token-diet
description: Reduce token and cost waste for coding agents. Use when the user asks to save tokens or money, hits usage limits, wants lean working rules, or mentions bloated context. Covers context hygiene, command-output caps, focused exploration, model tiering, tool trimming, and optional instruction-file updates. Triggers include "save tokens", "reduce token usage", "context is too big", "token cost", "usage limit", "make this agent cheaper".
---

# Token Diet

Use this skill to make a coding agent cheaper without making it dumber.

## First, Find The Waste

- Check the agent's context or usage view if it has one.
- Look for the usual culprits: old conversation, huge command output, broad file reads, tool schemas, and repeated explanations.
- Do not optimize blindly. Cut the biggest source first.

## Rules

### 1. Keep Context Small

- Start a fresh session for unrelated work.
- Summarize or compact after each finished chunk.
- If work is unfinished, use `$context-handoff` before starting fresh.
- Ignore files the task does not need. Use the platform's ignore mechanism when available.

### 2. Cap Output

Add a short output rule to the agent's instruction file:

```text
Keep answers short.
Do not restate the request.
For small code changes, explain only what changed.
For larger changes, explain the important tradeoffs in 1-3 bullets.
Cap long command output before bringing it into context.
Prefer: COMMAND 2>&1 | head -c 4000
```

Use the right file for the agent:

- Claude Code: `CLAUDE.md`
- Codex: `AGENTS.md`
- Other agents: their project instruction file

### 3. Explore Less

- Give exact file paths when you know them.
- Ask for one missing fact, not a broad investigation.
- Prefer targeted search over reading whole directories.
- Paste short filtered command output instead of raw logs.

### 4. Use The Right Model

- Use cheaper models for formatting, summaries, and mechanical edits.
- Use stronger models for architecture, debugging, and ambiguous product decisions.
- Plan with the stronger model when needed; execute routine edits with a cheaper one.

### 5. Trim Tools

- Disable tools and MCP servers that are not needed for the task.
- Avoid loading large tool schemas by default.
- Only install token-saving helper tools after the user agrees and you have checked current docs.

## When Asked To Set This Up

Read the existing instruction file first. Append the short output rule. Do not overwrite user rules.
