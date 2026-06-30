---
name: mcp-abap-tools-complete
description: 【历史参考】ADT API 工具列表（现已切换为 vsp-dev，工具名无前缀）
metadata:
  node_type: memory
  type: reference
---

> ⚠️ **注意：本项目现已切换为 vsp-dev MCP 服务器。**
> 以下工具列表来自旧的 abap-adt-api（Node.js MCP），仅作为历史参考。
> vsp-dev 工具名无前缀，直接调用（如 `SearchObject`、`GetSource`、`EditSource`）。
> 参见 [[feedback_mcp_tool_naming]] 和 [[feedback_mcp_abap_tool_usage]]。

## MCP ABAP ADT API 完整工具列表（历史参考）

通过阅读 `abap-dev-bundle/mcp-abap-adt-api/src/handlers/` 源码整理，仅供参考。

### 工具总数：50+ 个工具

分布在以下 handler 文件中：
1. **AuthHandlers.ts** — login, logout, dropSession
2. **ObjectHandlers.ts** — searchObject, objectStructure, findObjectPath, objectTypes, reentranceTicket
3. **ObjectSourceHandlers.ts** — getObjectSource, downloadObjectSource, setObjectSource
4. **ObjectLockHandlers.ts** — lock, unLock
5. **ObjectManagementHandlers.ts** — activateByName, activateObjects, inactiveObjects
6. **ObjectRegistrationHandlers.ts** — objectRegistrationInfo, validateNewObject, createObject
7. **ObjectDeletionHandlers.ts** — deleteObject
8. **NodeHandlers.ts** — nodeContents, mainPrograms
9. **ClassHandlers.ts** — classIncludes, classComponents, createTestInclude
10. **TransportHandlers.ts** — transportInfo, createTransport, hasTransportConfig, transportConfigurations, getTransportConfiguration, setTransportsConfig, createTransportsConfig, userTransports, transportsByConfig, transportDelete, transportRelease, transportSetOwner, transportAddUser, systemUsers, transportReference
11. **QueryHandlers.ts** — tableContents, runQuery
12. **DdicHandlers.ts** — annotationDefinitions, ddicElement, ddicRepositoryAccess, packageSearchHelp, setDomainProperties, setDataElementProperties
13. **ServiceBindingHandlers.ts** — publishServiceBinding, unPublishServiceBinding, bindingDetails
14. **CodeAnalysisHandlers.ts** — syntaxCheckCode, syntaxCheckCdsUrl, findDefinition, usageReferences, syntaxCheckTypes, runClass, usageReferenceSnippets, fixProposals, fixEdits, fragmentMappings, abapDocumentation
15. **UnitTestHandlers.ts** — unitTestRun, unitTestEvaluation, unitTestOccurrenceMarkers, createTestInclude
16. **PrettyPrinterHandlers.ts** — prettyPrinterSetting, setPrettyPrinterSetting, prettyPrinter
17. **RenameHandlers.ts** — renameEvaluate, renamePreview, renameExecute
18. **RefactorHandlers.ts** — extractMethodEvaluate, extractMethodPreview, extractMethodExecute
19. **RevisionHandlers.ts** — revisions
20. **FeedHandlers.ts** — feeds, dumps

### 关键发现

1. **runQuery 可以执行 SQL 查询** — 这是查询事务代码对应程序的唯一方法
   - 示例：`SELECT TCODE, PGMNA FROM TSTC WHERE TCODE = 'ZSD211'`

2. **tableContents 可以获取表内容** — 可以直接查看表数据
   - 参数：ddicEntityName, rowNumber?, decode?, sqlQuery?

3. **setDomainProperties 和 setDataElementProperties** — 可以设置域和数据元素属性

4. **syntaxCheckCode 支持 filePath** — 大文件可以用 filePath 避免上下文溢出

5. **setObjectSource 支持 filePath** — 大文件可以用 filePath 避免上下文溢出

**Why:** 完整了解 MCP 工具列表，避免重复造轮子，提高开发效率
**How to apply:** 需要查询 SAP 数据时，优先使用 runQuery；需要修改大文件时，使用 filePath 参数

## 相关记忆
- [[feedback_mcp_abap_tool_usage]] — MCP 工具使用规范
- [[feedback_mcp_tool_naming]] — MCP 工具命名格式
