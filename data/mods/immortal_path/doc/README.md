# 灾变仙路 设计文档 / Design Docs

> Path of Immortals (`immortal_path`) — total conversion mod for Cataclysm-DDA.
>
> 本目录是**开发设计文档**，面向开发者，正文用中文、结构标题用英文。
> 这些文档**不是游戏内文本**，不走 [LANGUAGE_CONVENTION](../LANGUAGE_CONVENTION.md) 的「中文值 + //en_us 注释」约定 —— 那只用于会进入游戏的 JSON 字符串。

## 文档结构 / Structure

| 文件 | 内容 | 状态 |
|------|------|------|
| [ROADMAP.md](ROADMAP.md) | 开发计划、里程碑与待办清单 | 活文档 |
| [00_overview.md](00_overview.md) | 总览：mod 一句话定位、设计支柱、玩法循环 | 草稿 |
| [01_lore.md](01_lore.md) | 世界观核心：灵气复苏与灾变同源 | 草稿 |
| [02_cultivation_realms.md](02_cultivation_realms.md) | 修炼境界体系：炼气→筑基→金丹→元婴→化神 | 草稿 |
| [03_qi_system.md](03_qi_system.md) | 灵气/灵根/功法等基础系统设定 | 草稿 |
| [99_glossary.md](99_glossary.md) | 术语表（中英对照，对接语言约定） | 草稿 |

## 约定 / Conventions

- **境界/术语的英文译名以 [99_glossary.md](99_glossary.md) 为准**，所有 JSON `//en_us_*` 注释从术语表取词，保持全 mod 一致。
- 设计文档里出现的具体数值（灵气上限、突破成功率等）都是**待平衡的占位值**，标记 `〔待平衡〕`。
- 文档编号留出间隔（00/01/02…99），方便后续插入（如 04_techniques、05_alchemy、06_factions 等）。

## 命名编号规划 / Planned Doc Numbers

后续按需新增，预留：

- `04_techniques.md` — 功法 / 法术系统（对接 dda 的 spell / SPELL）
- `05_alchemy.md` — 炼丹 / 炼器
- `06_spirit_beasts.md` — 妖兽 / 灵植
- `07_factions_npcs.md` — 宗门 / 势力 / NPC
- `08_progression_balance.md` — 成长曲线与平衡总表
- `09_integration.md` — 与 dda 原版系统（变异、CBM、技能）的接驳方式
