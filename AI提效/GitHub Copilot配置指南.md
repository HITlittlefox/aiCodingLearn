# GitHub Copilot 配置指南 —— SamrtNano-Iot APP Android 开发

> 本文档详细介绍如何为 SamrtNano-Iot APP Android 项目配置 GitHub Copilot，以最大化 AI 辅助开发效率。
> 时间基准：2026-08。

---

## 一、Tier 1 — 必做（开箱即用前 1 小时内完成）

### 1. IDE 插件安装与账户登录
- **作用**：在 Android Studio 中启用 Copilot 行内补全与 Chat。
- **配置位置**：`Settings → Plugins → Marketplace` 搜索 "GitHub Copilot" + "GitHub Copilot Chat" → 安装重启 → 右下角 Copilot 图标用 GitHub OAuth 登录。
- **推荐度**：⭐⭐⭐
- **官方文档**：[Configure GitHub Copilot in your environment (JetBrains)](https://docs.github.com/en/copilot/managing-copilot/configure-personal-settings/configuring-github-copilot-in-your-environment?tool=jetbrains)

### 2. 行内补全行为设置
- **作用**：控制补全触发时机、是否显示下一编辑建议（NES）、候选切换快捷键。
- **配置位置**：`Settings → Tools → GitHub Copilot`：
  - 勾选 `Enable inline completions`
  - 勾选 `Enable next edit suggestions`（如可用）
  - 习惯延迟补全可关闭 `Auto-display suggestions`，改用手动触发
- **快捷键**：`Alt+\` 触发 / `Alt+]` `Alt+[` 切换候选 / `Tab` 接受 / `Esc` 拒绝。
- **推荐度**：⭐⭐⭐
- **官方文档**：[Get started with GitHub Copilot completions](https://docs.github.com/en/copilot/using-github-copilot/getting-code-suggestions-in-your-ide-from-github-copilot)

### 3. Copilot Chat 基础模式选择
- **作用**：Android Studio 里的 Copilot 有三种模式：Ask（问答）/ Agent（自主修改文件）/ Plan（只读出方案）。日常写代码用 Ask，重构/补全任务用 Agent，先评估后改动用 Plan。
- **配置位置**：Chat 面板顶部模式下拉框。
- **推荐度**：⭐⭐⭐
- **官方文档**：[Use Copilot Chat in your IDE](https://docs.github.com/en/copilot/using-github-copilot/copilot-chat/using-github-copilot-chat-in-your-ide)

### 4. 模型与推理级别
- **作用**：不同任务选不同模型——日常补全用 GPT-5 mini（不消耗 premium 配额），复杂重构/调试用 GPT-5 或 Claude Sonnet；推理级别高 = 思考更深但更慢。
- **配置位置**：Chat 输入框上方模型选择器 → 同时可选 "Reasoning" 级别（low/medium/high）。
- **推荐度**：⭐⭐⭐
- **官方文档**：[Change the model for Copilot Chat](https://docs.github.com/en/copilot/using-github-copilot/copilot-chat/changing-the-model-for-copilot-chat)

---

## 二、Tier 2 — 推荐（项目级长期规则，1 周内必配）

### 5. 仓库级自定义指令 `.github/copilot-instructions.md`
- **作用**：团队最高优先级的自然语言规则源，对所有 Chat 请求和补全自动生效。SamrtNano-Iot 项目可写明：使用 Kotlin + Compose、Hilt 依赖注入、协程 + Flow、最小 SDK、命名约定、禁止引入某类库、协议层不得外泄等。
- **配置位置**：仓库根目录 `.github/copilot-instructions.md`。也可用 `/init` 让 Copilot 自动生成草稿。
- **推荐度**：⭐⭐⭐
- **官方文档**：[Add repository custom instructions](https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot)

### 6. 路径级指令 `.github/instructions/*.instructions.md`
- **作用**：对特定文件类型生效的细粒度规则，比仓库级指令更精准。例如只对 `**/*.kt` 要求显式返回类型、禁止 `any`。
- **配置位置**：`.github/instructions/*.instructions.md`，文件头部用 frontmatter 声明：
  ```markdown
  ---
  applyTo: "**/*.kt,**/*.kts"
  ---
  - 使用显式返回类型
  - 禁止使用 `!!`，改用 `requireNotNull` 或 `?:`
  ```
- **推荐度**：⭐⭐
- **官方文档**：[Add custom instructions for specific files](https://docs.github.com/en/copilot/customizing-copilot/adding-custom-instructions-for-specific-files)

### 7. Prompt Files `.github/prompts/*.prompt.md`
- **作用**：把团队高频任务封装成可复用提示词，用 `/` 斜杠调用。SamrtNano-Iot 适合做：`/device-list-test`（设备列表 ViewModel 测试）、`/protocol-review`（协议层代码审查）、`/onboarding-state-machine`（配网状态机生成）。
- **配置位置**：`.github/prompts/<name>.prompt.md`，调用时输入 `/<name>`。
- **推荐度**：⭐⭐
- **官方文档**：[Create a custom prompt file](https://docs.github.com/en/copilot/customizing-copilot/creating-a-custom-prompt-file-for-github-copilot)

### 8. 内容排除（Content Exclusion）
- **作用**：防止敏感文件（密钥、协议常量、用户数据、内部 SDK）被 Copilot 读取或补全。这是**企业/组织级**配置，不走仓库文件。
- **配置位置**：`GitHub.com → 仓库 Settings → Copilot → Content exclusions` 添加路径（支持 glob），或组织级 `Organization Settings → Copilot → Content exclusions`。变更后 VS Code 等 IDE 最多 30 分钟生效，可重载窗口立即生效。
- **SamrtNano-Iot 强烈建议排除**：`**/protocol/**`、`**/secret/**`、`**/credentials/**`、`**/internal-sdk/**`、`**/keys.json`。
- **推荐度**：⭐⭐⭐（合规红线）
- **官方文档**：[Exclude content from Copilot](https://docs.github.com/en/copilot/managing-copilot/managing-github-copilot-in-your-organization/setting-policies-for-copilot-in-your-organization/excluding-content-from-github-copilot)

### 9. 个人级全局指令（跨项目通用规则）
- **作用**：在你所有项目都生效的默认规则，作为仓库级指令的补充层。例如：永远用类型注解、禁止 `println`、注释用英文。
- **配置位置**：`GitHub.com → Profile → Settings → Copilot → Personal instructions`（在网页端配置，IDE 自动同步）。
- **推荐度**：⭐⭐
- **官方文档**：[Configure personal settings for Copilot](https://docs.github.com/en/copilot/managing-copilot/configuring-personal-settings/configuring-github-copilot-settings-on-githubcom)

---

## 三、Tier 3 — 进阶（按场景启用）

### 10. Custom Agents（自定义 Agent）`.github/copilot/agents/*.agent.md`
- **作用**：为专门角色定制 Agent，含专属 system prompt、工具白名单、模式。SamrtNano-Iot 可做：`protocol-expert.agent.md`（协议解析专家）、`test-writer.agent.md`（测试编写专家）、`compose-migrator.agent.md`（Compose 迁移专家）。
- **配置位置**：`.github/copilot/agents/<name>.agent.md`；也可全局放 `~/.copilot/agents/` 跨项目共享。
- **推荐度**：⭐⭐
- **官方文档**：[Create a custom agent](https://docs.github.com/en/copilot/customizing-copilot/creating-a-custom-agent-for-github-copilot)

### 11. Agent Skills（Agent 技能）
- **作用**：可被任何 Agent 调用的可复用能力包，比 prompt file 更结构化，可附带脚本和工具。SamrtNano-Iot 可封装"生成设备列表 Adapter"、"协议字段校验"等技能。
- **配置位置**：`.github/copilot/skills/<name>/SKILL.md`，可用 `/create-skill` 在 Chat 里让 Copilot 帮你生成骨架。
- **推荐度**：⭐
- **官方文档**：[Create Agent Skills](https://docs.github.com/en/copilot/customizing-copilot/creating-agent-skills)

### 12. Copilot Hooks（执行前后钩子）
- **作用**：在 Agent 执行命令、修改文件前后插入自定义校验逻辑，例如禁止删除 `release/*` 分支、提交前强制跑 `./gradlew detekt`、改动协议层文件前要求审批。
- **配置位置**：`.github/copilot/hooks/<name>.hook.json`；JetBrains IDE 在 `Settings → Tools → GitHub Copilot → Agent Customizations` 可视化管理。
- **推荐度**：⭐⭐（涉及安全/合规时强烈推荐）
- **官方文档**：[Copilot Hooks](https://docs.github.com/en/copilot/customizing-copilot/copilot-hooks)

### 13. MCP Server（扩展外部工具能力）
- **作用**：让 Copilot 调用外部工具（数据库查询、Jira、Sentry、Figma、Postman、内部协议文档库）。SamrtNano-Iot 可接：Bugly 崩溃查询、内部 Jira、协议 Mock 服务。
- **配置位置**：
  - 项目级：`.github/mcp.json`
  - 全局级：`~/.copilot/mcp.json`（macOS/Linux）或 `%USERPROFILE%\.copilot\mcp.json`（Windows）
  - JetBrains：`Settings → Tools → GitHub Copilot → Chat → Agent Customizations → MCP servers`
- **推荐度**：⭐⭐（接入内部知识库后 ROI 暴涨）
- **官方文档**：[Extend Copilot Chat with MCP](https://docs.github.com/en/copilot/customizing-copilot/extending-copilot-chat-with-mcp)

### 14. AGENTS.md（跨工具通用指令）
- **作用**：2026 年 7 月起 Copilot Code Review 会读取 `AGENTS.md`、`copilot-instructions.md`、`*.instructions.md`、`REVIEW.md`、`CLAUDE.md`、`GEMINI.md`。`AGENTS.md` 是跨 AI 工具（Copilot + Claude + Codex）通用的指令约定，写一份多工具共享。
- **配置位置**：仓库根目录 `AGENTS.md`。
- **推荐度**：⭐⭐（多 AI 工具协作时强烈推荐）
- **官方文档**：[Copilot code review: Customization and configurability improvements](https://github.blog/changelog/2026-07-17-copilot-code-review-customization-and-configurability-improvements/)

---

## 四、Tier 4 — 团队 / 企业级（需要管理员操作）

### 15. Copilot Code Review（PR 自动审查）
- **作用**：PR 提交后自动触发 Copilot 审查，按你的自定义指令挑出问题。
- **配置位置**：
  - 启用：GitHub.com 仓库 `Settings → Copilot → Code review`
  - 自定义环境：`.github/workflows/copilot-code-review.yml`（可安装依赖、配置工具链）
  - 自定义指令：上面提到的 `copilot-instructions.md` / `AGENTS.md` / `REVIEW.md`
- **推荐度**：⭐⭐
- **官方文档**：[Use Copilot code review](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review)

### 16. Copilot CLI（终端 Agent）
- **作用**：在终端里跑长任务 Agent，可调用内置子 Agent（Explore / Task / Plan / Code-review），JetBrains IDE 可把任务委托给本地 CLI Agent。SamrtNano-Iot 适合做：跑全量构建并分析失败、批量改命名、生成版本日志。
- **配置位置**：终端 `copilot` 命令启动；JetBrains 在 Chat Agent picker 选 "Copilot CLI"。
- **推荐度**：⭐
- **官方文档**：[GitHub Copilot CLI](https://docs.github.com/en/copilot/using-github-copilot/using-github-copilot-in-the-command-line)

### 17. 企业策略与审计日志
- **作用**：管理员控制可用模型、Editor preview 开关、数据是否用于训练、审计谁用了 Copilot 做了什么。
- **配置位置**：
  - 组织策略：`GitHub.com → Organization Settings → Copilot → Policies`
  - 审计日志：`Organization Settings → Audit log` 过滤 `copilot`
- **推荐度**：⭐⭐（合规要求高的项目必备）
- **官方文档**：[Manage policies for Copilot in your organization](https://docs.github.com/en/copilot/managing-copilot/managing-github-copilot-in-your-organization/managing-policies-for-copilot-in-your-organization)

---

## 五、SamrtNano-Iot Android 推荐配置组合（开箱即用版）

按下面顺序操作，半小时内形成基础工作流：

```
仓库根目录/
├── .github/
│   ├── copilot-instructions.md          # Tier 1 - 团队规则
│   ├── instructions/
│   │   ├── kotlin.instructions.md        # applyTo: **/*.kt
│   │   └── compose.instructions.md       # applyTo: **/*Screen.kt,**/*Content.kt
│   ├── prompts/
│   │   ├── vm-test.prompt.md             # ViewModel 测试模板
│   │   ├── protocol-review.prompt.md     # 协议层审查
│   │   └── bug-fix.prompt.md             # Bug 修复闭环
│   └── workflows/
│       └── copilot-code-review.yml       # PR 自动审查环境
├── AGENTS.md                             # 跨工具通用指令
└── (GitHub.com 仓库 Settings → Copilot → Content exclusions)
    ├── **/protocol/**
    ├── **/secret/**
    └── **/internal-sdk/**
```

`.github/copilot-instructions.md` 推荐起步内容：

```markdown
# SamrtNano-Iot APP Android 项目规则

- 语言：Kotlin 优先，新代码全部用 Kotlin；历史 Java 代码改不动时保留。
- UI：新页面一律用 Jetpack Compose；老 XML 页面维护时保留，不强制迁移。
- 依赖注入：统一用 Hilt，禁止再引入 Koin / Dagger 直配。
- 异步：协程 + Flow；禁止 RxJava 新代码；老 RxJava 代码维护时保留。
- 架构：MVVM + Clean Architecture，分层为 ui/domain/data。
- 数据库：Room；网络：Retrofit + OkHttp；图片：Coil。
- 命名：类 PascalCase，函数/变量 camelCase，常量 UPPER_SNAKE_CASE。
- 测试：ViewModel/Repository/UseCase 必须有单元测试，使用 JUnit4 + MockK + Turbine。
- 协议层（com.tapo.protocol）属于内部敏感代码，不要在 Chat 中讨论其实现细节，也不要让生成代码依赖其具体常量。
- 禁止引入未在 build.gradle.kts 中已声明的第三方库。
- 修改业务逻辑后，必须同步更新对应测试。
```

---

## 六、可直接套用的 SamrtNano-Iot 配置文件模板（完整内容）

### 6.1 `.github/copilot-instructions.md`（仓库级全局指令）

```markdown
# SamrtNano-Iot APP Android — Copilot Instructions

## 1. Tech Stack & Language
- Primary Language: **Kotlin** (strictly NO Java for new code; legacy Java only edited when necessary).
- Min SDK: 24 / Target SDK: 35 (adjust to actual project).
- Architecture: **Clean Architecture + MVVM** (ui / domain / data layers).
- Asynchrony: **Kotlin Coroutines + StateFlow/SharedFlow**. NO RxJava / LiveData for new code.
- UI: **Jetpack Compose** for new screens; legacy ViewBinding/XML maintained as-is, not force-migrated.
- DI: **Hilt** only. Do NOT introduce Koin or raw Dagger.
- DB: **Room**; Network: **Retrofit + OkHttp**; Image: **Coil**.
- JSON: **kotlinx.serialization** or **Moshi** (match existing project; do not mix).

## 2. Coding Guidelines
- **Immutability**: Prefer `val` over `var`. Use `data class` for state objects.
- **Null Safety**: Never use `!!`. Use `?.let {}`, `?:`, `requireNotNull`, or smart casts.
- **Architecture Flow**:
  - `data` layer: Repository returns `Flow<T>` or `Result<T>`.
  - `domain` layer: UseCase has single responsibility, suspend function.
  - `presentation` layer: ViewModel exposes exactly one `StateFlow<UiState>`. UiState is a sealed interface.
- **Naming**: Class PascalCase; function/variable camelCase; constant UPPER_SNAKE_CASE; package lowercase no underscore.
- **Resource**: strings.xml for all user-visible text; Compose preview for every screen.
- **No new dependency**: Do not introduce libraries not already in `build.gradle.kts` without explicit ask.

## 3. Testing Standard
- Framework: **JUnit4 + MockK + Turbine + kotlin-coroutines-test**.
- Mocking: `coEvery` / `coVerify` for suspend functions; `every` / `verify` for synchronous.
- Dispatcher: Always inject `TestDispatcher` (usually `StandardTestDispatcher`); use `runTest { }`.
- Structure: **Given-When-Then** comments in each test.
- Coverage target: ViewModel / Repository / UseCase must have unit tests.

## 4. SamrtNano-Iot Business Constraints
- 协议层 `com.tapo.protocol.*` 为内部敏感代码，生成代码不得依赖其具体常量，讨论时不暴露实现细节。
- 设备控制类操作（开关 / 亮度 / 色温）必须经过对应 UseCase，禁止 ViewModel 直连 Repository 调协议。
- 配网流程（onboarding）涉及状态机，修改前先用 `@workspace` 让 Copilot 输出现有流程图，确认后再改。
- 多语言文案改 `strings.xml`，禁止硬编码中文 / 英文字符串。

## 5. Workflow
- After changing business logic, update or add corresponding tests.
- Before commit, run `./gradlew detekt` and `./gradlew testDebugUnitTest`.
- Commit message follows Conventional Commits (feat / fix / refactor / chore / docs / test).
```

### 6.2 `.github/instructions/testing.instructions.md`（测试文件专用规则）

```markdown
---
applyTo: "**/*Test.kt,**/test/**/*.kt"
---
# Testing Instructions (Kotlin)

- Use **JUnit4** (`@Test`, `@Before`, `@After`). Do not use JUnit5.
- Mocking library: **MockK** only. Never use Mockito or PowerMock.
  - Suspend function: `coEvery { repo.getX() } returns ...` / `coVerify { repo.getX() }`
  - Flow: `coEvery { repo.observeX() } returns flowOf(...)`
- Flow testing: **Turbine** (`someFlow.test { awaitItem() shouldBe ... }`).
- Coroutines test: **kotlin-coroutines-test** with `runTest { }` block.
  - Always pass `TestDispatcher` (e.g. `StandardTestDispatcher`) into the class under test.
  - Use `scheduler.advanceUntilIdle()` to drain pending coroutines.
- Test structure: organize by **Given-When-Then** comment blocks.
- Test name: `fun \`when condition then expected behavior\`()` using backticks.
- Do not test Android framework classes; use Robolectric only when unavoidable.
- For Compose UI tests: `createComposeRule()`, assert with `onNodeWithText` / `onNodeWithTag`.
- Each public method on ViewModel/Repository/UseCase must have at least:
  - 1 happy path test
  - 1 empty/null input test
  - 1 error/exception path test
```

### 6.3 `.github/prompts/generate-unit-tests.prompt.md`（一键生成单测）

```markdown
---
mode: agent
description: "为选中的类生成符合团队规范的单元测试"
tools: ['changes', 'githubRepo', 'currentOpenFile']
---
为 #selection 指向的类生成单元测试，遵循以下要求：

1. 技术栈：JUnit4 + MockK + Turbine + kotlin-coroutines-test。
2. 使用 `runTest { }` 包裹，注入 `StandardTestDispatcher`。
3. 对所有 Repository / 依赖项使用 MockK 的 `coEvery` / `every` 进行 mock。
4. 测试结构按 **Given-When-Then** 三段注释组织。
5. 每个公共方法至少覆盖：
   - 正常态（happy path）
   - 空数据 / null 输入
   - 异常 / 协议超时 / 网络错误
6. 测试函数名使用反引号包裹自然语言：`fun \`when x then y\`()`
7. 测试文件放在 `src/test/java/<对应包路径>/` 下，文件名 `<原类名>Test.kt`。
8. 不要 mock 数据类 / value object，直接构造真实实例。

完成后运行 `./gradlew :app:testDebugUnitTest --tests "<生成的测试类>"` 验证通过。
```

### 6.4 `.github/prompts/protocol-review.prompt.md`（协议层代码审查）

```markdown
---
mode: agent
description: "审查协议层 / 设备控制层代码的安全性与一致性"
tools: ['changes', 'githubRepo']
---
对当前 PR 中改动的协议层代码（路径匹配 `**/protocol/**` 或 `**/device/control/**`）进行审查，重点检查：

1. 是否有硬编码的密钥 / token / 用户ID。
2. 是否在日志中打印了协议原文或敏感字段（应使用脱敏 wrapper）。
3. ViewModel 是否绕过 UseCase 直接调用了 Repository（违反分层）。
4. 设备控制指令是否缺少幂等性保护或超时处理。
5. 异常是否被静默吞掉（`catch {}` 空块）。
6. 是否引入了未声明的第三方依赖。
7. 协议常量是否泄漏到了 `data` / `domain` 之外的层。

输出格式：
- 🔴 严重问题（必须修复）：列表
- 🟡 建议改进：列表
- 🟢 通过项：列表
```

---

## 七、配置完成自检清单

- [ ] 已安装 GitHub Copilot + Copilot Chat 插件并登录。
- [ ] 行内补全可用，快捷键已熟悉。
- [ ] 已选好默认模型（推荐 GPT-5 mini 日常，GPT-5 复杂任务）。
- [ ] 仓库根目录有 `.github/copilot-instructions.md`。
- [ ] 至少 1 个路径级指令文件（Kotlin 规则）。
- [ ] 至少 1 个 Prompt File（建议先做 `vm-test.prompt.md`）。
- [ ] GitHub.com 仓库 Settings 里配置了 Content Exclusion（敏感目录）。
- [ ] 知道 `@workspace` `/tests` `#file` 三个最常用入口。
- [ ] 全员阅读过 Tier 2 配置文档链接。

---

## 八、社区资源与参考文档

- **社区资源库**：[github/awesome-copilot](https://github.com/github/awesome-copilot)
- **官方文档**：[GitHub Copilot 总文档首页](https://docs.github.com/en/copilot)
- **官方介绍博客**：[Introducing Awesome GitHub Copilot Customizations](https://developer.microsoft.com/blog/introducing-awesome-github-copilot-customizations-repo/)
