# 在 VSCode Copilot 插件中使用国内大模型中转 API

> 本指南介绍如何在 VSCode 的 GitHub Copilot 插件中配置国内大模型（如 DeepSeek、通义千问、智谱 AI、月之暗面等）的 OpenAI 兼容中转 API。

---

## 前提条件

- 已安装 [Visual Studio Code](https://code.visualstudio.com/)
- 已安装 [GitHub Copilot](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) 和 [GitHub Copilot Chat](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat) 插件
- 拥有国内大模型提供商的 API Key（各平台注册后获取）

---

## 方法一：使用 VSCode 内置的自定义语言模型功能（推荐，VSCode ≥ 1.99）

VSCode 1.99 起，GitHub Copilot Chat 支持直接添加兼容 OpenAI 格式的自定义模型提供商，无需任何第三方插件或本地代理。

### 配置步骤

1. 打开 VSCode，按 `Ctrl+Shift+P`（macOS: `Cmd+Shift+P`）打开命令面板。
2. 输入并执行 `GitHub Copilot: Manage Custom Language Model Providers`（或在 Copilot Chat 界面点击模型选择器 → **Add Language Model**）。
3. 在弹出的输入框中填写：
   - **Provider Name**：自定义名称，例如 `DeepSeek`
   - **API Base URL**：中转 API 地址（见下方常见提供商列表）
   - **API Key**：你的 API Key
4. 保存后，在 Copilot Chat 的模型选择器中即可选择你添加的模型。

---

## 方法二：修改 `settings.json` 配置代理端点

适用于旧版 VSCode 或希望全局替换 Copilot 请求地址的场景。

### 配置步骤

1. 打开 VSCode，按 `Ctrl+,` 进入设置，点击右上角 **打开设置 (JSON)** 图标。
2. 添加以下配置（以 DeepSeek 为例）：

```jsonc
{
  // Copilot 补全（inline completion）代理
  "github.copilot.advanced": {
    "debug.overrideProxyUrl": "https://api.deepseek.com",
    "debug.chatOverrideProxyUrl": "https://api.deepseek.com"
  }
}
```

3. 同时需要在系统环境变量或 VSCode 终端中设置 API Key：

```bash
export OPENAI_API_KEY="你的API_KEY"
```

> **注意**：此方法会覆盖所有 Copilot 请求，请确保中转 API 完整兼容 OpenAI 的 `/v1/chat/completions` 接口格式。

---

## 方法三：使用本地中转代理工具

若中转 API 并非完全兼容 OpenAI 格式，可以使用本地代理工具（如 [one-api](https://github.com/songquanpeng/one-api)、[new-api](https://github.com/Calcium-Ion/new-api) 等）在本地启动一个兼容层，再将 VSCode 指向本地地址。

```jsonc
{
  "github.copilot.advanced": {
    "debug.overrideProxyUrl": "http://localhost:3000",
    "debug.chatOverrideProxyUrl": "http://localhost:3000"
  }
}
```

---

## 常见国内大模型提供商 API 信息

| 提供商 | API Base URL | 官网 |
|--------|-------------|------|
| DeepSeek | `https://api.deepseek.com` | [deepseek.com](https://platform.deepseek.com/) |
| 阿里云通义千问 | `https://dashscope.aliyuncs.com/compatible-mode/v1` | [aliyun.com](https://dashscope.console.aliyun.com/) |
| 智谱 AI (GLM) | `https://open.bigmodel.cn/api/paas/v4` | [bigmodel.cn](https://open.bigmodel.cn/) |
| 月之暗面 (Kimi) | `https://api.moonshot.cn/v1` | [moonshot.cn](https://platform.moonshot.cn/) |
| 百度千帆 | `https://qianfan.baidubce.com/v2` | [bce.baidu.com](https://qianfan.cloud.baidu.com/) |
| 字节豆包 | `https://ark.cn-beijing.volces.com/api/v3` | [volcengine.com](https://www.volcengine.com/product/ark) |
| 腾讯混元 | `https://api.hunyuan.cloud.tencent.com/v1` | [tencent.com](https://cloud.tencent.com/product/hunyuan) |
| 硅基流动 | `https://api.siliconflow.cn/v1` | [siliconflow.cn](https://siliconflow.cn/) |

> 各平台均支持 OpenAI 兼容的 `/v1/chat/completions` 格式，可直接用于 Copilot Chat。

---

## 常见问题

**Q：配置后 Copilot 仍然连接 GitHub 服务器怎么办？**  
A：请确认 VSCode 版本已更新，且配置已保存到 `settings.json` 的正确位置（用户级或工作区级均可）。

**Q：API Key 应该填写到哪里？**  
A：使用方法一时，在添加提供商时直接填写；使用方法二时，部分实现通过环境变量 `OPENAI_API_KEY` 传递，或通过中转工具统一管理。

**Q：国内能直接访问这些 API 吗？**  
A：上表中的国内大模型提供商均可在国内网络环境下直接访问，无需代理。

---

## 参考资料

- [VSCode Copilot Chat 官方文档](https://code.visualstudio.com/docs/copilot/copilot-chat)
- [GitHub Copilot 设置说明](https://docs.github.com/en/copilot/configuring-github-copilot/configuring-github-copilot-in-your-environment)
- [one-api 开源中转项目](https://github.com/songquanpeng/one-api)
