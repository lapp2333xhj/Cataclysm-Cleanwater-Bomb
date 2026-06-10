# 03 基础系统 / Core Systems

> 把 lore 与境界落成可实现的机制。本文聚焦四块基础：**灵气、灵根、修炼/吐纳、功法**。
> 实现选型标注「**定**」者为已定稿决策；「**倾向**」者为当前偏好，M1 落地时确认。

## 一、灵气 / Qi（独立能量体系）

修炼的核心资源，玩家的「第二能量条」。

> **决策（定）**：灵气**不复用原版 mana**。
> 原因：原版法力上限公式 `(0.2 + int*0.1) * mana_base`（见 `src/magic.cpp:2552`）把法力与**智力深度绑定**，这既不符合「修仙靠灵根与修为、而非聪明」的设定，也会让修仙挤占法师向的属性加点。
> **方案：灵气是一种独立的自定义能量池**，用 dda 的 `vitamin`（`vit_type: "counter"`）实现，与智力解耦。

### 实现方式 / Implementation
- **灵气 = 一个自定义 vitamin**（如 `id: qi`，`vit_type: "counter"`，`min: 0`，`max` 随境界提升）。
  - 参考成熟案例：`data/mods/Xedra_Evolved/vitamin.json`（underhill_essence、ruach 等都是用 counter vitamin 当独立资源）。
- **法术以灵气为能量源**：dda 的 `SPELL` 原生支持
  ```jsonc
  "energy_source": { "type": "vitamin", "vitamin": "qi" }
  ```
  （见 `src/magic.cpp:450-455`，spell 可指定 vitamin 作为消耗源）。所有神通的施放消耗都从灵气 vitamin 扣。
- **灵气上限随境界提升**：通过境界 mutation 的 enchantment 抬高 vitamin `max`，或直接用不同 max 的设计〔M1 确认抬上限的具体手段〕。
- **灵气回复（吐纳）**：vitamin 的 `rate` 提供基础被动回复；主动「打坐吐纳」通过 activity + EOC 额外补充（见下文「修炼」）。

### 灵气的获取 / Sources of Qi
1. **吐纳打坐**：主动活动，吸纳环境灵气补充自身灵气（慢、安全、依赖环境浓度）。
2. **炼化灵材**：妖丹、灵植等灵材，吃下/炼化后回灵气（快、需资源）。
3. **丹药**：炼丹产物，瞬时回气或永久增益（见 05，待写）。

> **注**：原「浊气/业障」机制已**删除**（见本文末「已删除机制」）。灵气是纯粹的正向资源，不再有污染累积。

## 二、灵根 / Spirit Root（天赋 + 存活根基）

> 灵根是 lore 与机制的交汇点：它既解释**玩家为何在灾变中没尸变**（见 [01_lore](01_lore.md) 第三节），也是修炼资质的载体。

- **人人皆有灵根（幸存者前提）**：能保持神智活下来 = 拥有灵根。绝大多数人灵根驳杂微弱，懵懂不知。
- **灵根品质**：决定吐纳效率、灵气上限、突破成功率〔待平衡〕。玩家是灵根相对清晰的少数。
- **灵根属性（五行倾向，可选）**：金木水火土倾向，影响适合的功法流派。
  - **倾向**：先做「品质」单维度，五行属性留到功法流派分化时（金丹后）再引入，避免前期过载。
- **实现倾向**：用 dda **trait/mutation**（开局特性）表达灵根品质，或用场景（profession/scenario）绑定开局资质。

## 三、修炼 / Cultivation（进度推进）

把灵气与时间转化为境界进度的过程。

### 境界进度载体（定）/ Realm Progression Carrier — DECIDED
> **决策（定）**：**用 spell level 跟踪修为，每个大境界 = 一个 spell。**

- 五个大境界各对应**一个 spell**（炼气、筑基、金丹、元婴、化神），spell 的 **level** 即该境界内的**小层进度**。
  - 例：「炼气」spell level 0~9 表示炼气一层~九层（或 level 1~9，M1 定零基）。
  - spell 升级所需经验 = 修炼投入（吐纳、炼化、时间），通过给该 spell 加经验来推进。
- **大境界突破** = 当前境界 spell 练满后，满足条件则授予下一境界的 spell（并打上对应境界 mutation 标记）。
  - 突破是带风险的 EOC 事件（消耗资源、可能失败/反噬）。
- **境界解锁的能力挂靠**：
  - 被动（辟谷、强身、抗性、灵气上限）→ 境界 mutation 的 enchantment。
  - 主动神通 → 独立的施放型 spell，以境界 spell level 或 mutation 为**学习/可用前提**。
