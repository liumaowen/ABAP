---
name: feedback_mcp_abap_tool_usage
description: vsp-dev MCP 工具使用规范
metadata:
  node_type: memory
  type: feedback
---

## vsp-dev MCP 工具使用规范

进行任何 SAP/ABAP 相关操作时，必须使用 `vsp-dev` MCP 工具（工具名无前缀，直接调用）：

### 核心工具
- `SearchObject` — 搜索对象（query, objType）
- `GetSource` — 获取源码（object_type, name）
- `EditSource` — 精确字符串替换（object_type, name, find, replace）— 自动处理锁定/写入/解锁
- `WriteSource` — 完整写入源码（object_type, name, source）
- `SyntaxCheck` — 语法检查
- `Activate` — 激活单个对象
- `ActivatePackage` — 批量激活包内对象
- `RunATCCheck` — ATC 代码质量检查
- `RunUnitTests` — 运行 ABAP Unit 测试

### 调试工具
- `DebuggerListen` / `DebuggerAttach` / `DebuggerStep` / `DebuggerGetVariables`
- `AMDPDebuggerStart` — HANA SQLScript 调试

### 禁止行为
- 禁止写 Node.js/Python 脚本来调用 MCP server
- 禁止使用 curl 或其他 HTTP 客户端直接调用 ADT API

**Why:** vsp 工具已封装好 SAP 连接、认证、锁定/解锁、传输请求等逻辑，直接调用更可靠、更高效。

**How to apply:** 每次收到 SAP/ABAP 相关任务时，直接使用对应的 vsp 工具，不需要加任何命名空间前缀。

**相关记忆：** [[feedback_mcp_tool_naming]] — 工具名称格式说明
