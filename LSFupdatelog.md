# LSF Update Log

## 2026-06-14

### 12. Courtyard `14Ekaterina` 祈祷特长与开场施法确认

涉及文件：

- [Heroes of Might and Magic III Expansion 1.83/Mods/Courtyard/Content/config/courtyard/heroes/Ekaterina.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/Courtyard/Content/config/courtyard/heroes/Ekaterina.json:1)
- [Heroes of Might and Magic III Expansion 1.83/Mods/extra creatures specialty/content/config/spells/spellsNew.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/extra%20creatures%20specialty/content/config/spells/spellsNew.json:1)

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

### 11. Latest push `f9592afc` follow-up

Source files:

- [Heroes of Might and Magic III Expansion 1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json:1)
- [Heroes of Might and Magic III Expansion 1.83/Mods/Fairy/content/config/fairy/heroes/ft2_meri.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/Fairy/content/config/fairy/heroes/ft2_meri.json:1)
- [Heroes of Might and Magic III Expansion 1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/preserve/content/config/preserve/heroes/P_renata.json:1)
- [Heroes of Might and Magic III Expansion 1.83/Mods/preserve/mod.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/preserve/mod.json:1)
- [Heroes of Might and Magic III Expansion 1.83/config/spells/timed.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/config/spells/timed.json:1)

Current committed changes from latest push:

- `ft16_arina.json`: starting `wisdom` changed from `basic` to `expert`
- `ft2_meri.json`: added starting `spellbook` entries `townPortal` and `dimensionDoor`
- `preserve/mod.json`: added dependency list `["tidesOfWar", "cathedral"]`
- `P_renata.json`: enabled extra `SPECIAL_UPGRADE` mappings:
- `tidesofwar:ffpaladin -> creature.dreadKnight`
- `cathedral:cthPaladin -> creature.dreadKnight`
- `cathedral:cthTemplar -> creature.dreadKnight`

Current `timed.json` changes recorded in this push:

- `slayer.expert.range` was corrected from lowercase `x` to uppercase `X`
- `antiMagic.expert.range` is currently set to lowercase `x`
- `magicMirror` currently has `expert` nested inside `levels.base`, not as a sibling level block

Risk note:

- The `slayer` correction looks consistent with normal spell range notation
- `antiMagic.expert.range: "x"` may be invalid if the engine expects uppercase `X`
- `magicMirror.levels` currently looks structurally malformed and is a likely source of spell config issues
- This section records the pushed state as committed, not a correctness guarantee

### 10. Asylum `K_viola` horn spell integration

Source files:

- [Heroes of Might and Magic III Expansion 1.83/Mods/asylum/content/config/asylum/heroes/K_viola.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/asylum/content/config/asylum/heroes/K_viola.json:1)
- [Heroes of Might and Magic III Expansion 1.83/Mods/asylum/mod.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/asylum/mod.json:1)
- [Heroes of Might and Magic III Expansion 1.83/Mods/asylum/content/config/asylum/NewOldSpells/hornSpell.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/asylum/content/config/asylum/NewOldSpells/hornSpell.json:1)

Current `k_viola` setup:

- Keeps original `hypnotize` specialty scaling:
- `SPECIAL_SPELL_LEV`
- `subtype: spell.hypnotize`
- `updater: TIMES_HERO_LEVEL`
- `val: 5`
- Starting spellbook now contains:
- `hypnotize`
- `asylum:hornSpell`
- Starting `wisdom` changed to `expert`

Current `asylum` mod dependency and spell registration:

- Added dependency: `hota.neutralCreatures`
- Added local spell registration:
- `config/asylum/NewOldSpells/hornSpell.json`

Current local `asylum:hornSpell` configuration:

- Converted from the HotA artifact spell into an `asylum` local combat spell
- `type: combat`
- `targetType: CREATURE`
- `school: water`
- `level: 5`
- `levels.base.cost: 20`
- `levels.base.power: 40`
- `range: 0`
- Uses `core:demonSummon`
- Summons `hota.neutralcreatures:fangarm`
- `permanent: true`
- Target restrictions block `NON_LIVING`, `SIEGE_WEAPON`, `UNDEAD`, `GARGOYLE`, and `MECHANICAL`

## 2026-06-12 03:15:00

### 9. Preserve `P_renata` specialty rewrite

Source file:

- [Heroes of Might and Magic III Expansion 1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/preserve/content/config/preserve/heroes/P_renata.json:1)

Current `p_renata` specialty configuration:

