# claude-token-skills

两个给 AI 编码助手省 Token 的 Claude Code 技能（Agent Skills）。
Two token-saving skills for Claude Code (Agent Skills).

它们都是自包含的：只有一份 `SKILL.md`，没有脚本、二进制或外部依赖。
Both are self-contained: a single `SKILL.md` each, no scripts, binaries, or external dependencies.

## 技能 / Skills

### `context-handoff` — 上下文交接
会话上下文快满、变慢或变贵，但任务还没做完时触发。它把当前工作现场浓缩成一份结构化交接文档（目标 / 已完成 / 关键决策 / 涉及文件 / 下一步 / 坑），新会话读一遍就能无缝接着干。等于把「长对话总结 + 新开会话」做成一个可复用动作。

Fires when a session's context has grown large or expensive but the task isn't done. It condenses the current work into a structured handoff document (goal, done, decisions, files, exact next step, gotchas) so a fresh session resumes without losing state.

触发词 / Triggers: `交接一下`, `上下文太满`, `新开会话继续`, `接着干`, `baton pass`, `handoff`, `context is getting expensive`.

### `token-diet` — 省 Token 落地清单
用户想降低 token / 成本、频繁触及用量上限、或想要一套省 token 的工作规则时触发。给出按优先级排的可执行清单：先用 `/context` 诊断，再依次做上下文卫生、约束输出、模型分级、精准喂上下文，最后才是可选的外部工具。还能在用户同意后把常驻规则写进 `CLAUDE.md`。

Fires when the user wants to cut token/cost usage, keeps hitting limits, or wants token-efficient working rules. Provides a prioritized playbook: diagnose with `/context`, then context hygiene, output constraints, model tiering, precise context feeding, and optional external tools. Can write always-on rules into `CLAUDE.md` on request.

触发词 / Triggers: `省 token`, `token 太贵`, `用量上限`, `怎么少烧 token`, `reduce token usage`, `save tokens`.

## 安装 / Install

Claude Code 会自动加载 `~/.claude/skills/` 下的技能。把这两个目录放进去即可：
Claude Code auto-loads skills under `~/.claude/skills/`. Drop the two directories in:

```bash
git clone https://github.com/cocohahaha/claude-token-skills.git
cp -R claude-token-skills/context-handoff ~/.claude/skills/
cp -R claude-token-skills/token-diet ~/.claude/skills/
```

或者用软链接，便于随仓库更新：
Or symlink, to stay in sync with the repo:

```bash
ln -s "$(pwd)/claude-token-skills/context-handoff" ~/.claude/skills/context-handoff
ln -s "$(pwd)/claude-token-skills/token-diet" ~/.claude/skills/token-diet
```

装好后重开一个 Claude Code 会话，说一句 `帮我交接一下` 或 `帮我省点 token` 即可触发。
Restart a Claude Code session, then say something like `帮我交接一下` or `help me save tokens` to trigger.

## 设计原则 / Design

两个技能都遵循 Claude Skills 的「渐进披露」原则：`description` 只写触发条件、`SKILL.md` 正文精简（各 60 行内）、确定性操作给模板而非长篇说明。技能本身也要省 token。
Both follow Claude Skills' progressive-disclosure principle: `description` states only when to trigger, the `SKILL.md` body stays lean (under 60 lines each), and deterministic steps ship as templates rather than prose. The skills themselves are token-frugal.

## 来源 / Provenance

技巧提炼自社区的省 Token 经验，`context-handoff` 取自 `baton-pass` 的接力思路，`token-diet` 汇总了多条公开分享的实践。这里是原创重写的自包含版本，不复刻任何外部仓库的实现。
Techniques are distilled from community token-saving practice. `context-handoff` follows the handoff idea popularized by `baton-pass`; `token-diet` consolidates widely-shared tips. These are original, self-contained rewrites and do not reproduce any external repo's implementation.

## License

MIT
