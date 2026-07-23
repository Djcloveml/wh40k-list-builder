# wh40k-list-builder (AI Agent Skill)

战锤 40k(11th Edition)竞技军表组装助手 — 一个遵循通用 SKILL.md 约定的 AI Agent 技能,**不限于任何特定工具**:Claude Code、Codex、Kimi Code、Cursor 或任何支持 SKILL.md 技能规范的 Agent 都可以加载使用。

自动拉取最新单位分数、研究近期比赛冠军表、结合你自己的棋子库存,头脑风暴出 2~3 套构筑方向并最终生成一张可直接上桌的军表。

A portable AI agent skill (standard SKILL.md format) that builds competitive Warhammer 40,000 (11th Edition) army lists. Works with any agent that supports the SKILL.md convention — Claude Code, Codex, Kimi Code, Cursor, etc. It pulls the latest points data, researches recent tournament-winning lists, and brainstorms 2–3 archetypes based on your actual miniature collection before finalizing a table-ready list.

## 功能 Features

- **分数数据自动更新**:数据源为 [BSData/wh40k-11e](https://github.com/BSData/wh40k-11e),本地缓存 + 每次启动比对 GitHub commit 日期,有更新才重新下载(走 jsDelivr CDN)。
  Auto-updating points data from BSData, cached locally; only re-downloads when upstream changes (via jsDelivr CDN).
- **补丁感知**:BSData 滞后于 GW 官方 MFM / Balance Dataslate 时,自动搜索最新补丁分析并手动套用分差,估算值会明确标注。
  Balance-dataslate aware: applies the latest MFM changes on top of cached data, with estimates clearly flagged.
- **社区情报**:从 Listhammer 看近期上位记录,从 Tabletop Battles (Competitive Innovations) 抓冠军军表原文作为构筑参考。
  Researches current meta via Listhammer standings and full tournament-winning lists from Tabletop Battles.
- **库存记忆**:你的棋子库存持久化在本地,跨会话记忆,只需报备新购入的模型。
  Your miniature collection persists across sessions.
- **头脑风暴成表**:不直接甩表——先给 2~3 个构筑方向(定位 / 用哪些库存 / 缺什么件 / 强度对比),你选定后再细化成完整军表,输出 Markdown 文件。
  Brainstorms 2–3 list directions first, then writes a complete Markdown army list with points sources, combos, and game plan.

## 安装 Install

把本仓库的 `SKILL.md` 放进你所用 Agent 的技能目录,例如:

```bash
git clone https://github.com/Djcloveml/wh40k-list-builder.git

# Claude Code
cp -r wh40k-list-builder ~/.claude/skills/

# Kimi Code
cp -r wh40k-list-builder ~/.kimi-code/skills/

# 其他 Agent:按其技能目录约定放置即可(部分工具支持项目级 .agents/skills/)
```

首次使用时会自动下载分数数据到技能目录下的 `data/`(约 7 MB)。之后每次启动只检查更新,不重复下载。

## 使用 Usage

装上之后对 Agent 直接说,例如:

- `帮我组个 40k 的表`
- `按我的库存组一张 2000 分黑暗天使的表`
- `最近黑暗天使比赛什么构筑最强?`

## 目录结构 Structure

```
wh40k-list-builder/
├── SKILL.md            # 技能定义(触发条件 + 工作流程)
└── data/               # 运行时自动生成(已在 .gitignore)
    ├── meta.json       # 缓存数据的 commit 日期
    ├── inventory.md    # 你的棋子库存
    └── *.json          # BSData 分数数据缓存
```

## 数据与免责 Disclaimer

- 分数数据来源:[BSData/wh40k-11e](https://github.com/BSData/wh40k-11e)(感谢社区维护);比赛情报来自 [Tabletop Battles](https://www.tabletopbattles.com) 与 [Listhammer](https://listhammer.info)。
- 上桌前请用官方 Warhammer 40,000 App 复核分数与规则。
- 本项目与 Games Workshop 无关,Warhammer 40,000 为 Games Workshop 注册商标。非官方粉丝工具,仅用于个人组表参考。

## Keywords

agent-skill, skill-md, ai-agent, claude-code, codex, cursor, llm-agent, warhammer-40k, wh40k, 40k, 11th-edition, army-list, list-builder, dark-angels, space-marines, bsdata, munitorum-field-manual, balance-dataslate, tabletop, 战锤40k, 军表, 黑暗天使
