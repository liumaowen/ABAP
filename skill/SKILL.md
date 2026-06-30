# ABAP Development Skill（vsp 版）

SAP ABAP 开发专用 skill，封装了使用 vsp-dev MCP 工具进行源码查看、修改、激活等操作的完整工作流。

## ⚠️ 全局原则：最小改动原则

**修改 ABAP 代码时，必须遵循最小改动原则 — 只改必须改的，不碰不该碰的。**

- **只改需求要求的部分** — 不要顺便"优化"、"重构"或"改进"不在需求范围内的代码
- **不改已有逻辑的写法** — 即使你觉得有更好的写法，只要原逻辑没 bug，就不动它
- **不改已有注释** — 除非注释内容与修改直接相关
- **不改格式和排版** — 缩进、空行、换行等风格保持原样
- **新增代码风格必须匹配** — 新加的代码要和周围代码风格一致（命名、缩进、注释风格等）
- **修改范围最小化** — 能改一行解决的，不要改十行；能改一个 FORM 的，不要动整个 INCLUDE

**判断标准：** 每一行改动都应该能直接追溯到用户的需求。如果某行改动你解释不清"为什么必须改"，那就不要改。

---

## ⚠️ 全局原则：变量声明位置规则

**允许内联声明，禁止就地声明。**

### ✅ 允许
- 函数/程序开头的集中 `DATA:` / `TYPES:` 声明块
- **内联声明**（ABAP 7.4+ 现代语法）：
  - `DATA(变量名) = ...`
  - `LOOP AT itab INTO DATA(ls)`
  - `SELECT ... INTO TABLE @DATA(lt_x)`
  - `READ TABLE ... ASSIGNING FIELD-SYMBOL(<fs>)`
  - `READ TABLE ... INTO DATA(ls)`

### ❌ 禁止
- **就地声明**：在 `LOOP` / `IF` / `FORM` 块中间穿插传统 `DATA:` 声明块
- 在执行语句之间穿插声明区

### 示例

```abap
FORM xxx.
  " ✅ 允许：开头集中声明
  DATA: lt_mara TYPE TABLE OF mara,
        lv_count TYPE i.

  " ✅ 允许：内联声明
  SELECT * FROM mara INTO TABLE @DATA(lt_x).
  LOOP AT lt_x INTO DATA(ls_x).
    DATA(lv_str) = |{ ls_x-matnr }|.
  ENDLOOP.

  " ❌ 禁止：就地声明块（应挪到 FORM 开头）
  LOOP AT lt_x INTO ls_x.
    DATA: lv_tmp TYPE string.
    lv_tmp = ls_x-matnr.
  ENDLOOP.
ENDFORM.
```

**Why:** 传统 `DATA:` 声明块分散会让阅读变得困难，必须集中放在函数/FORM 开头。内联声明类型自动推导、作用域清晰，是现代 ABAP 标准用法，可以自由使用。

**2026-06-23 用户澄清**：之前误以为禁止所有内联声明，实际只禁止"就地声明块"。

---

## ⚠️ 全局原则：项目日志宏调用位置（ZFMDATASAVE1/2 + COMMIT WORK）

**修改 RFC 函数模块时，`ZFMDATASAVE1 / ZFMDATASAVE2 + COMMIT WORK` 必须放在函数体最顶部（IMPORTING/EXPORTING/TABLES 之后第一段可执行语句），不能放到业务逻辑后面。**

### 规则
- **位置**：`ENDFUNCTION` 之前那种 "末尾日志" 写法 ❌ 错误
- **正确顺序**：
  ```
  FUNCTION xxx.
    IMPORTING/EXPORTING/TABLES ...

    ZFMDATASAVE1 '函数名' .          ← 顶部，第一段可执行语句
    ZFMDATASAVE2 'B'.
    COMMIT WORK.

    " 业务逻辑：TYPES / DATA / SELECT / LOOP ...

    IF OT_TAB[] IS NOT INITIAL.
      RET_MESSAGE 'S' '成功!' 'X' 'X'.
    ELSE.
      RET_MESSAGE 'E' '无数据！' 'X' 'X'.
    ENDIF.
  ENDFUNCTION.
  ```

