# 微信 ClawBot · 安卓本地 AI 机器人（永久记忆版）

> 无需电脑、无需服务器、无需云端 API，只要一台安卓手机，就能把你的本地大模型接入微信，并且拥有**永久记忆**。

本项目基于 [SiverKing/weixin-ClawBot-API](https://github.com/SiverKing/weixin-ClawBot-API) 二次开发，在原项目的基础上增加了 **SQLite 永久记忆** 功能，并针对 Termux 安卓环境进行了深度适配。

---

## ✨ 特性

- 📱 **纯安卓本地运行**：基于 Termux + ServLlama，不依赖电脑和服务器
- 🧠 **永久记忆**：对话历史保存在本地 SQLite 数据库，断线重连不丢失
- 💾 **隐私安全**：所有数据只存在你自己手机里，不上传任何云端
- 🤖 **兼容本地大模型**：支持任意 OpenAI 兼容接口的本地模型（如 Qwen、MiniCPM 等）
- 🎭 **人设稳定**：可通过系统提示词自定义 AI 人格（傲娇女仆、贴心助手等）
- 🔄 **自动重连**：支持 24 小时自动重连，无需手动扫码
- 🔒 **安全的配置管理**：DusAPI / DeepSeek provider 配置与 API Key 脱敏显示
- 📡 **底层协议完善**：固定入口二维码扫码、长轮询收消息、游标持久化、受控重新登录等

---

## 🛠️ 环境要求

- Android 8.0 及以上
- 建议 8GB 及以上运行内存
- 已安装 [Termux](https://f-droid.org/packages/com.termux/)（推荐 F-Droid 版本）
- 已安装 [ServLlama](https://github.com/ArkaneFans/Servllama)（本地大模型服务器）
- 已在 ServLlama 中加载任意 GGUF 模型

---

## 🚀 快速开始

### 1. 准备本地 AI 服务
在 ServLlama 中加载你喜欢的模型（推荐小尺寸模型，如 `neon-veil-v2-e2b-it.Q4_K_M`），确认运行正常，并记下 API 地址（通常是 `http://127.0.0.1:8080`）。

### 2. 下载并安装本项目

```bash
git clone https://github.com/appses-yes/wechat-clawbot-android.git
cd wechat-clawbot-android
pip install -r requirements.txt
python bot.py
```

首次运行会选择 AI provider，并填写 API Key、接口地址、模型和系统提示词。登录成功后会按账号保存连接状态，正常重启直接复用；服务端返回 -14 或手动执行重连时进入受控重新扫码。

首次登录或 token 失效后的登录步骤：

1. 选择并确认 AI 配置。
2. 使用手机微信扫描终端显示的二维码。
3. 如果手机要求数字配对码，在终端输入配对码。
4. 登录成功后，在微信中发送消息；首次交互会收到指令列表。

---

📂 文件结构

```text
.
├── bot.py              # Bot 主程序
├── dusapi.py           # DusAPI 兼容封装
├── deepseek.py         # DeepSeek 兼容封装
├── requirements.txt    # Python 依赖
├── config.json         # 首次运行自动生成，请勿提交
├── weixin_state.json   # 首次登录自动生成，包含敏感连接状态，请勿提交
├── local_memory.db     # 本项目新增：SQLite 永久记忆数据库（自动生成，请勿提交）
└── README.md
```

---

⚙️ 配置文件说明

config.json 支持多个 provider，旧版扁平配置会自动迁移：

```json
{
  "provider": "deepseek",
  "providers": {
    "dusapi": {
      "api_key": "your-dusapi-key",
      "base_url": "https://api.dusapi.com",
      "model": "gpt-5",
      "prompt": "你是一个有帮助的AI助手，请用中文简洁地回复。字数尽量少一些"
    },
    "deepseek": {
      "api_key": "your-deepseek-key",
      "base_url": "https://api.deepseek.com",
      "model": "deepseek-v4-flash",
      "prompt": "你是一个有帮助的AI助手，请用中文简洁地回复。字数尽量少一些"
    }
  }
}
```

Provider 配置文件 默认地址 默认模型
DusAPI dusapi.py https://api.dusapi.com gpt-5
DeepSeek deepseek.py https://api.deepseek.com deepseek-v4-flash

启动时 API Key 只显示首尾各 5 位。config.json 和运行状态文件含有敏感凭据，请妥善保管。

---

🤖 Bot 指令

指令 说明
/help 或 /指令 查看指令列表
/time 查询当前连接剩余时间
/重新连接 请求立即重连，随后回复 Y 或 N

非指令文字会转发给 AI。图片、文件和未提供文字转写的语音目前只会收到能力提示，不会被错误地送入 AI。

---

🔄 自动重连配置

bot.py 顶部的 RECONNECT_CONFIG 可调整项目侧的提醒策略：

参数 默认值 说明
session_duration 24 * 3600 项目侧连接计时窗口（秒）
warning_before 2 * 3600 提前提醒时间（秒）
reminder_interval 30 * 60 用户回复 N 后再次提醒间隔（秒）
force_before 30 * 60 剩余时间低于此值时强制重连（秒）
qrcode_scan_timeout 480 整体扫码等待上限（秒）

---

📡 OpenClaw Weixin 2.4.6 协议要点

本项目代码按 OpenClaw Weixin 2.4.6 的公开 HTTP 行为对齐。协议细节和版本差异记录在 weixin-openclaw-api-py-docs.md。

请求头与基础信息

登录后的 POST 请求使用以下头部；Content-Length 由 aiohttp 自动计算，不手动设置：

```text
Content-Type: application/json
AuthorizationType: ilink_bot_token
X-WECHAT-UIN: <随机 uint32 的十进制字符串再 base64>
iLink-App-Id: bot
iLink-App-ClientVersion: 132102
Authorization: Bearer <bot_token>
```

登录流程 & 消息流程

```text
POST /ilink/bot/get_bot_qrcode?bot_type=3
GET  /ilink/bot/get_qrcode_status?qrcode=...
  → wait / scaned / need_verifycode / scaned_but_redirect
  → binded_redirect（本地仍有 token 时复用）
  → confirmed（保存 bot_token、baseurl、账号 ID）

POST getupdates（携带上次成功的 get_updates_buf）
POST getconfig
POST sendtyping { status: 1 }
调用 AI
POST sendmessage（校验 ret）
POST sendtyping { status: 2 }（finally 中尽力执行）
```
---

📦 依赖

详见 requirements.txt：aiohttp、requests、qrcode[pil]；打包可选 pyinstaller。

---

🙏 致谢

本项目参考并借鉴了以下项目，在此表示衷心感谢：

· 感谢 SiverKing/weixin-ClawBot-API 提供的原始项目架构与代码基础
· 感谢 OpenClaw 项目及其官方文档，本项目最初基于其架构思路开发
· 感谢 DeepSeek 提供的 API 服务
· 感谢 腾讯微信openclaw-weixin 提供的接口能力
