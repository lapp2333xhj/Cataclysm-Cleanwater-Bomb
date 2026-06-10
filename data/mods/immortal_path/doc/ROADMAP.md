# 路线图 / Roadmap

> 灾变仙路（`immortal_path`）开发计划与里程碑。本文是**活文档**，随进度更新；具体设计细节见各专题文档。
> This is a living document tracking development milestones for Path of Immortals.

## 当前状态 / Current Status

**M1 — 灵气基础系统（已实现，待运行时打磨）/ Qi core system (implemented)**

- ✅ 灵气独立 vitamin（`qi`，counter），与 mana/INT 解耦
- ✅ 境界进度容器（`realm_qi_refining` spell，level = 炼气层数）
- ✅ 动态灵气上限：jmath `immortal_path_qi_max()` 单一数据源 + RECURRING clamp EOC
- ✅ 吐纳打坐（可施放 spell + EOC 加经验/补灵气）
- ✅ 灵气上限载体特性 `immortal_path_qi_cultivator`（携带 `qi_max` enchantment，随境界层数提升）
- ✅ 三层并行授予（新角色 / 学会境界 spell 事件 / RECURRING 兜底旧存档）
- ✅ 侧边栏 widget（境界文字 / 灵气数值 / 进度条）

> 依赖本 fork 的自定义 enchantment 通道（`enchant_val` math 函数）—— 见基础设施 PR。
> Depends on this fork's string-keyed enchantment channel (the `enchant_val` math function) — see the infrastructure PR.

## 开发计划 / Development Plan

按依赖顺序排列。每项落地后回填 ✅ 与对应专题文档链接。
Ordered by dependency. Check off and link the topic doc as each lands.

- [ ] **接入 i18n / Hook up i18n**
  在主仓库 i18n 流程落地后，按 [LANGUAGE_CONVENTION.md](../LANGUAGE_CONVENTION.md) 的迁移步骤（中文值↔英文注释互换 → 删注释 → 导出 `.po`）把全 mod 文本接入正式翻译流程。
  Once the repo's i18n pipeline lands, migrate all mod text into proper translation per LANGUAGE_CONVENTION.md.

- [ ] **属性系统 / Attribute system**
  基于自定义 enchantment 通道（基础设施 PR）搭建修仙属性体系（如灵根资质、神识、根骨等），各属性走 `enchant_val('key')`，可被特性/法宝/丹药/境界多来源叠加。
  Build cultivation attributes on top of the custom enchantment channel; each attribute reads via `enchant_val('key')` and stacks across sources.

- [ ] **功法框架与修炼框架 / Technique & cultivation framework**
  基于 spell 与 action 搭建功法（被动/主动技艺）框架，以及修炼推进框架（吐纳、顿悟、突破等节点）。见 `04_techniques.md`（待写）。
  Build the technique framework (passive/active arts) on spells + actions, plus the cultivation-progression framework (meditation, insight, breakthrough). See `04_techniques.md` (TBD).

- [ ] **法术框架与基础法术 / Spell framework & basic spells**
  使用自定义能量来源（灵气 vitamin）的法术框架，并实现一批基础神通。
  A spell framework powered by the custom energy source (qi vitamin), plus a set of basic spells.

- [ ] **基础道具：丹药 / 符箓 / 法器 / Basic items: pills, talismans, artifacts**
  炼丹、符箓、法器等修仙道具的首批内容与制作。见 `05_alchemy.md`（待写）。
  First content + crafting for alchemy pills, talismans, and artifacts. See `05_alchemy.md` (TBD).

- [ ] **基础妖兽与敌人 / Basic spirit beasts & enemies**
  修仙世界的妖兽、敌对修士等。见 `06_spirit_beasts.md`（待写）。
  Spirit beasts, hostile cultivators, and other foes. See `06_spirit_beasts.md` (TBD).

## 里程碑 / Milestones

| 里程碑 | 目标 | 状态 |
|--------|------|------|
| M1 | 灵气基础系统跑通（上限/吐纳/境界容器） | 已实现，运行时打磨中 |
| M2 | 属性系统 + 功法/修炼框架 | 计划中 |
| M3 | 法术框架 + 基础法术 | 计划中 |
| M4 | 道具（丹药/符箓/法器） | 计划中 |
| M5 | 妖兽/敌人 + 早期完整循环 | 计划中 |

> 里程碑顺序可随设计调整；属性系统与功法框架是后续大量内容的共同地基，优先级最高。
> Milestone order may shift; the attribute system and technique framework are the shared foundation for most later content and take priority.
