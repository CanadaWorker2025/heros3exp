# LSF Update Log

本日志改为按系统模块维护。旧的 1-18 号记录不再作为单独流水尾巴保留，而是拆入对应模块；每条历史记录仍保留原编号、日期、涉及文件和关键细节。

## 全局规则与成长系统

### 当前未提交改动：英雄技能槽与升级候选

涉及文件：
- [Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json](Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json)
- [Hero3Expasion1.83/config/gameConfig.json](Hero3Expasion1.83/config/gameConfig.json)

当前记录：
- HotA gameBalance 覆盖中增加 `skillPerHero: 10`，英雄副技能上限从常规 8 格扩展到 10 格。
- `levelupTotalSkillsAmount` 保持 16。
- `levelupUpgradedSkillsAmount` 从 2 调整为 4，升级时已学技能可被升级的候选数量增加。
- `baseScoutingRange` 保持 7。

实现限制：
- `skillPerHero` 是全局上限，不是按英雄等级动态变化的规则。
- 仅靠 JSON 配置无法实现“20 级以后额外开放 2 个技能格”的条件逻辑；如果要做等级门槛，需要引擎侧支持或脚本化事件机制。

### 1. 英雄系统基线参考

来源日期：2026-06-07 12:29:19

涉及文件：
- [Hero3Expasion1.83/config/gameConfig.json](Hero3Expasion1.83/config/gameConfig.json)

当前 `gameConfig.json` 中 `heroes` 的基础默认值：
- `tavernInvite: false`
- `baseScoutingRange: 5`
- `specialtySecondarySkillGrowth: 5`
- `specialtyCreatureGrowth: 5`
- `levelupTotalSkillsAmount: 2`
- `levelupUpgradedSkillsAmount: 1`

说明：
- 这里保留的是引擎基线配置。实际游戏表现仍可能被已启用 mod 覆盖。

### 2. HotA balance 覆盖

来源日期：2026-06-07 12:29:19

涉及文件：
- [Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json](Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json)

当前 HotA 的 `heroes` 覆盖值：
- `tavernInvite: true`
- `specialtyCreatureGrowth: 40`
- `specialtySecondarySkillGrowth: 10`
- `levelupTotalSkillsAmount: 8`
- `levelupUpgradedSkillsAmount: 2`
- `baseScoutingRange: 7`

说明：
- 只要 `hota.gamebalance` 子模组启用，这组覆盖值就会替代 `gameConfig.json` 中的基线值。
- 之前错误使用的字段名 `secondarySkillGrowth` 与 `creatureSpecialtyGrowth` 已修正为 schema 可识别名称。

### 8. 全局规则、难度与 spellResearch 改动

来源日期：2026-06-12

涉及文件：
- [Hero3Expasion1.83/Mods/hota/Mods/spellResearch/mod.json](Hero3Expasion1.83/Mods/hota/Mods/spellResearch/mod.json)
- [Hero3Expasion1.83/config/difficulty.json](Hero3Expasion1.83/config/difficulty.json)
- [Hero3Expasion1.83/config/gameConfig.json](Hero3Expasion1.83/config/gameConfig.json)
- [Hero3Expasion1.83/config/spells/timed.json](Hero3Expasion1.83/config/spells/timed.json)

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

### 补记. `3a4ab7cf` 与 `a932a0c7` 的全局规则部分

来源日期：2026-06-17

涉及文件：
- [Hero3Expasion1.83/config/gameConfig.json](Hero3Expasion1.83/config/gameConfig.json)
- [Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json](Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json)

本次提交改动记录：
- `config/gameConfig.json`
- `combat.abilityBias` 从 `25` 改为 `0`
- 这会关闭基于历史结果的能力触发概率平滑修正。
- 结果是战斗特技触发改回完全独立随机。

- `hota/Mods/gameBalance/mod.json`
- `heroes.levelupTotalSkillsAmount` 从 `8` 改为 `16`。

当前结论：
- 战斗中的 `abilityBias` 已被关闭。
- `levelupTotalSkillsAmount = 16` 这一项后来也在后续日志中被再次记录，这里补的是最初提交来源。

### 16. 技能数值、学习与加载链记录

来源日期：2026-06-19

涉及文件：
- [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/spellsPavilion/learningPyramids.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/spellsPavilion/learningPyramids.json>)
- [Hero3Expasion1.83/Mods/hota/Mods/gameBalance/content/config/hotaGameBalance/skillHotA.json](Hero3Expasion1.83/Mods/hota/Mods/gameBalance/content/config/hotaGameBalance/skillHotA.json)
- [Hero3Expasion1.83/Mods/hota/Mods/gameBalance/content/config/hotaGameBalance/skills.json](Hero3Expasion1.83/Mods/hota/Mods/gameBalance/content/config/hotaGameBalance/skills.json)
- [Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/scouting.json](Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/scouting.json)
- [Hero3Expasion1.83/config/skills.json](Hero3Expasion1.83/config/skills.json)

