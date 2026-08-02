# AI Agent 配置总览

本目录包含针对 SamrtNano-Iot APP Android 开发的两种主流 AI Agent 配置指南，请根据你当前使用的工具查阅对应文档。

---

## 📄 文档列表

### 1. [GitHub Copilot 配置指南](GitHub Copilot配置指南.md)
**适用场景**：公司采购了 GitHub Copilot 订阅，主要开发环境为 JetBrains IDE（Android Studio / IntelliJ IDEA）。
**核心优势**：响应速度快，上下文理解能力强，支持云端同步和团队协作。
**配置重点**：通过仓库内的 `.github/copilot-instructions.md` 等文件自动注入规则。

### 2. [Android Studio 本地大模型配置指南](Android Studio本地大模型配置指南.md)
**适用场景**：注重数据隐私，主要处理包含敏感信息（如 SamrtNano-Iot 内部协议）的代码开发。
**核心优势**：完全本地化运行，数据不出本机，免费。
**配置重点**：通过 Android Studio 的 `Prompt Library` 功能配置全局规则（System Prompt）和快捷任务。

---

## 💡 选择建议

*   **如果你主要处理 SamrtNano-Iot 的敏感业务逻辑（如设备协议、加密算法）**：
    👉 优先选择 **Android Studio 本地大模型**，确保数据安全。

*   **如果你需要频繁查询最新的 Android API、进行跨文件的复杂重构、或团队中多人协作**：
    👉 选择 **GitHub Copilot**，享受云端服务的便利。

*   **最佳实践**：
    两者可以互补使用。例如：在编写协议层代码时切换到本地模型，在学习新技术或重构老代码时切换回 Copilot。
