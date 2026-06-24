---
name: douyin-distill
description: 抖音博主视频内容蒸馏——提取博主全部视频的标题、数据、AI章节摘要。当用户说要"蒸馏"某个抖音博主、"提取"抖音视频内容、分析抖音博主的内容策略、或者想看某个抖音号都发了什么视频时使用。支持单个视频链接和博主主页链接。
---

# 抖音视频蒸馏

## 核心原理

抖音是 JS SPA + X-Bogus 签名 + HttpOnly cookie，常规手段全部失效。唯一可靠路线：**用户 Chrome 的实时登录态 + AppleScript 执行 JS**。

## 前提条件

1. 用户 Chrome 已登录 douyin.com（手机扫码）
2. Chrome 开启：**View → Developer → ✅ Allow JavaScript from Apple Events**

> 如果用户尚未开启此选项，引导用户操作：
> Chrome 菜单栏 → 显示 → 开发者 → 勾选「允许 Apple 事件中的 JavaScript」

## 工作流程

### 第一步：获取博主 sec_uid

如果用户给的是单个视频链接（`v.douyin.com/...`），先通过 AppleScript 导航到该视频页面，从 API 响应中提取 `author.sec_uid`。

如果用户给的是博主主页链接（`douyin.com/user/...`），直接从中提取 sec_uid。

### 第二步：拉取全部视频列表

用 AppleScript 在用户 Chrome 中执行同步 XHR，调用 post API 翻页：

```bash
osascript << 'OSA'
tell application "Google Chrome"
    set URL of active tab of window 1 to "https://www.douyin.com/user/SEC_UID"
    delay 2
    set js to "
(function() {
    var xhr = new XMLHttpRequest();
    xhr.open('GET', 'https://www.douyin.com/aweme/v1/web/aweme/post/?device_platform=webapp&aid=6383&channel=channel_pc_web&sec_user_id=SEC_UID&max_cursor=CURSOR&count=20', false);
    xhr.withCredentials = true;
    xhr.send();
    return xhr.responseText;
})();"
    set result to execute active tab of window 1 javascript js
    return result
end tell
OSA
```

**API 端点**: `/aweme/v1/web/aweme/post/`
**关键参数**: `sec_user_id`, `max_cursor`, `count=20`
**分页逻辑**: `has_more: 1` 表示有下一页，用 `max_cursor` 作为下一页的 cursor 值

### 第三步：提取视频元数据

从 API 响应的 `aweme_list` 数组中提取：
- `aweme_id`, `desc`, `create_time`, `duration`
- `statistics` (digg_count, comment_count, collect_count, share_count)
- `is_top` (是否置顶)
- `author.sec_uid` (用于后续深度提取)

### 第四步（可选）：获取 AI 章节摘要

合集视频（`mix_info.mix_id` 存在）可通过合集 API 获取章节数据：
- **合集列表**: `/aweme/v1/web/mix/aweme/list/?mix_id=XXX`

单个视频的 AI 章节摘要在视频详情页的 `aweme_detail.recommend_chapter_info` 中。此 API 用 AppleScript XHR 可能返回空，可用 Playwright + profile 复制方案补充（参考 pitfalls.md）。

### 第五步：数据整理

```
作者信息 + 视频总数 + 每个视频的：
- 日期 | 时长 | 标题
- 点赞/评论/收藏/分享
- AI章节摘要（如有）
- 话题标签（如有）
```

输出为 JSON 文件 + 可读的摘要报告。

## 常见问题

**Q: AppleScript 返回空？**
检查 Chrome 是否已登录 douyin.com，以及"允许 Apple 事件中的 JavaScript"是否开启。

**Q: API 返回空？**
可能是 cookie 过期。让用户刷新 douyin.com 页面确认登录状态，然后重试。

**Q: 博主视频超过 50 个？**
翻页逻辑会自动处理，`has_more=0` 时停止。

**Q: 需要视频字幕/语音转文字？**
抖音视频通常没有公开字幕。如需字幕，建议引导用户提供手动转录或使用 ASR 工具处理下载的视频文件。

## 参考

- [踩坑记录](references/pitfalls.md) — 哪些方法不 work 以及为什么
