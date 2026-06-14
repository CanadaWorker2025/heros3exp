# LSF Update Log

## 2026-06-14

### 12. Courtyard `14Ekaterina` 祈祷特长与开场施法确认

涉及文件：

- [Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/heroes/Ekaterina.json](Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/heroes/Ekaterina.json)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/spellsNew.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/spellsNew.json>)

当前 `14Ekaterina` 配置确认如下：

- 初始技能为 `basic wisdom` 与 `basic logistics`
- 初始 `spellbook` 包含 `pacifism` 与 `prayer`
- `specialty.bonuses` 同时保留以下四项：
- `pacifism`：`type: SPELL`, `subtype: spell.pacifism`
- `prayer`：`type: SPECIAL_PECULIAR_ENCHANT`, `subtype: spell.prayer`
- `choir`：`type: OPENING_BATTLE_SPELL`, `subtype: spell.prayer`, `val: 10`
- `bind`：`type: SPELL`, `subtype: spell.prayer`, `val: 1`

本次确认结论：

- `Ekaterina` 现在已经可以在战斗开场自动释放 `prayer`
- 该 `prayer` 会按当前工程里与 `Loynis` 相同的 prayer-specialty 规则生效
- 不需要在 `spellHero.json` 再额外添加 `courtyard:14Ekaterina` 的 `specialty#override`
- 之前尝试添加 hero-level `specialty#override` 会覆盖原有 `specialty.bonuses`，反而导致 `choir` / `bind` / `pacifism` / `SPECIAL_PECULIAR_ENCHANT` 被冲掉

当前 prayer-specialty 的实际数值来源：

- 来源不是单独的 hero override，而是 `extra creatures specialty` 对 `core:prayer` 的 spell-level patch
- `spellsNew.json` 中 `core:prayer` 已按兵种等级分段写入固定加成
- 对应判定依赖 `HAS_ANOTHER_BONUS_LIMITER` + `SPECIFIC_SPELL_DAMAGE` + `spell.prayer`

当前 prayer-specialty 分段效果记录：

- `none/basic`
- `1-5级`：`attack/defence/speed +5`
- `6级`：`attack/defence/speed +4`
- `7级`：`attack/defence/speed +3`
- `advanced/expert`
- `1-5级`：`attack/defence/speed +7`
- `6级`：`attack/defence/speed +6`
- `7级`：`attack/defence/speed +5`

## 2026-06-13

### 11. 最近一次 push `f9592afc` 后续记录

涉及文件：

- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json](Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json)
- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft2_meri.json](Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft2_meri.json)
- [Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json](Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json)
- [Hero3Expasion1.83/Mods/preserve/mod.json](Hero3Expasion1.83/Mods/preserve/mod.json)
- [Hero3Expasion1.83/config/spells/timed.json](Hero3Expasion1.83/config/spells/timed.json)

本次 push 已提交的改动记录：

- `ft16_arina.json`：初始 `wisdom` 从 `basic` 改为 `expert`
- `ft2_meri.json`：新增初始 `spellbook` 条目 `townPortal` 与 `dimensionDoor`
- `preserve/mod.json`：新增依赖列表 `["tidesOfWar", "cathedral"]`
- `P_renata.json`：启用了额外的 `SPECIAL_UPGRADE` 映射：
- `tidesofwar:ffpaladin -> creature.dreadKnight`
- `cathedral:cthPaladin -> creature.dreadKnight`
- `cathedral:cthTemplar -> creature.dreadKnight`

本次 push 中 `timed.json` 的记录如下：

- `slayer.expert.range` 已从小写 `x` 修正为大写 `X`
- `antiMagic.expert.range` 当前仍为小写 `x`
- `magicMirror` 当前把 `expert` 写在了 `levels.base` 内部，而不是同级 level block

风险说明：

- `slayer` 这项修正与常规法术范围写法一致
- `antiMagic.expert.range: "x"` 如果引擎严格要求大写 `X`，则这里可能无效
- `magicMirror.levels` 当前结构看起来不合法，是较可能的法术配置问题来源
- 本条主要记录当时 push 的已提交状态，不代表已完成正确性验证

### 10. Asylum `K_viola` 深渊号角法术接入

涉及文件：

