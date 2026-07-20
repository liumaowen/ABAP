# Memory Index

> 此索引为 ABAP 开发工作区记忆，被 Claude Code 自动加载。
> 添加新记忆时在对应分类下追加 `- [标题](文件名.md) — 说明`。

## Feedback (开发规范与教训)
- [ABAP开发规则](feedback_abap_dev_rules.md) — ABAP 开发流程和关键规则
- [最小化修改](feedback_abap_minimal_change.md) — ABAP 代码最小化修改原则
- [激活前读取记忆](feedback_activate_fugr_first_read_memory.md) — 激活函数组前先读取记忆
- [先检查传输](feedback_check_transport_first.md) — 修改前先检查传输请求状态
- [代码修改确认](feedback_code_modify_consent.md) — 代码修改需要用户明确同意
- [创建传输请求](feedback_create_transport.md) — 创建传输请求的流程
- [需求确认](feedback_grill_me_on_unclear_requirements.md) — 需求不明确时要深入提问
- [MCP工具使用](feedback_mcp_abap_tool_usage.md) — MCP ABAP 工具使用规范
- [MCP工具命名](feedback_mcp_tool_naming.md) — abap-dev skill 的 MCP 工具名称格式
- [查询传输请求](feedback_query_transport_from_sap.md) — 从 SAP 查询传输请求信息
- [修改工作流](feedback_sap_modify_workflow.md) — SAP 修改工作流规范
- [写入前展示](feedback_show_code_before_write.md) — 写入代码前先展示给用户
- [使用ABAP技能](feedback_use_abap_dev_skill.md) — 使用 abap-dev skill
- [ABAP开发不手动调试MCP](feedback_abap_dev_no_manual_debug.md) — ABAP 开发中不要脱离 abap-dev skill 调试 MCP
- [严格对照规范](feedback_strict_spec_compliance.md) — 必须严格对照规范执行，不要凭记忆或感觉操作
- [注释时间精确到秒](feedback_comment_timestamp_precision.md) — ABAP 注释修改时间必须精确到秒
- [使用sap-abap skill](feedback_use_sap_abap_skill.md) — ABAP 开发时使用 sap-abap skill 检查语法
- [变量声明位置](feedback_abap_data_declaration_position.md) — 允许内联声明，禁止就地声明块
- [setObjectSource用主请求号](feedback_setobjectsource_use_header_request.md) — 必须传 HEADER 请求号(K)，任务号(S) 会 500
- [DDIC表激活双重确认](feedback_ddic_table_double_activate.md) — activateObjects success:true 仍需 inactiveObjects 确认
- [程序激活二次确认](feedback_prog_activate_inactive_confirm.md) — 程序激活后必须 inactiveObjects 确认
- [FAE不支持GROUP BY](feedback_for_all_entries_no_group_by.md) — FOR ALL ENTRIES 不能配合 GROUP BY
- [FAE字段长度必须一致](feedback_for_all_entries_type_length_match.md) — FOR ALL ENTRIES 两边字段类型长度必须严格一致
- [ZFMDATASAVE放顶部](feedback_zfmdatasave_top_position.md) — RFC 函数日志宏必须放在函数体最顶部
- [CALL FUNCTION不支持内联声明](feedback_call_function_no_inline_decl.md) — CALL FUNCTION 参数位置禁止内联声明
- [vsp-dev LockObject 404 fallback](feedback_vsp_lockobject_404_fallback.md) — vsp-dev 锁失败时回退到 mcp-abap 写代码

## Reference (参考资料)
- [SAP连接配置](reference_sap_connection.md) — SAP 连接配置（请改成你自己的）
- [激活函数模块](reference_activate_function_module.md) — 激活对象通用规则
- [MCP ABAP工具完整列表](reference_mcp_abap_tools_complete.md) — MCP 工具完整清单
- [CL_SALV_BS_RUNTIME_INFO](reference_cl_salv_runtime_info.md) — 拦截 SUBMIT 报表 ALV 数据
- [WBS外码转内码](reference_conversion_exit_abpsp_input.md) — CONVERSION_EXIT_ABPSP_INPUT 用法