- **为何用 spell level**：dda 原生支持 spell 经验、等级、等级门槛与 UI 展示，无需自定义计数器或 C++ 改动，最省力且可读性好。

> 这几个「境界 spell」本身可设为**被动/不可主动施放**（fake_spell 或无 effect 的占位 spell），只借用其 level 作进度容器。M1 验证这种用法的可行性与 UI 表现。

### 吐纳打坐 / Meditation
- 一个可主动发起的活动（接驳 dda activity），消耗时间、吸纳环境灵气：
  - 补充灵气 vitamin。
  - 给当前境界 spell 加经验，推进小层。
- 环境灵气越浓越快；安全但慢。

## 四、功法 / Cultivation Techniques（方法）

玩家「怎么修」「能放什么术」的载体。

- **心法 / 主修功法**：决定吐纳效率、灵气性质、可达上限。一般主修一部。
  - **倾向**：心法体现为被动 enchantment（挂在境界 mutation 或一个「功法」trait 上）。
- **法术 / 神通**：主动能力（御物、御剑、神识、攻击法术等）。
  - **定**：用 dda **SPELL** 系统实现，`energy_source` 指向灵气 vitamin，按境界解锁（详见 `04_techniques`，待写）。
- **功法来源**：残卷、玉简、传承、宗门（探索奖励），对接物品/战利品/NPC。

## 五、系统接驳总览 / Integration Map（速查）

| 设计概念 | 实现 | 状态 | 备注 |
|----------|------|------|------|
| 自身灵气 | 自定义 vitamin (counter) | 定 | 独立能量池，不碰 int |
| 法术能量源 | spell `energy_source: vitamin=qi` | 定 | 原生支持 |
| 灵气上限随境界 | 境界 mutation enchantment 抬 vitamin max | 倾向 | M1 确认手段 |
| 灵根 | trait/mutation 或 scenario | 倾向 | 先做品质单维 |
| 境界标记 | 每境一个 mutation，互为前置 | 定 | 见 02 |
| 小层进度 | 境界 spell 的 level | 定 | 每大境界一个 spell |
| 吐纳打坐 | activity + EOC（回气 + 加 spell 经验） | 倾向 | 安全慢，环境相关 |
| 突破 | EOC 事件 | 倾向 | 带风险，可失败 |
| 心法（被动） | enchantment | 倾向 | 挂在 mutation/trait |
| 神通（主动） | SPELL | 定 | 按境界解锁 |
| 灵材/丹药 | item + use_action/EOC | 待写 | 见 05/06 |

> 完整接驳细节与字段级方案放 `09_integration.md`（待写）。本表只做索引。

## 已删除机制 / Removed Mechanics

- **浊气 / 业障（taint）系统已删除**。
  - 原设想：灵气混浊、吸纳累积污染、需提纯。
  - 删除原因：(1) 持续追踪一条全局污染值有**性能开销**隐患；(2) 不利玩家体验——把修炼变成「边修边还债」的负担，与「灾变中求长生」的爽感相悖。
  - 影响：灵气改为**纯正向资源**；lore 中灵气不再「带域外恶意」，只是「浩瀚狂暴、凡躯难承」（解释尸变），但对有灵根者是可驾驭的恩赐。
  - 保留：「走火入魔」作为**急于求成/突破失败**的**事件性**风险仍可存在，但不依赖任何持续污染条——只在突破等节点临时判定。

## 待决问题汇总 / Open Questions

1. ~~灵气上限「随境界提升」的具体手段~~ **已查明（定）**：见下「灵气上限实现」。
2. 境界 spell 设为被动/占位 spell 的可行性与 UI 表现（M1 验证）。
3. 灵气环境浓度的数据来源（地形字段 vs 动态估算 vs 简化为定值起步）。
4. 灵根用 trait 还是 scenario 绑定开局资质。

## 灵气上限实现（定）/ Qi Max — Implementation DECIDED

> 调研结论（重要的引擎坑）：vitamin 的 `max` 字段是**静态值**，`Character::vitamin_mod()`（`src/consumption.cpp`）把累加值硬 clamp 到该静态 `max`，且**没有 enchantment 修正钩子**。
> 所以「直接用 enchantment 抬 vitamin max」**行不通**，改用「算出来 + EOC 钳制」的方案。

**灵气上限不是「存」出来的，而是「算」出来的**——由 jmath 函数 `immortal_path_qi_max()` 实时计算，两处共用：

