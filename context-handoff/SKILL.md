---
name: context-handoff
description: Use when an AI coding agent session's context has grown large, slow, or expensive and the user wants to continue the same work in a fresh session without losing state. Generates a structured handoff (交接) document — current goal, key decisions, files touched, exact next step, and gotchas — so a new session resumes seamlessly. Triggers include "上下文太满", "上下文太贵", "交接一下", "新开会话继续", "接着干", "baton pass", "handoff", "context is getting expensive", "compact 不好用".
---

# Context Handoff — 上下文交接

把一段快满/变贵的会话浓缩成一份交接文档，让新会话读一遍就能无缝接着干。等于把「长对话总结 + 新开会话」做成一个可复用动作。灵感来自 `baton-pass` / Matt Pocock 的 handoff 思路，这里是**手动按需触发**的自包含版本，不依赖任何外部脚本或二进制。

## 什么时候用

- `/context` 显示上下文占用高，或对话明显变慢、变贵。
- 当前任务还没做完，但想换个干净会话继续。
- 用户说要「新开会话继续」「交接」「接着干」。

判断标准：如果新任务和当前进度**必须互相引用**才能继续，就该交接（而不是简单 `/clear` 丢掉）。

## 怎么做

1. **确认落盘位置。** 默认写到当前工作目录的 `HANDOFF.md`（新会话在同目录能直接读到）。若无写入权限或用户另有指定，写到 scratchpad 或用户给的路径，并把路径告诉用户。
2. **按下面模板生成文档。** 只写「继续干活真正需要的信息」，不复述整段对话历史——那正是要省掉的 token。代码/路径/命令原样保留，不要压缩成模糊描述。
3. **给出接力指令。** 告诉用户在新会话里说一句：`读一下 HANDOFF.md，从"下一步"接着干`。

## 交接文档模板

```markdown
# 交接文档 — <一句话任务名>
> 生成于 <日期>，上一棒会话

## 目标
<这段工作最终要达成什么，一两句话>

## 已完成
- <关键进展 1（含具体文件/函数/结论）>
- <关键进展 2>

## 关键决策与约束
- <为什么选 A 不选 B；踩过的坑；不能碰的东西>

## 涉及的文件
- `path/to/file` — <改了什么 / 为什么相关>

## 下一步（接力从这里开始）
1. <非常具体的下一个动作，能直接执行>
2. <再下一步>

## 注意事项 / 坑
- <环境、依赖、边界条件、验证命令等>
```

## 原则

- **只留必要信息。** 交接文档本身也要省 token——它是给下一棒看的工作现场，不是会议纪要。
- **可执行优先。** 「下一步」要具体到能直接动手，避免下一棒重新摸索。
- **别丢硬信息。** 文件路径、命令、报错原文、版本号照抄，别改写成概述。
- 写完只报告落盘路径和接力指令，不复述文档全文。
