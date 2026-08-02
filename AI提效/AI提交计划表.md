# AI 提效计划表 — SamrtNano-Iot APP Android 开发

> 目标：在不泄露公司代码与协议的前提下，借助 GitHub Copilot 把日常重复劳动、排查成本、测试欠债三类工作显著降低，并在 4 周内形成可度量、可复用的工作习惯。

---

## 一、Copilot 能力地图（先知道有什么，再谈怎么用）

| 能力 | 触发方式 | 适合做什么 |
|------|----------|------------|
| 行内补全 | 编辑时自动出现 / `Alt+]` 切换候选 | 模板代码、样板字段、续写逻辑 |
| 行内聊天 | `Ctrl+I` | 局部重构、解释当前选区、改写一段代码 |
| Copilot Chat | 侧边栏对话 / `Ctrl+Shift+I` | 设计方案、排查 bug、生成测试、解释模块 |
| `@workspace` | Chat 中加 `@workspace` | 跨文件问答、定位实现、整体重构 |
| `@terminal` | Chat 中加 `@terminal` | 解释命令行报错、生成脚本 |
| Copilot Edits | 多文件编辑模式 | 一次改多个文件（如新增 ViewModel+Repository+测试） |
| Copilot Review | PR 页面 / `gh copilot review` | PR 自动审查 |
| Commit message | 提交框 `Copilot` 按钮 | 自动生成符合规范的提交信息 |
| Agent 模式 | Chat 切到 Agent | 让它自主规划并执行多步任务 |

> 优先掌握：行内补全 + Copilot Chat + `@workspace`，覆盖 80% 日常场景。

---

## 二、Android 开发高频落地场景

### 1. 样板代码自动生成（最高 ROI）

把以下重复劳动交给 Copilot，自己只写"意图注释"：

- **RecyclerView Adapter + ViewHolder**：写一行 `// 设备列表 Adapter，item 布局为 item_device.xml`，让 Copilot 补完整。
- **ViewModel + StateFlow**：注释里写"管理设备列表状态，含 loading/error/data 三态"，自动出骨架。
- **Room Entity + DAO**：给出表结构注释，自动出 `@Entity`、`@Dao`、`@Query`。
- **Hilt Module**：写好 `@Module`，绑定关系让它补。
- **Retrofit API**：给出接口描述，自动出 `@GET`/`@POST` 签名。
- **ViewBinding 委托**、**DataStore 封装**、**Parcelable（@Parcelize）数据类**：基本一行搞定。

**练习方式**：每天选一个新模块，强制自己只写注释 + 关键字段，剩下交给补全，再 review。

### 2. 单元测试（解决测试欠债）

SamrtNano-Iot 业务里 ViewModel、Repository、UseCase 最值得测。流程：

1. 打开目标类 → Copilot Chat 输入：`/tests 为这个类生成单元测试，使用 JUnit4 + MockK + Turbine，覆盖正常态、空数据、异常态`。
2. Review 生成的测试，补齐业务边界（如设备离线、协议超时、权限拒绝）。
3. 跑通后加入 CI。

**目标**：每周至少给 2 个 ViewModel / Repository 补齐测试，4 周后个人模块测试覆盖率 +15%。

### 3. 代码理解与排雷

- **接手老模块**：`@workspace 解释一下设备配网(onboarding)整体流程，涉及哪些类`。
- **第三方库用法**：`如何用 MQTT v5 的 autoReconnect？给一段 Kotlin 示例`。
- **报错日志排查**：把 stacktrace（脱敏后）贴给 Chat，让它列 Top3 可能原因 + 验证方式。

### 4. Bug 修复闭环

固定流程，避免凭感觉改：

1. 复现：写一个失败的单测/集成测试 reproducing the bug。
2. 让 Copilot 分析根因：`基于这个失败用例，分析可能的原因`。
3. 让 Copilot 给修复方案，自己 review 后采纳。
4. 用例变绿 → 提交（commit message 让 Copilot 生成）。

### 5. 重构

常见可让 Copilot 介入的：

- Java → Kotlin（自动 + 手动校验）。
- 命令式 View → Compose（小步迁移）。
- 回调嵌套 → 协程 `suspend`/`Flow`。
- 巨型类拆分：让 `@workspace` 给拆分方案，再分步执行。

---

## 三、SamrtNano-Iot 业务场景对照表

