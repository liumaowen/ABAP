---
name: feedback-for-all-entries-type-length-match
description: ABAP FOR ALL ENTRIES 字段类型和长度必须严格一致，否则语法报错
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 5f20e045-498e-4753-8c82-f44a21449ae8
---

ABAP OPEN SQL 中 `FOR ALL ENTRIES IN itab WHERE table_field = itab-field` 要求**两边字段类型和长度完全一致**，否则语法检查报：
`If the addition "FOR ALL ENTRIES IN itab" is used, the fields "X" and "Y-Z" must have the same type and the same length.`

**踩坑案例：** ZTFI_PYREHD-ZCONT 是 CHAR(18)，ZTFI_PYREHD-ZOANO 是 CHAR(20)，直接写
```abap
... FOR ALL ENTRIES IN lt_main WHERE zcont = lt_main-zoano ...
```
会报错 ❌

**Why:** ABAP 内核要求 FAE 优化时两边字段二进制对齐，类型/长度不一致无法做高效拼接。

**How to apply:**
- **优先方案**：构造一个**目标类型**的 key 表作驱动表
  ```abap
  TYPES: BEGIN OF ty_key,
           zcont TYPE ztfi_pyrehd-zcont,   " 用 ZCONT 类型（CHAR 18）
         END OF ty_key.
  DATA: lt_keys TYPE STANDARD TABLE OF ty_key,
        ls_key  TYPE ty_key.

  LOOP AT lt_main INTO DATA(ls_drv).
    ls_key-zcont = ls_drv-zoano.   " CHAR(20) → CHAR(18)（截断/赋值兼容）
    APPEND ls_key TO lt_keys.
  ENDLOOP.
  SORT lt_keys BY zcont.
  DELETE ADJACENT DUPLICATES FROM lt_keys COMPARING zcont.

  SELECT ... FROM ztab
    FOR ALL ENTRIES IN lt_keys
    WHERE zcont = lt_keys-zcont.
  ```
- **次选方案**：把驱动表中的字段提前用 `CAST` / 中间变量转换成匹配类型
- **判断**：写 FAE 前先用 `runQuery SELECT FIELDNAME, DATATYPE, LENG FROM DD03L WHERE TABNAME = ...` 确认两边字段长度

**案例：** ZOA_RFC_ZCONT_JINE_READ1 (2026-06-24) — 主单据 ZOANO 反向查核销单据 ZCONT 时踩坑

相关：[[feedback-for-all-entries-no-group-by]]