- [Hero3Expasion1.83/Mods/asylum/content/config/asylum/heroes/K_viola.json](Hero3Expasion1.83/Mods/asylum/content/config/asylum/heroes/K_viola.json)
- [Hero3Expasion1.83/Mods/asylum/mod.json](Hero3Expasion1.83/Mods/asylum/mod.json)
- [Hero3Expasion1.83/Mods/asylum/content/config/asylum/NewOldSpells/hornSpell.json](Hero3Expasion1.83/Mods/asylum/content/config/asylum/NewOldSpells/hornSpell.json)

当前 `k_viola` 配置记录：

- 保留原有 `hypnotize` 特长成长：
- `SPECIAL_SPELL_LEV`
- `subtype: spell.hypnotize`
- `updater: TIMES_HERO_LEVEL`
- `val: 5`
- 初始 `spellbook` 现包含：
- `hypnotize`
- `asylum:hornSpell`
- 初始 `wisdom` 改为 `expert`

当前 `asylum` 模组依赖与法术注册：

- 新增依赖：`hota.neutralCreatures`
- 新增本地法术注册：
- `config/asylum/NewOldSpells/hornSpell.json`

当前本地 `asylum:hornSpell` 配置：

- 将 HotA 的宝物附带法术改造成 `asylum` 本地普通战斗法术
- `type: combat`
- `targetType: CREATURE`
- `school: water`
- `level: 5`
- `levels.base.cost: 20`
- `levels.base.power: 40`
- `range: 0`
- 使用 `core:demonSummon`
- 召唤单位为 `hota.neutralcreatures:fangarm`
- `permanent: true`
- 目标限制排除了 `NON_LIVING`、`SIEGE_WEAPON`、`UNDEAD`、`GARGOYLE`、`MECHANICAL`

## 2026-06-12 03:15:00

### 9. Preserve `P_renata` 特长重写

涉及文件：

- [Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json](Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json)

当前 `p_renata` 特长配置：

- 保留初始 `spellbook` 条目 `mirth`
- `base.type: SPECIAL_UPGRADE`
- `base.addInfo: creature.dreadKnight`
- 当前启用的升级映射：
- `creature.champion -> creature.dreadKnight`
- `creature.cavalier -> creature.dreadKnight`
- 额外规划的 `ffpaladin`、`cthPaladin`、`cthTemplar` 映射已写入但仍处于注释状态
- 开场战斗施法：
- `type: OPENING_BATTLE_SPELL`
- `subtype: spell.mirth`
- `val: 10`
- 保留法术绑定：
- `type: SPELL`
- `subtype: spell.mirth`
- 黑骑士系特长加成修正为 `includeUpgrades: true`：
- `CREATURE_DAMAGE creatureDamageBoth +15`
- `PRIMARY_SKILL primarySkill.attack +15`
- `PRIMARY_SKILL primarySkill.defence +15`
- `STACKS_SPEED +2`

当前特长文本意图：

- 战斗开场自动释放 `mirth`
- 隐藏效果给予 `dreadKnight` 战斗加成
- 隐藏效果同时允许选定的骑士系兵种升级为 `dreadKnight`

## 2026-06-12

### 8. 最近一次 push 改动记录

涉及文件：

- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft12_seliva.json](Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft12_seliva.json)
- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json](Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json)
- [Hero3Expasion1.83/Mods/hota/Mods/spellResearch/mod.json](Hero3Expasion1.83/Mods/hota/Mods/spellResearch/mod.json)
- [Hero3Expasion1.83/config/difficulty.json](Hero3Expasion1.83/config/difficulty.json)
- [Hero3Expasion1.83/config/gameConfig.json](Hero3Expasion1.83/config/gameConfig.json)
- [Hero3Expasion1.83/config/spells/timed.json](Hero3Expasion1.83/config/spells/timed.json)

当前 `Seliva` 特长配置：

- 保留原有效果：所带兵种最小与最大伤害都获得 `+2`
- 通过 `SPECIAL_UPGRADE` 新增类似 Gelu 的隐藏升级效果
- 升级映射为：`creature.archer`、`creature.marksman`、`creature.woodElf`、`creature.grandElf` 全部可升级为 `creature.sharpshooter`
- 特长文本已补充该隐藏的神射手升级效果

当前 `Arina` 初始技能：

- `basic diplomacy`
- `basic wisdom`
- `basic logistics`

当前 `spellResearch` 设置：

- `spellResearchCostMultiplierPerReroll: [0, 0, 0, 0, 0]`
- `spellResearchPerDay: [99, 99, 99, 99, 99]`
- `spellResearchCost` 1-3级：`1000 gold`
- `spellResearchCost` 4-5级：`1000 gold + 1 mercury + 1 sulfur + 1 crystal + 1 gems`