### 与"顶部集中声明"原则的关系
- ABAP 通用最佳实践是"声明在顶部"，但本项目惯例是**先记录调用日志再声明变量再写业务**
- 这种顺序在 ABAP 语法上完全合法（`TYPES/DATA` 必须在使用之前即可，无需紧贴 FUNCTION 之后）
- **修改现有 RFC 函数时遵循已有结构**：观察原始函数 `ZFMDATASAVE` 的位置，按原位置放业务逻辑

### 触发场景
- 新加业务逻辑到 RFC 函数（如 `ZOA_RFC_*`）
- 重构 RFC 函数代码顺序
- 拼接新代码时，**绝不要把 ZFMDATASAVE 移到业务逻辑后面**

**Why:** ZFMDATASAVE1/2 是项目自定义的 RFC 调用日志保存宏，必须在业务逻辑执行前记录调用入口，否则业务异常（DUMP/退出）时日志会丢失，无法追溯调用记录。

---

## 触发条件

当用户要求进行 SAP/ABAP 相关操作时自动触发：
- 查看 ABAP 程序/函数/类的源码
- 修改 ABAP 代码
- 激活 ABAP 对象
- 创建/删除 ABAP 对象
- 语法检查

---

## 一、MCP 工具说明（vsp-dev）

本 skill 使用 `vsp-dev` MCP 服务器提供的工具，工具名称无前缀，直接调用：

| 工具 | 参数 | 说明 |
|------|------|------|
| `SearchObject` | query, objType? | 搜索 ABAP 对象 |
| `GetSource` | object_type, name | 获取对象源码（PROG/CLAS/INTF/FUNC/DDLS 等） |
| `EditSource` | object_type, name, find, replace | 精确字符串替换（自动处理锁定/写入/解锁） |
| `WriteSource` | object_type, name, source | 完整写入源码 |
| `SyntaxCheck` | object_type, name | 语法检查 |
| `Activate` | object_type, name | 激活单个对象 |
| `ActivatePackage` | package | 批量激活包内对象 |
| `RunATCCheck` | object_type, name | ATC 代码质量检查 |
| `RunUnitTests` | object_type, name | 运行 ABAP Unit 测试 |
| `AnalyzeABAPCode` | object_type, name | 静态代码分析 |
| `DebuggerListen` | — | 监听调试器连接 |
| `DebuggerAttach` | program | 附加调试器 |
| `DebuggerStep` | — | 单步调试 |
| `DebuggerGetVariables` | — | 获取当前变量值 |

**⚠️ vsp 与 ADT API 的区别：** vsp 内部封装了 lock/write/unlock 流程，`EditSource` 和 `WriteSource` 自动处理锁定，不需要手动调用 lock/unlock 工具。

---

## 二、查看源码流程

```
SearchObject → GetSource → 分析代码（包括所有 INCLUDE）
```

**步骤：**
1. `SearchObject(query="对象名")` 搜索对象，确认对象类型
2. `GetSource(object_type="PROG", name="ZXXX")` 获取源码
3. **必须展开所有 INCLUDE**，读完整个包含树后再分析（见 CLAUDE.md 规则）
4. 向用户汇报分析结果

---

## 三、修改代码流程

### 3.1 简单修改（用 EditSource）

```
GetSource → 确认修改内容 → EditSource(find="旧代码", replace="新代码") → SyntaxCheck → Activate → 确认激活成功
```

**步骤：**

| 步骤 | 工具 | 说明 |
|------|------|------|
| 1 | `GetSource` | 读取当前源码，理解上下文 |
| 2 | 展示修改方案 | **修改前必须展示给用户，获得确认后才能写入** |
| 3 | `EditSource` | 精确字符串替换，vsp 自动处理 lock+write+unlock |
| 4 | `SyntaxCheck` | 语法检查 |
| 5 | `Activate` | 激活对象 |
| 6 | 确认激活结果 | 检查 Activate 返回，必要时用 GetSource 二次验证 |

### 3.2 大范围修改（用 WriteSource）

