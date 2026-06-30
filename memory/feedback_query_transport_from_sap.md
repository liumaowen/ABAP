---
name: feedback_query_transport_from_sap
description: 从SAP查询传输请求信息
metadata: 
  node_type: memory
  type: feedback
  originSessionId: fb4997e6-0088-4856-a24f-2bf939b6bbac
---

## 查询传输请求

从SAP系统查询传输请求信息：
1. 使用 `transportInfo` 工具获取传输请求详情
2. 检查传输请求的状态和内容
3. 确认是否有未释放的请求

### 查询内容
- 传输请求编号
- 请求状态（已释放/未释放）
- 包含的对象列表
- 目标系统

**Why:** 了解传输请求状态，避免冲突
**How to apply:** 修改对象前，先查询相关传输请求