当前 `difficulty.json` 的 `human.king.resources`：

- `wood: 90`
- `mercury: 90`
- `ore: 90`
- `sulfur: 90`
- `crystal: 90`
- `gems: 90`
- `gold: 999990`

当前 `gameConfig.json` 相关值：

- `heroes.baseScoutingRange: 7`
- `heroes.specialtySecondarySkillGrowth: 10`
- `heroes.specialtyCreatureGrowth: 40`
- `heroes.levelupTotalSkillsAmount: 4`
- `heroes.levelupUpgradedSkillsAmount: 2`
- `towns.buildingsPerTurnCap: 5`

当前 `slayer` 的 expert 设置：

- 在 `expert` 下新增了 `range: "x"`
- 这里记录的是当时 push 的实际提交值，不代表该写法已经过正确性验证

## 2026-06-09 22:42:21

### 7. Fairy 依赖、spellResearch 与 `disruptingRay` 更新

涉及文件：

- [Hero3Expasion1.83/Mods/Fairy/mod.json](Hero3Expasion1.83/Mods/Fairy/mod.json)
- [Hero3Expasion1.83/Mods/hota/Mods/spellResearch/mod.json](Hero3Expasion1.83/Mods/hota/Mods/spellResearch/mod.json)
- [Hero3Expasion1.83/config/spells/timed.json](Hero3Expasion1.83/config/spells/timed.json)

当前 `Fairy` 模组依赖列表：

- `hota.highlandsterrain`
- `hota.factory`
- `courtyard`
- `newoldspells+`
- `tidesofwar`
- `new pavilion.new pavillion`

当前 `HotA spellResearch` 覆盖：

- `spellResearch: true`
- `spellResearchCostMultiplierPerReroll: [0, 0, 0, 0, 0]`
- `spellResearchPerDay: [10, 10, 10, 10, 10]`
- `spellResearchCost` 改为：
- 1级：`1000 gold + 1 mercury + 1 sulfur + 1 crystal + 1 gems`
- 2级：`1000 gold + 1 mercury + 1 sulfur + 1 crystal + 1 gems`
- 3级：`1000 gold + 1 mercury + 1 sulfur + 1 crystal + 1 gems`
- 4级：`1000 gold + 1 mercury + 2 sulfur + 1 crystal + 2 gems`
- 5级：`1000 gold + 2 mercury + 2 sulfur + 2 crystal + 2 gems`

当前 `disruptingRay` 的 expert 级行为：

- 在 `expert` 下新增 `range: "X"`
- 结果是 `毁灭之光 / disruptingRay` 在 expert 时变为群体目标
- `base` 与 `advanced` 仍保持单体，且持续累积永久减防

## 2026-06-07 14:32:00

### 6. Elrisa 初始 spellbook 清理

涉及文件：

- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft15_elrisa.json](Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft15_elrisa.json)

当前 `Elrisa` 特长文本与 spellbook 设置：

- 特长描述更新为 `初始即掌握所有1-3级魔法`
- `tooltip` 更新为 `All 1-3 level spells in spellbook.`
- 初始 `spellbook` 已清理为唯一且去重后的 1-3 级法术列表

当前 `spellbook` 已确认包含以下 spell id：

- `magicArrow`
- `haste`
- `curse`
- `manaFlarePyra`
- `primeMissile`
- `precision`
- `bloodlust`
- `protectFire`
- `slow`
- `disruptingRay`
- `lightningBolt`
- `luck`
- `shield`
- `stoneSkin`
- `cure`
- `protectAir`
- `fireWall`
- `blind`
- `dispel`
- `bless`
- `protectWater`
- `mdtCancel`
- `waspSwarm`
- `quicksand`
- `deathRipple`
- `iceBolt`
- `removeObstacle`
- `fireball`
- `landMine`
- `quickStrikes`
- `weakness`
- `timeStasis`
- `airShield`
- `sacrifice`
- `counterstrike`
- `earthquake`
- `hypnotize`
- `levitation`
- `mdtDivineArrows`
- `mdtBind`
- `animateDead`
- `antiMagic`
- `destroyUndead`
- `misfortune`
- `mdtAuraOfPower`
- `reconstruction`
- `protectEarth`
- `enchantedWeapon`
- `frostRing`
- `mirth`
- `regeneration`
- `confusion`
- `forgetfulness`
- `teleport`
- `curseOfPharaoh`