当修改内容超过 10 行，或需要重构整个 FORM/方法时：

```
GetSource → 构建完整新源码 → 展示给用户 → WriteSource → SyntaxCheck → Activate → 确认激活成功
```

### 3.3 传输请求（Transport Request）

- **修改任何可运输对象前，必须先确认有可用的传输请求**
- 用 `SearchObject` 或询问用户获取当前请求号
- 写入时 vsp 会自动绑定传输请求
- **请求号必须由用户提供，禁止自己选择或创建**

---

## 四、激活注意事项

vsp 的 `Activate` 工具内部封装了 ADT 激活流程，以下规则仍然重要（来自历史踩坑）：

1. **激活结果不能只看 success:true** — 如果 `Activate` 返回成功但用户反馈对象未激活，用 `GetSource` 重新读取确认
2. **INCLUDE 必须先于主程序激活** — INCLUDE 有错误时主程序也无法激活，先修 INCLUDE 再激活主程序
3. **DDIC 表激活后下游程序要重新激活** — 新增字段后，依赖此表的程序需要重新激活才能识别新字段
4. **会话超时（Session timed out）** — 看到此错误，调用 `login` 重新登录后继续

---

## 五、全部错误清单（25个错误，来自多个案例）

> 以下错误来自 ADT API 经验，大部分规则同样适用于 vsp。vsp 自动处理锁定的工具（EditSource/WriteSource）可避免错误 #1、#6、#18，但其余规则仍然有效。

### 错误1：锁定状态激活 → 404
- **案例：** ZFM_GP_OCP_PP_POSTPCZL (2026-06-05)
- **现象：** 激活报 `Error 404`
- **原因：** 对象在锁定状态下激活（ADT API 问题）
- **vsp 情况：** EditSource/WriteSource 自动处理 lock/unlock，通常不会遇到此问题

### 错误2：success:true 但未真正激活
- **案例：** ZFM_OA_MM_GETMROS_CK (2026-05-27)
- **现象：** 激活工具返回 `success:true` 但对象并未真正激活
- **正确做法：** 激活后用 `GetSource` 或让用户在 SE80 中二次确认

### 错误3：函数组激活 success:false + inactive 对象
- **案例：** ZFM_GP_OA_MM_POSP (2026-06-05)
- **现象：** 函数组激活失败，返回未激活对象列表
- **正确做法：** 确保所有相关对象（含系统节点）一起激活

### 错误4：写入时未传 transport → 500
- **案例：** ZFM_GP_SD_DMS_HTZXBBCX (2026-05-27)
- **现象：** 写入源码报 `Error 500`
- **原因：** 未传传输请求参数
- **vsp 情况：** vsp 自动管理 transport，通常不会遇到

### 错误5：activateByName 参数错误
- **案例：** ZFM_GP_SD_DMS_HTZXBBCX (2026-05-27)
- **正确做法：** `Activate(object_type, name)` 只用这两个参数

### 错误6：lock 参数名错误
- **案例：** ZFM_GP_SD_DMS_HTZXBBCX (2026-05-27)
- **vsp 情况：** vsp 不需要手动 lock，EditSource/WriteSource 自动处理

### 错误7：激活后 FORM 不存在
- **案例：** DMS 签收状态同步 (2026-05-27)
- **现象：** 激活成功但运行时报 `FORM "xxx" 不存在`
- **原因：** PERFORM 调用写入了，但 FORM 定义没有实际追加到源码
- **正确做法：** 写入源码前必须同时确认 PERFORM 调用和 FORM 定义都存在

### 错误8：忘记经验反复试错
- **案例：** ZFM_OA_MM_GETMROS_CK (2026-06-01)
- **正确做法：** 操作前先读取相关 memory 文件

### 错误9：变量名冲突导致激活失败
- **案例：** ZFID201_9020 钉钉提醒 (2026-06-05)
- **现象：** 激活时报 "XXX 已声明" 错误
- **原因：** 新增变量名与程序中已有的全局变量重名
- **正确做法：** 新增变量使用独特前缀（如 `ZLV_DD_`），避免与全局变量冲突

