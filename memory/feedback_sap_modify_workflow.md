---
name: feedback_sap_modify_workflow
description: SAP修改工作流规范
metadata: 
  node_type: memory
  type: feedback
  originSessionId: fb4997e6-0088-4856-a24f-2bf939b6bbac
---

## SAP修改工作流

### 标准流程
1. **搜索对象** - 使用 `searchObject` 找到对象URI
2. **读取源码** - 使用 `getObjectSource` 读取当前代码
3. **分析修改** - 理解现有代码，规划修改方案
4. **锁定对象** - 使用 `lock` 锁定要修改的对象
5. **写入修改** - 使用 `setObjectSource` 写入新代码
6. **语法检查** - 使用 `syntaxCheckCode` 验证语法
7. **激活对象** - 使用 `activate` 激活修改
8. **解锁对象** - 使用 `unLock` 解锁对象

### 关键点
- 不要编造ABAP代码，不确定时询问用户
- 复杂变更先与用户确认方案
- 传输释放需要用户明确批准
- SAP是生产系统，错误会影响业务

**Why:** 确保SAP修改的正确性和安全性
**How to apply:** 每次SAP修改都遵循此工作流
