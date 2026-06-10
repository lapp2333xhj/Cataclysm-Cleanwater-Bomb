# 99 术语表 / Glossary

> **本表是全 mod 中英对照的唯一权威来源。**
> 所有游戏内 JSON 的 `//en_us_*` 注释（见 [LANGUAGE_CONVENTION](../LANGUAGE_CONVENTION.md)）从本表取英文译名，保持全 mod 一致。
> 新增术语先在此登记，再用于 JSON。

## 境界 / Realms

| 中文 | en_US | 备注 |
|------|-------|------|
| 修仙 / 修炼 | cultivation | 动词/名词通用 |
| 修士 / 修行者 | cultivator | |
| 境界 | realm | 大境界 |
| 小层 | minor stage | 大境界内分层 |
| 突破 | breakthrough | 升大境界事件 |
| 瓶颈 | bottleneck | |
| 炼气 | Qi Refining | 境界 1 |
| 筑基 | Foundation Establishment | 境界 2 |
| 金丹 | Golden Core | 境界 3 |
| 元婴 | Nascent Soul | 境界 4 |
| 化神 | Spirit Severing | 境界 5 |
| 飞升 | ascension | 远期叙事 |

## 灵气与修炼 / Qi & Cultivation

| 中文 | en_US | 备注 |
|------|-------|------|
| 灵气 | qi | 核心资源；独立 vitamin 能量池（非原版 mana） |
| 灵气复苏 | the Qi Awakening | 灾变=灵气复苏 |
| 末法之世 | the Age of Ending Law | 复苏前的灵气枯竭时代 |
| 引气入体 | drawing qi into the body | 入门动作 |
| 吐纳 / 打坐 | qi breathing / meditation | 吸纳环境灵气 |
| 灵根 | spirit root | 修炼天赋；幸存=有灵根 |
| 灵根品质 | spirit root grade | |
| 五行（金木水火土） | the Five Phases (metal, wood, water, fire, earth) | 五行属性 |
| 丹田 | dantian | 灵气储藏 |
| 神识 | spirit sense | 元婴境探测 |
| 辟谷 | inedia | 免食机制术语 |
| 走火入魔 | qi deviation | 突破/急修反噬（事件性） |
| 元婴出窍 | nascent soul projection | |

## 功法与神通 / Techniques & Arts

| 中文 | en_US | 备注 |
|------|-------|------|
| 功法 | cultivation technique | 总称 |
| 心法 / 主修功法 | core method | 被动主修 |
| 法术 / 神通 | art / spell | 主动能力；底层用 SPELL |
| 御物 | telekinesis | 隔空操物 |
| 御剑 | flying sword | 御剑飞行/攻击 |
| 残卷 / 玉简 / 传承 | scroll / jade slip / legacy | 功法来源 |

## 物品与生物 / Items & Creatures

| 中文 | en_US | 备注 |
|------|-------|------|
| 灵材 | spirit material | 修炼材料总称 |
| 妖丹 | demon core | 妖兽内丹 |
| 炼化（灵材） | refine (material) | 炼化灵材回灵气 |
| 灵植 | spirit plant | 被灵气催化的植物 |
| 妖兽 | demon beast | 被灵气催化的兽 |
| 丹药 | elixir / pill | 炼丹产物 |
| 炼丹 | alchemy | |
| 炼器 | artifact forging | 炼制法器 |
| 法器 / 法宝 | magic tool / treasure | 修士器物 |
| 洞府 | cave abode | 修士居所/据点 |

## 势力 / Factions（预留）

| 中文 | en_US | 备注 |
|------|-------|------|
| 宗门 | sect | |
| 隐世宗门 | hidden sect | 灾变前隐世者 |
| 邪修 | demonic cultivator | 行邪道、不择手段者 |
| 散修 | rogue cultivator | 无门无派，玩家默认 |

## 登记规范 / How to Add Terms

1. 新术语先加进对应分类表（中文 + en_US + 备注）。
2. JSON 里写 `"name": "<中文>"` + `"//en_us_name": "<en_US>"`，英文严格照本表。
3. 译名拿不准时先在备注标 `〔暂译〕`，待定稿；定稿后去掉标记。
4. 同一概念全 mod 只用一个译名，避免 Golden Core / Gold Pellet 混用。
