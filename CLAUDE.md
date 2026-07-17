# CLAUDE.md

本文件为 Claude Code (claude.ai/code) 在此仓库中工作时提供指导。

## 项目概述

这是精智 ERP 系统的 **ABAP 开发工作区**。实际的 ABAP 源代码存放在**远程 SAP 系统**中，而非本地目录。所有代码操作（读取/写入/激活/测试）均通过 **`vsp` MCP 服务器**（`vsp-dev`）执行。

## 语言要求
- 默认使用简体中文输出
- 所有过程步骤、计划、风险提示、总结都必须使用中文
- 代码、SAP 对象名、函数名、类名、表名保持原样
- 如果需要解释英文术语，请用中文解释，不要整段切换成英文

## ABAP 包含程序（INCLUDE）处理规则

当查看或分析任何一个 ABAP 程序时，必须始终：
- 先读取主程序的源码；
- 提取其中所有的 `INCLUDE ...` 语句；
- 递归地打开每一个被包含的 include 程序；
- 继续沿着 include 链往里展开，直到整棵包含树都读完；
- 然后再把主程序和所有 include 的代码当作一个整体来看待并进行分析。

回答任何问题之前，都必须先完成对包含树的完整展开，不得只根据主程序的代码就给出结论。

## 会话策略

每次打开 Claude Code 时：
1. 先读取 CLAUDE.md 和项目规则。
2. 再读取当前任务范围。
3. 先输出修改计划。
4. 确认后再执行。

## SAP 环境信息

详见 [ABAP_ENV.md](ABAP_ENV.md)。

## 变更工作流

详见 [CHANGE_WORKFLOW.md](CHANGE_WORKFLOW.md)。

## 架构与开发模式

- **数据源**: 通过 ADT（ABAP Development Tools）协议访问的 SAP 系统
- **本地职责**: 配置、规则和 MCP 连接管理
- **MCP 服务器**: 两个并行开启
  - `vsp-dev` — 全能开发工具（80+ 工具：搜索、读写、激活、调试、分析、部署）
  - `mcp-abap` — ADT 底层能力（重构、DDIC 属性、Transport 管理、服务绑定发布）
- **开发模式**: `focused`（仅启用核心工具）

## MCP 选择规则

两个 MCP 按以下规则选择：

### 默认偏好
- 重叠功能（读/写/激活/语法检查/搜索/SQL/测试/锁/传输查询）：**优先 vsp-dev**（最快最稳）
- 独家功能：必须用有该能力的 MCP

### 强制使用 mcp-abap
- 代码重构：提取方法（`extractMethod*`）、重命名（`rename*`）
- 快速修复建议（`fixProposals/fixEdits`）
- DDIC 属性设置（`setDataElementProperties/setDomainProperties`）
- Transport 生命周期：创建/释放/配置（`createTransport/transportRelease/setTransportsConfig`）
- 服务绑定发布（`publishServiceBinding/unPublishServiceBinding`）
- 创建测试 Include（`createTestInclude`）
- 注解定义查询（`annotationDefinitions`）

### 强制使用 vsp-dev
- 调试（`Debugger*`、`AMDPDebugger*`、ZADT_VSP WebSocket 运行时调试）
- 代码分析（调用图、覆盖率、CDS 依赖、包边界、代码质量、上下文压缩）
- 搜索（`GrepObjects/GrepPackages` 跨对象/跨包搜索）
- 运行时工具（Dump、Trace、SQL Trace、RFC 调用、报表执行）
- abapGit 操作（导出/安装/DeployZip）
- Fiori/UI5 应用查看
- 版本历史与对比（`GetRevisions/CompareVersions/CompareSource`）
- 对象克隆/移动
- 直接建表/建包（`CreateTable/CreatePackage`）
- ATC 检查（`RunATCCheck`）

## 关键文件

