# LSF Update Log

## 2026-06-07 14:32:00

### 6. Elrisa starting spellbook cleanup

Source file:
- [Heroes of Might and Magic III Expansion 1.83/Mods/Fairy/content/config/fairy/heroes/ft15_elrisa.json](/d:/coding/heros3exp/Heroes%20of%20Might%20and%20Magic%20III%20Expansion%201.83/Mods/Fairy/content/config/fairy/heroes/ft15_elrisa.json:27)

Current `Elrisa` specialty text and spellbook setup:
- Specialty description updated to `初始即掌握所有1-3级魔法`
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
- `"{召唤恶灵}\r\n\r\n召唤恶灵参战。"`
