# wechat-clawbot-android
基于 Termux 的安卓本地微信 AI 机器人，无需电脑，支持永久记忆、本地模型，可在 8GB 低配手机上运行

```text
基于 Termux 的安卓本地微信 AI 机器人，无需电脑、无需服务器，支持永久记忆、本地大模型，可在 8GB 低配手机上运行。
```

---

# 微信 ClawBot · 安卓本地 AI 机器人（永久记忆版）

> 无需电脑、无需服务器、无需云端 API，只要一台安卓手机，就能把你的本地大模型接入微信，并且拥有**永久记忆**。

本项目基于 [SiverKing/weixin-ClawBot-API](https://github.com/SiverKing/weixin-ClawBot-API) 二次开发，在原项目基础上增加了**SQLite 永久记忆**功能。

---

## ✨ 特性

- 📱 **纯安卓本地运行**：基于 Termux + ServLlama，不依赖电脑和服务器
- 🧠 **永久记忆**：对话历史保存在本地 SQLite 数据库，断线重连不丢失
- 💾 **隐私安全**：所有数据只存在你自己手机里，不上传任何云端
- 🤖 **兼容本地大模型**：支持任意 OpenAI 兼容接口的本地模型（如 Qwen、MiniCPM 等）
- 🎭 **人设稳定**：可通过系统提示词自定义 AI 人格（傲娇女仆、贴心助手等）
- 🔄 **自动重连**：支持 24 小时自动重连，无需手动扫码

---

## 🛠️ 环境要求

- Android 8.0 及以上
- 建议 8GB 及以上运行内存
- 已安装 [Termux](https://f-droid.org/packages/com.termux/)（推荐 F-Droid 版本）
- 已安装 [ServLlama](https://github.com/ArkaneFans/Servllama)（本地大模型服务器）
- 已在 ServLlama 中加载任意 GGUF 模型

---

## 🚀 安装步骤

### 1. 准备本地 AI 服务

在 ServLlama 中加载你喜欢的模型（推荐小尺寸模型，如 `neon-veil-v2-e2b-it.Q4_K_M`），确认运行正常，并记下 API 地址（通常是 `http://127.0.0.1:8080`）。

### 2. 下载并安装本项目

```bash
git clone https://github.com/【你的用户名】/【你的仓库名】.git
cd 【你的仓库名】
pip install -r requirements.txt
```

3. 配置 AI 接口

```bash
python bot.py
```

首次运行会引导你：

· 选择 AI 提供商（DusAPI / DeepSeek 均可）
· 填入 API 地址（本地 ServLlama 填 http://127.0.0.1:8080）
· 填入 模型名称（必须是 ServLlama 中加载的模型全名）
· 填入 系统提示词（自定义 AI 人设）

4. 扫码连接微信

配置完成后，终端会显示二维码，用手机微信扫码即可完成绑定。

---

🧠 永久记忆功能说明

本项目在原版基础上增加了 SQLite 数据库，用于持久化聊天记录：

· 每收到一条消息，会先去数据库里检索最近的相关记忆
· 将记忆拼接到当前对话的 prompt 中，再发给 AI
· AI 回复后，把本轮对话写回数据库

数据库文件为 local_memory.db，保存在项目目录下，请勿上传到公开仓库。

## 🙏 致谢

· 原作者：SiverKing
· 原项目：weixin-ClawBot-API
· 本项目在其基础上增加了永久记忆功能