- Keeps starting spellbook entry `mirth`
- `base.type: SPECIAL_UPGRADE`
- `base.addInfo: creature.dreadKnight`
- Upgrade mapping currently enabled:
- `creature.champion -> creature.dreadKnight`
- `creature.cavalier -> creature.dreadKnight`
- Additional planned mappings for `ffpaladin`, `cthPaladin`, and `cthTemplar` are present but commented out
- Opening battle spell:
- `type: OPENING_BATTLE_SPELL`
- `subtype: spell.mirth`
- `val: 10`
- Spell binding bonus retained:
- `type: SPELL`
- `subtype: spell.mirth`
- Fixed black knight specialty bonuses with `includeUpgrades: true`:
- `CREATURE_DAMAGE creatureDamageBoth +15`
- `PRIMARY_SKILL primarySkill.attack +15`
- `PRIMARY_SKILL primarySkill.defence +15`
- `STACKS_SPEED +2`

Current specialty text intent:

- Opening battle automatically casts `mirth`
- Hidden effect grants dread knight combat bonuses
- Hidden effect also allows selected knight-line units to upgrade into `dreadKnight`

## 2026-06-12

### 8. Recent push changes

Source files:

- [Heroes of Might and Magic III Expansion 1.83/Mods/Fairy/content/config/fairy/heroes/ft12_seliva.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/Fairy/content/config/fairy/heroes/ft12_seliva.json:1)
- [Heroes of Might and Magic III Expansion 1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json:1)
- [Heroes of Might and Magic III Expansion 1.83/Mods/hota/Mods/spellResearch/mod.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/hota/Mods/spellResearch/mod.json:1)
- [Heroes of Might and Magic III Expansion 1.83/config/difficulty.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/config/difficulty.json:1)
- [Heroes of Might and Magic III Expansion 1.83/config/gameConfig.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/config/gameConfig.json:1)
- [Heroes of Might and Magic III Expansion 1.83/config/spells/timed.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/config/spells/timed.json:1)

Current `Seliva` specialty configuration:

- Retains the base effect: led creatures get `+2` to both minimum and maximum damage
- Added Gelu-style hidden upgrade behavior through `SPECIAL_UPGRADE`
- Upgrade mapping: `creature.archer`, `creature.marksman`, `creature.woodElf`, and `creature.grandElf` all upgrade into `creature.sharpshooter`
- Specialty text now mentions the hidden sharpshooter-upgrade effect

Current `Arina` starting skills:

- `basic diplomacy`
- `basic wisdom`
- `basic logistics`

Current `spellResearch` settings:

- `spellResearchCostMultiplierPerReroll: [0, 0, 0, 0, 0]`
- `spellResearchPerDay: [99, 99, 99, 99, 99]`
- `spellResearchCost` level 1-3: `1000 gold`
- `spellResearchCost` level 4-5: `1000 gold + 1 mercury + 1 sulfur + 1 crystal + 1 gems`

Current `difficulty.json` `human.king.resources`:

- `wood: 90`
- `mercury: 90`
- `ore: 90`
- `sulfur: 90`
- `crystal: 90`
- `gems: 90`
- `gold: 999990`

Current `gameConfig.json` related values:

- `heroes.baseScoutingRange: 7`
- `heroes.specialtySecondarySkillGrowth: 10`
- `heroes.specialtyCreatureGrowth: 40`
- `heroes.levelupTotalSkillsAmount: 4`
- `heroes.levelupUpgradedSkillsAmount: 2`
- `towns.buildingsPerTurnCap: 5`

Current `slayer` expert setting:

- Added `range: "x"` under `expert`
- This push records the actual committed config as-is

## 2026-06-09 22:42:21

### 7. Fairy dependency, spell research, and Disrupting Ray update

Source files:

- [Heroes of Might and Magic III Expansion 1.83/Mods/Fairy/mod.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/Fairy/mod.json:1)
- [Heroes of Might and Magic III Expansion 1.83/Mods/hota/Mods/spellResearch/mod.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/hota/Mods/spellResearch/mod.json:16)
- [Heroes of Might and Magic III Expansion 1.83/config/spells/timed.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/config/spells/timed.json:648)

Current `Fairy` mod dependency list:

- `hota.highlandsterrain`
- `hota.factory`
- `courtyard`
- `newoldspells+`
- `tidesofwar`
- `new pavilion.new pavillion`

Current `HotA spellResearch` override:

- `spellResearch: true`
- `spellResearchCostMultiplierPerReroll: [0, 0, 0, 0, 0]`
- `spellResearchPerDay: [10, 10, 10, 10, 10]`
- `spellResearchCost` changed to:
  - level 1: `1000 gold + 1 mercury + 1 sulfur + 1 crystal + 1 gems`
  - level 2: `1000 gold + 1 mercury + 1 sulfur + 1 crystal + 1 gems`
  - level 3: `1000 gold + 1 mercury + 1 sulfur + 1 crystal + 1 gems`
  - level 4: `1000 gold + 1 mercury + 2 sulfur + 1 crystal + 2 gems`
  - level 5: `1000 gold + 2 mercury + 2 sulfur + 2 crystal + 2 gems`

Current `disruptingRay` expert-level behavior:

- Added `range: "X"` under `expert`
- Result: `毁灭之光 / disruptingRay` is now group-targeted at expert mastery
- Base and advanced remain single-target with cumulative permanent defense reduction

