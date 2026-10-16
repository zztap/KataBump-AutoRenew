# KataBump Auto Renew 🔄

KataBump 免费服务器自动续期，基于 **Python + undetected-chromedriver + Selenium**（Xvfb 有头模式）绕过 Cloudflare Turnstile。

> 每 4 天需续期一次，过期 1 天即永久删除！

## ✨ 特性

- 🛡️ **uc + Selenium**：`undetected-chromedriver` + ActionChains 偏移点击 Turnstile
- ☑️ **Altcha**：续期弹窗内勾选 Altcha checkbox 再提交
- 🔄 **每账号 3 次重试**：失败自动重建浏览器重试
- 👥 **多账号**：`ACCOUNTS` 支持 JSON 或 `user:pass,user:pass`
- 📡 **代理支持**：`HTTP_PROXY`（Chrome `--proxy-server`）
- 📱 **TG 通知**：成功/失败/跳过均推送（失败可带截图）
- ⏱️ **防休眠**：自动提交 `time.txt`（冲突时重试，不拖垮续期结果）

## 🚀 配置

### 1. Fork 本仓库

### 2. 配置 Secrets（Settings → Secrets and variables → Actions）

**必需：**

| Secret | 说明 | 格式 |
|--------|------|------|
| `ACCOUNTS` | 账号列表 | JSON：`[{"username":"邮箱","password":"密码"}]`；或 `邮箱:密码,邮箱:密码` |

JSON 多账号示例：
```json
[{"username":"a@example.com","password":"p1"},{"username":"b@example.com","password":"p2"}]
```

简写示例：
```
a@example.com:p1,b@example.com:p2
```

> 兼容说明：脚本也认环境变量 / Secret 别名 `USERS_JSON`（与 `ACCOUNTS` 同格式）。Workflow 注入的是 `ACCOUNTS`，建议以它为准。

**可选：**

| Secret | 说明 | 格式 |
|--------|------|------|
| `TG_BOT_TOKEN` | Telegram Bot Token | |
| `TG_CHAT_ID` | Telegram Chat ID | |
| `HTTP_PROXY` | HTTP/SOCKS 代理（传给 Chrome） | `http://host:port` 或 `socks5://host:port` |

### 3. 设置权限

Settings → Actions → General → Workflow permissions → ✅ **Read and write permissions**（写 `time.txt` 心跳需要）

### 4. 启用 Workflow

Actions 页面 → 选中 **KataBump Auto Renew** → Enable workflow → 可手动 **Run workflow** 测试

> 定时：每天两次（UTC 22:37 / 14:37 ≈ 北京 06:37 / 22:37）。脚本仅在剩余 ≤1 天才真正续期。

## 🔧 续期流程

1. 打开 `dashboard.katabump.com` 登录页
2. 填写邮箱密码
3. ActionChains 偏移点击 Cloudflare Turnstile
4. 点击 **See** 进入服务器页 → 检查到期日（剩余 >1 天则跳过）
5. 点击 **Renew** → 弹窗内勾选 **Altcha** → 再点 Renew 提交
6. 核对到期日是否更新；失败截图上传 Artifact，并尽量 TG 通知

## 📁 结构

```
├── .github/workflows/kata-renew.yml   # 定时 / 手动续期
├── renew_katabump.py                  # 主脚本
├── requirements.txt                   # undetected-chromedriver / selenium / requests
├── time.txt                           # 自动更新（防休眠心跳）
└── README.md
```
