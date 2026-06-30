---
name: feedback-ddic-table-double-activate
description: DDIC 表 activateObjects 返回 success:true 仍可能未真正激活，需 activateByName 兜底 + inactiveObjects 确认
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 0b14126b-a180-4f4e-a672-2055b0068432
---

DDIC 透明表 (TABL/DT) 激活时，先用 `activateObjects`（parentUri=对象自身 URI）。即使返回 `success:true, inactive:[]`，**激活可能未真正生效到 runtime**——后续依赖该表的程序激活会报"数据对象 XXX 没有名为 YYY 的组件"。

**Why:** 2026-06-18 ZTPP_205 案例：activateObjects 返回 success:true 后立即激活 ZPPD201_V4_FORM，wa_205 (TYPE ztpp_205) 找不到新增字段。再调一次 `activateByName` 才真正激活，inactiveObjects 才把 ZTPP_205 移出列表。属于已知错误 #2/#13 的组合变种。

**How to apply:**
1. DDIC 表激活：先 `activateObjects` (parentUri=对象自身 URI)
2. **必须**用 `inactiveObjects` 二次确认对象已离开列表，不能只信 success:true
3. 如果 inactiveObjects 仍含此对象，再调 `activateByName(objectName, objectUrl)` 兜底
4. 再次 `inactiveObjects` 确认 [] 后，才能继续激活依赖此表的程序

相关 [[reference-activate-function-module]]、[[feedback-mcp-abap-tool-usage]]
