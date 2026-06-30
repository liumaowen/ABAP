---
name: call-function-no-inline-decl
description: CALL FUNCTION 的 IMPORTING/EXPORTING/TABLES 参数位置不允许内联声明
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 32cb3404-3f06-4dc5-857a-189b882becb4
---

ABAP 中 `CALL FUNCTION 'xxx' ... IMPORTING output = DATA(LV_X)` 这种内联声明会报语法错误：
`"此位置不允许内嵌声明 DATA(LV_X)"`。

CALL FUNCTION 的所有参数位置（EXPORTING/IMPORTING/CHANGING/TABLES/EXCEPTIONS）都不支持内联声明 `DATA(...)` 和 `FIELD-SYMBOL(<...>)`。

**Why:** CALL FUNCTION 是经典 ABAP 语法，参数绑定走旧式 RFC 解析路径，不进入新内联声明语法分析。其他场景（如 SELECT INTO @DATA, READ TABLE INTO DATA(), LOOP AT INTO DATA()）都支持内联声明。

**How to apply:**
- ❌ 错误：`CALL FUNCTION 'X' IMPORTING output = DATA(LV_X).`
- ✅ 正确：在 CALL FUNCTION 之前用 `DATA: LV_X TYPE T.` 显式声明，再传入

2026-06-26 案例：ZFM_GP_FICO_JKPZ 修改时调用 CONVERSION_EXIT_ABPSP_INPUT 用 `DATA(LV_POSID)` 报错。

相关：[[feedback-abap-data-declaration-position]]
