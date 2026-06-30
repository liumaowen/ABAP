---
name: reference-cl-salv-runtime-info
description: CL_SALV_BS_RUNTIME_INFO 对 REUSE_ALV / CL_SALV / 自建 Grid 都有效
metadata: 
  node_type: memory
  type: reference
  originSessionId: fc967fa7-d165-4541-ad49-3958de84417f
---

`CL_SALV_BS_RUNTIME_INFO=>SET( DATA = ABAP_TRUE )` 是底层 ALV Runtime 拦截器，会拦截 SUBMIT 子程序的 ALV 显示请求并把数据保存下来。

**对所有标准 ALV 显示路径都有效**：
- `CL_SALV_TABLE`（OO 方式）
- `REUSE_ALV_GRID_DISPLAY` / `REUSE_ALV_LIST_DISPLAY`（函数式）
- `CL_GUI_ALV_GRID`（自建 Grid）

**典型使用模式：**
```abap
CL_SALV_BS_RUNTIME_INFO=>SET( DISPLAY = ABAP_FALSE METADATA = ABAP_FALSE DATA = ABAP_TRUE ).
SUBMIT zxxx WITH ... AND RETURN.
TRY.
    CL_SALV_BS_RUNTIME_INFO=>GET_DATA_REF( IMPORTING R_DATA = ls_data ).
  CATCH cx_salv_bs_sc_runtime_info.
    " 失败处理
ENDTRY.
ASSIGN ls_data->* TO FIELD-SYMBOL(<fs_tab>).
CL_SALV_BS_RUNTIME_INFO=>CLEAR_ALL( ).
```

**Why:** 2026-06-24 修改 ZFM_GP_SD_DMS_XMCX 时，我曾错误判断"REUSE_ALV 取不到数据，要改用 EXPORT/IMPORT MEMORY"。实际上 ZRPS211 是 REUSE_ALV_GRID_DISPLAY 报表，函数里这套写法依然能取到数据。用户纠正：CL_SALV_BS_RUNTIME_INFO 通吃所有 ALV。

**How to apply:** 看到函数里用 `CL_SALV_BS_RUNTIME_INFO=>SET + SUBMIT + GET_DATA_REF` 模式时，**不要**因为 SUBMIT 的报表用 REUSE_ALV 就判定取不到数据。这个机制对所有标准 ALV 都有效。