本次 push 已提交的改动记录：
- `learningPyramids.json`
- `core:learning` 的经验加成改为：
- `basic: 50%`
- `advanced: 100%`
- `expert: 200%`
- 源力系赠送法术链仍保留：
- `primeMissile`
- `manaFlarePyra`
- `timeStasis`
- `curseOfPharaoh`

- `skillHotA.json`
- `core:pathfinding` 的 `BASE_TILE_MOVEMENT_COST` 进一步下调。
- 当前最低地格消耗描述与数值对应为：
- `basic: 85`
- `advanced: 70`
- `expert: 65`
- 对应 `main2` 调整为：
- `-15 / -20 / -35`

- `tidesOfWar/content/config/skills/scouting.json`
- `core:necromancy` 的数值改为：
- `basic: 15`
- `advanced: 30`
- `expert: 60`
- `core:scouting` 仍保持：
- 视野 `1 / 3 / 5`
- 额外移动力 `100 / 200 / 300`

- `config/skills.json`
- 原版基线技能数值被改为：
- `pathfinding: 50 / 75 / 100`
- `archery: 15 / 30 / 60`
- `logistics: 20 / 40 / 80`

- `hotaGameBalance/skills.json`
- 文件内也同步写入了 `core:logistics = 20 / 40 / 80`。
- 但当前仓库下 `gameBalance/mod.json` 没有把这个 `skills.json` 挂进 `skills` 加载列表。
- 因此这部分内容目前仅记录“文件已改”，不单独认定为最终一定生效。

当前结论：
- 本次 push 同时改动了原版基线技能、Tides of War 技能覆盖、New Pavilion 的 learning 覆盖、以及 Fairy / Courtyard 的局部玩法配置。
- `learning / necromancy / pathfinding / logistics / archery` 相关效果，需要按加载链分别判断最终生效值。
- `hotaGameBalance/skills.json` 当前仍属于“文件存在改动，但未确认进入加载链”的状态。

## 英雄可选性与特殊英雄

### 当前未提交改动：特殊英雄放开

涉及文件：
- [Hero3Expasion1.83/config/heroes/special.json](Hero3Expasion1.83/config/heroes/special.json)
- [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Bast.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Bast.json>)
- [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Varn.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Varn.json>)
- [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Zamonth.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Zamonth.json>)

当前记录：
- `roland` 的 `special` 从 `true` 改为 `false`。
- `dracon` 的 `special` 从 `true` 改为 `false`。
- New Pavilion 英雄中：
- `Bast` 的 `special` 从 `true` 改为 `false`。
- `Varn` 的 `special` 从 `true` 改为 `false`。
- `Zamonth` 的 `special` 从 `true` 改为 `false`。

注意：
- `special: false` 只解决全局英雄池/普通可选性问题。
- 固定地图仍可能通过地图自身的 available heroes mask 禁用英雄；例如某些 H3M 地图即使全局放开 Dracon，也不一定能在开局选择。

## 英雄特长与英雄配置

### 当前未提交改动：Azarias / 亚撒利雅

涉及文件：
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/pavilion.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/pavilion.json>)

当前记录：
- 亚撒利雅的“沙蛇”特长不再只覆盖 `wadjet`。
- 已在 extra creatures specialty 覆盖层中扩展为所有蛇类/蛇形相关生物及升级形态。
- 每个目标生物获得：
- 攻击成长：`PRIMARY_SKILL primarySkill.attack`，`val: 2`，`updater: TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL`
- 防御成长：`PRIMARY_SKILL primarySkill.defence`，`val: 2`，`updater: TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL`
- 速度：`STACKS_SPEED +1`
- `includeUpgrades: true`

当前覆盖目标：
- `wadjet`
- `serpentFly`
- `naga`
- `twArcane06`
- `cthCouatl`
- `mdtCouatl`
- `seaserpent`
- `havC13`
- `hotaCouatl`

文本同步：
- 描述改为：`每对应生物等级提高所有蛇类生物及其升级形态20%的攻击力和防御力。速度+1。`

### 当前未提交改动：Zamonth / 萨蒙特

涉及文件：
- [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Zamonth.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Zamonth.json>)

当前记录：
- 保留木乃伊转化特长。
- 额外增加木乃伊速度特长：
- `STACKS_SPEED +4`
- 目标：`mummy`
- `includeUpgrades: true`

### 当前未提交改动：Elspeth / 埃尔斯佩思

涉及文件：
- [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Elspeth.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Elspeth.json>)

当前记录：
- 开场自动施法 `spell.dragonStrength` 的 `val` 从 10 调整为 50。
- 其他生命之源、火系法术掌握等配置未在本次 diff 中继续改动。

### 当前确认：干扰术特长

