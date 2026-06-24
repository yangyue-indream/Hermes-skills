# 蒸馏方法论

> 如何从抖音博主的视频中系统性地提取知识并转化为 Claude Code Skills

## 总览

"蒸馏"指通过技术手段提取博主全部视频内容，分析其知识体系，转化为可复用的工具或方法论。

本次蒸馏了三位博主：

| 博主 | 视频数 | AI相关 | 产出 |
|------|--------|--------|------|
| 雷饼干 | 37 | 全部写作相关 | webnovel-writer + 写小说方法论 |
| 柱子哥 | 60 | 34 | ai-news-scout + 去AI味方法 + 变现参考 |
| 蜗牛学长 | 40 | 29 | 内容公式 + 开源策略 |

## 技术方案

详见 `skills/douyin-distill/references/pitfalls.md`。

核心路径：**AppleScript + Chrome 实时登录态 + XHR API**

抖音是封闭平台（JS SPA + X-Bogus签名 + HttpOnly cookie），常规抓取手段全部无效。唯一可靠方案是利用用户自己 Chrome 的已登录状态，通过 AppleScript 在页面上下文中执行同步 XHR 请求。

### 关键 API

| API | 用途 |
|-----|------|
| `/aweme/v1/web/aweme/post/` | 获取博主作品列表（分页） |
| `/aweme/v1/web/aweme/detail/` | 获取视频详情+AI章节摘要 |
| `/aweme/v1/web/mix/aweme/list/` | 获取合集视频列表 |
| `/aweme/v1/web/discover/search/` | 搜索用户/视频 |

### 从视频到 Skill 的方法

1. **全量提取** → 拉取全部视频标题、数据、AI章节摘要
2. **筛选过滤** → 按关键词筛出目标领域内容
3. **分类归纳** → 按主题分组，识别内容体系
4. **深挖核心** → 对最有价值的视频提取详细章节
5. **交叉验证** → 对比不同博主的同类内容，取交集
6. **封装为 Skill** → 方法论→SKILL.md + references

## 踩过的坑

12 种不可行方案记录在 `skills/douyin-distill/references/pitfalls.md`，包括：
- WebFetch/curl（JS 渲染问题）
- yt-dlp/f2（Keychain 加密）
- Chrome profile 复制（Cookie 加密）
- 第三方服务（不稳定）
- Python 手动解密（参数不确定）
