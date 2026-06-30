---
name: feedback_abap_dev_rules
description: ABAP开发规则和最佳实践反馈
metadata: 
  node_type: memory
  type: feedback
  originSessionId: fb4997e6-0088-4856-a24f-2bf939b6bbac
---

## ABAP开发规则

### 代码修改流程
1. 先搜索对象：`searchObject`
2. 读取源码：`getObjectSource`（URI后加 `/source/main`）
3. 分析代码，参考类似SAP代码或网络搜索
4. 锁定对象：`lock`
5. 写入修改：`setObjectSource`
6. 语法检查：`syntaxCheckCode`
7. 激活对象：`activate`
8. 解锁对象：`unLock`

### 关键规则
- 不要编造ABAP代码 - 如果不确定，询问用户
- 修改后必须运行 `syntaxCheckCode`
- 复杂变更需先与用户确认方案
- 传输释放（`transportRelease`）需要用户明确批准
- SAP是生产系统 - 错误会影响业务运营

**Why:** 确保ABAP开发的正确性和安全性
**How to apply:** 每次进行ABAP开发时遵循此流程
