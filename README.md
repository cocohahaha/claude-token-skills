# coding-agent-token-skills

Small skills for keeping coding agents cheap, focused, and easy to resume.

They work with any coding agent that supports skill-style instructions. There are no scripts, binaries, or external dependencies: each skill is a `SKILL.md` plus optional `agents/openai.yaml` metadata.

## Skills

### `token-diet`

Use this when a coding agent is burning too much context or money.

It gives the agent a short playbook:

- keep context small
- cap command output
- avoid vague exploration
- use cheaper models for mechanical work
- write lean rules into the agent's instruction file when useful

Good prompts:

- `Help me reduce token usage.`
- `This agent is getting expensive.`
- `Set up lean working rules for this repo.`

### `context-handoff`

Use this when the current session is too large, but the work is not done.

It creates a compact handoff file with:

- the goal
- what changed
- key decisions
- files touched
- the exact next step
- risks and gotchas

Good prompts:

- `Create a handoff for this session.`
- `The context is too large. Help me continue in a fresh session.`
- `Write a baton pass for the next agent.`

## Install

Clone the repo:

```bash
git clone https://github.com/cocohahaha/coding-agent-token-skills.git
```

Claude Code:

```bash
mkdir -p ~/.claude/skills
cp -R coding-agent-token-skills/token-diet ~/.claude/skills/
cp -R coding-agent-token-skills/context-handoff ~/.claude/skills/
```

Codex:

```bash
mkdir -p ~/.codex/skills
cp -R coding-agent-token-skills/token-diet ~/.codex/skills/
cp -R coding-agent-token-skills/context-handoff ~/.codex/skills/
```

Symlink instead if you want local edits to stay live:

```bash
ln -s "$(pwd)/coding-agent-token-skills/token-diet" ~/.claude/skills/token-diet
ln -s "$(pwd)/coding-agent-token-skills/context-handoff" ~/.claude/skills/context-handoff
```

Use `~/.codex/skills/` as the target for Codex.

## Design

The rules are intentionally small.

Agents already know how to code. These skills only add the bits they usually get wrong when a session gets expensive: keep less context, output less noise, and leave a clean handoff before starting fresh.

## License

MIT
