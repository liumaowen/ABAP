---
name: use-sap-abap-skill
description: ABAP开发时要善于使用sap-abap skill进行代码检查和语法参考
metadata: 
  node_type: memory
  type: feedback
  originSessionId: d6ef1b87-eeae-400c-a72a-7cd566525bdf
---

## 使用 sap-abap skill 进行代码检查和语法参考

在进行 ABAP 开发时，要善于使用 `sap-abap` skill 来进行：
1. **代码检查** — 修改代码后，调用 sap-abap skill 检查语法正确性、最佳实践、性能问题
2. **语法参考** — 遇到不确定的语法时，调用 sap-abap skill 查询 SAP 官方参考

**Why:** sap-abap skill 包含 SAP 官方 ABAP Cheat Sheets 的完整参考（28个参考文件），覆盖 ABAP 7.40 SP08 到 ABAP Cloud 的所有语法，能确保代码质量和兼容性。

**How to apply:**
- 每次修改代码后，使用 `/sap-abap` 进行检查
- 遇到语法疑问时，使用 `/sap-abap` 查询参考
- 已更新到 abap-dev skill 的第十二章

## 相关记忆
- [[feedback_abap_dev_rules]] — ABAP 开发规范
- [[feedback_strict_spec_compliance]] — 严格对照规范执行
