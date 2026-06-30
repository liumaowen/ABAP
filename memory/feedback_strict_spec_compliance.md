---
name: strict-spec-compliance
description: 必须严格对照规范执行，不要凭记忆或感觉操作
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 4a02140f-2905-4d71-847d-f1c133d8809f
---

修改代码时必须严格对照规范文档执行，不能凭记忆或"觉得是这样"就直接做。

**Why:** 2026-06-10 修改 ZFM_GP_FICO_YTFYPZ 时，abap-dev skill 明确要求注释格式为 `" <改动描述> by lzf YYYY-MM-DD HH:MM:SS`（时间精确到秒），但我只写了日期 `2026-06-10`，漏掉了时间。用户指出这是因为我没有对照规范，而是凭记忆操作。

**How to apply:**
1. 修改代码前，先打开 abap-dev SKILL.md 对照注释格式规范
2. 不要凭记忆写格式，要逐字对照规范要求
3. 特别注意细节：时间必须精确到秒 `HH:MM:SS`
4. 任何不确定的地方，先查规范再执行

**相关记忆：** [[feedback_show_code_before_write]]、[[feedback_abap_dev_rules]]
