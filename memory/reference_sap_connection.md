---
name: sap-connection
description: SAP 系统连接配置参考（请按你的实际系统填写）
metadata:
  type: reference
---

# SAP 连接配置

> 此文件为模板，请根据你的实际 SAP 系统替换以下信息。

## 连接信息

- **SAP URL**：`http://<your-sap-host>:<port>`（示例：`http://192.168.x.x:8001`）
- **Client**：`<your-client>`（示例：`100` / `151` 等）
- **Language**：`ZH`（中文）或 `EN`（英文）
- **User**：你的 SAP 用户名
- **Password**：你的 SAP 密码

## 实际配置位置

MCP server 通过环境变量读取连接信息，配置位置二选一：

### 方式一：`.env` 文件（位于 MCP 项目根目录）

```env
SAP_URL=http://192.168.x.x:8001
SAP_USER=YOUR_USER
SAP_PASSWORD=YOUR_PASSWORD
SAP_CLIENT=151
SAP_LANGUAGE=ZH
NODE_TLS_REJECT_UNAUTHORIZED=0
```

### 方式二：Claude Code MCP 配置中的 `env` 字段

`~/.claude/.mcp.json` 中：

```json
{
  "mcpServers": {
    "abap-adt-api": {
      "command": "node",
      "args": ["<path>/dist/index.js"],
      "env": {
        "SAP_URL": "http://...",
        "SAP_USER": "...",
        "SAP_PASSWORD": "...",
        "SAP_CLIENT": "151",
        "SAP_LANGUAGE": "ZH"
      }
    }
  }
}
```

## 注意事项

- **不要把真实密码提交到 git** —— `.env` 应已在 `.gitignore`
- **自签名证书 / 内网 HTTP** —— 需要设置 `NODE_TLS_REJECT_UNAUTHORIZED=0`
- **多环境切换** —— 可准备 `.env.dev` / `.env.qas`，按需重命名
