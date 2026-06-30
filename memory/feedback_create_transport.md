---
name: feedback_create_transport
description: 创建传输请求的流程
metadata: 
  node_type: memory
  type: feedback
  originSessionId: fb4997e6-0088-4856-a24f-2bf939b6bbac
---

## 创建传输请求

当需要创建新的传输请求时：
1. 确认是否真的需要新请求（可能已有合适的请求）
2. 使用 `createObject` 创建传输请求
3. 记录传输请求编号供后续使用

### 注意事项
- 传输请求描述要清晰明了
- 选择正确的传输层和目标系统
- 记录创建的传输请求编号

**Why:** 确保传输请求的正确创建和管理
**How to apply:** 需要新传输请求时，按照标准流程创建
