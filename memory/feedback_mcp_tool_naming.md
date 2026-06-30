---
name: mcp-tool-naming
description: vsp-dev MCP 工具名称格式（无前缀，直接调用）
metadata:
  node_type: memory
  type: feedback
---

vsp-dev MCP 工具**没有命名空间前缀**，直接使用工具名调用，不需要用 ToolSearch 搜索。

**工具名称格式：** 直接调用，如 `SearchObject`、`GetSource`、`EditSource`、`Activate` 等。

**常用工具：**
- `SearchObject` — 搜索 ABAP 对象
- `GetSource` — 获取源码（object_type, name）
- `EditSource` — 精确字符串替换（自动处理 lock+write+unlock）
- `WriteSource` — 完整写入源码
- `SyntaxCheck` — 语法检查
- `Activate` — 激活对象
- `ActivatePackage` — 批量激活
- `RunATCCheck` — ATC 代码检查
- `RunUnitTests` — 单元测试

**⚠️ 重要区别：**
- 旧（abap-dev-bundle）：`mcp__mcp-abap-abap-adt-api__searchObject`（带前缀）
- 新（vsp-dev）：`SearchObject`（无前缀，直接调用）

**Why:** 之前用 ToolSearch 搜索工具名称浪费了很多时间，vsp-dev 工具名称是固定的，直接调用即可。

**How to apply:** 收到 SAP/ABAP 任务时，直接调用 `SearchObject`、`GetSource` 等工具名，不要用 ToolSearch 搜索，不要加前缀。

**相关记忆：** [[feedback_mcp_abap_tool_usage]] — vsp MCP 工具使用规范