涉及文件：
- [Hero3Expasion1.83/Mods/hota/Mods/interference/content/config/rampart/heroes/giselle.json](Hero3Expasion1.83/Mods/hota/Mods/interference/content/config/rampart/heroes/giselle.json)
- [Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/heroes/Hanester.json](Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/heroes/Hanester.json)

当前记录：
- 干扰术英雄特长目前有 2 个：
- `Giselle / 吉赛尔`
- `Hanester / 哈内斯特`
- 两者特长配置一致：
- `secondary: interference`
- `PRIMARY_SKILL spellpower`
- `val: -5`
- `valueType: PERCENT_TO_TARGET_TYPE`
- `updater: TIMES_HERO_LEVEL`

区别：
- 吉赛尔初始高级干扰术。
- 哈内斯特初始初级干扰术 + 初级智力，并自带 `antiMagic`。

### 18. `havAhero05B / havAhero05special` 航海术英雄重做，`elspeth` 生命之源扩展

来源日期：2026-07-05

涉及文件：
- [Hero3Expasion1.83/Mods/haven/content/config/haven/heroes/havAhero05B.json](Hero3Expasion1.83/Mods/haven/content/config/haven/heroes/havAhero05B.json)
- [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Elspeth.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Elspeth.json>)

本次更新记录：
- `havAhero05B.json`
- 统一整理 `Wesley / 艾伦` 文本与 JSON 结构，修正为干净可读版本。
- `Wesley`
- 保留原有航海术成长特长：`MOVEMENT heroMovementSea`，`updater = TIMES_HERO_LEVEL`，`val = 5`，`valueType = PERCENT_TO_TARGET_TYPE`
- `艾伦 / havAhero05special`
- 在原有航海术成长基础上新增：
- `FREE_SHIP_BOARDING`
- `SIGHT_RADIUS +5`
- `MOVEMENT heroMovementSea +5000`
- `GENERATE_RESOURCE gold +5000`
- `GENERATE_RESOURCE wood +5`
- `GENERATE_RESOURCE ore +5`
- `WHIRLPOOL_PROTECTION`
- `MANA_REGENERATION +50`
- `MANA_PERCENTAGE_REGENERATION +30`
- `SPELLS_OF_SCHOOL water`
- `MAGIC_SCHOOL_SKILL spellSchool.water = 3`
- 同步更新艾伦特长说明，明确当前实际效果为：航海术强化、上下船免费、海上高机动、资源收益、漩涡免疫、每日法力恢复、直接掌握并专家化全部水系魔法。

- `Elspeth.json`
- 保留 `health1 = STACK_HEALTH +1`
- 将 `health2` 改为成长型生命特长：
- `STACK_HEALTH`
- `updater = TIMES_HERO_LEVEL`
- `val = 4`
- `valueType = PERCENT_TO_BASE`
- 这意味着生命值百分比加成改为随英雄等级成长，而不是固定总值。
- 新增开场自动施法：
- `OPENING_BATTLE_SPELL -> spell.dragonStrength`
- 新增火系法术掌握：
- `SPELLS_OF_SCHOOL fire`
- `MAGIC_SCHOOL_SKILL spellSchool.fire = 3`
- 同步重写 `texts.specialty.description / tooltip`，使“生命之源”文本与当前配置一致。

### 17. `ft12_seliva` 幻影射手特长追加能力同步

来源日期：2026-06-19

涉及文件：
- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft12_seliva.json](Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft12_seliva.json)

本次更新记录：
- 重新确认 `hota.factory:hotaGunmanUp` 的“无限反击”原生写法不是 `ADDITIONAL_RETALIATION 99`。
- 当前引擎/模组已支持直接使用：
- `UNLIMITED_RETALIATIONS`
- `RANGED_RETALIATION`
- `FIRST_STRIKE`

- `ft12_seliva.json`
- 明确采用“英雄特长给兵种加 bonus”的实现方式。
- 不改 `sharpshooter` 兵种本体。
- 只在 `ftL_Seliva.specialty.bonuses` 下通过 `CREATURE_TYPE_LIMITER creature=sharpshooter` 生效。
- 保留原有：
- `CREATURE_DAMAGE creatureDamageBoth +2`
- 升级来源：
- `creature.archer`
- `creature.marksman`
- `creature.woodElf`
- `creature.grandElf`
- `deathvalley:boneHook`
- `deathvalley:flayer`
- `asylum:shadowhunter`
- `asylum:phantomhunter`
- 赛丽娃的幻影射手现追加：
- `LIFE_DRAIN 100`
- `FREE_SHOOTING`
- `UNLIMITED_RETALIATIONS`
- `RANGED_RETALIATION`
- `FIRST_STRIKE`
- `ENEMY_DEFENCE_REDUCTION 100`
- `CATAPULT`
- `STACK_HEALTH +10`

- 文本已同步更新为当前实际效果：
- `description` 改为中文实装说明。
- `tooltip` 改为英文实装说明。
- “骷髅护卫 / 骷髅法师” 文案同步修正为当前实际升级对象：
- `白骨弓箭手`
- `剥皮者`

