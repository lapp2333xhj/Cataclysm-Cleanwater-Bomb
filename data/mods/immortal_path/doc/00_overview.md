# 00 总览 / Overview

## 一句话定位 / Pitch

> 在新英格兰的灾变废土上，灵气随末日一同苏醒。你既要躲避丧尸与饥寒，也要引气入体、踏上一条在末世求长生的仙路。

A total conversion where the apocalypse and the return of spiritual energy (qi) are one and the same event. Survive the wasteland *and* cultivate toward immortality.

## 核心设计支柱 / Design Pillars

1. **同源灾变 / One Cataclysm, Two Faces**
   不另起炉灶。灾变（异次元入侵 + Blob）就是「灵气复苏」的现代科学描述。丧尸、传送门、变异，都被重新解释为灵气紊乱的产物。玩家不抛弃原版生存压力，而是在其之上叠加修仙维度。

2. **修炼即生存的第二条命脉 / Cultivation as a Second Lifeline**
   原版的「吃饱穿暖、造车造枪」依然成立；修炼是平行的成长线，前期弱小、回报缓慢，中后期带来质变（御物、辟谷、神识、法术）。两条线互相供养：修炼帮你活下去，废土资源反哺修炼。

3. **境界带来玩法质变，而非单纯数值 / Realms Change How You Play**
   每突破一个境界，解锁的是新机制（如筑基辟谷免食、金丹御剑远程、元婴神识扫描），而不只是 +X 属性。让「升境」有里程碑感。

4. **低魔起步、慢热 / Low-Magic Start, Slow Burn**
   开局你只是个能勉强感应到灵气的凡人。前期修仙收益克制，避免碾压原版生存体验；把「飞天遁地」放到后期作为长线目标。

## 玩法循环 / Core Loop

```
感应灵气 → 引气入体（攒灵气）→ 修炼功法（消耗灵气换境界进度）
   ↑                                          ↓
废土探索 ←── 灵材/丹药/功法书（探索奖励）── 突破瓶颈 → 解锁新境界机制
   ↑                                          ↓
生存压力（食物/危险/装备）←──────── 新机制反哺生存（辟谷/御物/神识/法术）
```

## 与原版的关系 / Relationship to Vanilla

- **依赖 dda**，作为 total_conversion 加载。
- 复用而非重写底层系统：
  - 灵气 → **独立自定义 vitamin 能量池**（不复用 int 绑定的原版 mana）；神通用 dda 的 `SPELL`，能量源指向灵气 vitamin。
  - 境界/体质变化 → 用 `mutation` + `mutation_category` 表达（详见 [09_integration](09_integration.md)，待写）。
  - 修炼进度 → **每个大境界一个 spell，用其 level 表征小层**（见 [03_qi_system](03_qi_system.md)）。
- 设计阶段尽量**先用原版机制拼装**，只有原版表达不了时才考虑 C++ 改动。

## 开发阶段路线（粗略）/ Rough Roadmap

- **M0 文档与设定**（当前）：lore、境界、基础系统、术语表。
- **M1 可玩骨架**：炼气境 + 引气入体（攒灵气资源）+ 1 部基础功法 + 几样灵材，能在游戏里跑通「感应→攒气→突破炼气小层」。
- **M2 第二境界**：筑基 + 辟谷机制 + 第一个法术。
- **M3+**：金丹/元婴/化神逐境扩展，宗门 NPC，炼丹炼器。

> 路线只是方向，不是承诺；每个 M 完成后回看再调。
