---
name: feedback_use_abap_dev_skill
description: 使用 ABAP 开发技能（现位于 skill/SKILL.md，配合 vsp-dev MCP）
metadata:
  node_type: memory
  type: feedback
---

## ABAP 开发技能使用 ⚠️⚠️⚠️ 最高优先级规则

**只要是 ABAP 开发相关的问题，必须优先调用 `abap-dev` skill 来处理（对应 `skill/SKILL.md`）。禁止直接手动拼 MCP 工具。**

### 触发条件（必须调用 skill）
- 查看 ABAP 程序/函数/类的源码
- 修改 ABAP 代码
- 激活 ABAP 对象
- 创建/删除 ABAP 对象
- 语法检查
- 任何涉及 SAP/ABAP 的开发任务

### 禁止行为
- ❌ 禁止直接调用 MCP 工具（即使知道工具名称）
- ❌ 禁止跳过 skill 中的流程
- ❌ 禁止自己摸索激活方式
- ❌ 禁止写脚本调用 MCP server

### 正确流程
1. 收到 ABAP 任务 → 第一时间加载 `skill/SKILL.md`
2. 按照 skill 中的标准流程执行（使用 vsp-dev 工具：`GetSource`/`EditSource`/`SyntaxCheck`/`Activate`）
3. 遵循最小改动原则

### 违规记录
- **2026-06-10**：未调用 skill，直接手动拼 MCP 工具调用，导致中文注释编码损坏（乱码）、激活流程出错。用户严厉要求必须遵守此规则。

**Why:** skill 封装了完整的开发流程、错误处理和最佳实践，避免重复踩坑。直接调用 MCP 工具容易遗漏步骤、编码出错。

**How to apply:** 收到任何 ABAP 开发任务时，第一步就是加载 `skill/SKILL.md`，不要犹豫，不要自己手动拼 MCP 工具。这是最高优先级规则，无例外。

**相关记忆：** [[feedback_mcp_tool_naming]] — vsp 工具名称格式说明