### 15. `P_renata` 死骑特长扩展

来源日期：2026-06-17

涉及文件：
- [Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json](Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json)

本次更新记录：
- `P_renata.json`
- 将文件重写为可严格通过 JSON 解析的干净版本。
- 修复原先损坏的 `texts.name`、`texts.biography`、`texts.specialty.name`、`texts.specialty.description` 乱码与引号问题。
- 保留 `SPECIAL_UPGRADE -> creature.dreadKnight`
- 保留并确认以下升级映射：
- `creature.champion -> creature.dreadKnight`
- `creature.cavalier -> creature.dreadKnight`
- `tidesofwar:ffpaladin -> creature.dreadKnight`
- `cathedral:cthPaladin -> creature.dreadKnight`
- `cathedral:cthTemplar -> creature.dreadKnight`
- `creature.blackKnight -> creature.dreadKnight`
- `tidesofwar:gNec06 -> creature.dreadKnight`
- 保留开场施法：
- `OPENING_BATTLE_SPELL -> spell.mirth`
- 保留法术绑定：
- `SPELL -> spell.mirth`
- 对 `blackKnight + includeUpgrades: true` 新增/保留以下特长 bonus：
- `CREATURE_DAMAGE creatureDamageBoth +15`
- `PRIMARY_SKILL primarySkill.attack +15`
- `PRIMARY_SKILL primarySkill.defence +15`
- `STACKS_SPEED +3`
- `FIRST_STRIKE`
- `BLOCKS_RETALIATION`
- `LIFE_DRAIN 100`
- `ATTACKS_ALL_ADJACENT`
- `JOUSTING 5`
- `DEATH_STARE deathStareGorgon 10`
- `ADDITIONAL_ATTACK 1`
- `FEROCITY 1`
- `DESTRUCTION destructionKillPercentage 25, addInfo 20`

校验记录：
- `P_renata.json` 当前已通过严格 JSON 校验。

### 13. `eovacius` 与 `P_renata` 当前配置更新记录

来源日期：2026-06-14

涉及文件：
- [Hero3Expasion1.83/Mods/hota/Mods/cove/Content/config/hota/cove/heroes/eovacius.json](Hero3Expasion1.83/Mods/hota/Mods/cove/Content/config/hota/cove/heroes/eovacius.json)
- [Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json](Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json)

当前 `eovacius` 配置记录：
- 初始技能现为 `basic wisdom`、`basic mysticism`、`basic waterMagic`
- 保留原有 `clone` 相关特长：`type: SPELL`, `subtype: spell.specialClone`
- `specialty.base` 现为 `type: SPECIAL_UPGRADE`, `addInfo: hota.factory:hotaGunmanUp`
- 当前可升级为 `hota.factory:hotaGunmanUp` 的兵种包括：
- `haven:havC05`
- `haven:havC06`
- `hota.factory:hotaGunman`
- `courtyard:arquebusier`
- `courtyard:musketeer`
- `cathedral:cthWitchHunter`
- `cathedral:cthEvilHunter`
- `creature.pirate`
- `creature.corsair`
- `creature.seadog`
- 额外加成已写入：
- `CREATURE_DAMAGE creatureDamageBoth +10`，限制到 `hota.factory:hotaGunmanUp` 及其升级
- `STACKS_SPEED +2`，限制到 `hota.factory:hotaGunman` 及其升级
- 传记文本末尾补充了技能备注：`(智慧/神秘/气/土/水/后勤/箭术/战术)`

当前 `P_renata` 配置记录：
- 初始技能现为 `basic wisdom`、`basic runes`、`basic airMagic`
- 保留初始 `spellbook` 条目 `mirth`
- `specialty.base` 维持为 `type: SPECIAL_UPGRADE`, `addInfo: creature.dreadKnight`
- 当前启用的升级映射包括：
- `creature.champion -> creature.dreadKnight`
- `creature.cavalier -> creature.dreadKnight`
- `tidesofwar:ffpaladin -> creature.dreadKnight`
- `cathedral:cthPaladin -> creature.dreadKnight`
- `cathedral:cthTemplar -> creature.dreadKnight`
- 开场战斗施法仍为：
- `type: OPENING_BATTLE_SPELL`
- `subtype: spell.mirth`
- `val: 10`
- 法术绑定仍为：
- `type: SPELL`
- `subtype: spell.mirth`
- 黑骑士系加成维持：
- `CREATURE_DAMAGE creatureDamageBoth +15`
- `PRIMARY_SKILL primarySkill.attack +15`
- `PRIMARY_SKILL primarySkill.defence +15`
- `STACKS_SPEED +2`

校验记录：
- `eovacius.json` 当前已通过 JSON 语法校验。
- `P_renata.json` 当前已通过 JSON 语法校验。

