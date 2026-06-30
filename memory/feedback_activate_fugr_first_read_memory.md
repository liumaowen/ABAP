---
name: feedback_activate_fugr_first_read_memory
description: 激活函数组前先读取记忆
metadata: 
  node_type: memory
  type: feedback
  originSessionId: fb4997e6-0088-4856-a24f-2bf939b6bbac
---

## 激活函数组前的准备

在激活函数组（Function Group）之前，应该：
1. 先读取相关的记忆文件，了解历史上下文
2. 检查是否有未完成的修改
3. 确认所有依赖对象都已正确配置

### 常见问题
- 函数组激活失败通常是因为依赖对象未激活
- 需要按正确顺序激活：先激活被引用的对象

**Why:** 避免激活失败和依赖问题
**How to apply:** 激活函数组前，先检查记忆和依赖关系
