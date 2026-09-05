---
name: grill-with-docs
description: "仅在用户明确调用 `$grill-with-docs`、`/grill-with-docs`，或明确要求在高强度访谈中同步沉淀术语表和 ADR 时使用。Explicit-entry wrapper for a relentless design interview that updates CONTEXT.md and records qualifying ADRs as decisions crystallize. 中文关键词：带文档拷问、边问边写文档、同步术语表、同步 ADR。"
disable-model-invocation: true
---

# Grill With Docs

同时调用 `grilling` 和 `domain-modeling` 技能：前者按决策树压力测试计划或设计，后者在术语与决策明确时同步维护领域文档。

完整执行两个技能的规则。在这个组合中，更新 `CONTEXT.md`、`CONTEXT-MAP.md` 和符合条件的 ADR，是 `grilling`“确认共识前不修改文件”规则的唯一例外；确认之前仍不得实施方案、修改产品代码或执行被讨论的决定。