### 错误10：代码重复插入
- **案例：** ZFID201_9020 钉钉提醒 (2026-06-05)
- **现象：** 同一段代码被插入两次
- **正确做法：** 写入后用 `GetSource` 验证，检查是否有重复代码

### 错误11：写错对象导致主程序被覆盖
- **案例：** ZFID201 主程序 (2026-06-05)
- **现象：** 主程序被 INCLUDE 的内容覆盖
- **原因：** 写入时指定了错误的对象名
- **正确做法：** 写入前必须确认 object_type 和 name 是否匹配目标对象

### 错误12：INCLUDE 错误导致主程序无法激活
- **案例：** ZFID201_9020 (2026-06-05)
- **正确做法：** 先修复 INCLUDE 错误，再激活主程序

### 错误13：DDIC 表激活方式
- **案例：** ZTMM266L (2026-06-08)
- **现象：** DDIC 表激活失败
- **正确做法：** DDIC 表用 `Activate(object_type="TABL", name="表名")`，vsp 内部处理激活逻辑

### 错误14：源码未持久化到文件，反复解析失败
- **案例：** ZFM_GP_OA_MM_POSP (2026-06-10)
- **现象：** 读取源码后，写脚本尝试从内存解析源码，全部失败
- **正确做法：** 直接用 vsp 的 `EditSource` 做精确替换，不要手动解析源码 JSON

### 错误15：中文注释编码损坏
- **案例：** ZMMD200 (2026-06-10)
- **现象：** 写入 SAP 的中文注释变成乱码
- **正确做法：** 使用 vsp 的 `EditSource`/`WriteSource`，内部处理编码；不要手动处理编码

### 错误16：ToolSearch 搜索工具失败
- **案例：** ZFM_GP_FICO_LYPZ (2026-06-10)
- **现象：** 用 ToolSearch 搜索工具名称失败
- **正确做法：** vsp 工具名无前缀，直接调用 `SearchObject`、`GetSource` 等，不需要搜索

### 错误17：注释时间格式不精确到秒
- **案例：** ZMMD200 (2026-06-12)
- **正确做法：** 使用 `date '+%Y-%m-%d %H:%M:%S'` 获取完整时间戳，注释格式必须是 `by lzf YYYY-MM-DD HH:MM:SS`

### 错误18：程序锁定状态激活报 404
- **案例：** ZRSD210 (2026-06-15)
- **vsp 情况：** vsp 自动处理 lock/unlock，通常不会遇到

### 错误19：setObjectSource 用任务号传 transport 报 500
- **案例：** ZFM_GP_SD_DMS_HTZXBBCX (2026-06-18)
- **正确做法：** 传输请求必须用主请求号（K 类型），不能用任务号（S 类型）

### 错误20：DDIC 表激活返回 success:true 但实际未激活
- **案例：** ZTPP_205 (2026-06-18)
- **现象：** 激活返回成功，但下游程序激活时报找不到字段
- **正确做法：** 激活 DDIC 表后，继续激活依赖此表的程序前，用 `GetSource` 或 `Activate` 二次确认

### 错误21：会话超时 Session timed out
- **案例：** ZFM_GP_SD_DMS_XMCX (2026-06-24)
- **正确做法：** 看到此错误，调用 `login` 重新登录后继续

### 错误22：ADT API 不支持创建 DDIC 结构 (STRU)
- **案例：** ZSSD_XMCX (2026-06-24)
- **正确做法：** 需要 DDIC 结构时让用户在 SE11/Eclipse 手动创建，或用 `TABL/DT` 替代，或在函数内用 `TYPES BEGIN OF ... END OF` 局部定义

### 错误23：误判 CL_SALV_BS_RUNTIME_INFO 对 REUSE_ALV 无效
- **案例：** ZFM_GP_SD_DMS_XMCX (2026-06-24)
- **正确做法：** `CL_SALV_BS_RUNTIME_INFO` 对所有标准 ALV 路径都有效（REUSE_ALV / CL_SALV / CL_GUI_ALV_GRID），默认能取到数据

