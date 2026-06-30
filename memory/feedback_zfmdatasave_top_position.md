---
name: feedback-zfmdatasave-top-position
description: RFC 函数中 ZFMDATASAVE1/2 + COMMIT WORK 必须放在函数体最顶部
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 5f20e045-498e-4753-8c82-f44a21449ae8
---

修改 RFC 函数模块时，`ZFMDATASAVE1 / ZFMDATASAVE2 + COMMIT WORK` **必须放在函数体最顶部**（IMPORTING/EXPORTING/TABLES 之后第一段可执行语句），不能放到业务逻辑后面。

**正确结构：**
```abap
FUNCTION xxx.
  IMPORTING/EXPORTING/TABLES ...

  ZFMDATASAVE1 '函数名' .          ← 顶部，第一段可执行语句
  ZFMDATASAVE2 'B'.
  COMMIT WORK.

  " 业务逻辑：TYPES / DATA / SELECT / LOOP ...

  IF OT_TAB[] IS NOT INITIAL.
    RET_MESSAGE 'S' '成功!' 'X' 'X'.
  ELSE.
    RET_MESSAGE 'E' '无数据！' 'X' 'X'.
  ENDIF.
ENDFUNCTION.
```

**Why:** ZFMDATASAVE1/2 是项目自定义的 RFC 调用日志保存宏，必须在业务逻辑执行前记录调用入口；否则业务异常（DUMP/退出）时日志会丢失，无法追溯调用记录。

**How to apply:**
- 修改 RFC 函数时，先看 `ZFMDATASAVE` 当前位置
- 若不在顶部，新业务代码插在 `COMMIT WORK` **之后**
- 若已在顶部，保持原样
- 新加 RFC 函数时，按"顶部日志 + 业务逻辑 + 末尾返回消息"的顺序写

**与"顶部集中声明"原则的协调：** ABAP 通用最佳实践"DATA/TYPES 在顶部"在这里让位 — `TYPES/DATA` 紧跟在 COMMIT WORK 之后即可（仍在业务逻辑使用之前，语法合法）

**案例：** ZOA_RFC_ZCONT_JINE_READ1 (2026-06-24) — 第一次写时把 ZFMDATASAVE 留在业务逻辑后面（保留原结构），用户明确要求必须放最前面

相关：[[feedback-abap-data-declaration-position]] · [[feedback-abap-minimal-change]]
