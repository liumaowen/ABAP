---
name: feedback-abap-dev-no-manual-debug
description: ABAP 开发中不要脱离 skill 去查看 MCP 源码或自行调试工具
metadata:
  node_type: memory
  type: feedback
---

ABAP 开发任务中，即使遇到 MCP 工具报错，也必须继续遵循 `skill/SKILL.md` 的流程，不要自行打开 MCP server 源码、调试工具实现或偏离 SAP 修改流程。用户在 2026-06-26 明确纠正："不是用 abap-dev 来处理 abap 开发吗"。

**Why:** ABAP 开发规则的最高优先级是由 `skill/SKILL.md` 封装流程和工具使用方式；自行排查工具源码会偏离业务修改目标，也违反用户对 ABAP 开发流程的要求。

**How to apply:** 收到 ABAP 开发任务先加载 `skill/SKILL.md`；后续只按该 skill 指定流程执行。若 `SyntaxCheck` 等工具异常，向用户说明工具异常和已完成的 skill 步骤，不要改查 MCP server 源码或自创流程。链接：[[feedback_use_abap_dev_skill]] [[feedback_mcp_abap_tool_usage]]