## 2026-06-07 14:32:00

### 6. Elrisa starting spellbook cleanup

Source file:

- [Heroes of Might and Magic III Expansion 1.83/Mods/Fairy/content/config/fairy/heroes/ft15_elrisa.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/Fairy/content/config/fairy/heroes/ft15_elrisa.json:27)

Current `Elrisa` specialty text and spellbook setup:

- Specialty description updated to `初始即掌握所�?-3级魔法`
- Tooltip updated to `All 1-3 level spells in spellbook.`
- Starting `spellbook` cleaned into a unique 1-3 level spell list

Current `spellbook` now includes these confirmed spell ids:

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

### 5. Scholar skill and Arina diplomacy changes

Source files:

- [Heroes of Might and Magic III Expansion 1.83/config/skills.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/config/skills.json:551)
- [Heroes of Might and Magic III Expansion 1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json:7)

Current `scholar` configuration:

- `basic: LEARN_MEETING_SPELL_LIMIT = 3`
- `advanced: LEARN_MEETING_SPELL_LIMIT = 4`
- `expert: LEARN_MEETING_SPELL_LIMIT = 5`

Current `Arina` diplomacy specialty configuration:

- Base specialty remains `secondary: diplomacy`
- Added custom bonus: `WANDERING_CREATURES_JOIN_BONUS +3`
- Updated description to match the mechanic:
  - Reduces surrender cost by `5% per level`
  - Reduces neutral creature hostility by `3`
- Updated tooltip to include both surrender discount and neutral-creature hostility reduction

## 2026-06-07 12:29:19

### 1. Hero system baseline reference

Source file:

- [Heroes of Might and Magic III Expansion 1.83/config/gameConfig.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/config/gameConfig.json:507)

Current base `heroes` defaults in `gameConfig.json`:

- `tavernInvite: false`
- `baseScoutingRange: 5`
- `specialtySecondarySkillGrowth: 5`
- `specialtyCreatureGrowth: 5`
- `levelupTotalSkillsAmount: 2`
- `levelupUpgradedSkillsAmount: 1`

This file is kept here as the engine baseline reference. Active gameplay behavior may still be overridden by enabled mods.

### 2. HotA balance override

Source file:

- [Heroes of Might and Magic III Expansion 1.83/Mods/hota/Mods/gameBalance/mod.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/hota/Mods/gameBalance/mod.json:8)

Current HotA `heroes` override:

- `tavernInvite: true`
- `specialtyCreatureGrowth: 40`
- `specialtySecondarySkillGrowth: 10`
- `levelupTotalSkillsAmount: 4`
- `levelupUpgradedSkillsAmount: 2`
- `baseScoutingRange: 7`

Notes:

- This override replaces the baseline values from `gameConfig.json` while the `hota.gamebalance` submod is enabled.
- Earlier invalid field names such as `secondarySkillGrowth` and `creatureSpecialtyGrowth` were corrected to schema-valid names.

### 3. Necromancy artifact changes

Source file:

- [Heroes of Might and Magic III Expansion 1.83/config/artifacts.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/config/artifacts.json:833)

Necromancy-related artifact configuration currently present:

- `amuletOfTheUndertaker`: `UNDEAD_RAISE_PERCENTAGE +5`
- `vampiresCowl`: `UNDEAD_RAISE_PERCENTAGE +10`
- `deadMansBoots`: `UNDEAD_RAISE_PERCENTAGE +15`

Combined artifact:

- [cloakOfTheUndeadKing](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/config/artifacts.json:1944)

Current `cloakOfTheUndeadKing` necromancy upgrade chain:

- `skeleton -> creature.skeleton`
- `walkingDead -> creature.wight`
- `wight -> creature.lich`
- `lich -> creature.dreadKnight`

Artifact components:

- `amuletOfTheUndertaker`
- `vampiresCowl`
- `deadMansBoots`

### 4. Summon Ghost spell changes

Source file:

- [Heroes of Might and Magic III Expansion 1.83/Mods/tidesOfWar/content/config/spells/summonGhost/summonGhost.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/tidesOfWar/content/config/spells/summonGhost/summonGhost.json:1)

Current spell configuration for `summonGhost` / `召唤恶灵`:

- Spell level: `5`
- Base power: `10`
- Base gain chance: `2`
- Faction gain chance:
  - `necropolis: 12`
  - `dungeon: 6`
  - `inferno: 6`
- School flags:
  - `earth: true`
  - `air/fire/water: false`
- Mana cost by mastery:
  - `none: 25`
  - `base: 20`
- Summoned unit id: `tidesofwar:mdtGhost`
- Summon type:
  - `exclusive: false`
  - `permanent: true`
- Power scaling by mastery:
  - `none: 1`
  - `basic: 2`
  - `advanced: 3`
  - `expert: 4`

Description text:

- `"{召唤恶灵}\r\n\r\n召唤恶灵参战`
