---
name: vsp-dev LockObject 404 fallback 到 mcp-abap
description: vsp-dev LockObject 返回 404 时，回退到 mcp-abap 写代码
metadata:
  type: feedback
---

# vsp-dev LockObject 404 fallback 到 mcp-abap

## 问题场景

vsp-dev 的 `LockObject` 对某些旧式 ABAP 程序（如传统 SE38 创建的）返回 404 错误，导致 `EditSource`/`WriteSource` 无法使用。

## 解决方案

立即回退到 mcp-abap 的写操作工具链：
```
lock(objectUrl) → setObjectSource(objectSourceUrl + lockHandle + transport) → unLock
```

## 关键要点

1. **objectUrl vs objectSourceUrl**：
   - `lock` 使用 `objectUrl`（如 `/sap/bc/adt/programs/programs/ZRMM206A`）
   - `setObjectSource` 使用 `objectSourceUrl`（如 `/sap/bc/adt/programs/programs/ZRMM206A/source/main`）

2. **transport 参数**：
   - 必须使用 `transportInfo` 返回的 HEADER 请求号（K 类型）
   - 不能用任务号（S 类型），否则报 500 错误

3. **验证方法**：
   - 如果 vsp-dev `LockObject` 返回 404，立即切换到 mcp-abap
   - 不要反复尝试 vsp-dev，浪费时间

## 实际案例

2026-07-20 修改 ZRMM206A 程序时：
- vsp-dev `LockObject` 返回 404
- 切换到 mcp-abap `lock` 成功
- 使用 `setObjectSource` + transport S4DK927248 成功上传代码

## 相关链接

- [MCP工具使用](feedback_mcp_abap_tool_usage.md)
- [setObjectSource用主请求号](feedback_setobjectsource_use_header_request.md)
