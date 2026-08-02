# Android Studio 本地大模型（Ollama）配置指南 —— SamrtNano-Iot APP 开发专属

> 本文档详细介绍如何为 SamrtNano-Iot APP Android 开发配置 Android Studio 的本地大模型（如 Ollama），通过 **Prompt Library** 实现规则自动化注入，完全无需网络连接。
> 适用场景：涉及敏感代码（如协议层）的开发，注重数据隐私。

---

## 一、核心差异：本地模型 vs GitHub Copilot

本地模型（如 Ollama）与 GitHub Copilot 在规则注入机制上完全不同。Copilot 依赖仓库文件（`.github/copilot-instructions.md`），而本地模型需要通过 IDE 自带的 **Prompt Library** 功能实现。

| 配置类型 | GitHub Copilot | 本地大模型 (Ollama) | 解决方案 |
| :--- | :--- | :--- | :--- |
| **项目级规则** | `.github/copilot-instructions.md` 自动读取 | ❌ **不支持**。无法读取仓库文件。 | ✅ 改为在 **Prompt Library → Rules** 中配置全局 System Prompt。 |
| **快捷命令** | `.github/prompts/*.prompt.md` (`/` 调用) | ❌ **不支持**。 | ✅ 将常用任务保存到 **Prompt Library → Saved Prompts**。 |
| **内容排除** | GitHub.com 后台配置 | 🎉 **不需要**。代码数据不离本机。 | 跳过此配置，天然保证隐私。 |

---

## 二、Prompt Library 配置详解

Android Studio 的 `Settings → Tools → AI → Prompt Library` 是本地模型的“大脑”，分为三个核心模块：

### 1. Rules (全局规则)
- **作用**：定义项目的技术栈、编码规范、业务红线。这部分内容将作为 **System Prompt（系统提示词）**，在你每次与 AI 交互时**自动注入**。
- **配置步骤**：
  1. 打开 `Settings → Tools → AI → Prompt Library`。
  2. 在 **Rules** 分组下，点击 `+` 创建新规则。
  3. **Name**: 命名为 `SamrtNano-Iot Project Rules`。
  4. **Prompt**: **直接复制以下内容**：
     ```text
     请在后续的回答和代码生成中严格遵守以下 SamrtNano-Iot APP Android 项目规则：

     1. 技术栈：
     - 主要语言 Kotlin，严禁新写 Java。
     - 架构：Clean Architecture + MVVM (ui/domain/data)。
     - 异步：Coroutines + StateFlow。
     - UI：Jetpack Compose。
     - 依赖注入：Hilt。
     - 数据库：Room；网络：Retrofit + OkHttp。

     2. 编码规范：
     - 优先使用 val，严禁使用 `!!` 非空断言。
     - 架构分层：data 层返回 Flow/Result；domain 层 UseCase 单一职责；presentation 层 ViewModel 暴露单一 StateFlow。
     - 命名规范：类 PascalCase；函数/变量 camelCase；常量 UPPER_SNAKE_CASE。

     3. 测试标准：
     - 框架：JUnit4 + MockK + Turbine。
     - 结构：Given-When-Then 注释。

     4. SamrtNano-Iot 业务红线：
     - 协议层 (com.tapo.protocol.*) 为内部敏感代码，严禁生成依赖其常量的代码。
     - 设备控制必须经过 UseCase，严禁 ViewModel 直连 Repository 调协议。
     - 禁止引入未在 build.gradle.kts 中声明的第三方库。
     ```
  5. **Scope**: 选择 `IDE` (对所有项目生效)。

### 2. Saved Prompts (快捷命令)
- **作用**：将高频任务模板保存下来，通过特定方式（如 `/` 命令）快速调用，无需每次重新描述。
- **配置步骤**：
  1. 在 **Saved Prompts** 分组下，点击 `+` 创建新 Prompt。
  2. **Prompt 1：SamrtNano-Iot Generate Unit Tests**
     - **Name**: `SamrtNano-Iot Generate Unit Tests`
     - **Prompt**:
       ```text
       为我当前打开的 Kotlin 文件生成单元测试。要求：
       1. 使用 JUnit4 + MockK + Turbine。
       2. 使用 `runTest {}` 包裹，注入 `StandardTestDispatcher`。
       3. 所有依赖使用 MockK 的 `coEvery` / `every` 进行 mock。
       4. 覆盖正常态、空数据、异常三种情况。
       5. 测试函数名使用反引号，例如 `fun `when condition then expected behavior``。
       ```
  3. **Prompt 2：SamrtNano-Iot Code Review**
     - **Name**: `SamrtNano-Iot Code Review`
     - **Prompt**:
       ```text
       请审查我当前打开的代码，重点检查：
       1. 是否违反了架构分层？（例如 ViewModel 是否直接调用了 Repository）
       2. 是否使用了 `!!` 进行非空断言？
       3. 是否有硬编码的字符串或颜色值？
       4. 协程作用域 (CoroutineScope) 是否正确？
       5. 是否有内存泄漏风险？
       ```

### 3. Built-in Actions (进阶自动化)
- **作用**：定义更强大的自动化流程，可能允许 Agent 自主跨文件修改、生成代码。适合封装复杂的重构逻辑。
- **配置步骤 (示例)**：
  1. 在 **Built-in Actions** 分组下，点击 `+` 创建新 Action。
  2. **Name**: `SamrtNano-Iot Quick Mapper & Test`
  3. **Prompt**:
     ```text
     为选中的 Entity 类执行以下操作：
     1. 生成对应的 UiModel 数据类。
     2. 生成 Entity 到 UiModel 的 Mapper 扩展函数。
     3. 针对 Mapper 生成单元测试。
     ```

---

## 三、本地大模型推荐（针对 Android 开发）

并非所有本地模型都适合代码生成，特别是 Kotlin 和中文混合的场景。

| 模型 | 参数量 | 推荐度 | 说明 |
| :--- | :--- | :--- | :--- |
| **qwen2.5-coder** | 14B+ | ⭐⭐⭐ | 阿里开源，对中文注释和 Kotlin 语法支持最好，是目前国产首选。 |
| **deepseek-coder** | 6.7B+ | ⭐⭐ | 性能强劲，代码生成质量高，适合复杂重构。 |
| **CodeLlama** | 13B+ | ⭐ | Meta 开源，基于 Llama 2，支持多语言。 |

**注意**：
- 模型参数量越大，生成质量越高，但对显卡/CPU 要求也越高。
- 如果电脑没有独立显卡，建议使用 7B 以下的模型。

---

## 四、本地模型 vs Copilot 工作流建议

| 场景 | 推荐工具 | 原因 |
| :--- | :--- | :--- |
| **敏感代码开发** (如协议层) | **本地模型** | 数据绝对安全，不会上传到任何服务器。 |
| **日常代码补全** | Copilot 或本地模型 | Copilot 响应更快，本地模型无需网络。 |
| **学习新技术/查 API** | Copilot Chat (联网) | Copilot 拥有最新的 API 文档。 |
| **复杂重构/跨文件修改** | Copilot Agent 模式 | Copilot 能更好地理解整个项目上下文。 |
| **编写单元测试** | **本地模型** 或 Copilot | 两者均可，本地模型在无网络时优势明显。 |

**总结**：处理 SamrtNano-Iot 核心业务（特别是涉及协议、密钥的部分）时，**本地模型是首选**。通过 **Prompt Library** 的配置，你完全可以获得类似 GitHub Copilot 的便捷体验，同时享受数据本地化的隐私保护。
