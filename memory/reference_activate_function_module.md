---
name: reference_activate_function_module
description: 激活函数模块参考
metadata: 
  node_type: memory
  type: reference
  originSessionId: fb4997e6-0088-4856-a24f-2bf939b6bbac
---

## 激活对象通用规则

### ⚠️ 统一规则：任何对象激活前都必须先解锁

**所有对象类型（函数模块、程序、INCLUDE）在锁定状态下激活都会报 404！**

### 标准流程
1. 使用 `lock` 锁定对象
2. 使用 `setObjectSource` 写入代码
3. 使用 `syntaxCheckCode` 进行语法检查
4. **使用 `unLock` 解锁对象** ← 必须在激活前
5. 使用 `activateByName` 激活对象

### 常见问题
- **404 错误** - 对象在锁定状态激活，必须先 unLock
- **语法错误** - 使用 `syntaxCheckCode` 查看详细错误
- **锁定冲突** - 检查是否有其他用户正在修改

### 依赖顺序
1. 先激活数据元素和域
2. 再激活结构和表类型
3. 然后激活函数组
4. 最后激活函数模块

**Why:** 所有对象类型在锁定状态下激活都会报 404，统一规则避免混淆
**How to apply:** 激活任何对象时，都遵循 lock → write → syntaxCheck → unLock → activate 顺序
