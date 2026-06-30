---
name: feedback-for-all-entries-no-group-by
description: ABAP FOR ALL ENTRIES 不支持 GROUP BY，需用 COLLECT 替代
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 5f20e045-498e-4753-8c82-f44a21449ae8
---

ABAP OPEN SQL 中 `FOR ALL ENTRIES IN itab` **不能与 `GROUP BY` 一起使用**，否则语法检查报：
`"GROUP" 在此处无效。需要使用 '.'。expected.`

**Why:** ABAP 旧版 OPEN SQL 限制；新版（7.51+ 部分场景）也仍受限。

**How to apply:**
- 需要按驱动表汇总时，**先取明细，再用 `COLLECT` 在内表中按 key 聚合**：
  ```abap
  SELECT zcont dmbtr_act AS zyhxje
    FROM ztab
    INTO TABLE lt_raw
    FOR ALL ENTRIES IN lt_keys
    WHERE zcont = lt_keys-zcont
      AND zprstus IN ( '20', '28' ).

  LOOP AT lt_raw INTO ls_collect.
    COLLECT ls_collect INTO lt_sum.   " COLLECT 自动按非数值字段做 key 聚合数值字段
  ENDLOOP.
  ```
- `COLLECT` 要求目标结构里**数值字段（如 CURR/INT/QUAN）和非数值字段都明确**，非数值字段作为 key，数值字段自动累加
- 案例：ZOA_RFC_ZCONT_JINE_READ1 (2026-06-24)

相关：[[feedback-for-all-entries-type-length-match]]