### 12. Courtyard `14Ekaterina` 祈祷特长与开场施法确认

来源日期：2026-06-14

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
- `Ekaterina` 现在已经可以在战斗开场自动释放 `prayer`。
- 该 `prayer` 会按当前工程里与 `Loynis` 相同的 prayer-specialty 规则生效。
- 不需要在 `spellHero.json` 再额外添加 `courtyard:14Ekaterina` 的 `specialty#override`。
- 之前尝试添加 hero-level `specialty#override` 会覆盖原有 `specialty.bonuses`，反而导致 `choir` / `bind` / `pacifism` / `SPECIAL_PECULIAR_ENCHANT` 被冲掉。

### 11. Fairy / Preserve 英雄配置后续记录

来源日期：2026-06-13

涉及文件：
- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json](Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json)
- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft2_meri.json](Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft2_meri.json)
- [Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json](Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json)
- [Hero3Expasion1.83/Mods/preserve/mod.json](Hero3Expasion1.83/Mods/preserve/mod.json)

本次 push 已提交的改动记录：
- `ft16_arina.json`：初始 `wisdom` 从 `basic` 改为 `expert`
- `ft2_meri.json`：新增初始 `spellbook` 条目 `townPortal` 与 `dimensionDoor`
- `preserve/mod.json`：新增依赖列表 `["tidesOfWar", "cathedral"]`
- `P_renata.json`：启用了额外的 `SPECIAL_UPGRADE` 映射：
- `tidesofwar:ffpaladin -> creature.dreadKnight`
- `cathedral:cthPaladin -> creature.dreadKnight`
- `cathedral:cthTemplar -> creature.dreadKnight`

### 10. Asylum `K_viola` 深渊号角法术接入

来源日期：2026-06-13

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

### 9. Preserve `P_renata` 特长重写

来源日期：2026-06-12 03:15:00

涉及文件：
- [Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json](Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json)

当前 `p_renata` 特长配置：
- 保留初始 `spellbook` 条目 `mirth`
- `base.type: SPECIAL_UPGRADE`
- `base.addInfo: creature.dreadKnight`
- 当前启用的升级映射：
- `creature.champion -> creature.dreadKnight`
- `creature.cavalier -> creature.dreadKnight`
- 额外规划的 `ffpaladin`、`cthPaladin`、`cthTemplar` 映射已写入但仍处于注释状态。
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

### 8. Fairy 英雄配置部分

来源日期：2026-06-12

涉及文件：
- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft12_seliva.json](Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft12_seliva.json)
- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json](Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json)

当前 `Seliva` 特长配置：
- 保留原有效果：所带兵种最小与最大伤害都获得 `+2`
- 通过 `SPECIAL_UPGRADE` 新增类似 Gelu 的隐藏升级效果。
- 升级映射为：`creature.archer`、`creature.marksman`、`creature.woodElf`、`creature.grandElf` 全部可升级为 `creature.sharpshooter`
- 特长文本已补充该隐藏的神射手升级效果。

当前 `Arina` 初始技能：
- `basic diplomacy`
- `basic wisdom`
- `basic logistics`

### 6. Elrisa 初始 spellbook 清理

来源日期：2026-06-07 14:32:00

涉及文件：
- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft15_elrisa.json](Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft15_elrisa.json)

当前 `Elrisa` 特长文本与 spellbook 设置：
- 特长描述更新为 `初始即掌握所有1-3级魔法`
- `tooltip` 更新为 `All 1-3 level spells in spellbook.`
- 初始 `spellbook` 已清理为唯一且去重后的 1-3 级法术列表。

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

### 5. Scholar 技能与 Arina 外交特长调整

来源日期：2026-06-07 14:05:00

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
- `tooltip` 也已同步写入投降折扣与中立生物敌意降低效果。

## 魔法、法术研究与战斗效果

### 当前未提交改动：Sacrifice / 牺牲

涉及文件：
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/sacrificeSelfkill.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/sacrificeSelfkill.json>)

当前记录：
- 保留现有技能效果：
- `type: core:damage`
- `killByPercentage: true`
- 恢复原始牺牲魔法表现：
- 动画：`C01SPE0`
- 透明度：`0.5`
- 音效：`SACRIF1`
- 清理 `targetCondition` 中的 JSON 注释，使文件保持标准 JSON 可解析。

### 16. 法术与技能相关部分

来源日期：2026-06-19

涉及文件：
- [Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/quagmire.json](Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/quagmire.json)
- [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/spellsPavilion/learningPyramids.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/spellsPavilion/learningPyramids.json>)

本次 push 已提交的改动记录：
- `quagmire.json`
- `禁足 / quagmire` 的实际持续回合数改为：
- `basic: 3`
- `advanced: 6`
- `expert: 12`
- 当前描述文本仍写作 `1 / 2 / 3 回合`
- 也就是说当前该法术存在“文本未同步到实际数值”的状态。