### 错误24：误创建对象后才发现类型不对
- **案例：** ZSSD_XMCX (2026-06-24)
- **正确做法：** 创建对象前必须明确 typeId 的语义，DDLS/DF 是 CDS 视图不是结构

### 错误25：程序 success:true 但仍未激活
- **案例：** ZRPS211 (2026-06-26)
- **正确做法：** 激活后必须二次确认，用 `GetSource` 重新读取验证，或让用户在 SE80 检查

---

## 六、错误速查表

| # | 错误现象 | 原因 | 解决方案 | vsp 状态 |
|---|----------|------|----------|---------|
| 1 | `404` 激活时 | 锁定状态激活 | vsp 自动处理 lock/unlock | ✅ 已避免 |
| 2 | `success:true` 但未激活 | 激活接口假成功 | 用 GetSource 二次确认 | 仍需注意 |
| 3 | `success:false` + inactive | 系统节点未包含 | vsp 内部处理 | ✅ 已避免 |
| 4 | `500` 写入时 | 未传 transport | vsp 自动管理 transport | ✅ 已避免 |
| 5 | 参数格式错误 | 参数名错误 | 只用 object_type + name | 仍需注意 |
| 6 | lock 调用失败 | 参数名错误 | vsp 不需要手动 lock | ✅ 已避免 |
| 7 | FORM 不存在 | 定义未写入 | 同时验证 PERFORM 和 FORM | 仍需注意 |
| 8 | 反复试错 | 没先读记忆 | 操作前先读 memory 文件 | 仍需注意 |
| 9 | 变量名冲突 | 新变量与全局变量重名 | 用独特前缀 `ZLV_DD_` | 仍需注意 |
| 10 | 代码重复插入 | 脚本多次执行 | 写入后用 GetSource 验证 | 仍需注意 |
| 11 | 主程序被覆盖 | 写入错误对象 | 写入前确认对象名 | 仍需注意 |
| 12 | 主程序激活失败 | INCLUDE 有错误 | 先修 INCLUDE 再激活主程序 | 仍需注意 |
| 13 | DDIC 表激活失败 | 激活方式错误 | 用 Activate(object_type="TABL") | 仍需注意 |
| 14 | 源码未持久化 | 小文件不自动保存 | 用 EditSource 精确替换，不解析 JSON | ✅ 已避免 |
| 15 | 中文注释编码损坏 | 手动处理编码出错 | 用 EditSource/WriteSource，vsp 处理编码 | ✅ 已避免 |
| 16 | ToolSearch 搜索工具失败 | 不知道工具名格式 | vsp 工具无前缀，直接调用 | ✅ 已避免 |
| 17 | 注释时间只写日期 | 忘记精确到秒 | 用 `date '+%Y-%m-%d %H:%M:%S'` | 仍需注意 |
| 18 | 程序锁定状态激活 404 | 锁定状态激活 | vsp 自动处理 lock/unlock | ✅ 已避免 |
| 19 | transport 报 500 | 用了任务号(S) 不是主请求号(K) | vsp 自动管理 | ✅ 已避免 |
| 20 | DDIC 表激活后下游找不到字段 | activateObjects 假成功 | 激活后用 GetSource 二次确认 | 仍需注意 |
| 21 | `Session timed out` | 会话超时 | 调用 login 重新登录 | 仍需注意 |
| 22 | 创建 DDIC 结构报 Unsupported | API 不支持 STRU | 让用户手动建 / 用 TABL 替代 | 仍需注意 |
| 23 | 怀疑 REUSE_ALV 取不到数据 | 误判 CL_SALV_BS_RUNTIME_INFO | 对所有 ALV 路径都有效，默认能取数 | 仍需注意 |
| 24 | 误创建对象后才发现类型不对 | 不看 typeId 语义就试错创建 | 创建前明确语义，DDLS/DF 是 CDS 视图 | 仍需注意 |
| 25 | 程序 success:true 但未激活 | 只信激活返回值 | 激活后用 GetSource 二次确认 | 仍需注意 |

---

## 七、变量命名规范

### 新增变量命名规则
- **必须使用独特前缀** — 避免与全局变量冲突
- **推荐前缀：** `ZLV_DD_`（钉钉相关）、`ZLV_`（通用局部变量）
- **禁止使用：** 无前缀的通用名如 `LV_STR`、`LV_MSG`、`LV_RTYPE`

