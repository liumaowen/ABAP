---
name: feedback-comment-timestamp-precision
description: ABAP注释修改时间必须精确到秒
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 61b80cbe-20ed-4470-82dc-3a827131b0ee
---

ABAP 代码修改注释的格式必须精确到秒，不能只写日期。

**正确格式：** `" <改动描述> by lzf YYYY-MM-DD HH:MM:SS`
**错误格式：** `" <改动描述> by lzf YYYY-MM-DD`

**示例：**
```abap
" ✅ 正确
IF WA_EKKO-ZGBJC <> 'X'.  " ZGBJC=X 则不走集采 by lzf 2026-06-12 15:51:13

" ❌ 错误
IF WA_EKKO-ZGBJC <> 'X'.  " ZGBJC=X 则不走集采 by lzf 2026-06-12
```

**Why:** 精确到秒可以追踪修改的具体时间点，便于问题排查和版本管理。
**How to apply:** 每次修改 ABAP 代码时，使用 `date '+%Y-%m-%d %H:%M:%S'` 获取当前时间，确保注释包含完整的时分秒。

相关记忆：[[feedback_abap_dev_rules]]、[[feedback_sap_modify_workflow]]