1. **显示**：widget `var:custom` 的 `custom_var.range` 上限调 `immortal_path_qi_max()`，进度条按动态上限自动缩放。
2. **钳制**：一条 `recurrence` EOC 周期把 `u_vitamin('qi')` clamp 回 `[0, immortal_path_qi_max()]`。

vitamin 自身的静态 `max` 设一个远高于满级的安全兜底值，只防极端溢出。

### 可扩展性升级：自定义 enchantment 通道（定，2026-06-10）

> **问题**：若上限公式直接写 `100 + u_spell_level('realm_qi_refining')*20`，第三方 mod 想加「灵气上限 +50」只能**整个覆写** `immortal_path_qi_max()`——两个 mod 同时覆写就冲突，jmath 无法叠加多个来源。

**方案：在 C++ 层新增一个字符串键的通用 enchantment 通道**（fork 提交 `feat(magic): string-keyed enchantment values readable from math`）。仿引擎现有 `skill_values`（string 键 enchantment 先例）实现：

- `enchantment`/`enchant_cache` 加 `custom_values_add/multiply`（`std::map<std::string, ...>`），加载键名 `"custom"`，引擎自动累加所有来源。
- 新增 math 函数 `enchant_val('key')`（`src/math_parser_diag.cpp`，桥接 `enchant_cache::modify_value(string, 0.0)`）。
- 这些 map 是运行时重算的缓存（`// NOLINT(cata-serialize)`，跟 skill_values 一样不存盘），**无存档兼容问题**。

于是上限公式变成：
```
immortal_path_qi_max() = 100 + u_enchant_val('qi_max')
```
任何特性/物品/效果/第三方 mod 只要挂一条 `"custom": [{ "value": "qi_max", "add": ... }]` 的 enchantment，引擎自动累加进 `qi_max`，**无需覆写公式、零冲突**。这是 jmath 覆写给不了的可扩展性。同一通道可承载任意自定义修仙属性（`spirit_root_purity`、`pill_quality`…），一次 C++ 改动永久开放。

## M1 实现落地 / M1 Implementation — DONE

已实现并通过 JSON 语法校验的实际文件：

| 文件 | 作用 |
|------|------|
| `vitamins/qi.json` | `qi` vitamin（counter，静态 max=1000000 仅防溢出兜底，rate 0 无被动回复） |
| `jmath/qi_formulas.json` | `immortal_path_qi_max()` —— 灵气动态上限的**唯一数据源**（`100 + u_enchant_val('qi_max')`，各来源经 enchantment 自动累加） |
| `magic/realms.json` | `realm_qi_refining` 境界 spell：纯等级容器，`effect:none`、`energy_source:NONE`、`HIDDEN_SPELL`+`NO_FAIL`、max_level 13 |
| `mutations/cultivation_traits.json` | `immortal_path_qi_cultivator` 隐藏载体特性（携带 `qi_max` enchantment，按境界层数 ×20 抬上限）+ `immortal_path_qi_max_demo` 示范叠加特性（固定 +50，证明同一 `qi_max` 通道可叠加） |
| `effects_on_condition/qi_clamp.json` | `EOC_immortal_path_qi_clamp` RECURRING（30~90s），灵气超上限即钳回 |
| `magic/meditation.json` | `immortal_path_meditate` 可施放 spell（施法=打坐，约 30s）+ `EOC_immortal_path_meditate`：加炼气经验、补灵气并钳到上限 |
| `effects_on_condition/grant_meditation.json` | `game_avatar_new` EVENT EOC，新角色开局授予打坐能力 + `immortal_path_qi_cultivator` 特性（M1 测试用，后续应由灵根特性/传承授予） |
| `ui/sidebar.json` | 三种灵气 widget（境界文字 desc / 数值 num / 进度条 graph）+ 各自 layout 分区，并 extend 进全部 7 个原版侧边栏预设 |

**炼气经验曲线**：默认 spell 经验曲线是指数（到 1 级需 ~4881 exp），为战斗法术调的、过陡。改用自定义线性公式 `immortal_path_realm_exp_for_level`（每层 100 exp）+ 其反函数 `immortal_path_realm_get_level`（spell 用自定义曲线时两者必须成对、互为反函数）。一轮打坐给 25 exp，即 4 轮升一层。〔待平衡〕

**关键决策更新（取代上文「M2 再抽公共变量」）**：上限公式不重复——只写在 jmath `immortal_path_qi_max()` 一处。widget 的 `custom_var.range` 上限、clamp EOC 的 condition 与 effect 全部调用它。jmath 函数在 math 里直接按 id 调用（无 `u_` 前缀），如 `immortal_path_qi_max()`。

