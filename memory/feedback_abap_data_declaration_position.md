---
name: feedback-abap-data-declaration-position
description: ABAP 代码允许内联声明（DATA(x)），但禁止就地声明（在LOOP/IF块中间写DATA:块）
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 369ed2a3-d31e-4274-809d-ce973a24d6e6
---

ABAP 代码声明规则（2026-06-23 用户更新）：

**✅ 允许**：
- 函数/程序开头的集中 `DATA:` / `TYPES:` 声明块
- **内联声明** `DATA(变量名)`、`@DATA(...)`、`FIELD-SYMBOL(<fs>)`、`LOOP AT itab INTO DATA(ls)`、`SELECT ... INTO TABLE @DATA(lt_x)` 等现代 ABAP 7.4+ 语法

**❌ 禁止**：
- **就地声明**：在 `LOOP` / `IF` / `FORM` 块中间穿插 `DATA: xxx TYPE ...` 这种传统声明块（应当挪到开头）
- 在执行语句之间穿插声明区

**Why:** 内联声明（`DATA(...)`）是现代 ABAP 标准用法，类型自动推导且作用域清晰，团队接受。但传统的 `DATA:` 声明块仍要求集中在函数顶部，避免分散难找。

**历史**：2026-06-17 在修改 ZFM_GP_FICO_LYPZ 时曾要求"全部挪到函数开头"。2026-06-23 用户在 ZDMS001 修改后澄清规则：**允许内联声明，但禁止就地声明**。

**How to apply:**
1. 写新代码时，传统 `DATA:` 声明块必须放在函数/程序开头的 DATA 声明区
2. **可以**自由使用 `DATA(x)`、`@DATA(lt)`、`FIELD-SYMBOL(<fs>)`、`INTO DATA(ls)` 等内联语法
3. **不要**在 `LOOP`/`IF`/`FORM` 中间写 `DATA: xxx TYPE ...` 块，挪到函数开头
4. 修改已有代码时，新增的传统 DATA 块也必须放到开头，与原有 DATA 声明保持一致

**示例对比**：
```abap
" ✅ 允许：开头集中声明
DATA: lt_mara TYPE TABLE OF mara,
      lv_count TYPE i.

" ✅ 允许：内联声明
SELECT * FROM mara INTO TABLE @DATA(lt_x).
LOOP AT lt_x INTO DATA(ls_x).
  DATA(lv_str) = |{ ls_x-matnr }|.
ENDLOOP.

" ❌ 禁止：就地声明块
LOOP AT lt_x INTO ls_x.
  DATA: lv_tmp TYPE string.   " <-- 应挪到开头
  lv_tmp = ls_x-matnr.
ENDLOOP.
```

相关：[[feedback-abap-minimal-change]] [[feedback-abap-dev-rules]]