### 命名示例
```abap
" ✅ 正确：使用独特前缀
DATA: ZLV_DD_NOTIFY_MSG TYPE string,
      ZLV_DD_RTYPE TYPE bapi_mtype,
      ZLV_DD_RTMSG TYPE bapi_msg.

" ❌ 错误：通用名容易冲突
DATA: LV_DDSTR TYPE string,
      LV_RTYPE TYPE bapi_mtype,
      LV_RTMSG TYPE bapi_msg.
```

---

## 八、代码修改规范

1. **修改前必须读取源码** — 用 `GetSource` 读取，理解上下文
2. **修改前必须展示代码** — 向用户展示修改内容，获得确认后再写入
3. **传输请求号必须由用户提供** — 禁止自己选择或创建请求号
4. **修改后必须语法检查** — 使用 `SyntaxCheck` 验证
5. **修改处加注释** — 格式：`" <改动描述> by lzf YYYY-MM-DD HH:MM:SS`
   - **必须使用 `date '+%Y-%m-%d %H:%M:%S'` 获取当前时间，不能只写日期！**
   - **错误示例：** `" 改动描述 by lzf 2026-06-12` ← 缺少时分秒
   - **正确示例：** `" 改动描述 by lzf 2026-06-12 15:51:13`
6. **激活成功后二次验证** — 用 `GetSource` 重新读取，确认写入正确

**⚠️ 铁律：不要自己选请求号！必须让用户来选！**

---

## 九、特殊查询技巧

### 查询事务代码对应程序
使用 `runQuery` 查询表 `TSTC`（如果 vsp 支持 runQuery）：
```sql
SELECT TCODE, PGMNA FROM TSTC WHERE TCODE = '事务代码'
```

如果 vsp 不支持 runQuery，让用户在 SE16N 查 TSTC 表。

### 查询函数模块所在函数组
```sql
SELECT FUNCNAME, AREA FROM TFDIR WHERE FUNCNAME = '函数模块名'
```

### 查询表字段信息
```sql
SELECT FIELDNAME, ROLLNAME, DATATYPE, LENG, DECIMALS FROM DD03L WHERE TABNAME = '表名'
```

---

## 十、代码检查与语法参考

### 使用 sap-abap skill 进行代码审查

**在编写或修改 ABAP 代码后，必须调用 `sap-abap` skill 进行代码检查。**

触发条件：
- 新增代码段完成后
- 修改现有代码后
- 激活前的最终检查

检查内容：
1. **语法正确性** — 检查 ABAP 语法是否符合 7.40 SP08+ 规范
2. **最佳实践** — 检查是否遵循 SAP 官方推荐的编码模式
3. **性能问题** — 检查是否有 SELECT in LOOP、嵌套循环等性能隐患
4. **兼容性** — 检查语法是否与目标系统版本兼容

---

## 十一、相关记忆

- [[feedback-sap-modify-workflow]] — SAP 修改总工作流
- [[feedback-mcp-abap-tool-usage]] — MCP 工具调用教训
- [[reference-activate-function-module]] — 激活对象技巧（8个错误详细记录）
- [[reference-sap-connection]] — SAP 连接信息
- [[feedback-abap-dev-rules]] — ABAP 开发规范
- [[feedback-show-code-before-write]] — 修改前必须展示代码
- [[feedback-strict-spec-compliance]] — 必须严格对照规范执行
- [[feedback-ddic-table-double-activate]] — DDIC 表激活双重确认
- [[feedback-setobjectsource-use-header-request]] — 必须用 HEADER 请求号(K)
- [[feedback-for-all-entries-no-group-by]] — FOR ALL ENTRIES 不能配合 GROUP BY
- [[feedback-for-all-entries-type-length-match]] — FAE 字段长度必须一致
- [[feedback-call-function-no-inline-decl]] — CALL FUNCTION 禁止内联声明
- [[feedback-zfmdatasave-top-position]] — RFC 函数日志宏必须放顶部
