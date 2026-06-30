---
name: feedback_abap_minimal_change
description: ABAP代码最小化修改原则
metadata: 
  node_type: memory
  type: feedback
  originSessionId: fb4997e6-0088-4856-a24f-2bf939b6bbac
---

## 最小化修改原则

进行ABAP代码修改时，应遵循最小化原则：
- 只修改必要的部分，不要"改进"周围的代码
- 不要重构没有问题的代码
- 保持现有代码风格，即使你会用不同的方式写
- 如果发现无关的死代码，提一下但不要删除

### 测试标准
每一行修改都应该直接追溯到用户的需求。

**Why:** 避免引入不必要的变更和潜在问题
**How to apply:** 修改ABAP代码时，只做用户要求的最小必要变更