| 业务模块 | Copilot 可介入点 |
|----------|------------------|
| 设备配网（onboarding） | 状态机文档化、流程图注释、各步骤单测 |
| 设备列表/分组 | DiffUtil、列表过滤、分组逻辑生成 |
| 设备控制（开关/亮度/色温） | 控制协议参数封装、防抖/节流工具 |
| MQTT 通信 | 消息体解析、重连策略、订阅管理单测 |
| 场景自动化（Smart Action） | 条件表达式解析器、规则执行器测试 |
| 家庭/房间管理 | 树形数据结构、CRUD Repository |
| 固件升级（OTA） | 进度状态机、断点续传逻辑 |
| 多语言/资源 | strings.xml 批量校验、占位符检查 |

---

## 四、4 周落地计划

### 第 1 周 — 熟悉与上手
- [ ] 安装并配置 GitHub Copilot（Android Studio 插件 / IntelliJ 平台）。
- [ ] 完成官方 Copilot 入门练习（1 小时）。
- [ ] 每天至少用行内补全完成 1 个小功能。
- [ ] 用 Chat 解释 1 段不熟悉的老代码。
- **验证**：能流畅切换候选、知道何时该写注释引导。

### 第 2 周 — 测试与排查
- [ ] 给 2 个 ViewModel / Repository 补齐单元测试。
- [ ] 用 Chat 排查 1 个线上/测试 bug，记录对比以往耗时。
- [ ] 尝试 `@workspace` 做 1 次跨文件代码理解。
- **验证**：单测能跑通并入 CI；bug 平均定位时长有下降。

### 第 3 周 — 业务模块实战
- [ ] 选 1 个 SamrtNano-Iot 业务模块（建议设备列表或场景），完整跑通"需求→补全→测试→Review→提交"。
- [ ] 让 Copilot 生成 PR 描述与 commit message。
- [ ] 记录一次"被 Copilot 节省时间最多"的场景。
- **验证**：交付 1 个完整 feature，且自评效率提升。

### 第 4 周 — 度量与复盘
- [ ] 汇总指标（见第五节）。
- [ ] 总结个人 Prompt 模板（保存到笔记）。
- [ ] 在团队分享 1 个最佳实践 + 1 个踩坑。
- **验证**：形成可复用工作流，能教给同事。

---

## 五、效率度量（不度量就没有改进）

每周五填一次：

| 指标 | 第1周 | 第2周 | 第3周 | 第4周 |
|------|-------|-------|-------|-------|
| 行内补全接受率（IDE 自带统计） |  |  |  |  |
| 单元测试新增数量 |  |  |  |  |
| 模块测试覆盖率 |  |  |  |  |
| Bug 平均定位时长（h） |  |  |  |  |
| PR Review 平均轮次 |  |  |  |  |
| 主观效率评分（1-10） |  |  |  |  |
| 节省时间最多的场景 |  |  |  |  |

> Android Studio / IntelliJ 可在 `Settings → Tools → Copilot` 看到补全统计；团队管理员可在 GitHub 后台看组织级数据。

---

## 六、红线与注意事项

- **代码合规**：SamrtNano-Iot 内部协议、密钥、用户数据、未公开 SDK，**禁止**贴到 Copilot Chat 或公开 PR。
- **生成代码必须 review**：尤其涉及线程安全、生命周期、权限申请、网络请求的部分。
- **不盲信**：Copilot 会"自信地写错"，关键逻辑自己验证 + 单测兜底。
- **保留判断**：能简化的就别让它生成复杂方案；它的复杂度有时会反向污染代码。
- **知识产权**：公司有 Copilot 企业版/商业版许可时，代码不会用于训练（确认贵司采购版本对应条款）。

---

## 七、个人 Prompt 模板（持续补充）

```
# 生成 ViewModel 测试
/tests 为 ${文件名} 生成单元测试，技术栈：JUnit4 + MockK + Turbine + kotlin coroutines-test。
要求覆盖：正常态、空数据、网络异常、协议超时。Mock 所有 Repository 依赖。

# 解释老代码
@workspace 解释 ${模块名} 的整体流程，重点说明：数据流向、线程切换、关键状态机。

# Bug 排查
以下是脱敏后的崩溃栈，请给出 Top3 可能原因与对应验证方法：
${stacktrace}

# 重构建议
@workspace 选中的方法圈复杂度过高，请给出 2 种重构方案并对比 trade-off。
```

---

**一句话总结**：先让 Copilot 接手"重复 + 模板 + 测试 + 解释"四类活，自己专注"协议设计、架构判断、产品逻辑"，4 周形成肌肉记忆，再用数据复盘。
