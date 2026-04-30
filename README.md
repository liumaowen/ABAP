# ABAP Development with Claude Code & MCP

[![GitHub](https://img.shields.io/badge/GitHub-liumaowen%2FABAP-blue)](https://github.com/liumaowen/ABAP)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Active-brightgreen.svg)]()

**Claude Code 辅助 ABAP 开发 MCP 规则环境**

一套完整的 ABAP 开发工作区规则框架和 MCP（Model Context Protocol）环境配置，用于在 Claude Code 中进行 SAP ABAP 系统开发、维护、重构和测试。

## 📖 概述

本项目为 Claude Code 在 SAP ABAP 开发中的工作提供了以下支持：

- **规则引擎**：详细的 ABAP 开发规范、修改流程、测试要求
- **MCP 集成**：通过 MCP 服务器连接本地 Claude Code 与远端 SAP 系统
- **工作流自动化**：从需求理解到代码提交的完整流程定义
- **安全机制**：高风险操作审核、Transport Request 追踪、权限管理
- **中文优先**：所有文档和指导均以简体中文输出

## 🎯 主要特性

### 1. **ABAP 包含程序递归处理**
- 自动识别和展开所有 `INCLUDE` 语句
- 完整构建包含树并进行整体分析
- 避免只看主程序的片面理解

### 2. **完整的开发工作流**
```
理解需求 → 分析对象 → 输出方案 → 确认 → 最小变更 → 生成测试 → 人工复核 → 提交
```

### 3. **丰富的 ABAP 对象支持**
| 对象类型 | 说明 | 示例 |
|---------|------|------|
| PROG | ABAP 程序/报表 | ZLMW_REPORT |
| CLAS | ABAP 类 | ZCL_LMW_ORDER |
| INTF | ABAP 接口 | ZIF_LMW_SERVICE |
| FUNC | 函数模块 | Z_LMW_FM |
| FUGR | 函数组 | Z_LMW_FG |
| DDLS | CDS DDL 源 | ZLMW_I_ORDER |
| BDEF | 行为定义 | ZLMW_C_ORDER |
| SRVD | 服务定义 | ZLMW_SRVD_ORDER |
| MSAG | 消息类 | ZLMW_MSG |
| TABL | DDIC 数据表 | ZLMW_T_LOG |

### 4. **MCP 工具集成**（80+ ABAP 工具）
- **搜索**: SearchObject - 按名称、类型、包查询
- **读取**: GetSource - 获取对象源代码
- **编辑**: EditSource、WriteSource - 精确修改或完整写入
- **检查**: SyntaxCheck、RunATCCheck、AnalyzeABAPCode
- **激活**: Activate、ActivatePackage - 单个或批量激活
- **测试**: RunUnitTests、GetCodeCoverage - ABAP Unit 执行和覆盖率
- **调试**: DebuggerListen、DebuggerAttach、DebuggerStep 等
- **AMDP**: AMDPDebuggerStart - SQLScript 调试

### 5. **智能变更管理**
- **Transport Request 追踪**：所有可运输对象必须绑定请求
- **高风险操作审核**：标准对象、大范围重构、权限变更等需确认
- **最小变更原则**：避免顺手重构、范围扩大
- **完整的变更前/后清单**

## 📁 项目结构

```
.
├── README.md                      # 项目说明（本文件）
├── CLAUDE.md                      # Claude Code 工作指南 ⭐ 必读
├── ABAP_PROJECT_RULES.md          # ABAP 开发规范
├── CHANGE_WORKFLOW.md             # 变更流程定义
├── TEST_CHECKLIST.md              # 测试清单
├── OBJECT_SCOPE.md                # 对象范围和约束
├── ABAP_ENV.md                    # SAP 环境配置
│
├── .mcp.json                      # MCP 服务器配置 ⭐ 需填充
├── .mcp.json.example              # MCP 配置示例
├── .env                           # SAP 连接凭证 ⭐ 需填充
├── .env.example                   # 环境变量示例
├── .vsp.json                      # 多系统 VSP 配置 ⭐ 需填充
├── .vsp.json.example              # VSP 配置示例
│
├── .claude/                       # Claude Code 工作目录
│
└── vsp.exe                        # ABAP 命令行工具（24.3 MB）
```

## 🚀 快速开始

### 前置要求
- Claude Code IDE 或 claude.ai/code
- SAP S/4HANA 系统访问权限
- ABAP 基础知识

### 1. 克隆项目
```bash
git clone https://github.com/liumaowen/ABAP.git
cd ABAP
```

### 2. 配置 SAP 连接
复制示例文件并填入真实信息：

```bash
cp .env.example .env
cp .mcp.json.example .mcp.json
cp .vsp.json.example .vsp.json
```

编辑 `.env`：
```env
SAP_URL=https://your-sap-system:port
SAP_USER=your_username
SAP_PASSWORD=your_password
SAP_CLIENT=151
SAP_LANGUAGE=EN
```

编辑 `.mcp.json`：
```json
{
  "name": "vsp-dev",
  "command": "./vsp.exe",
  "args": ["--mode", "mcp", "--system", "S4D"],
  "env": {
    "SAP_URL": "${SAP_URL}",
    "SAP_USER": "${SAP_USER}",
    "SAP_PASSWORD": "${SAP_PASSWORD}"
  }
}
```

### 3. 启用 MCP 服务
在 Claude Code 设置中配置 MCP：
1. 打开项目设置
2. 启用 MCP 服务器：`vsp-dev`
3. 加载此项目的 `.mcp.json`

### 4. 开始开发
在 Claude Code 中使用 Slash 命令：

```
/read-abap                 # 读取并解释当前 ABAP 对象
/plan-change               # 规划变更（先输出方案，不直接修改）
/review-change             # 严格 Code Review
/generate-unit-tests       # 为对象生成或补充 ABAP Unit 测试
/analyze-dump              # 分析 dump / 日志 / 异常
```

## 📋 核心文档说明

### 🔷 [CLAUDE.md](CLAUDE.md) — 必读指南
- Claude Code 在此项目中的工作规则
- ABAP INCLUDE 处理规则
- SAP 环境信息（S4D, Client 151, DEV）
- MCP 工具调用流程
- 常用开发流程示例

### 🔷 [ABAP_PROJECT_RULES.md](ABAP_PROJECT_RULES.md) — 开发规范
- 基本原则（5 项）
- 代码规范（6 项）
- ABAP 约束（7 项）
- 修改策略（5 项）
- 测试要求（4 项）
- AI 行为要求（5 项）

### 🔷 [CHANGE_WORKFLOW.md](CHANGE_WORKFLOW.md) — 变更流程
- 标准流程（6 个步骤）
- 修改前必输出内容
- 修改时的 4 项原则
- 修改后必输出内容
- 高风险操作审核

### 🔷 [TEST_CHECKLIST.md](TEST_CHECKLIST.md) — 测试清单
- 基础检查项
- 业务检查项
- 技术检查项
- 回归检查项
- 测试类型
- 验收标准

### 🔷 [OBJECT_SCOPE.md](OBJECT_SCOPE.md) — 对象范围
- 环境说明
- 允许操作的对象
- 操作约束
- 修改原则
- Transport Request 要求

### 🔷 [ABAP_ENV.md](ABAP_ENV.md) — 环境配置
- SAP 系统信息
- 访问方式
- 权限边界
- 允许范围
- 风险关注点

## 📊 SAP 环境信息

| 配置项 | 值 |
|--------|-----|
| **系统名称** | S4D |
| **Client** | 151 |
| **环境** | DEV（开发） |
| **权限级别** | FULL_WRITE |
| **允许创建 Transport Request** | ✅ 是 |
| **允许激活对象** | ✅ 是 |
| **允许包** | Z*, Y*, $TMP, $ZADT_VSP |

## 🔒 安全和风险管理

### 高风险操作（必须暂停等待确认）
- ❌ 修改标准对象
- ❌ 大范围重构
- ❌ 修改权限逻辑
- ❌ 修改 Transport 策略
- ❌ 修改接口契约
- ❌ 修改数据模型
- ❌ 修改生产相关逻辑

### 必须执行的检查
- ✅ 语法检查（SyntaxCheck）
- ✅ ATC 检查（RunATCCheck）
- ✅ ABAP Unit 测试（RunUnitTests）
- ✅ Transport Request 追踪
- ✅ 人工代码审查

## 💡 工作流示例

### 场景：修改一个 ABAP 报表

```
1. 理解需求
   └─ 用户提出需求：在报表中增加一个过滤字段

2. 分析相关对象
   └─ 用 /read-abap 读取报表源码和所有 INCLUDE
   └─ 理解数据逻辑、屏幕流程、输出格式

3. 输出方案（不直接修改）
   └─ 用 /plan-change 制定变更方案
   └─ 说明目标、影响对象、风险点、验证方法

4. 确认方案
   └─ 用户确认方案是否可接受

5. 执行最小变更
   └─ 创建 Transport Request
   └─ 通过 EditSource / WriteSource 进行修改
   └─ 执行 SyntaxCheck 检查语法

6. 生成测试
   └─ 用 /generate-unit-tests 生成 ABAP Unit 测试
   └─ 执行 RunUnitTests 验证

7. 人工复核
   └─ 用 /review-change 进行严格代码审查
   └─ 手工测试报表功能

8. 提交
   └─ 激活对象（Activate）
   └─ 提交 Transport Request
   └─ 输出修改总结
```

## 🛠️ 常用命令

### MCP 工具调用
```
搜索对象: SearchObject(pattern="ZCL_*", type="CLAS")
读取源码: GetSource(object_type="PROG", name="ZLMW_REPORT")
编辑代码: EditSource(object_type="CLAS", name="ZCL_LMW", find="...", replace="...")
语法检查: SyntaxCheck(object_type="PROG", name="ZLMW_REPORT")
激活对象: Activate(object_type="CLAS", name="ZCL_LMW")
运行测试: RunUnitTests(test_class="ZCL_LMW_TEST", coverage=true)
```

### Slash Commands
```
/read-abap              # 阅读当前对象
/plan-change            # 规划变更
/review-change          # 代码审查
/generate-unit-tests    # 生成测试
/analyze-dump           # 分析异常
```

## 📌 重要提示

⚠️ **在开始使用前，请务必：**

1. **仔细阅读 CLAUDE.md** — 了解 Claude 在此项目中的工作规则
2. **理解 INCLUDE 处理规则** — 必须完整展开包含树后再分析
3. **遵循变更流程** — 先方案、再确认��再修改、再测试
4. **配置 SAP 连接** — 填入真实的 `.env` 和 `.mcp.json` 信息
5. **启用 MCP 服务** — 在 Claude Code 中正确配置 MCP 服务器
6. **Track Transport Requests** — 所有变更必须绑定请求号

## 🤝 工作流最佳实践

### ✅ 要做的事
- 每次修改前先给方案，等待确认
- 完整展开 ABAP INCLUDE 后再分析
- 优先最小变更，避免扩大范围
- 所有变更都绑定 Transport Request
- 生成或补充 ABAP Unit 测试
- 进行严格的人工代码审查
- 记录修改内容、影响范围、验证步骤

### ❌ 不要做的事
- 不编造 SAP 对象、表名、字段名
- 不只看主程序就下结论（必须展开 INCLUDE）
- 不顺手重构无关代码
- 不随意扩大变更范围
- 不修改未被确认的对象
- 不跳过 Transport Request 追踪
- 不忽略高风险操作的审核确认

## 📞 故障排除

### MCP 连接失败
```
错误：MCP 服务启动失败
原因：.env 或 .mcp.json 配置不正确
解决：检查 SAP_URL、SAP_USER、SAP_PASSWORD 是否正确填入
```

### 找不到 ABAP 对象
```
错误：SearchObject 返回空结果
原因：对象不存在或名称不正确
解决：使用 SAP 系统中的 SE80 事务确认对象名称
```

### 激活失败
```
错误：Activate 返回错误
原因：可能有语法错误或依赖问题
解决：先执行 SyntaxCheck，然后检查对象依赖
```

## 📚 相关资源

- [SAP ABAP 官方文档](https://help.sap.com/docs/SAP_S4HANA_CLOUD/0f69f8fb28ac4bef9dbc2b1b7e46109b/8b6cbbf3f9294f27816a8c8b4c3ff14d.html)
- [ABAP 最佳实践](https://www.sap.com/about/legal/copyrights/index.epx)
- [MCP 协议规范](https://modelcontextprotocol.io/)
- [Claude Code 官方文档](https://claude.ai/code)

## 📄 许可证

本项目采用 MIT 许可证。详见 [LICENSE](LICENSE) 文件。

## 👤 作者

**liumaowen**

- GitHub: [@liumaowen](https://github.com/liumaowen)
- 项目地址: [liumaowen/ABAP](https://github.com/liumaowen/ABAP)

## 🎓 学习资源

- **ABAP 语言**: 了解 ABAP 基本语法、内表操作、数据库访问
- **SAP 系统**: 理解 SAP ERP 业务流程、模块架构、权限体系
- **MCP 协议**: 学习 MCP 的工具定义、参数传递、错误处理
- **Code Review**: 掌握代码审查要点、性能优化、安全审核

## 🔄 更新日志

### v1.0.0 (2026-04-30)
- ✅ 初始化项目
- ✅ 完成所有规则文档
- ✅ 配置 MCP 环境
- ✅ 添加示例配置文件

---

**祝您使用愉快！如有问题，欢迎提 Issue 或 PR。** 🚀
