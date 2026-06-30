---
name: feedback_show_code_before_write
description: 写入代码前先展示给用户
metadata: 
  node_type: memory
  type: feedback
  originSessionId: fb4997e6-0088-4856-a24f-2bf939b6bbac
---

## 代码展示流程

在使用 `setObjectSource` 写入代码之前：
1. 先向用户展示要写入的代码
2. 解释修改的内容和原因
3. 等待用户确认后再执行写入

### 展示方式
- 使用代码块格式展示修改部分
- 高亮显示变更的行
- 说明修改的目的和影响

**Why:** 确保用户看到实际要写入的代码，避免意外修改
**How to apply:** 每次写入代码前，先展示给用户确认