| 文件 | 用途 |
|------|------|
| [ABAP_PROJECT_RULES.md](ABAP_PROJECT_RULES.md) | ABAP 开发规则大全 — 代码规范、约束、测试要求、风险策略 |
| [CHANGE_WORKFLOW.md](CHANGE_WORKFLOW.md) | 变更工作流 — 修改前/中/后的输出要求和高风险流程 |
| [TEST_CHECKLIST.md](TEST_CHECKLIST.md) | 测试清单 — 基础/业务/技术检查项、测试类型、回归检查 |
| [OBJECT_SCOPE.md](OBJECT_SCOPE.md) | 对象范围 — 允许操作的对象、约束、修改原则、Transport Request 要求 |
| [ABAP_ENV.md](ABAP_ENV.md) | 环境信息 — SAP 系统连接、权限边界、允许范围、验证方式 |
| [.mcp.json](.mcp.json) | MCP 服务器配置（将 `vsp-dev` 连接到 SAP 系统） |
| [.env](.env) | SAP 连接凭证（SAP_URL、SAP_USER、SAP_PASSWORD、SAP_MODE） |
| [.vsp.json](.vsp.json) | 多系统 vsp 配置（开发/生产等） |
| [skill/SKILL.md](skill/SKILL.md) | ABAP 开发 Skill — 封装完整开发工作流 + 25 个历史踩坑案例 |
| [memory/MEMORY.md](memory/MEMORY.md) | 记忆索引 — 29 条 ABAP 开发规范与踩坑经验（跨会话持久化） |

### Slash Commands

| 命令 | 用途 |
|------|------|
| `/read-abap` | 读取并解释当前 ABAP 对象（业务作用、调用链、依赖、风险、重构建议） |
| `/plan-change` | 规划变更（不修改代码，先输出方案、影响、风险、验证方法） |
| `/review-change` | 对当前 ABAP 变更做严格 Code Review |
| `/generate-unit-tests` | 为当前 ABAP 对象生成或补充 ABAP Unit 测试 |
| `/analyze-dump` | 分析 dump / 日志 / 异常信息 |

### Skill 与 Memory

- **`skill/SKILL.md`**：封装完整的 ABAP 开发工作流（查看→修改→语法检查→激活），包含 25 个历史踩坑案例。**收到 ABAP 任务时优先加载此文件。**
- **`memory/`**：29 条持久化记忆（跨会话保留），含开发规范、工具使用教训、参考资料。详见 `memory/MEMORY.md`。

## 常用开发流程

所有操作均通过 MCP 工具调用完成，而非本地 Shell 命令：

1. **搜索对象**: `SearchObject` + 查询条件（如 `ZCL_*`、`ZLMW_*`）
2. **读取源码**: `GetSource` — 指定 `object_type`（PROG/CLAS/INTF/FUNC/DDLS/BDEF/SRVD）+ `name`
3. **编辑源码**: `EditSource` 用于精确字符串替换，`WriteSource` 用于完整写入
4. **语法检查**: `SyntaxCheck` — 保存前检查语法
5. **激活**: `Activate` 激活单个对象，`ActivatePackage` 批量激活
6. **运行测试**: `RunUnitTests` 执行 ABAP Unit，`GetCodeCoverage` 分析覆盖率
7. **代码质量**: `RunATCCheck` 获取 ATC 检查结果，`AnalyzeABAPCode` 静态分析
8. **调试**: `DebuggerListen` → `DebuggerAttach` → `DebuggerStep` → `DebuggerGetVariables`
9. **AMDP 调试**: `AMDPDebuggerStart` 用于 HANA SQLScript 调试

## 对象类型

| 类型 | 说明 | 示例 |
|------|------|------|
| PROG | ABAP 程序/报表 | ZLMW_REPORT |
| CLAS | ABAP 类（含：定义、实现、测试类、宏） | ZCL_LMW_ORDER |
| INTF | ABAP 接口 | ZIF_LMW_SERVICE |
| FUNC | 函数模块（需指定父级 FUGR） | Z_LMW_FM / 父级 Z_LMW_FG |
| FUGR | 函数组 | Z_LMW_FG |
| DDLS | CDS DDL 源 | ZLMW_I_ORDER |
| BDEF | 行为定义 | ZLMW_C_ORDER |
| SRVD | 服务定义 | ZLMW_SRVD_ORDER |
| MSAG | 消息类 | ZLMW_MSG |
| TABL | DDIC 数据表 | ZLMW_T_LOG |

## 测试清单

详见 [TEST_CHECKLIST.md](TEST_CHECKLIST.md)。

## 项目特定规则

详见 [ABAP_PROJECT_RULES.md](ABAP_PROJECT_RULES.md)。

## ABAP 技能

已启用 `sap-abap` 技能。在编写 ABAP 代码、操作内表、RAP/CDS 视图、EML 语句、ABAP Cloud 开发、RTTI/RTTC 或 ABAP Unit 测试时使用。
