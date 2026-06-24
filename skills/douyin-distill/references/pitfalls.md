# 抖音蒸馏踩坑记录

> 记录了在苹果芯片 Mac (macOS 14) + Python 3.9 环境下尝试过的所有不可用方案。

## ❌ 直接抓取类

| 方法 | 失败原因 |
|------|----------|
| WebFetch / curl 抓页面 | 抖音是 JS SPA，返回空壳（无实际内容） |
| `curl + 各种 UA` | 即使拿到 HTML，也只有基础 meta，无视频内容 |
| Douyin API 直调（无 cookie）| 需要 X-Bogus 签名（JS 动态生成，无法离线计算） |
| oEmbed 端点 | 无响应（抖音未开放） |
| Googlebot UA 伪装 | 同样被反爬拦截 |

## ❌ 下载工具类

| 工具 | 失败原因 |
|------|----------|
| yt-dlp | macOS 上 Chrome cookie 被 Keychain 加密，`--cookies-from-browser chrome` 无法解密 |
| yt-dlp + 手动 cookie 文件 | 需要浏览器"新鲜"cookie（非持久化），curl 抓的 cookie 不够 |
| f2 | Python 3.9 兼容性问题（asyncio 事件循环错误）；即使绕过，同样卡在 cookie 解密 |

## ❌ Cookie 提取类

| 方法 | 失败原因 |
|------|----------|
| `document.cookie` | 拿不到 HttpOnly 的 session cookie（最关键的那个） |
| Chrome profile 复制 | macOS Keychain 加密 cookie，复制 SQLite 数据库无效 |
| `browser_cookie3` | 无法获取 Keychain 解密密钥 |
| `pycookiecheat` | 同上，Keychain 权限被 TCC 拦截 |
| Python + PBKDF2 手动解密 | 即使拿到 Keychain 密钥（`security find-generic-password -wa Chrome`），解密参数不确定（salt/iterations/hash 组合全部失败） |
| AppleScript 读 cookie | Chrome 的 AppleScript cookie 接口返回空 |
| f2 `--auto-cookie` | 同 Keychain 权限问题 |

## ❌ 第三方服务

| 服务 | 失败原因 |
|------|----------|
| tikwm.com API | URL 解析失败 |
| douyin.wtf API | 服务挂了 (520) |

## ⚠️ 部分可用

| 方法 | 可用性 |
|------|--------|
| Playwright + Chrome profile 复制 | 能访问**公开**内容（视频详情页、合集 API），但 **post API 需要完整登录态**，profile 复制解决不了 |
| Playwright + 手动注入 cookie | `document.cookie` 拿到的非 HttpOnly cookie 不全，注入后仍无法通过 post API 认证 |

## ✅ 唯一完全可用

**AppleScript + 用户实时 Chrome 的 JS 执行**
- 利用用户 Chrome 本身已登录，session 完整
- 同步 XHR 在页面 context 中执行，自动带上所有 cookie（含 HttpOnly）
- 不需要提取/解密 cookie
- post API 和 detail API 均可访问

**代价**: 需要用户手动开启 Chrome 的 Apple Events 支持（一次性操作）
