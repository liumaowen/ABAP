---
name: MCP选择规则
description: 三个SAP MCP（vsp-dev/mcp-abap/adt-mcp）的选择偏好——重叠功能默认vsp-dev，独家功能按场景分流
metadata:
  type: reference
---

# MCP 选择规则

> 三个 MCP 并行开启：`vsp-dev`、`mcp-abap`、`adt-mcp`。本规则定义何时用谁。

## 核心原则

1. **重叠功能默认 vsp-dev**：读/写/激活/语法检查/搜索/SQL/测试/锁/传输查询 —— vsp-dev 最快最稳。
2. **独家功能必选对应 MCP**：谁独有就用谁，不要绕路。

## 强制 adt-mcp

| 场景 | 工具 |
|------|------|
| RAP 生成器建对象（表+CDS+BDEF+SRV+Fiori） | `abap_generators-list/get_schema/generate_objects` |
| ATC + AI 自动修复 | `abap_atc_apply_ai_fix`、`abap_atc_execute_deterministic_quickfixes` |
| OData 服务信息查询 | `abap_business_services-fetch_services/fetch_service_information` |

## 强制 mcp-abap

| 场景 | 工具 |
|------|------|
| 代码重构（提取方法） | `extractMethodEvaluate/Preview/Execute` |
| 代码重构（重命名） | `renameEvaluate/Preview/Execute` |
| 快速修复建议 | `fixProposals/fixEdits` |
| DDIC 属性设置 | `setDataElementProperties/setDomainProperties` |
| Transport 创建/释放/配置 | `createTransport/transportRelease/setTransportsConfig` |
| 服务绑定发布 | `publishServiceBinding/unPublishServiceBinding` |
| 创建测试 Include | `createTestInclude` |
| 注解定义查询 | `annotationDefinitions` |

## 强制 vsp-dev

| 场景 | 工具 |
|------|------|
| 调试（断点/单步/变量/AMDP/运行时） | `Debugger*`、`AMDPDebugger*`、ZADT_VSP WebSocket |
| 代码分析（调用图/覆盖率/CDS依赖/包边界/代码质量/上下文） | `GetCallGraph/GetCodeCoverage/GetCDSDependencies/CheckBoundaries/AnalyzeABAPCode/GetContext` |
| 搜索（跨对象/跨包） | `GrepObjects/GrepPackages` |
| 运行时（Dump/Trace/SQL Trace/RFC/报表） | `ListDumps/GetDump/ListTraces/GetTrace/CallRFC/RunReport` |
| abapGit（导出/安装/DeployZip） | `GitExport/InstallAbapGit/DeployZip` |
| Fiori/UI5 应用查看 | `UI5GetApp/UI5GetFileContent/UI5ListApps` |
| 版本历史与对比 | `GetRevisions/CompareVersions/CompareSource/CompareLanguages` |
| 对象克隆/移动 | `CloneObject/MoveObject` |
| 建表/建包 | `CreateTable/CreatePackage` |

## 重叠功能（默认 vsp-dev）

| 功能 | vsp-dev | mcp-abap | adt-mcp |
|------|---------|----------|---------|
| 读源码 | `GetSource` ✅默认 | `getObjectSource` | - |
| 写源码 | `WriteSource/EditSource` ✅默认 | `setObjectSource` | - |
| 激活 | `Activate/ActivatePackage` ✅默认 | `activateByName` | `abap_activate_objects` |
| 语法检查 | `SyntaxCheck` ✅默认 | `syntaxCheckCode` | - |
| 单元测试 | `RunUnitTests` ✅默认 | `unitTestRun` | `abap_run_unit_tests` |
| SQL 查询 | `RunQuery` ✅默认 | `runQuery` | - |
| 搜索对象 | `SearchObject` ✅默认 | `searchObject` | - |
| 锁/解锁 | `LockObject/UnlockObject` ✅默认 | `lock/unLock` | - |

## 例外情况

- 如果 vsp-dev 响应异常/超时，可临时切换到 mcp-abap 做等价操作。
- 批量激活用 adt-mcp 的 `abap_activate_objects`（支持 URI 列表）更方便。
- Transport 查询三者都能做，但**创建/释放必须 mcp-abap**，**diff 必须 adt-mcp**。
