---
name: feedback-prog-activate-inactive-confirm
description: 程序激活不能只信 activateByName success:true，必须 inactiveObjects 二次确认
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 811dd9b9-ccd2-4c79-8ae3-815d2cb5de58
---

ABAP 程序对象（PROG/P）激活后，不能只依据 `activateByName` 或 `activateObjects` 返回 `success:true, inactive:[]` 就声称已激活；必须立即调用 `inactiveObjects` 二次确认目标对象已离开未激活列表。2026-06-26 修改 ZRPS211 时，`activateByName` 返回成功但用户发现程序未激活，随后 `inactiveObjects` 仍显示 ZRPS211 未激活；必须把 `inactiveObjects` 确认为 `[]` 或至少不含目标对象，才算真正激活。

**Why:** ADT 激活接口可能返回成功但对象仍挂在 inactive 列表；提前报告“已激活”会误导用户，影响生产系统变更判断。

**How to apply:** 修改任意 ABAP 程序/INCLUDE/函数/表后，激活步骤结束必须执行 `inactiveObjects`。如果目标对象仍在列表中，继续用 `activateObjects` 传入 inactiveObjects 返回的完整对象引用（必要时包含传输节点）并再次确认，直到目标对象消失。链接：[[feedback_use_abap_dev_skill]] [[feedback_ddic_table_double_activate]]
