---
name: feedback_check_transport_first
description: 先检查传输请求状态
metadata: 
  node_type: memory
  type: feedback
  originSessionId: fb4997e6-0088-4856-a24f-2bf939b6bbac
---

## 传输请求检查

在进行任何修改之前，应该先检查传输请求的状态：
1. 使用 `transportInfo` 获取传输请求信息
2. 确认传输请求是否已释放
3. 检查是否有冲突的修改

### 检查要点
- 传输请求是否处于可修改状态
- 是否有其他用户正在修改同一对象
- 传输请求的依赖关系

**Why:** 避免修改冲突和传输问题
**How to apply:** 每次修改前先检查传输请求状态