- `learningPyramids.json`
- `core:learning` 的经验加成改为：
- `basic: 50%`
- `advanced: 100%`
- `expert: 200%`
- 源力系赠送法术链仍保留：
- `primeMissile`
- `manaFlarePyra`
- `timeStasis`
- `curseOfPharaoh`

### 14. 牺牲改版、召唤额度与 HotA 升级候选

来源日期：2026-06-15

涉及文件：
- [Hero3Expasion1.83/Mods/asylum/content/config/asylum/creatures/shadowmare.json](Hero3Expasion1.83/Mods/asylum/content/config/asylum/creatures/shadowmare.json)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/sacrificeSelfkill.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/sacrificeSelfkill.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/mod.json](<Hero3Expasion1.83/Mods/extra creatures specialty/mod.json>)
- [Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json](Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json)
- [Hero3Expasion1.83/config/creatures/inferno.json](Hero3Expasion1.83/config/creatures/inferno.json)

本次 push 已提交的改动记录：
- `shadowmare.json`
- 阴影兽的 `SPECIFIC_SPELL_POWER` 对 `spell.summonApps` 的值从 `40` 改为 `4000000`。
- 这会极大抬高阴影兽对“驱役幽影”的可用生命额度上限。

- `inferno.json`
- 邪神王的 `SPECIFIC_SPELL_POWER` 对 `spell.summonDemons` 的值从 `50` 改为 `5000000`。
- 这会极大抬高邪神王对“召唤恶鬼”的可用生命额度上限。

- `sacrificeSelfkill.json`
- 新增 `core:sacrifice` 覆写文件。
- 将原版 `牺牲 / core:sacrifice` 改为直接伤害法术。
- 动画与音效改为霹雳闪电风格：`C03SPA0` / `C11SPA1` + `LIGHTBLT`
- `battleEffects.sacrifice.type` 改为 `core:damage`
- 启用 `killByPercentage: true`
- 四档法术等级统一设为 `cost: 0`
- 四档法术等级统一设为 `power: 10000`
- `flags` 调整为负面伤害法术：`positive: false`, `rising: false`, `damage: true`, `negative: true`

- `extra creatures specialty/mod.json`
- 在 `spells` 列表中新增 `config/spells/sacrificeSelfkill.json`
- 让 `extra creatures specialty` 启用时自动加载牺牲覆写。
- 顺手去掉了 `heroes` 列表末尾的多余尾逗号。

- `gameBalance/mod.json`
- `heroes.levelupTotalSkillsAmount` 从 `4` 改为 `16`
- 当前 HotA balance 的升级可选技能总槽位恢复为 `16`。

当前结论：
- `牺牲` 的改版已转为并入 `extra creatures specialty` 加载，不再依赖单独测试 mod。
- 阴影兽与邪神王这两类“吃尸体转生物”的能力都被改成了极高生命额度版本。
- 实际召唤数量仍然会继续受目标尸体总生命值限制。

### 补记. 士气/幸运类 timed spell 数值

来源日期：2026-06-17

涉及文件：
- [Hero3Expasion1.83/config/spells/timed.json](Hero3Expasion1.83/config/spells/timed.json)

本次提交改动记录：
- `mirth` 调整为：
- `base: +2 morale`
- `advanced: +4 morale`
- `expert: +8 morale`
- `sorrow` 调整为：
- `base: -2 morale`
- `advanced: -4 morale`
- `expert: -8 morale`
- `fortune` 调整为：
- `base: +2 luck`
- `advanced: +4 luck`
- `expert: +8 luck`
- `misfortune` 调整为：
- `base: -2 luck`
- `advanced: -4 luck`
- `expert: -8 luck`

当前结论：
- 四个士气/幸运类 timed spell 当前都被改成了远高于原版的数值版本。

### 12. Prayer 特长实际数值来源

来源日期：2026-06-14

涉及文件：
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/spellsNew.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/spellsNew.json>)

当前 prayer-specialty 的实际数值来源：
- 来源不是单独的 hero override，而是 `extra creatures specialty` 对 `core:prayer` 的 spell-level patch。
- `spellsNew.json` 中 `core:prayer` 已按兵种等级分段写入固定加成。
- 对应判定依赖 `HAS_ANOTHER_BONUS_LIMITER` + `SPECIFIC_SPELL_DAMAGE` + `spell.prayer`。

当前 prayer-specialty 分段效果记录：
- `none/basic`
- `1-5级`：`attack/defence/speed +5`
- `6级`：`attack/defence/speed +4`
- `7级`：`attack/defence/speed +3`
- `advanced/expert`
- `1-5级`：`attack/defence/speed +7`
- `6级`：`attack/defence/speed +6`
- `7级`：`attack/defence/speed +5`

### 11. Timed spell 风险记录

来源日期：2026-06-13

涉及文件：
- [Hero3Expasion1.83/config/spells/timed.json](Hero3Expasion1.83/config/spells/timed.json)

