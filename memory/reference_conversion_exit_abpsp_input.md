---
name: conversion-exit-abpsp-input-returns-internal
description: CONVERSION_EXIT_ABPSP_INPUT 的 OUTPUT 直接是 WBS 内码 PSPNR，不是外码 POSID
metadata: 
  node_type: memory
  type: reference
  originSessionId: 32cb3404-3f06-4dc5-857a-189b882becb4
---

WBS 外码（PS_POSID, CHAR 24）→ 内码（PS_PSP_PNR, NUMC 8）转换：

**只需一步**，直接用 `CONVERSION_EXIT_ABPSP_INPUT`：

```abap
DATA LV_PSPNR TYPE PS_PSP_PNR.
CALL FUNCTION 'CONVERSION_EXIT_ABPSP_INPUT'
  EXPORTING
    input     = LV_POSID_EXT     " 外码 PS_POSID
  IMPORTING
    output    = LV_PSPNR         " 内码 PS_PSP_PNR（直接拿，不用查 PRPS）
  EXCEPTIONS
    not_found = 1
    OTHERS    = 2.
IF SY-SUBRC <> 0.
  CLEAR LV_PSPNR.
ENDIF.
```

**实现原理（看源码 CONVERSION_EXIT_KONPR_INPUT）：**

ABPSP_INPUT 是包装函数，内部转发到 `CONVERSION_EXIT_KONPR_INPUT`。该函数最后一行：
```abap
OUTPUT = PRPS-PSPNR.
```
所以 OUTPUT 本身就是内码 PSPNR。

**Why:** 不要画蛇添足做两步（先 CONVERSION_EXIT 得 POSID，再 SELECT PRPS-PSPNR） — output 接 PS_POSID（CHAR 24）会把内码字符串塞进 24 位带空格的字段里，后续 SELECT PRPS WHERE POSID = ... 用错码格式匹配，查不到。

**How to apply:**
- output 参数类型直接声明为 `PS_PSP_PNR`
- 不需要 `SELECT FROM PRPS`
- 用 EXCEPTIONS 兜底，找不到时 CLEAR

2026-06-26 案例：ZFM_GP_FICO_JKPZ 修改时第一次走了"两步法"，导致 FITAB-PROJK 实际是 0，BAPI 报"科目 6600250000 需要分配到 CO 对象"。改成"一步法"后修复。

相关：[[project-zsd203-dms-sync]]
