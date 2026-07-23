---
name: wh40k-list-builder
description: 战锤40k(11版)竞技军表组装助手。当用户想组军表、查单位分数、参考比赛冠军表、或按自己棋子库存配表时使用。自动从 GitHub(BSData/wh40k-11e)获取最新分数数据并缓存到本地,再从 Listhammer/Tabletop Battles 等社区研究当前版本最强军表,结合用户库存头脑风暴出 2-3 套方案并最终成表。
---

# 战锤40k 军表组装助手

本技能遵循通用的 SKILL.md 约定,任何支持该约定的 AI Agent(Claude Code、Codex、Kimi Code、Cursor 等)都可加载使用。

按以下流程执行,不要跳步。所有路径中的 `SKILL_DIR` 指本 SKILL.md 所在目录。

## 第 0 步:本地数据缓存(每次必做)

数据目录:`SKILL_DIR/data/`(不存在则创建)。元数据文件:`SKILL_DIR/data/meta.json`,格式:

```json
{ "files": { "Imperium - Dark Angels.json": { "commitDate": "2026-07-19T01:50:51Z", "fetchedAt": "2026-07-23" } } }
```

对每个需要用到的阵营文件(如 `Imperium - Dark Angels.json`,基础星际战士单位还要 `Imperium - Space Marines.json`):

1. 读 `meta.json`。用 GitHub API 查该文件最新提交日期(只需 1 条):
   `curl -s "https://api.github.com/repos/BSData/wh40k-11e/commits?path=<URL编码的文件名>&per_page=1"`
2. **本地已有且 commitDate 一致 → 跳过下载**,直接使用本地文件。
3. 本地没有或有更新 → 下载,**优先走 jsDelivr CDN**(raw.githubusercontent.com 在部分网络环境下极慢/超时,不要直接用):
   `curl -sL --max-time 100 "https://cdn.jsdelivr.net/gh/BSData/wh40k-11e@master/<URL编码的文件名>" -o <文件名>`
   jsDelivr 失败再试 `https://mirror.ghproxy.com/https://raw.githubusercontent.com/BSData/wh40k-11e/master/<文件名>`。
4. 下载成功后更新 `meta.json` 的 commitDate 和 fetchedAt。

注意:BSData 更新可能滞后于 GW 官方 MFM/平衡补丁。如果近期(1-2 周内)有平衡性更新,用网络搜索确认补丁改动,在 BSData 分数基础上手动套用差值,并在最终军表中标注哪些是估算值。

## 第 1 步:读取用户棋子库存

读 `SKILL_DIR/data/inventory.md`(用户棋子库存,跨会话持久化)。

- 不存在或与用户本次描述不符 → 让用户报库存,写入/更新该文件。格式示例:`- Ravenwing Command Squad ×1`、`- Lion El'Jonson ×1`。
- 用户消息里已说明库存 → 以本次消息为准并同步更新该文件。

## 第 2 步:社区情报研究

1. **近期冠军表**:抓 `https://listhammer.info/?faction=<阵营名>`,看近 1-2 个月该阵营上位记录(哪些 Detachment 组合、Disposition 在赢)。
2. **完整军表原文**:在 tabletopbattles.com 搜 "Competitive Innovations" 最新几期,找到该阵营的上位表并抓全文(里面有精确到装备和分数的完整表)。BCP(bestcoastpairings)需要 JS 抓不了,不要浪费时间。
3. **补丁影响**:搜最新的 Balance Dataslate / MFM 分析文章,确认关键单位分数和规则改动。
4. 提炼:当前版本该阵营的强势构筑方向 1-3 个,各自的核心单位、combo、费用区间。

## 第 3 步:头脑风暴(必须和用户交互)

不要直接甩一张表。先给用户 2-3 个方向,例如:

- 每个方向一句话定位(如"纯鸦翼速攻" vs "死翼铁砧+鸦翼游击" vs "绿翼射击表")
- 各自利用了库存里哪些棋子、还缺哪些关键件(标出建议优先补的模型和大致价格)
- 强度和上手难度对比

等用户选定方向后再细化成完整 2000 分表(或用户指定的分数上限)。

## 第 4 步:成表与保存

最终军表写成 Markdown 保存到用户指定位置(默认桌面,文件名如 `dark-angels-2000pts.md`),内容包含:

- 分队(Detachment 组合)、军表气质(Disposition)
- 全部单位+分数+强化,合计总分(留出 5-50 分弹性)
- 每个关键选择的一句话理由(为什么是这套组合)
- 打法思路:核心 combo、关键计策、先后手注意事项
- 分数依据:哪些是已确认值、哪些是估算值,提醒上桌前用官方 40k App 复核
- 参考来源链接

## 注意事项

- 下载/解析 BSData 用命令行 + Python(注意 Windows 上 `python` 可能是商店占位符,要用 `py`);JSON 结构为 `catalogue.sharedSelectionEntries` 递归,单位分数在 `costs` 里 name 为 `pts` 的项;大模型数量分档的精确价格以比赛导出表为准,BSData 只含基础档时按比例估算并标注。
- 抓取网页正文优先用 Agent 自带的网页抓取工具,没有则用 curl;listhammer 赛果页只含排名不含表内容,完整表去 tabletopbattles。
- 当前是 11th Edition:组表时是「主分队 + 若干 1DP 附加分队」结构,Disposition(气质)也是表的一部分,别漏。