## 2026-06-07 14:05:00

### 5. Scholar 技能与 Arina 外交特长调整

涉及文件：

- [Hero3Expasion1.83/config/skills.json](Hero3Expasion1.83/config/skills.json)
- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json](Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json)

当前 `scholar` 配置：

- `basic: LEARN_MEETING_SPELL_LIMIT = 3`
- `advanced: LEARN_MEETING_SPELL_LIMIT = 4`
- `expert: LEARN_MEETING_SPELL_LIMIT = 5`

当前 `Arina` 外交特长配置：

- 基础特长仍为 `secondary: diplomacy`
- 新增自定义 bonus：`WANDERING_CREATURES_JOIN_BONUS +3`
- 描述已按实际机制更新：
- 每级降低投降费用 `5%`
- 使中立生物敌意值降低 `3`
- `tooltip` 也已同步写入投降折扣与中立生物敌意降低效果

## 2026-06-07 12:29:19

### 1. 英雄系统基线参考

涉及文件：

- [Hero3Expasion1.83/config/gameConfig.json](Hero3Expasion1.83/config/gameConfig.json)

当前 `gameConfig.json` 中 `heroes` 的基础默认值：

- `tavernInvite: false`
- `baseScoutingRange: 5`
- `specialtySecondarySkillGrowth: 5`
- `specialtyCreatureGrowth: 5`
- `levelupTotalSkillsAmount: 2`
- `levelupUpgradedSkillsAmount: 1`

这里保留的是引擎基线配置。实际游戏表现仍可能被已启用 mod 覆盖。

### 2. HotA balance 覆盖

涉及文件：

- [Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json](Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json)

当前 HotA 的 `heroes` 覆盖值：

- `tavernInvite: true`
- `specialtyCreatureGrowth: 40`
- `specialtySecondarySkillGrowth: 10`
- `levelupTotalSkillsAmount: 4`
- `levelupUpgradedSkillsAmount: 2`
- `baseScoutingRange: 7`

说明：

- 只要 `hota.gamebalance` 子模组启用，这组覆盖值就会替代 `gameConfig.json` 中的基线值。
- 之前错误使用的字段名 `secondarySkillGrowth` 与 `creatureSpecialtyGrowth` 已修正为 schema 可识别名称。

### 3. 招魂相关宝物改动

涉及文件：

- [Hero3Expasion1.83/config/artifacts.json](Hero3Expasion1.83/config/artifacts.json)

当前已记录的招魂相关宝物配置：

- `amuletOfTheUndertaker`: `UNDEAD_RAISE_PERCENTAGE +5`
- `vampiresCowl`: `UNDEAD_RAISE_PERCENTAGE +10`
- `deadMansBoots`: `UNDEAD_RAISE_PERCENTAGE +15`

组合宝物：

- [cloakOfTheUndeadKing](Hero3Expasion1.83/config/artifacts.json)

当前 `cloakOfTheUndeadKing` 的招魂升级链：

- `skeleton -> creature.skeleton`
- `walkingDead -> creature.wight`
- `wight -> creature.lich`
- `lich -> creature.dreadKnight`

组合部件：

- `amuletOfTheUndertaker`
- `vampiresCowl`
- `deadMansBoots`

### 4. `summonGhost` / 召唤恶灵 法术改动

涉及文件：

- [Hero3Expasion1.83/Mods/tidesOfWar/content/config/spells/summonGhost/summonGhost.json](Hero3Expasion1.83/Mods/tidesOfWar/content/config/spells/summonGhost/summonGhost.json)

当前 `summonGhost` / `召唤恶灵` 法术配置：

- 法术等级：`5`
- 基础威力：`10`
- 基础出现概率：`2`
- 阵营出现概率：
- `necropolis: 12`
- `dungeon: 6`
- `inferno: 6`
- 魔法学派标记：
- `earth: true`
- `air/fire/water: false`
- 不同 mastery 的法力消耗：
- `none: 25`
- `base: 20`
- 召唤单位 id：`tidesofwar:mdtGhost`
- 召唤类型：
- `exclusive: false`
- `permanent: true`
- 不同 mastery 的威力倍率：
- `none: 1`
- `basic: 2`
- `advanced: 3`
- `expert: 4`

描述文本：

- `"{召唤恶灵}\r\n\r\n召唤恶灵参战`
