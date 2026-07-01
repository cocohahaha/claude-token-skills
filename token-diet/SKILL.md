---
name: token-diet
description: Use when the user wants to cut token / cost usage of an AI coding agent (Claude Code, Codex, etc.), asks how to save tokens, keeps hitting usage limits, or wants token-efficient working rules. Provides a prioritized playbook — context hygiene, lean delivery rules, model tiering, command-output capping, MCP/tool trimming — and can write the always-on rules into CLAUDE.md on request. Triggers include "省 token", "省钱", "token 太贵", "用量上限", "降低消耗", "reduce token usage", "save tokens", "context 太大", "怎么少烧 token".
---

# Token Diet — 省 Token 落地清单

给 AI 编码助手省 Token 的可执行清单。核心不是少用 AI，而是**少带无用上下文、少让 Agent 乱探索、少因方向错了反复返工**。按「先诊断、再按优先级动手」的顺序用。

## 先诊断（Claude Code）

- `/context` — 直接看 token 花在哪：系统提示、MCP schema、历史、文件引用各占多少。先看到大头再动手，别凭感觉。
- 两个隐形吃 token 大户：① MCP 工具的 schema，装了就每轮全量加载（几十个工具轻松几万 token）；② 命令行原始输出，`git status`/`ls`/`curl` 的格式化文本会整段塞进上下文，其中大部分模型根本不需要。

## 按优先级动手

### 1. 上下文卫生（省得最多）
- 换任务先 `/clear`，做完一个子任务 `/compact`。
- 每个独立任务新开会话——判断标准：两个任务不需要引用对方结果，就果断新开。
- 长对话变臃肿时，先让它总结关键结论，新开会话把总结粘在开头。
- 任务没做完但要换会话时，用 [[context-handoff]] 生成交接文档再走。
- 项目里配 `.claudeignore` 排除无关文件，别让它翻不该翻的目录。

### 2. 约束输出（输出比输入贵 3-4 倍）
把硬要求写死进 CLAUDE.md，而不是每次靠模型自由发挥：

```
交互风格
- 零客套：禁止"好的""我明白""为您生成"
- 直接给最优解，不列 A/B 选项，不复述需求

高信噪比代码交付
- 简单改动（改变量名、修小 Bug）：零解释，直给代码
- 新增功能：代码块上方 1-2 句说明；关键决策（安全/性能/架构）必须说理由
- 只输出修改的函数，不贴未改的上下文
- 最小修改，不主动重构；需求模糊时只问 1 个最关键问题

命令输出协议
- 预期输出超过 ~20 行的命令，先过滤或截断
- 默认：COMMAND 2>&1 | head -c 4000
```

Rules 宁短勿长——太长反而干扰模型、催生幻觉。

### 3. 模型分级
机械活（格式转换、简单摘要、代码格式化）用便宜/轻量模型；复杂推理才上最强模型。规划阶段用高档位，方案定了切低一档执行。

### 4. 精准喂上下文
- 直接给完整文件路径，别等它问「文件在哪」。
- 需要精确信息时用 `!bash` 主动喂，而不是让它自己探索翻文件。
- 复杂任务先出计划（只列步骤不写代码），确认后再动手，避免跑偏后整段返工。用 task list 即可，不必开重型 Plan Mode。

### 5. 工具层脱水（可选，需外部工具）
- 命令输出脱水代理（如 RTK，`rtk-ai/rtk`）：把命令输出的格式化废话砍掉只留核心。
- MCP 按需加载（如 mcp2cli，`knowsuchagency/mcp2cli`）：把「全量加载」变「用到才加载」。
- 这些是第三方开源工具，安装会跑外部脚本、宣称的节省率多为作者自测口径。**装前先征得用户同意并自行核实**，不要静默安装。

## 落地动作

当用户想「一次配好」时，主动提议把第 2 节的规则块写进 CLAUDE.md：
- 项目级：`<项目根>/CLAUDE.md`（只对该项目生效）
- 全局：`~/.claude/CLAUDE.md`（对所有项目生效）

写之前先读现有 CLAUDE.md，追加而不是覆盖，并让用户确认范围（项目级还是全局）。