**待运行时验证**（无现成构建产物，本次仅做 JSON 语法校验）：
- 境界 spell 用 `effect:none` + `HIDDEN_SPELL` 作纯进度容器，能被 `u_spell_level` 读取且不出现在施法菜单。
- `var:custom` 的 widget 用 jmath 函数作 `range` 上限是否正常缩放（Xedra 用的是内联 math，本 mod 用 jmath 调用，需实测确认 range 接受函数调用）。
- RECURRING + `global:true` EOC 在新游戏自动注册并按 recurrence 触发。

### 校验结果（2026-06-10）/ Validation
用主仓库构建产物 `cataclysm-tiles.exe --check-mods immortal_path` 校验，**干净通过、零加载错误**（exit 1 且无错误输出 = 成功，与 no_hope/rural_biome 等已知良好 mod 表现一致；带数据错误的 mod 会 segfault 并打印错误）。

补充确认（反证测试）：把 `immortal_path_qi_max()` 临时改成不存在的函数名后，check-mods **报错** `unknown function`——证明该校验确实在加载期解析所有 math 表达式与 jmath/spell/vitamin 引用。故本 mod 通过即说明：
- `effect:none` + `shape:blast` + `HIDDEN_SPELL` 的境界 spell 定义合法（注：spell 的 `shape` 是必填字段，即使无实际效果也要给）。
- jmath `immortal_path_qi_max()`、其内部的 `u_spell_level('realm_qi_refining')`、widget 与 EOC 的 `u_vitamin('qi')` 引用全部解析通过。
- RECURRING EOC、vitamin、widget extend `custom_sidebar` 均加载无误。

> 仍未做的是**进游戏实跑**（攒气→升层→进度条缩放的动态行为）；加载期校验只保证数据正确、引用有效。M1 加入「吐纳打坐」EOC 后可在游戏内实测动态闭环。

### 补充校验（吐纳打坐 + 测试框架，2026-06-10）
加入打坐后，又用两种方式校验：
1. `--check-mods immortal_path`：仍干净通过。
2. 测试二进制 `Cataclysm-test ... --mods immortal_path "[math_parser]"`：mod 加载**零错误零 debugmsg**（「灾变仙路 pack load time: 6685us」），434 条断言全过。日志里的 init 错误（PLAYER_MAX_ATTR_VALUE modinfo、borax_box 容器、弹夹容器等）经基线对照确认**与本 mod 无关，主仓库本来就报**。

**静态校验的已知盲区（诚实记录）**：spell 的 `effect_str` → EOC 链接是**施放时**解析的，加载期不校验。反证：把 `immortal_path_meditate` 的 `effect_str` 改成不存在的 EOC，check-mods **不报错**。对比之下，math 表达式里的 jmath/spell/vitamin 引用是加载期解析的、能被抓到。

**因此「打坐施放→真正触发 EOC→加经验/补气→升层→上限增长→进度条缩放」这条动态链，目前仍未经运行时验证**，只能进游戏实跑确认。已验证的是：所有数据合法加载、所有 math/jmath 引用有效、spell 与 EOC 定义本身结构正确。

### 侧边栏注册（参照 Xedra，2026-06-10）
仿 Xedra_Evolved（`ui/sidebar.json` + `ui/ruach_counter.json`）的多 widget + 多预设注册套路：
- 三种 widget：`immortal_path_qi_desc`（境界文字，clauses+math 显示「未入门 / 炼气 N 层」）、`immortal_path_qi_num`（数值，custom number）、`immortal_path_qi_graph`（进度条，custom graph 按动态上限缩放）。各包一层 `_layout`。
- **extend 进全部 7 个原版侧边栏预设**（custom_sidebar、legacy_classic/compact/labels/labels_narrow、my_labels、my_labels_cleaner），这样玩家切到任何侧边栏布局都能看到灵气分区。注意：Xedra 还 extend 了 `structured_sidebar`/`spacebar` 等，那是 Xedra 自建的、原版没有，本 mod 不能 extend 否则报错。
- 境界文字 widget 用 `<u_val:qi_refining_layer>` tag 显示层数；该 u_val 由 meditate EOC 每轮 `u_qi_refining_layer = max(0, u_spell_level('realm_qi_refining'))` 更新。「未入门」分支不引用 tag，所以 tag 总在被显示前已赋值。
- 校验：`--check-mods` 干净通过；测试框架 `[widget]` 1108 断言全过、mod 加载零错误。
