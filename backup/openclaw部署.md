## AI 与开发基础名词解析

- **Base URL (基础网址):** 调用 AI 服务时要访问的“服务器地址”或“门牌号”。

- **API Key (接口密钥):** 调用 AI 服务时的身份凭证，用于验证权限和计费。

- **Model (模型):** AI 的“大脑核心”，不同模型的能力、擅长领域和运行成本各不相同。

- **Claude Code:** Anthropic 推出的“智能编程助手”，主要在终端和 IDE 里通过自然语言帮你写代码、调试、重构。

- **Codex:** OpenAI 的编程智能体（Coding Agent），可以在终端、桌面应用、IDE 扩展和云端环境里帮你写代码、理解代码、审查代码、修 Bug 和自动化开发任务。

- **Token (词元):** 模型处理文本时使用的“计量单位”，决定了处理长度和计费标准。

- **Skills (技能/插件):** 可以理解成“给 AI 的专项能力包”、“操作说明书”或“工作 SOP”，让 AI 掌握特定任务的执行方式。

- **Vibe Coding (直觉编程):** 一种高度依赖 AI 辅助、以自然语言为驱动的全新编程模式。开发者主要负责把控逻辑和产品“感觉”，由 AI 生成底层代码。

  - **推荐学习:** 1. [[Easy-Vibe 中文指南](https://datawhalechina.github.io/easy-vibe/zh-cn/)](https://datawhalechina.github.io/easy-vibe/zh-cn/)

    \2. [[OpenBuild Vibe Coding 课程](https://openbuild.xyz/learn/courses/1084822912)](https://openbuild.xyz/learn/courses/1084822912)

> **核心流程串联：**
>
> 调用 AI 时，先要知道服务地址，也就是 **Base URL**；再用 **API Key** 证明身份；后面真正负责思考的是 **Model**。如果你是在开发场景里，像 **Claude Code** 和 **Codex** 这种工具，会把模型变成更能干活的编程代理。整个过程的文本消耗通常按 **Token** 计量和计费。而 **Skills** 则像给 AI 的专项说明书，让它更懂某类任务怎么做。

------

## 什么是 OpenClaw？

如果普通 AI 聊天工具更像“打开网页和 AI 对话”，那 OpenClaw 更像是：**你自己搭一个 AI 总控台**，然后从手机、聊天软件、电脑上随时召唤它来做事。

它的核心不是“只是聊天”，而是专注于做：

- **会话管理**
- **渠道连接**
- **路由**
- **工具扩展**
- **智能体（Agent）接入**

------

## OpenClaw 安装步骤

**⚠️ 注意：** 国内服务器在拉取代码或镜像时可能会遇到网络连通性问题，建议提前配置好代理或镜像加速源。

### 1. 使用 NPM 安装

国内环境推荐安装（快速，方便），在一台服务器或者不用的电脑上

可以参考，很完整

[[OpenClaw 最新保姆级飞书对接指南教程 搭建属于你的 AI 助手 - JaguarJack - 博客园](https://www.cnblogs.com/catchadmin/p/19592309)](https://www.cnblogs.com/catchadmin/p/19592309)



### 2. 使用 Docker 安装

- 需要docker基础环境
- 需要docker加速源
- 优势 安全，隔离，方便清理

推荐

https://linux.do/t/topic/1636463