本次 push 中 `timed.json` 的记录如下：
- `slayer.expert.range` 已从小写 `x` 修正为大写 `X`
- `antiMagic.expert.range` 当前仍为小写 `x`
- `magicMirror` 当前把 `expert` 写在了 `levels.base` 内部，而不是同级 level block

风险说明：
- `slayer` 这项修正与常规法术范围写法一致。
- `antiMagic.expert.range: "x"` 如果引擎严格要求大写 `X`，则这里可能无效。
- `magicMirror.levels` 当前结构看起来不合法，是较可能的法术配置问题来源。
- 本条主要记录当时 push 的已提交状态，不代表已完成正确性验证。

### 10. Asylum 本地 `hornSpell` 法术配置

来源日期：2026-06-13

涉及文件：
- [Hero3Expasion1.83/Mods/asylum/content/config/asylum/NewOldSpells/hornSpell.json](Hero3Expasion1.83/Mods/asylum/content/config/asylum/NewOldSpells/hornSpell.json)

当前本地 `asylum:hornSpell` 配置：
- 将 HotA 的宝物附带法术改造成 `asylum` 本地普通战斗法术。
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

### 8. Slayer expert 范围记录

来源日期：2026-06-12

涉及文件：
- [Hero3Expasion1.83/config/spells/timed.json](Hero3Expasion1.83/config/spells/timed.json)

当前 `slayer` 的 expert 设置：
- 在 `expert` 下新增了 `range: "x"`
- 这里记录的是当时 push 的实际提交值，不代表该写法已经过正确性验证。

### 7. Fairy 依赖、spellResearch 与 `disruptingRay` 更新

来源日期：2026-06-09 22:42:21

涉及文件：
- [Hero3Expasion1.83/Mods/Fairy/mod.json](Hero3Expasion1.83/Mods/Fairy/mod.json)
- [Hero3Expasion1.83/Mods/hota/Mods/spellResearch/mod.json](Hero3Expasion1.83/Mods/hota/Mods/spellResearch/mod.json)
- [Hero3Expasion1.83/config/spells/timed.json](Hero3Expasion1.83/config/spells/timed.json)

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
- 结果是 `毁灭之光 / disruptingRay` 在 expert 时变为群体目标。
- `base` 与 `advanced` 仍保持单体，且持续累积永久减防。

### 4. `summonGhost` / 召唤恶灵 法术改动

来源日期：2026-06-07 12:29:19

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

## 模组依赖、加载链与配置清理

### 16. 复合 push 的加载链结论

来源日期：2026-06-19

涉及文件：
- [Hero3Expasion1.83/Mods/Fairy/mod.json](Hero3Expasion1.83/Mods/Fairy/mod.json)
- [Hero3Expasion1.83/Mods/hota/Mods/gameBalance/content/config/hotaGameBalance/skills.json](Hero3Expasion1.83/Mods/hota/Mods/gameBalance/content/config/hotaGameBalance/skills.json)
- [Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json](Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json)

本次 push 已提交的改动记录：
- `Fairy/mod.json`
- 新增依赖：
- `asylum`
- `deathvalley`

当前结论：
- 本次 push 同时改动了原版基线技能、Tides of War 技能覆盖、New Pavilion 的 learning 覆盖、以及 Fairy / Courtyard 的局部玩法配置。
- `hotaGameBalance/skills.json` 当前仍属于“文件存在改动，但未确认进入加载链”的状态。

### 15. `preserve` 依赖确认与 JSON 清理

来源日期：2026-06-17

涉及文件：
- [Hero3Expasion1.83/Mods/preserve/mod.json](Hero3Expasion1.83/Mods/preserve/mod.json)

本次更新记录：
- `preserve/mod.json`
- 重写为可严格通过 JSON 解析的干净版本。
- 修复 `depends` 后缺失逗号导致的结构错误。
- 将 `chinese.name` 与 `chinese.description` 修正为正常 UTF-8 中文。
- 当前依赖确认如下：
- 保留 `tidesOfWar`
- 保留 `cathedral`
- 保留 `hota.bulwark`
- 不需要新增 `New Pavilion`

校验记录：
- `preserve/mod.json` 当前已通过严格 JSON 校验。

### 补记. Courtyard 依赖与职业技能

来源日期：2026-06-17

涉及文件：
- [Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/classes/magicHero.json](Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/classes/magicHero.json)
- [Hero3Expasion1.83/Mods/Courtyard/mod.json](Hero3Expasion1.83/Mods/Courtyard/mod.json)

本次提交改动记录：
- `Courtyard/classes/magicHero.json`
- `philosopher` 职业的 `secondarySkills` 新增：
- `"runes" : 4`

- `Courtyard/mod.json`
- 新增依赖：
- `hota.bulwark`

当前结论：
- `philosopher` 现在已允许出现 `runes`。
- `Courtyard` 现在显式依赖 `hota.bulwark`。

