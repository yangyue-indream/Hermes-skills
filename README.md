# Hermes Skills

> 三个 Claude Code Skill，从零开始搭建的 AI 能力栈。

## 这是什么

Claude Code 的 Skill 系统允许封装可复用的工作流。这个仓库包含三个从实战中蒸馏出来的 Skill：

| Skill | 一句话 | 来源 |
|-------|--------|------|
| **[douyin-distill](skills/douyin-distill/SKILL.md)** | 抖音博主视频内容全量蒸馏 | AppleScript + Chrome JS 逆向工程 |
| **[webnovel-writer](skills/webnovel-writer/SKILL.md)** | 网文写作全流程：策划→写稿→审稿→去AI味 | 雷饼干 37 个视频方法论 + 柱子哥去味术 |
| **[ai-news-scout](skills/ai-news-scout/SKILL.md)** | AI 资讯多源扫描 + 五维打分 + 每日精选 | 柱子哥 + @数字生命卡兹克 信源体系 |

## 为什么开源

柱子哥和蜗牛学长教我们的：

> 开源是最好的名片。不是卖课，是让人看到你真的会。

## 快速开始

1. 克隆到 `~/.claude/skills/`：

```bash
git clone https://github.com/xiaokeai/hermes-skills.git
cp -r hermes-skills/skills/* ~/.claude/skills/
```

2. 之后在 Claude Code 中直接触发：

```
"帮我蒸馏这个抖音博主"
"帮我写一章网文"
"最近AI有什么值得看的"
```

## 蒸馏方法论

详见 [docs/distillation-methodology.md](docs/distillation-methodology.md) — 记录了从雷饼干、柱子哥、蜗牛学长三个抖音博主的视频中蒸馏知识的完整过程，以及过程中踩过的所有坑。

## 目录结构

```
hermes-skills/
├── README.md
├── skills/
│   ├── douyin-distill/       # 抖音蒸馏
│   │   ├── SKILL.md
│   │   └── references/
│   │       └── pitfalls.md   # 12种失败方法的踩坑记录
│   ├── webnovel-writer/      # 网文写作
│   │   ├── SKILL.md
│   │   └── references/
│   │       ├── leibinggan-methodology.md   # 雷饼干37视频方法论
│   │       ├── humanizer-checklist.md      # 去AI味铁律
│   │       └── momo-agent-prompt.md        # 墨墨Agent模板
│   └── ai-news-scout/        # AI资讯搜集
│       ├── SKILL.md
│       └── references/
│           ├── zhuzige-method.md            # 柱子哥信源方法论
│           └── content-automation.md        # 自媒体全链路参考
└── docs/
    └── distillation-methodology.md  # 蒸馏方法论总览
```

## 鸣谢

- 抖音博主 [雷饼干](https://www.douyin.com/user/MS4wLjABAAAA3rGmTLOeSqcaZet19wS8GzavYqU-TB4pWQ2ISsba9h6Om9jly5ItZTOSje2bUkm-) — 写小说赚钱方法论
- 抖音博主 [柱子哥](https://www.douyin.com/user/MS4wLjABAAAAjN32ZoC90W_FXxpeck2ATV5PCQcnnHM2cSzm8SHdcGCEC3P_fxGweCSTutk3Mvqq) — AI 工具生态 + 去 AI 味方法
- 抖音博主 [蜗牛学长](https://www.douyin.com/user/MS4wLjABAAAAJ6QEvp2qIMoHUYjghp3KROM1E0e7t9Wqx_bhTb8voqE) — 开源驱动个人 IP
- @数字生命卡兹克 — AI 信源搜集体系
