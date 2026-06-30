---
name: setobjectsource-use-header-request
description: "setObjectSource 的 transport 必须传主请求号(HEADER, TRFUNCTION=K)，不能传任务号(TASK, TRFUNCTION=S)，否则报 500"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 24fc442d-022c-44a6-9b99-789cee0f98fb
---

`setObjectSource` 写入时 `transport` 参数必须传**主请求号**（HEADER request, TRFUNCTION='K'），不能传任务号（TASK, TRFUNCTION='S'），否则 ADT API 返回 `Error 500: Internal Server Error`。

**Why:** 2026-06-18 修改 ZFM_GP_SD_DMS_HTZXBBCX 时，第一次用 transportInfo 返回的 `LOCKS.TASKS[0].TRKORR=S4DK926814`（任务号）传 setObjectSource 报 500；改用 `LOCKS.HEADER.TRKORR=S4DK926813`（主请求号）后立即成功。任务号不被 ADT 接受，必须用主请求号。

**How to apply:**
- 从 `transportInfo` 取传输请求时，永远读 `LOCKS.HEADER.TRKORR`
- 看 `TRFUNCTION` 字段区分：`K`=开发请求（主），`S`=任务（子）
- 询问用户传输请求时只展示主请求号，不展示任务号
- 关联 [[feedback-mcp-abap-tool-usage]] [[feedback-sap-modify-workflow]]