### 11. Preserve 依赖新增

来源日期：2026-06-13

涉及文件：
- [Hero3Expasion1.83/Mods/preserve/mod.json](Hero3Expasion1.83/Mods/preserve/mod.json)

本次 push 已提交的改动记录：
- `preserve/mod.json`：新增依赖列表 `["tidesOfWar", "cathedral"]`

### 10. Asylum 模组依赖与法术注册

来源日期：2026-06-13

涉及文件：
- [Hero3Expasion1.83/Mods/asylum/mod.json](Hero3Expasion1.83/Mods/asylum/mod.json)
- [Hero3Expasion1.83/Mods/asylum/content/config/asylum/NewOldSpells/hornSpell.json](Hero3Expasion1.83/Mods/asylum/content/config/asylum/NewOldSpells/hornSpell.json)

当前 `asylum` 模组依赖与法术注册：
- 新增依赖：`hota.neutralCreatures`
- 新增本地法术注册：
- `config/asylum/NewOldSpells/hornSpell.json`

### 7. Fairy 模组依赖列表

来源日期：2026-06-09 22:42:21

涉及文件：
- [Hero3Expasion1.83/Mods/Fairy/mod.json](Hero3Expasion1.83/Mods/Fairy/mod.json)

当前 `Fairy` 模组依赖列表：
- `hota.highlandsterrain`
- `hota.factory`
- `courtyard`
- `newoldspells+`
- `tidesofwar`
- `new pavilion.new pavillion`

## 生物、宝物与召唤能力

### 14. 阴影兽与邪神王召唤额度

来源日期：2026-06-15

涉及文件：
- [Hero3Expasion1.83/Mods/asylum/content/config/asylum/creatures/shadowmare.json](Hero3Expasion1.83/Mods/asylum/content/config/asylum/creatures/shadowmare.json)
- [Hero3Expasion1.83/config/creatures/inferno.json](Hero3Expasion1.83/config/creatures/inferno.json)

本次 push 已提交的改动记录：
- `shadowmare.json`
- 阴影兽的 `SPECIFIC_SPELL_POWER` 对 `spell.summonApps` 的值从 `40` 改为 `4000000`。
- 这会极大抬高阴影兽对“驱役幽影”的可用生命额度上限。

- `inferno.json`
- 邪神王的 `SPECIFIC_SPELL_POWER` 对 `spell.summonDemons` 的值从 `50` 改为 `5000000`。
- 这会极大抬高邪神王对“召唤恶鬼”的可用生命额度上限。

当前结论：
- 阴影兽与邪神王这两类“吃尸体转生物”的能力都被改成了极高生命额度版本。
- 实际召唤数量仍然会继续受目标尸体总生命值限制。

### 3. 招魂相关宝物改动

来源日期：2026-06-07 12:29:19

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

## 战役与地图

### 当前确认：沙堡战役第 4 关

涉及文件：
- `Hero3Expasion1.83/Mods/new-menu/Content/Maps/Expansion/campaign-5 - Copy.vcmp`
- `Hero3Expasion1.83/Mods/new-menu/Content/Maps/Expansion/campaign-5 - Copy.vcmp/c504.vmap`

当前记录：
- 沙堡战役第 4 关 `c504.vmap` 中：
- “怨影亚伯拉罕”是地图实例显示名。
- 实际英雄类型继承 `new pavilion.new pavillion:ibrahim`。
- 监狱中的英雄是 `new pavilion.new pavillion:varn`，不是怨影亚伯拉罕。

## 待验证与提交前检查

### 当前未提交改动检查

- 验证已改 JSON 文件可解析：
- `pavilion.json`
- `Zamonth.json`
- `sacrificeSelfkill.json`
- `gameBalance/mod.json`
- `special.json` 使用 VCMI 配置格式确认；该文件含 `//` 注释，不能用标准 JSON 解析器直接校验。
- 进游戏检查：
- 亚撒利雅是否对所有目标蛇类生物生效。
- 萨蒙特手下木乃伊速度是否 +4。
- 牺牲魔法动画/音效是否恢复为原版表现。
- `skillPerHero: 10` 是否实际放开到 10 个副技能格。

### 历史待验证事项

- `禁足 / quagmire` 当前实际持续回合数为 `3 / 6 / 12`，但描述文本仍写作 `1 / 2 / 3 回合`。
- `hotaGameBalance/skills.json` 文件存在 `core:logistics = 20 / 40 / 80`，但未确认进入 `gameBalance/mod.json` 加载链。
- `antiMagic.expert.range: "x"` 如果引擎严格要求大写 `X`，则这里可能无效。
- `magicMirror` 当前把 `expert` 写在了 `levels.base` 内部，而不是同级 level block，是较可能的法术配置问题来源。
- `slayer.expert.range` 在不同历史记录中出现过小写 `x` 与大写 `X`；最终状态需要以当前文件为准。
