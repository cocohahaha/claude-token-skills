---
name: token-diet
description: Reduce token/cost waste for AI coding agents. Use when the user asks to save tokens or money, hits usage limits, wants lean rules, or mentions context bloat. Covers context hygiene, output caps, model tiering, tool/MCP trimming, and instruction-file updates. Triggers include "省 token", "省钱", "token 太贵", "用量上限", "reduce token usage", "save tokens", "context 太大".
---

# Token Diet — 省 Token 落地清单

给 AI 编码助手省 Token 的可执行清单。核心不是少用 AI，而是**少带无用上下文、少让 Agent 乱探索、少因方向错了反复返工**。按「先诊断、再按优先级动手」的顺序用。

## 先诊断

- 先看平台的上下文/用量视图。Claude Code 用 `/context`；其他 agent 用等价的 usage/context 面板或日志。
- 优先找两类大头：① 自动注入的工具/MCP schema；② 原始命令输出。它们常常比真正的业务上下文更占空间。

## 按优先级动手

### 1. 上下文卫生（省得最多）
- 换任务先清上下文；做完一个子任务后压缩/总结。Claude Code 用 `/clear`、`/compact`。
- 每个独立任务新开会话——判断标准：两个任务不需要引用对方结果，就果断新开。
- 长对话变臃肿时，先让它总结关键结论，新开会话把总结粘在开头。
- 任务没做完但要换会话时，调用 `$context-handoff` 生成交接文档再走。
- 项目里配忽略文件排除无关目录。Claude Code 用 `.claudeignore`；其他平台用对应的 ignore/上下文规则。

### 2. 约束输出
把硬要求写进项目指令文件，而不是每次靠模型自由发挥。Claude Code 用 `CLAUDE.md`；Codex 常用 `AGENTS.md`；其他平台用对应的 instructions 文件：

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
- 需要精确信息时主动提供短命令输出，而不是让它自己大范围探索。Claude Code 可用 `!bash`；其他平台直接贴过滤后的输出。
- 复杂任务先出计划（只列步骤不写代码），确认后再动手，避免跑偏后整段返工。用 task list 即可，不必开重型 Plan Mode。

### 5. 工具层脱水（可选）
- 评估命令输出压缩/摘要代理，只把核心结果塞回上下文。
- 评估按需加载的工具桥接，避免每轮全量注入大量 schema。
- 这些通常是第三方工具，版本和节省率会变。装前先征得用户同意、核实当前文档，不要静默安装。

## 落地动作

当用户想「一次配好」时，主动提议把第 2 节的规则块写进对应指令文件：
- Claude Code 项目级：`<项目根>/CLAUDE.md`
- Claude Code 全局：`~/.claude/CLAUDE.md`
- Codex 项目级：`<项目根>/AGENTS.md`

写之前先读现有文件，追加而不是覆盖，并让用户确认范围（项目级还是全局）。
