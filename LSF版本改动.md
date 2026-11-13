# LSF 版本改动

> 整理日期：2026-11-13。主游戏基准：`da29b328`（2026-11-12）；本地 `main` 与 `origin/main` 一致。

## 阅读说明

本文件重整 [LSFupdatelog.md](LSFupdatelog.md)，从本地可见的90个主游戏提交（2026-06-03至2026-11-12）中筛选实际功能变化，另核对独立引擎分支。纯文字修订不作为版本改动收录。按对象归并，不再把同一英雄拆散成多条流水。历史旧值不代表当前值，正文以当前配置和最近完整初始化日志的加载顺序合并后为准。

- 英雄章节逐人列出19位有能力或其他独立配置改动的英雄；4位仅放开默认特殊英雄限制的英雄合并为单独列表，不再逐人展开。另涵盖8项实际生效的技能改动与23项直接改动法术及关联效果。只受全局参数影响的英雄不单列；纯文字修订不收录本文件。
- “完整状态”指当前合并后的静态英雄定义，包括原有保留特长、初始技能／部队／法术书、限定目标和成长参数；不是任意地图中某个存档实例的即时属性。各对象附可展开快照，方便核查遗漏的隐藏bonus。
- 依据最近完整启动日志重建74个加载模组、452位英雄；日志顺序不是本次实际启动验收。验证缓存validatedMods不是可靠启用清单。未注册的磁盘文件与有效加载链分开列出。
- Git记录的是提交而非每次push事件；“过去所有push”在此解释为本地origin/main可达的全部提交及独立引擎本地远端分支。未联网fetch，不能保证涵盖远端新增／已删除分支／被强推丢失的历史。
- 本次仅整理文档与审计，不修改游戏配置、描述、程序或存档，不编译、不推送。发现描述不一致会明确列出，而不是把建议当作已修复。
- JSON对象通常递归合并，删除覆盖文件中的某键不等于删除基础键；`#override`会整体替换指定节点。英雄初始化数据可能被旧存档或地图自定义保留，修改后通常需重启并新建英雄验证。

## 目录

- [英雄改动](#英雄改动)
- [放开默认特殊英雄限制](#放开默认特殊英雄限制)
- [技能改动](#技能改动)
- [魔法改动](#魔法改动)
- [全局参数](#全局参数)
- [生物与宝物](#生物与宝物)
- [建筑与战役](#建筑与战役)
- [引擎与界面](#引擎与界面)
- [加载与维护](#加载与维护)
- [描述检查汇总](#描述检查汇总)
- [提交与覆盖索引](#提交与覆盖索引)

## 英雄改动

统一语义：通用兵种特长成长默认40%，通用辅助技能特长每级相对增强10%，显式stepSize或自定义bonus可例外。`TIMES_HERO_LEVEL`为随等级增长；`TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL`还除以兵种等级。`OPENING_BATTLE_SPELL.val`是持续回合，`addInfo=3`是专家级，缺省也按专家处理；`SPELL.val`是手动施法最低熟练度，0／1／2／3分别无／初／中／专家，不能与开场持续时间混淆。

分类原则：本章只按英雄记录能力或其他独立配置变化；如果只有special从true改为false，则合并到“放开默认特殊英雄限制”列表，不逐人展开原有能力。不收录纯文字修订。通用兵种成长40%、通用辅助技能特长每级10%统一记入“全局参数”，不逐人重复列条目。保留英雄的完整状态可以包含原有通用特长；当前说明与描述检查仅用于解释、核对实际功能，不作为独立改动。

### 薇奥拉（`asylum:k_viola`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：保留催眠特长，最终hypnotize系数10，并有深渊号角法术。新增每天直接往背包发1件随机宝物，从第二天起发放，允许普通组合宝物与有效卷轴，不要求附近有空地；不自动装备，背包满跳过。依赖自定义 DAILY_RANDOM_ARTIFACT 的新引擎。

- 可用性：`special=false`（缺省按非特殊）；职业：`mentalist`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"expert","skill":"wisdom"},{"level":"basic","skill":"logistics"}]。
- 初始法术书：["hypnotize","asylum:hornSpell"]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `hypnotize` | 法术特长随等级增强 `SPECIAL_SPELL_LEV`；对象／属性：`"spell.hypnotize"`；值：`10`；成长规则：`"TIMES_HERO_LEVEL"` |
| `dailyRandomArtifact` | 特殊效果 `extra creatures specialty:DAILY_RANDOM_ARTIFACT`；值：`1`；数值方式：`"BASE_NUMBER"` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：蛊惑人心。
- 当前说明：施放蛊惑人心时，当提升和目标生物等级相同个英雄等级时，可以多影响10%血量的单位。从第二天起，每个新日向背包获得1件随机宝物；不受地图宝物禁用列表限制，包含普通组合宝物与随机法术卷轴。排除引擎标记为SPECIAL的特殊类宝物（普通卷轴除外，包括圣杯、魔法书等）以及战争机器；背包满时当天跳过，不补发。此效果需要支持每日宝物特技的新引擎。
- 当前tooltip：Spell bonus: Hypnotize

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/asylum/content/config/asylum/heroes/K_viola.json](<Hero3Expasion1.83/Mods/asylum/content/config/asylum/heroes/K_viola.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/spellhero.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/spellhero.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-06-13 `892590ca`：`skills`：`[{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"logistics"}]` → `[{"level":"expert","skill":"wisdom"},{"level":"basic","skill":"logistics"}]`；`spellbook`：`["hypnotize"]` → `["hypnotize","asylum:hornSpell"]`。
- 2026-11-05 `e6be84b8`：`specialty.bonuses.dailyRandomArtifact.type`：`null` → `"extra creatures specialty:DAILY_RANDOM_ARTIFACT"`；`specialty.bonuses.dailyRandomArtifact.val`：`null` → `1`；`specialty.bonuses.dailyRandomArtifact.valueType`：`null` → `"BASE_NUMBER"`；`texts.specialty.description`：`"施放蛊惑人心时，当提升和目标生物等级相同个英雄等级时，可以多影响10%血量的单位。"` → `"施放蛊惑人心时，当提升和目标生物等级相同个英雄等级时，可以多影响10%血量的单位。从第二天起，每个新日向背包获得1件随机宝物；不受地图宝物禁用列表限制，包含普通组合宝物与随机法术卷轴。排除引擎标记为SPECIAL的特殊类宝物（普通卷轴除外，包括圣杯、魔法书等）以及战争机器；背包满时当天跳过，不补发。此效果需要支持每日宝物特技的新引擎。"`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "class": "mentalist",
 "female": true,
 "texts": {
  "name": "薇奥拉",
  "biography": "薇奥拉是从梦境中穿越到现实的女巫。她认为控制人的思想首先需要有魅力。所以如何魅惑对手成为了她的必修课。当然她也很享受从梦境进入现实的感觉。她认为只要打破梦境到现实的那条界限，那两个不相干的世界将合二为一。",
  "specialty": {
   "description": "施放蛊惑人心时，当提升和目标生物等级相同个英雄等级时，可以多影响10%血量的单位。从第二天起，每个新日向背包获得1件随机宝物；不受地图宝物禁用列表限制，包含普通组合宝物与随机法术卷轴。排除引擎标记为SPECIAL的特殊类宝物（普通卷轴除外，包括圣杯、魔法书等）以及战争机器；背包满时当天跳过，不补发。此效果需要支持每日宝物特技的新引擎。",
   "tooltip": "Spell bonus: Hypnotize",
   "name": "蛊惑人心"
  }
 },
 "images": {
  "specialtySmall": "asylum/heroes/viola/UN32T206.bmp",
  "specialtyLarge": "asylum/heroes/viola/UN44T206.bmp",
  "large": "asylum/heroes/viola/HPL002MN.bmp",
  "small": "asylum/heroes/viola/HPS002MN.bmp"
 },
 "spellbook": [
  "hypnotize",
  "asylum:hornSpell"
 ],
 "army": [
  {
   "creature": "darksquire",
   "max": 30,
   "min": 20
  },
  {
   "creature": "winddancer",
   "max": 8,
   "min": 5
  },
  {
   "creature": "shadowhunter",
   "max": 5,
   "min": 3
  }
 ],
 "skills": [
  {
   "level": "expert",
   "skill": "wisdom"
  },
  {
   "level": "basic",
   "skill": "logistics"
  }
 ],
 "specialty": {
  "bonuses": {
   "hypnotize": {
    "subtype": "spell.hypnotize",
    "type": "SPECIAL_SPELL_LEV",
    "updater": "TIMES_HERO_LEVEL",
    "val": 10
   },
   "dailyRandomArtifact": {
    "type": "extra creatures specialty:DAILY_RANDOM_ARTIFACT",
    "val": 1,
    "valueType": "BASE_NUMBER"
   }
  }
 }
}
```

</details>

### 琳恩（`asylum:p_Lynn`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：开场专家欢欣鼓舞，己方符合条件全体士气+8，持续3回合；初始书与bind改为mirth，手动bind保留初级1。大小特长图标改为欢欣鼓舞。不是蕾娜塔的50回合配置。

- 可用性：`special=false`（缺省按非特殊）；职业：`mentalist`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"pathfinding"}]。
- 初始法术书：["mirth"]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `choir` | 开场自动施法 `OPENING_BATTLE_SPELL`；对象／属性：`"spell.mirth"`；值：`3`；数值方式：`"BASE_NUMBER"`；附加参数：`3` |
| `bind` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.mirth"`；值：`1`；数值方式：`"BASE_NUMBER"` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：欢欣鼓舞。
- 当前说明：战斗开始时自动施放专家级欢欣鼓舞，使符合条件的己方全体士气+8，持续3回合；实际士气受游戏上下限限制。自身也可施放欢欣鼓舞。
- 当前tooltip：开场自动施放专家级欢欣鼓舞，持续3回合。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/asylum/content/config/asylum/heroes/P_lynn.json](<Hero3Expasion1.83/Mods/asylum/content/config/asylum/heroes/P_lynn.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-11-12 `da29b328`：`images.specialtyLarge`：`"asylum/heroes/lynn/UN44T211.bmp"` → `"asylum/heroes/lynn/mirth44.png"`；`images.specialtySmall`：`"asylum/heroes/lynn/UN32T211.bmp"` → `"asylum/heroes/lynn/mirth32.png"`；`specialty.bonuses.bind.subtype`：`"spell.sorrow"` → `"spell.mirth"`；`specialty.bonuses.choir.addInfo`：`null` → `3`；`specialty.bonuses.choir.subtype`：`"spell.sorrow"` → `"spell.mirth"`；`spellbook`：`["sorrow"]` → `["mirth"]`；`texts.specialty.description`：`"开场可以施放悲痛欲绝。"` → `"战斗开始时自动施放专家级欢欣鼓舞，使符合条件的己方全体士气+8，持续3回合；实际士气受游戏上下限限制。自身也可施放欢欣鼓舞。"`；`texts.specialty.name`：`"悲痛欲绝"` → `"欢欣鼓舞"`；`texts.specialty.tooltip`：`"Spell bonus: Sorrow"` → `"开场自动施放专家级欢欣鼓舞，持续3回合。"`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "class": "mentalist",
 "female": true,
 "texts": {
  "name": "琳恩",
  "biography": "因为家乡的战乱，琳恩在很小的时候就和姐姐蕾娜塔失散了。在悲伤中度过童年的她将这种悲痛转换成了魔力。当然寻找姐姐也是她能够支撑自己活下去的目标。就算是和邪恶力量合作，她也不会犹豫。",
  "specialty": {
   "description": "战斗开始时自动施放专家级欢欣鼓舞，使符合条件的己方全体士气+8，持续3回合；实际士气受游戏上下限限制。自身也可施放欢欣鼓舞。",
   "tooltip": "开场自动施放专家级欢欣鼓舞，持续3回合。",
   "name": "欢欣鼓舞"
  }
 },
 "images": {
  "specialtySmall": "asylum/heroes/lynn/mirth32.png",
  "specialtyLarge": "asylum/heroes/lynn/mirth44.png",
  "large": "asylum/heroes/lynn/HPL007MN.bmp",
  "small": "asylum/heroes/lynn/HPS007MN.bmp"
 },
 "spellbook": [
  "mirth"
 ],
 "army": [
  {
   "creature": "darksquire",
   "max": 30,
   "min": 20
  },
  {
   "creature": "winddancer",
   "max": 8,
   "min": 5
  },
  {
   "creature": "shadowhunter",
   "max": 5,
   "min": 3
  }
 ],
 "skills": [
  {
   "level": "basic",
   "skill": "wisdom"
  },
  {
   "level": "basic",
   "skill": "pathfinding"
  }
 ],
 "specialty": {
  "bonuses": {
   "choir": {
    "type": "OPENING_BATTLE_SPELL",
    "subtype": "spell.mirth",
    "val": 3,
    "addInfo": 3,
    "valueType": "BASE_NUMBER"
   },
   "bind": {
    "subtype": "spell.mirth",
    "type": "SPELL",
    "val": 1,
    "valueType": "BASE_NUMBER"
   }
  }
 }
}
```

</details>

### 加布里埃尔（`cathedral:sveneld`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：圣灵血鬼：敌方幸运-2，开场己方全体吸血鬼化与圣灵佑佐50回合；圣灵佑佐状态下，GENERAL_DAMAGE_PREMY系数10按英雄等级／兵种等级成长。

- 可用性：`special=false`（缺省按非特殊）；职业：`cthbishop`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"earthMagic"}]。
- 初始法术书：["slow"]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `luckReduce` | 幸运 `LUCK`；值：`-2`；作用限制：`["OPPOSITE_SIDE"]`；传播范围：`"BATTLE_WIDE"`；叠加组：`"面对-2运气特长的英雄"` |
| `bless` | 伤害增幅 `GENERAL_DAMAGE_PREMY`；值：`10`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"HAS_ANOTHER_BONUS_LIMITER","bonusSourceType":"SPELL_EFFECT","bonusSourceID":"spell.bless"}]` |
| `vampirismMass` | 特殊附魔特长 `SPECIAL_PECULIAR_ENCHANT`；对象／属性：`"spell.vampirism"`；附加参数：`0` |
| `openingBless` | 开场自动施法 `OPENING_BATTLE_SPELL`；对象／属性：`"spell.bless"`；值：`50`；数值方式：`"BASE_NUMBER"` |
| `openingVampirism` | 开场自动施法 `OPENING_BATTLE_SPELL`；对象／属性：`"spell.vampirism"`；值：`50`；数值方式：`"BASE_NUMBER"` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：圣灵血鬼。
- 当前说明：战斗中使敌人运气-2。师从阿德拉的圣灵佑佐特长。开局依次为己方全体释放圣灵佑佐和吸血鬼化，均持续50回合。
- 当前tooltip：Creature Bonus: Nerubians

**额外校对结论**：主描述已写圣灵佑佐、吸血鬼化及50回合，但tooltip仍指向旧的兵种特长“Nerubians”，没有同步当前特长名称。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/cathedral/Content/config/cathedral/heroes/Sveneld.json](<Hero3Expasion1.83/Mods/cathedral/Content/config/cathedral/heroes/Sveneld.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/cathedral.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/cathedral.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-08-02 `b9376775`：`specialty.bonuses.luckReduce.limiters`：`null` → `["OPPOSITE_SIDE"]`；`specialty.bonuses.luckReduce.propagationUpdater`：`null` → `"BONUS_OWNER_UPDATER"`；`specialty.bonuses.luckReduce.propagator`：`null` → `"BATTLE_WIDE"`；`specialty.bonuses.luckReduce.stacking`：`null` → `"面对-1运气特长的英雄"`；`specialty.bonuses.luckReduce.type`：`null` → `"LUCK"`；`specialty.bonuses.luckReduce.val`：`null` → `-2`；`specialty.bonuses.openingVampirism.subtype`：`null` → `"spell.vampirism"`；`specialty.bonuses.openingVampirism.type`：`null` → `"OPENING_BATTLE_SPELL"`；`specialty.bonuses.openingVampirism.val`：`null` → `50`；`specialty.bonuses.openingVampirism.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.vampirismMass.addInfo`：`null` → `0`；`specialty.bonuses.vampirismMass.subtype`：`null` → `"spell.vampirism"`；`specialty.bonuses.vampirismMass.type`：`null` → `"SPECIAL_PECULIAR_ENCHANT"`；`texts.specialty.description`：`null` → `"战斗中使敌人运气-2。开局为己方全体释放吸血鬼化，持续50回合。"`；`texts.specialty.name`：`null` → `"-2运气"`。
- 2026-09-20 `7fb29ac2`：`specialty.bonuses.bless.limiters`：`null` → `[{"type":"HAS_ANOTHER_BONUS_LIMITER","bonusSourceType":"SPELL_EFFECT","bonusSourceID":"spell.bless"}]`；`specialty.bonuses.bless.type`：`null` → `"GENERAL_DAMAGE_PREMY"`；`specialty.bonuses.bless.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.bless.val`：`null` → `10`；`specialty.bonuses.luckReduce.stacking`：`"面对-1运气特长的英雄"` → `"面对-2运气特长的英雄"`；`specialty.bonuses.openingBless.subtype`：`null` → `"spell.bless"`；`specialty.bonuses.openingBless.type`：`null` → `"OPENING_BATTLE_SPELL"`；`specialty.bonuses.openingBless.val`：`null` → `50`；`specialty.bonuses.openingBless.valueType`：`null` → `"BASE_NUMBER"`；`texts.specialty.description`：`"战斗中使敌人运气-2。开局为己方全体释放吸血鬼化，持续50回合。"` → `"战斗中使敌人运气-2。师从阿德拉的圣灵佑佐特长。开局依次为己方全体释放圣灵佑佐和吸血鬼化，均持续50回合。"`；`texts.specialty.name`：`"-2运气"` → `"圣灵血鬼"`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "class": "cthbishop",
 "female": true,
 "images": {
  "specialtySmall": "cathedral/heroes/sveneld/spec-small.bmp",
  "specialtyLarge": "cathedral/heroes/sveneld/spec-large.bmp",
  "large": "cathedral/heroes/sveneld/portrait-large.bmp",
  "small": "cathedral/heroes/sveneld/portrait-small.bmp"
 },
 "army": [
  {
   "creature": "cthAttackDog",
   "min": 15,
   "max": 25
  },
  {
   "creature": "cthSlayer",
   "min": 6,
   "max": 9
  },
  {
   "creature": "cthWitchHunter",
   "min": 3,
   "max": 6
  }
 ],
 "skills": [
  {
   "level": "basic",
   "skill": "wisdom"
  },
  {
   "level": "basic",
   "skill": "earthMagic"
  }
 ],
 "texts": {
  "name": "加布里埃尔",
  "biography": "加布里埃尔是一位牧师的女儿。当年还是孩子的她就目睹了这场灾难。在漆黑的小屋中，她整整度过了几个星期。没有人比她更痛恨罪恶。她选择成为一名牧师，当然是为了惩罚所有的罪恶者。",
  "specialty": {
   "description": "战斗中使敌人运气-2。师从阿德拉的圣灵佑佐特长。开局依次为己方全体释放圣灵佑佐和吸血鬼化，均持续50回合。",
   "tooltip": "Creature Bonus: Nerubians",
   "name": "圣灵血鬼"
  }
 },
 "spellbook": [
  "slow"
 ],
 "specialty": {
  "bonuses": {
   "luckReduce": {
    "type": "LUCK",
    "val": -2,
    "propagator": "BATTLE_WIDE",
    "propagationUpdater": "BONUS_OWNER_UPDATER",
    "limiters": [
     "OPPOSITE_SIDE"
    ],
    "stacking": "面对-2运气特长的英雄"
   },
   "bless": {
    "type": "GENERAL_DAMAGE_PREMY",
    "limiters": [
     {
      "type": "HAS_ANOTHER_BONUS_LIMITER",
      "bonusSourceType": "SPELL_EFFECT",
      "bonusSourceID": "spell.bless"
     }
    ],
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "val": 10
   },
   "vampirismMass": {
    "type": "SPECIAL_PECULIAR_ENCHANT",
    "subtype": "spell.vampirism",
    "addInfo": 0
   },
   "openingBless": {
    "type": "OPENING_BATTLE_SPELL",
    "subtype": "spell.bless",
    "val": 50,
    "valueType": "BASE_NUMBER"
   },
   "openingVampirism": {
    "type": "OPENING_BATTLE_SPELL",
    "subtype": "spell.vampirism",
    "val": 50,
    "valueType": "BASE_NUMBER"
   }
  }
 }
}
```

</details>

### 凯瑟琳（`core:catherine`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：保留剑士／十字军通用兵种特长；新增枪兵、戟兵、弓箭手、神射手、剑士转十字军。特殊升级不是免费转化，仍走升级收费流程。通用特长默认成长40%，速度+1；12级配4级剑士时，裸攻10／防12的特长额外攻12／防15，再加英雄攻防。

- 通用兵种特长目标：`swordsman`；成长参数：`40`；未显式重写时按通用机制给予目标及升级形态速度+1。
- 可用性：`special=false`（缺省按非特殊）；职业：`knight`。地图英雄白名单仍可能禁用。
- 初始技能：[{"skill":"leadership","level":"basic"},{"skill":"offence","level":"basic"}]。
- 初始法术书：[]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `pikeman2crusader` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.pikeman"`；附加参数：`"creature.crusader"` |
| `halberdier2crusader` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.halberdier"`；附加参数：`"creature.crusader"` |
| `archer2crusader` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.archer"`；附加参数：`"creature.crusader"` |
| `marksman2crusader` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.marksman"`；附加参数：`"creature.crusader"` |
| `swordsman2crusader` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.swordsman"`；附加参数：`"creature.crusader"` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：剑士。
- 当前说明：剑士及十字军随英雄等级提升攻击力和防御力，速度+1。<br><br>可将枪兵、戟兵、弓箭手、神射手和剑士升级为十字军。
- 当前tooltip：生物奖励：剑士

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/heroes/special.json](<Hero3Expasion1.83/config/heroes/special.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/castle.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/castle.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-11-08 `61910a66`：`specialty.bonuses.archer2crusader.addInfo`：`null` → `"creature.crusader"`；`specialty.bonuses.archer2crusader.subtype`：`null` → `"creature.archer"`；`specialty.bonuses.archer2crusader.type`：`null` → `"SPECIAL_UPGRADE"`；`specialty.bonuses.halberdier2crusader.addInfo`：`null` → `"creature.crusader"`；`specialty.bonuses.halberdier2crusader.subtype`：`null` → `"creature.halberdier"`；`specialty.bonuses.halberdier2crusader.type`：`null` → `"SPECIAL_UPGRADE"`；`specialty.bonuses.marksman2crusader.addInfo`：`null` → `"creature.crusader"`；`specialty.bonuses.marksman2crusader.subtype`：`null` → `"creature.marksman"`；`specialty.bonuses.marksman2crusader.type`：`null` → `"SPECIAL_UPGRADE"`；`specialty.bonuses.pikeman2crusader.addInfo`：`null` → `"creature.crusader"`；`specialty.bonuses.pikeman2crusader.subtype`：`null` → `"creature.pikeman"`；`specialty.bonuses.pikeman2crusader.type`：`null` → `"SPECIAL_UPGRADE"`；`specialty.bonuses.swordsman2crusader.addInfo`：`null` → `"creature.crusader"`；`specialty.bonuses.swordsman2crusader.subtype`：`null` → `"creature.swordsman"`；`specialty.bonuses.swordsman2crusader.type`：`null` → `"SPECIAL_UPGRADE"`；`specialty.creature`：`null` → `"swordsman"`；`texts.specialty.description`：`"每4级提高剑士及其升级形态20%的攻击力和防御力。速度+1。"` → `"剑士及十字军随英雄等级提升攻击力和防御力，速度+1。\n\n可将枪兵、戟兵、弓箭手、神射手和剑士升级为十字军。"`。
- 2026-11-08 `61910a66`：`special`：`true` → `false`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "texts": {
  "name": "凯瑟琳",
  "specialty": {
   "name": "剑士",
   "tooltip": "生物奖励：剑士",
   "description": "剑士及十字军随英雄等级提升攻击力和防御力，速度+1。\n\n可将枪兵、戟兵、弓箭手、神射手和剑士升级为十字军。"
  }
 },
 "index": 146,
 "class": "knight",
 "female": true,
 "special": false,
 "skills": [
  {
   "skill": "leadership",
   "level": "basic"
  },
  {
   "skill": "offence",
   "level": "basic"
  }
 ],
 "specialty": {
  "creature": "swordsman",
  "bonuses": {
   "pikeman2crusader": {
    "type": "SPECIAL_UPGRADE",
    "subtype": "creature.pikeman",
    "addInfo": "creature.crusader"
   },
   "halberdier2crusader": {
    "type": "SPECIAL_UPGRADE",
    "subtype": "creature.halberdier",
    "addInfo": "creature.crusader"
   },
   "archer2crusader": {
    "type": "SPECIAL_UPGRADE",
    "subtype": "creature.archer",
    "addInfo": "creature.crusader"
   },
   "marksman2crusader": {
    "type": "SPECIAL_UPGRADE",
    "subtype": "creature.marksman",
    "addInfo": "creature.crusader"
   },
   "swordsman2crusader": {
    "type": "SPECIAL_UPGRADE",
    "subtype": "creature.swordsman",
    "addInfo": "creature.crusader"
   }
  }
 }
}
```

</details>

### 珍尼（`core:gem`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：保留急救术特长，新增双倍防御术成长与全军50点再生，并获得八种原有药剂的专家／纯化施放权限（不是新增八个药剂魔法）。防御术必须另行学习：基础减伤×(1+0.2×英雄等级)，专家基础15%时10级45%、20级75%、30级理论105%；未添加封顶，超过100%不能解释为回血。再生只能治疗存活个体，不复活；失败的复活帐篷方案已删除。

- 通用技能特长：`firstAid`；每级相对成长参数：`10`，不是每级直接加相同百分点。
- 可用性：`special=false`（缺省按非特殊）；职业：`druid`。地图英雄白名单仍可能禁用。
- 初始技能：[{"skill":"wisdom","level":"basic"},{"skill":"firstAid","level":"basic"}]。
- 初始法术书：["summonBoat"]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `doubleArmorerGrowth` | 伤害减免 `GENERAL_DAMAGE_REDUCTION`；对象／属性：`"damageTypeAll"`；值：`20`；数值方式：`"PERCENT_TO_TARGET_TYPE"`；成长规则：`"TIMES_HERO_LEVEL"` |
| `armyRegeneration` | 每队首次行动前再生（不复活） `HP_REGENERATION`；值：`50`；数值方式：`"BASE_NUMBER"` |
| `regenerationPotion` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"courtyard:spell.regenerationPotion"`；值：`3` |
| `weaponPoisonPotion` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"courtyard:spell.weaponPoisonPotion"`；值：`3` |
| `dragonPotion` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"courtyard:spell.dragonPotion"`；值：`3` |
| `toughnessPotion` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"courtyard:spell.toughnessPotion"`；值：`3` |
| `etheralPotion` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"courtyard:spell.etheralPotion"`；值：`3` |
| `immortalPotion` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"courtyard:spell.immortalPotion"`；值：`3` |
| `icePotion` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"courtyard:spell.icePotion"`；值：`3` |
| `invPotion` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"courtyard:spell.invPotion"`；值：`3` |

**描述检查**：描述未明确8种药剂均按专家／纯化级施放。双倍防御成长20%是独立自定义bonus，不能拿它与保留的急救术通用特长10%比较并判为文字错误。

- 当前特长名：钢板与急救。
- 当前说明：保留急救术特长。每升一级，额外提高防御术基础减伤效果的20%，需要学会防御术。<br><br>全队获得每回合50点自我再生，在各队本回合首次轮到行动前触发，只治疗存活个体，不复活阵亡数量。<br><br>永久获得再生药水、毒液瓶、龙血瓶、强壮药水、以太提取物、不朽药水、寒冷药水和保护药水的施放能力，无需药剂道具，无药剂使用次数限制；仍占用正常施法机会，目标限制和效果等级按各法术规则结算。
- 当前tooltip：辅助技能奖励：急救术

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/heroes/rampart.json](<Hero3Expasion1.83/config/heroes/rampart.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/rampart.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/rampart.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/specialtyDescriptions.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/specialtyDescriptions.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-11-08 `7bc983db`：`specialty.bonuses.armyRegeneration.type`：`null` → `"HP_REGENERATION"`；`specialty.bonuses.armyRegeneration.val`：`null` → `50`；`specialty.bonuses.armyRegeneration.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.doubleArmorerGrowth.subtype`：`null` → `"damageTypeAll"`；`specialty.bonuses.doubleArmorerGrowth.targetSourceType`：`null` → `"SECONDARY_SKILL"`；`specialty.bonuses.doubleArmorerGrowth.type`：`null` → `"GENERAL_DAMAGE_REDUCTION"`；`specialty.bonuses.doubleArmorerGrowth.updater`：`null` → `"TIMES_HERO_LEVEL"`；`specialty.bonuses.doubleArmorerGrowth.val`：`null` → `20`；`specialty.bonuses.doubleArmorerGrowth.valueType`：`null` → `"PERCENT_TO_TARGET_TYPE"`；`specialty.bonuses.dragonPotion.subtype`：`null` → `"courtyard:spell.dragonPotion"`；`specialty.bonuses.dragonPotion.type`：`null` → `"SPELL"`；`specialty.bonuses.dragonPotion.val`：`null` → `3`；`specialty.bonuses.etheralPotion.subtype`：`null` → `"courtyard:spell.etheralPotion"`；`specialty.bonuses.etheralPotion.type`：`null` → `"SPELL"`；`specialty.bonuses.etheralPotion.val`：`null` → `3`；`specialty.bonuses.icePotion.subtype`：`null` → `"courtyard:spell.icePotion"`；`specialty.bonuses.icePotion.type`：`null` → `"SPELL"`；`specialty.bonuses.icePotion.val`：`null` → `3`；`specialty.bonuses.immortalPotion.subtype`：`null` → `"courtyard:spell.immortalPotion"`；`specialty.bonuses.immortalPotion.type`：`null` → `"SPELL"`；`specialty.bonuses.immortalPotion.val`：`null` → `3`；`specialty.bonuses.invPotion.subtype`：`null` → `"courtyard:spell.invPotion"`；`specialty.bonuses.invPotion.type`：`null` → `"SPELL"`；`specialty.bonuses.invPotion.val`：`null` → `3`；`specialty.bonuses.regenerationPotion.subtype`：`null` → `"courtyard:spell.regenerationPotion"`；`specialty.bonuses.regenerationPotion.type`：`null` → `"SPELL"`；`specialty.bonuses.regenerationPotion.val`：`null` → `3`；`specialty.bonuses.toughnessPotion.subtype`：`null` → `"courtyard:spell.toughnessPotion"`；`specialty.bonuses.toughnessPotion.type`：`null` → `"SPELL"`；`specialty.bonuses.toughnessPotion.val`：`null` → `3`；`specialty.bonuses.weaponPoisonPotion.subtype`：`null` → `"courtyard:spell.weaponPoisonPotion"`；`specialty.bonuses.weaponPoisonPotion.type`：`null` → `"SPELL"`；`specialty.bonuses.weaponPoisonPotion.val`：`null` → `3`；`specialty.secondary`：`null` → `"firstAid"`；`texts.specialty.name`：`null` → `"钢板与急救"`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "texts": {
  "name": "珍尼",
  "specialty": {
   "name": "钢板与急救",
   "tooltip": "辅助技能奖励：急救术",
   "description": "保留急救术特长。每升一级，额外提高防御术基础减伤效果的20%，需要学会防御术。\n\n全队获得每回合50点自我再生，在各队本回合首次轮到行动前触发，只治疗存活个体，不复活阵亡数量。\n\n永久获得再生药水、毒液瓶、龙血瓶、强壮药水、以太提取物、不朽药水、寒冷药水和保护药水的施放能力，无需药剂道具，无药剂使用次数限制；仍占用正常施法机会，目标限制和效果等级按各法术规则结算。"
  }
 },
 "index": 27,
 "class": "druid",
 "female": true,
 "spellbook": [
  "summonBoat"
 ],
 "skills": [
  {
   "skill": "wisdom",
   "level": "basic"
  },
  {
   "skill": "firstAid",
   "level": "basic"
  }
 ],
 "specialty": {
  "secondary": "firstAid",
  "bonuses": {
   "doubleArmorerGrowth": {
    "type": "GENERAL_DAMAGE_REDUCTION",
    "subtype": "damageTypeAll",
    "val": 20,
    "valueType": "PERCENT_TO_TARGET_TYPE",
    "targetSourceType": "SECONDARY_SKILL",
    "updater": "TIMES_HERO_LEVEL"
   },
   "armyRegeneration": {
    "type": "HP_REGENERATION",
    "val": 50,
    "valueType": "BASE_NUMBER"
   },
   "regenerationPotion": {
    "type": "SPELL",
    "subtype": "courtyard:spell.regenerationPotion",
    "val": 3
   },
   "weaponPoisonPotion": {
    "type": "SPELL",
    "subtype": "courtyard:spell.weaponPoisonPotion",
    "val": 3
   },
   "dragonPotion": {
    "type": "SPELL",
    "subtype": "courtyard:spell.dragonPotion",
    "val": 3
   },
   "toughnessPotion": {
    "type": "SPELL",
    "subtype": "courtyard:spell.toughnessPotion",
    "val": 3
   },
   "etheralPotion": {
    "type": "SPELL",
    "subtype": "courtyard:spell.etheralPotion",
    "val": 3
   },
   "immortalPotion": {
    "type": "SPELL",
    "subtype": "courtyard:spell.immortalPotion",
    "val": 3
   },
   "icePotion": {
    "type": "SPELL",
    "subtype": "courtyard:spell.icePotion",
    "val": 3
   },
   "invPotion": {
    "type": "SPELL",
    "subtype": "courtyard:spell.invPotion",
    "val": 3
   }
  }
 }
}
```

</details>

#### 新增八种原有药剂的施放权限（珍妮专属改动）

**本次新增的是珍妮的施法权限，不是八个新魔法，也没有重做这些药剂本身。**八种药剂均为Courtyard原有内容；提交`7bc983db`给珍妮增加对应的`SPELL` bonus，`val=3`表示按专家／纯化级施放，无需携带药剂车或消耗药剂道具，仍占正常施法机会并遵守目标限制。

以下仅作为珍妮新增能力的效果依据，保留原有药剂的完整配置；不计入“魔法改动”的法术数量。

##### 再生药水（`courtyard:regenerationPotion`）

- 文件：[Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/regenerationPotion.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/regenerationPotion.json>)


<details>
<summary>普通与纯化药剂完整效果、目标与说明</summary>

```json
{
 "type": "combat",
 "targetType": "CREATURE",
 "name": "再生药水",
 "school": {
  "courtPotion": true
 },
 "level": 1,
 "power": 1,
 "defaultGainChance": 0,
 "gainChance": {},
 "animation": {
  "affect": [
   "courtyard/spells/Potions/Regeneration/regenPotion.def"
  ]
 },
 "flags": {
  "positive": true,
  "special": true
 },
 "targetCondition": {
  "noneOf": {
   "bonus.NON_LIVING": "normal",
   "bonus.GARGOYLE": "normal",
   "bonus.MECHANICAL": "normal",
   "bonus.SIEGE_WEAPON": "absolute",
   "bonus.UNDEAD": "normal"
  }
 },
 "graphics": {
  "iconBook": "courtyard/spells/Potions/Regeneration/regenP_book1",
  "iconEffect": "courtyard/spells/Potions/Regeneration/regenP_effect1",
  "iconScroll": "courtyard/spells/Potions/Regeneration/regenP_scroll1",
  "iconScenarioBonus": "courtyard/spells/Potions/Regeneration/regenP_bonus1"
 },
 "sounds": {
  "cast": "courtyard/spells/REGENPOT.wav"
 },
 "levels": {
  "base": {
   "cost": 0,
   "range": "0",
   "power": 1,
   "targetModifier": {
    "smart": true
   }
  },
  "none": {
   "description": "{再生药水}\n\n使单位获得每回合恢复5点生命值的能力。\n\n需要： 药剂车",
   "effects": {
    "regeneration": {
     "val": 5,
     "type": "HP_REGENERATION",
     "duration": "N_TURNS"
    },
    "potionMarkerEffect": {
     "type": "SPECIFIC_SPELL_DAMAGE",
     "subtype": "spell.potionSupply",
     "val": 0,
     "duration": "N_TURNS"
    }
   }
  },
  "basic": {
   "description": "{浓缩再生药水}\n\n使单位获得每回合恢复15点生命值的能力。\n\n需要： 药剂车，初级急救术",
   "effects": {
    "regeneration": {
     "type": "HP_REGENERATION",
     "val": 15,
     "duration": "N_TURNS"
    },
    "potionMarkerEffect": {
     "type": "SPECIFIC_SPELL_DAMAGE",
     "subtype": "spell.potionSupply",
     "val": 0,
     "duration": "N_TURNS"
    }
   }
  },
  "advanced": {
   "description": "{蒸馏再生药水}\n\n使单位获得每回合恢复30点生命值的能力。\n\n需要： 药剂车，中级急救术",
   "effects": {
    "regeneration": {
     "type": "HP_REGENERATION",
     "val": 30,
     "duration": "N_TURNS"
    },
    "potionMarkerEffect": {
     "type": "SPECIFIC_SPELL_DAMAGE",
     "subtype": "spell.potionSupply",
     "val": 0,
     "duration": "N_TURNS"
    }
   }
  },
  "expert": {
   "description": "{纯化再生药水}\n\n使单位获得每回合恢复全部生命值的能力。\n\n需要： 药剂车，高级急救术",
   "effects": {
    "regeneration": {
     "val": 2000,
     "type": "HP_REGENERATION",
     "duration": "N_TURNS"
    },
    "potionMarkerEffect": {
     "type": "SPECIFIC_SPELL_DAMAGE",
     "subtype": "spell.potionSupply",
     "val": 0,
     "duration": "N_TURNS"
    }
   }
  }
 }
}
```

</details>

##### 毒液瓶（`courtyard:weaponPoisonPotion`）

- 文件：[Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/weaponPoisonPotion.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/weaponPoisonPotion.json>)


<details>
<summary>普通与纯化药剂完整效果、目标与说明</summary>

```json
{
 "type": "combat",
 "targetType": "CREATURE",
 "name": "毒液瓶",
 "school": {
  "courtPotion": true
 },
 "level": 1,
 "power": 1,
 "defaultGainChance": 0,
 "gainChance": {},
 "animation": {
  "affect": [
   "courtyard/spells/Potions/Poison/poisonPotion.def"
  ]
 },
 "flags": {
  "positive": true,
  "special": true
 },
 "graphics": {
  "iconBook": "courtyard/spells/Potions/Poison/poisonP_book1",
  "iconEffect": "courtyard/spells/Potions/Poison/poisonP_effect1",
  "iconScroll": "courtyard/spells/Potions/Poison/poisonP_scroll1",
  "iconScenarioBonus": "courtyard/spells/Potions/Poison/poisonP_bonus1"
 },
 "sounds": {
  "cast": "courtyard/spells/POISONPOT.wav"
 },
 "levels": {
  "base": {
   "cost": 0,
   "range": "0",
   "power": 1,
   "targetModifier": {
    "smart": true
   }
  },
  "none": {
   "description": "{毒液瓶}\n\n攻击时有10%的几率使对方部队中毒。\n\n需要： 药剂车",
   "effects": {
    "poison": {
     "type": "SPELL_AFTER_ATTACK",
     "subtype": "spell.poison",
     "val": 10,
     "duration": "N_TURNS"
    }
   }
  },
  "basic": {
   "description": "{浓缩毒液瓶}\n\n攻击时有15%的几率使对方部队中毒。\n\n需要： 药剂车，初级急救术",
   "effects": {
    "poison": {
     "type": "SPELL_AFTER_ATTACK",
     "subtype": "spell.poison",
     "val": 15,
     "duration": "N_TURNS"
    }
   }
  },
  "advanced": {
   "description": "{蒸馏毒液瓶}\n\n攻击时有20%的几率使对方部队中毒。\n\n需要： 药剂车，中级急救术",
   "effects": {
    "poison": {
     "type": "SPELL_AFTER_ATTACK",
     "subtype": "spell.poison",
     "val": 20,
     "duration": "N_TURNS"
    }
   }
  },
  "expert": {
   "description": "{纯化毒液瓶}\n\n攻击时有30%的几率使对方部队中毒。\n\n需要： 药剂车，高级急救术",
   "effects": {
    "poison": {
     "type": "SPELL_AFTER_ATTACK",
     "subtype": "spell.poison",
     "val": 30,
     "duration": "N_TURNS"
    }
   }
  }
 }
}
```

</details>

##### 龙血瓶（`courtyard:dragonPotion`）

- 文件：[Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/dragonPotion.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/dragonPotion.json>)


<details>
<summary>普通与纯化药剂完整效果、目标与说明</summary>

```json
{
 "type": "combat",
 "targetType": "CREATURE",
 "name": "龙血瓶",
 "school": {
  "courtPotion": true
 },
 "level": 2,
 "power": 1,
 "defaultGainChance": 0,
 "gainChance": {},
 "animation": {
  "affect": [
   "courtyard/spells/Potions/Dragon/dragonPotion.def"
  ]
 },
 "flags": {
  "positive": true,
  "special": true
 },
 "targetCondition": {
  "noneOf": {
   "bonus.NON_LIVING": "normal",
   "bonus.GARGOYLE": "normal",
   "bonus.SIEGE_WEAPON": "absolute",
   "bonus.UNDEAD": "normal"
  }
 },
 "graphics": {
  "iconBook": "courtyard/spells/Potions/Dragon/dragonP_book1",
  "iconEffect": "courtyard/spells/Potions/Dragon/dragonP_effect1",
  "iconScroll": "courtyard/spells/Potions/Dragon/dragonP_scroll1",
  "iconScenarioBonus": "courtyard/spells/Potions/Dragon/dragonP_bonus1"
 },
 "sounds": {
  "cast": "courtyard/spells/DRAGONPOT.wav"
 },
 "levels": {
  "base": {
   "cost": 0,
   "range": "0",
   "power": 1,
   "targetModifier": {
    "smart": true
   }
  },
  "none": {
   "description": "{龙血瓶}\n\n选定部队能攻击2格，即拥有龙息特技，并且具有龙的特性。攻击后造成数量/2+15的伤害。\n\n需要： 药剂车，初级急救术",
   "effects": {
    "breathAttack": {
     "type": "TWO_HEX_ATTACK_BREATH",
     "duration": "N_TURNS"
    },
    "isDragon": {
     "type": "DRAGON_NATURE",
     "duration": "N_TURNS"
    },
    "potionMarkerEffect": {
     "type": "SPECIFIC_SPELL_DAMAGE",
     "subtype": "spell.potionSupply",
     "val": 0,
     "duration": "N_TURNS"
    },
    "setAflame": {
     "type": "SPELL_AFTER_ATTACK",
     "addInfo": [
      0,
      2
     ],
     "subtype": "spell.immolationCurse",
     "val": 15,
     "duration": "N_TURNS"
    },
    "setAflameDuration": {
     "type": "CREATURE_ENCHANT_POWER",
     "val": 2,
     "duration": "N_TURNS"
    }
   }
  },
  "basic": {
   "description": "{浓缩龙血瓶}\n\n选定部队能攻击2格，即拥有龙息特技，并且具有龙的特性。攻击后造成数量/2+20的伤害。\n\n需要： 药剂车，中级急救术",
   "effects": {
    "breathAttack": {
     "type": "TWO_HEX_ATTACK_BREATH",
     "duration": "N_TURNS"
    },
    "isDragon": {
     "type": "DRAGON_NATURE",
     "duration": "N_TURNS"
    },
    "setAflame": {
     "type": "SPELL_AFTER_ATTACK",
     "subtype": "spell.immolationCurse",
     "addInfo": [
      1,
      2
     ],
     "val": 20,
     "duration": "N_TURNS"
    },
    "setAflameDuration": {
     "type": "CREATURE_ENCHANT_POWER",
     "val": 2,
     "duration": "N_TURNS"
    },
    "potionMarkerEffect": {
     "type": "SPECIFIC_SPELL_DAMAGE",
     "subtype": "spell.potionSupply",
     "val": 0,
     "duration": "N_TURNS"
    }
   }
  },
  "advanced": {
   "description": "{蒸馏龙血瓶}\n\n选定部队能攻击2格，即拥有龙息特技，并且具有龙的特性。攻击后造成数量/2+25的伤害。\n\n需要： 药剂车，高级急救术",
   "effects": {
    "breathAttack": {
     "type": "TWO_HEX_ATTACK_BREATH",
     "duration": "N_TURNS"
    },
    "isDragon": {
     "type": "DRAGON_NATURE",
     "duration": "N_TURNS"
    },
    "setAflame": {
     "type": "SPELL_AFTER_ATTACK",
     "subtype": "spell.immolationCurse",
     "addInfo": [
      2,
      2
     ],
     "val": 25,
     "duration": "N_TURNS"
    },
    "setAflameDuration": {
     "type": "CREATURE_ENCHANT_POWER",
     "val": 3,
     "duration": "N_TURNS"
    },
    "potionMarkerEffect": {
     "type": "SPECIFIC_SPELL_DAMAGE",
     "subtype": "spell.potionSupply",
     "val": 0,
     "duration": "N_TURNS"
    }
   }
  },
  "expert": {
   "description": "{纯化龙血瓶}\n\n选定部队能攻击2格，即拥有龙息特技，并且具有龙的特性。攻击后造成数量/2+30的伤害。\n\n需要： 药剂车，高级急救术，在魔法平原上",
   "effects": {
    "breathAttack": {
     "type": "TWO_HEX_ATTACK_BREATH",
     "duration": "N_TURNS"
    },
    "isDragon": {
     "type": "DRAGON_NATURE",
     "duration": "N_TURNS"
    },
    "setAflame": {
     "type": "SPELL_AFTER_ATTACK",
     "subtype": "spell.immolationCurse",
     "addInfo": [
      2,
      2
     ],
     "val": 30,
     "duration": "N_TURNS"
    },
    "setAflameDuration": {
     "type": "CREATURE_ENCHANT_POWER",
     "val": 3,
     "duration": "N_TURNS"
    },
    "potionMarkerEffect": {
     "type": "SPECIFIC_SPELL_DAMAGE",
     "subtype": "spell.potionSupply",
     "val": 0,
     "duration": "N_TURNS"
    }
   }
  }
 }
}
```

</details>

##### 强壮药水（`courtyard:toughnessPotion`）

- 文件：[Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/toughnessPotion.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/toughnessPotion.json>)


<details>
<summary>普通与纯化药剂完整效果、目标与说明</summary>

```json
{
 "type": "combat",
 "targetType": "CREATURE",
 "name": "强壮药水",
 "school": {
  "courtPotion": true
 },
 "level": 3,
 "power": 1,
 "defaultGainChance": 0,
 "gainChance": {},
 "animation": {
  "affect": [
   "courtyard/spells/Potions/Toughness/toughnessPotion.def"
  ]
 },
 "flags": {
  "positive": true,
  "special": true
 },
 "targetCondition": {
  "noneOf": {
   "bonus.NON_LIVING": "normal",
   "bonus.GARGOYLE": "normal",
   "bonus.MECHANICAL": "normal",
   "bonus.SIEGE_WEAPON": "absolute",
   "bonus.UNDEAD": "normal"
  }
 },
 "graphics": {
  "iconBook": "courtyard/spells/Potions/Toughness/toughnessP_book1",
  "iconEffect": "courtyard/spells/Potions/Toughness/toughnessP_effect1",
  "iconScroll": "courtyard/spells/Potions/Toughness/toughnessP_scroll1",
  "iconScenarioBonus": "courtyard/spells/Potions/Toughness/toughnessP_bonus1"
 },
 "sounds": {
  "cast": "courtyard/spells/TOUGHNESSPOT.wav"
 },
 "levels": {
  "base": {
   "cost": 0,
   "range": "0",
   "power": 1,
   "targetModifier": {
    "smart": true
   }
  },
  "none": {
   "description": "{强壮药水}\n\n选定部队生命值上限提升25%，伤害提升10%。\n\n需要： 药剂车，中级急救术",
   "effects": {
    "increaseDamage": {
     "val": 10,
     "type": "CREATURE_DAMAGE",
     "subtype": "creatureDamageBoth",
     "valueType": "PERCENT_TO_BASE",
     "duration": "N_TURNS"
    },
    "increaseHealth": {
     "val": 25,
     "type": "STACK_HEALTH",
     "valueType": "PERCENT_TO_BASE",
     "duration": "N_TURNS"
    },
    "potionMarkerEffect": {
     "type": "SPECIFIC_SPELL_DAMAGE",
     "subtype": "spell.potionSupply",
     "val": 0
    }
   }
  },
  "basic": {
   "description": "{浓缩强壮药水}\n\n选定部队生命值上限提升40%，伤害提升15%。\n\n需要： 药剂车，高级急救术",
   "effects": {
    "increaseDamage": {
     "val": 15,
     "type": "CREATURE_DAMAGE",
     "subtype": "creatureDamageBoth",
     "valueType": "PERCENT_TO_BASE",
     "duration": "N_TURNS"
    },
    "increaseHealth": {
     "val": 40,
     "type": "STACK_HEALTH",
     "valueType": "PERCENT_TO_BASE",
     "duration": "N_TURNS"
    },
    "potionMarkerEffect": {
     "type": "SPECIFIC_SPELL_DAMAGE",
     "subtype": "spell.potionSupply",
     "val": 0
    }
   }
  },
  "advanced": {
   "description": "{蒸馏强壮药水}\n\n选定部队生命值上限提升40%，伤害提升20%。",
   "effects": {
    "increaseDamage": {
     "val": 20,
     "type": "CREATURE_DAMAGE",
     "subtype": "creatureDamageBoth",
     "valueType": "PERCENT_TO_BASE",
     "duration": "N_TURNS"
    },
    "increaseHealth": {
     "val": 40,
     "type": "STACK_HEALTH",
     "valueType": "PERCENT_TO_BASE",
     "duration": "N_TURNS"
    },
    "potionMarkerEffect": {
     "type": "SPECIFIC_SPELL_DAMAGE",
     "subtype": "spell.potionSupply",
     "val": 0
    }
   }
  },
  "expert": {
   "description": "{纯化强壮药水}\n\n选定部队生命值上限提升40%，伤害提升25%。\n\n需要： 药剂车，高级急救术，在魔法平原上",
   "effects": {
    "increaseDamage": {
     "val": 25,
     "type": "CREATURE_DAMAGE",
     "subtype": "creatureDamageBoth",
     "valueType": "PERCENT_TO_BASE",
     "duration": "N_TURNS"
    },
    "increaseHealth": {
     "val": 40,
     "type": "STACK_HEALTH",
     "valueType": "PERCENT_TO_BASE",
     "duration": "N_TURNS"
    },
    "potionMarkerEffect": {
     "type": "SPECIFIC_SPELL_DAMAGE",
     "subtype": "spell.potionSupply",
     "val": 0
    }
   }
  }
 }
}
```

</details>

##### 以太提取物（`courtyard:etheralPotion`）

- 文件：[Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/etheralPotion.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/etheralPotion.json>)


<details>
<summary>普通与纯化药剂完整效果、目标与说明</summary>

```json
{
 "type": "combat",
 "targetType": "CREATURE",
 "name": "以太提取物",
 "school": {
  "courtPotion": true
 },
 "level": 4,
 "power": 45,
 "defaultGainChance": 0,
 "gainChance": {},
 "animation": {
  "affect": [
   "courtyard/spells/Potions/Etheral/etheralPotion.def"
  ]
 },
 "flags": {
  "positive": true,
  "special": true
 },
 "targetCondition": {
  "noneOf": {
   "bonus.NON_LIVING": "normal",
   "bonus.GARGOYLE": "normal",
   "bonus.SIEGE_WEAPON": "absolute",
   "bonus.UNDEAD": "normal"
  }
 },
 "graphics": {
  "iconBook": "courtyard/spells/Potions/Etheral/etheralP_book1",
  "iconEffect": "courtyard/spells/Potions/Etheral/etheralP_effect1",
  "iconScroll": "courtyard/spells/Potions/Etheral/etheralP_scroll1",
  "iconScenarioBonus": "courtyard/spells/Potions/Etheral/etheralP_bonus1"
 },
 "sounds": {
  "cast": "courtyard/spells/ETHERALPOT.wav"
 },
 "levels": {
  "base": {
   "cost": 0,
   "range": "0",
   "power": 1,
   "targetModifier": {
    "smart": true
   }
  },
  "none": {
   "description": "{以太提取物}\n\n选定部队获得飞行能力并减少20%所受伤害。\n\n需要： 药剂车，高级急救术",
   "effects": {
    "flying": {
     "type": "FLYING",
     "duration": "N_TURNS"
    },
    "damageReduction": {
     "type": "GENERAL_DAMAGE_REDUCTION",
     "subtype": "damageTypeMelee",
     "val": 20,
     "duration": "N_TURNS"
    },
    "damageReductionShots": {
     "type": "GENERAL_DAMAGE_REDUCTION",
     "subtype": "damageTypeRanged",
     "val": 20,
     "duration": "N_TURNS"
    },
    "magicDamageReduction": {
     "type": "SPELL_DAMAGE_REDUCTION",
     "subtype": "spellSchool.any",
     "val": 20,
     "duration": "N_TURNS"
    },
    "potionMarkerEffect": {
     "type": "SPECIFIC_SPELL_DAMAGE",
     "subtype": "spell.potionSupply",
     "val": 0,
     "duration": "N_TURNS"
    }
   }
  },
  "basic": {
   "description": "{纯化以太提取物}\n\n选定部队获得飞行能力并减少20%所受伤害。"
  },
  "advanced": {
   "description": "{纯化以太提取物}\n\n选定部队获得飞行能力并减少20%所受伤害。"
  },
  "expert": {
   "description": "{纯化以太提取物}\n\n选定部队获得飞行能力并减少30%所受伤害。\n\n需要： 药剂车，高级急救术，在魔法平原上",
   "effects": {
    "flying": {
     "type": "FLYING",
     "duration": "N_TURNS"
    },
    "damageReduction": {
     "type": "GENERAL_DAMAGE_REDUCTION",
     "subtype": "damageTypeMelee",
     "val": 30,
     "duration": "N_TURNS"
    },
    "damageReductionShots": {
     "type": "GENERAL_DAMAGE_REDUCTION",
     "subtype": "damageTypeRanged",
     "val": 30,
     "duration": "N_TURNS"
    },
    "magicDamageReduction": {
     "type": "SPELL_DAMAGE_REDUCTION",
     "subtype": "spellSchool.any",
     "val": 30,
     "duration": "N_TURNS"
    },
    "potionMarkerEffect": {
     "type": "SPECIFIC_SPELL_DAMAGE",
     "subtype": "spell.potionSupply",
     "val": 0,
     "duration": "N_TURNS"
    }
   }
  }
 }
}
```

</details>

##### 不朽药水（`courtyard:immortalPotion`）

- 文件：[Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/immortalPotion.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/immortalPotion.json>)


<details>
<summary>普通与纯化药剂完整效果、目标与说明</summary>

```json
{
 "type": "combat",
 "targetType": "CREATURE",
 "name": "不朽药水",
 "school": {
  "courtPotion": true
 },
 "level": 4,
 "power": 45,
 "defaultGainChance": 0,
 "gainChance": {},
 "animation": {
  "affect": [
   {
    "defName": "courtyard/spells/Potions/Immortal/ImmortalPotion.def",
    "transparency": 0.5
   }
  ]
 },
 "flags": {
  "positive": true,
  "special": true
 },
 "targetCondition": {
  "noneOf": {
   "bonus.NON_LIVING": "absolute",
   "bonus.SIEGE_WEAPON": "absolute",
   "bonus.MECHANICAL": "absolute",
   "bonus.UNDEAD": "absolute",
   "bonus.GARGOYLE": "absolute"
  }
 },
 "graphics": {
  "iconBook": "courtyard/spells/Potions/Immortal/immorP_book1",
  "iconEffect": "courtyard/spells/Potions/Immortal/immorP_effect1",
  "iconScroll": "courtyard/spells/Potions/Immortal/immorlP_scroll1",
  "iconScenarioBonus": "courtyard/spells/Potions/Immortal/immorlP_bonus1"
 },
 "sounds": {
  "cast": "RESURECT"
 },
 "levels": {
  "base": {
   "cost": 0,
   "range": "0",
   "power": 1,
   "targetModifier": {
    "smart": true
   }
  },
  "none": {
   "description": "{不朽药水}\n\n补充阵亡部队的生命力。",
   "battleEffects": {
    "heal": {
     "type": "core:heal",
     "healLevel": "resurrect",
     "healPower": "permanent",
     "minFullUnits": 1
    },
    "cure": {
     "type": "core:dispel",
     "indirect": true,
     "optional": true,
     "dispelNegative": false,
     "dispelNeutral": false,
     "dispelPositive": false
    }
   }
  },
  "basic": {
   "description": "{不朽药水}\n\n补充阵亡部队的生命力。"
  },
  "advanced": {
   "description": "{纯化不朽药水}\n\n补充阵亡部队的生命力。"
  },
  "expert": {
   "description": "{纯化不朽药水}\n\n补充阵亡部队的生命力，并驱除负面状态。",
   "battleEffects": {
    "heal": {
     "type": "core:heal",
     "healLevel": "resurrect",
     "healPower": "permanent",
     "minFullUnits": 1
    },
    "cure": {
     "type": "core:dispel",
     "indirect": true,
     "optional": true,
     "dispelNegative": true,
     "dispelNeutral": false,
     "dispelPositive": false
    }
   }
  }
 }
}
```

</details>

##### 寒冷药水（`courtyard:icePotion`）

- 文件：[Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/icePotion.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/icePotion.json>)


<details>
<summary>普通与纯化药剂完整效果、目标与说明</summary>

```json
{
 "type": "combat",
 "targetType": "CREATURE",
 "name": "寒冷药水",
 "school": {
  "courtPotion": true
 },
 "level": 4,
 "power": 1,
 "defaultGainChance": 0,
 "gainChance": {},
 "animation": {
  "affect": [
   {
    "effectName": "freeze",
    "transparency": 0.5
   }
  ]
 },
 "flags": {
  "negative": true,
  "special": true
 },
 "targetCondition": {
  "noneOf": {
   "bonus.SIEGE_WEAPON": "absolute"
  }
 },
 "graphics": {
  "iconBook": "courtyard/spells/Potions/icePotion/iceP_Book1.png",
  "iconEffect": "courtyard/spells/Potions/icePotion/iceP_effect1",
  "iconScroll": "courtyard/spells/Potions/icePotion/iceP_Scroll1.png",
  "iconScenarioBonus": "courtyard/spells/Potions/icePotion/iceP_bonus1"
 },
 "sounds": {
  "cast": "TAILWIND"
 },
 "levels": {
  "base": {
   "cost": 0,
   "range": "0",
   "power": 1,
   "targetModifier": {
    "smart": true
   }
  },
  "none": {
   "description": "{寒冷药水}\r\n\r\n使敌人被冻结，3个回合无法行动。可以对除战争机器外的任何生物使用，且可以无视水系抗性。",
   "cost": 0,
   "effects": {
    "notActive": {
     "val": 0,
     "type": "NOT_ACTIVE",
     "subtype": "spell.stoneGaze",
     "duration": [
      "UNTIL_BEING_ATTACKED",
      "N_TURNS"
     ],
     "turns": 3
    },
    "noRetaliation": {
     "val": 0,
     "type": "NO_RETALIATION",
     "duration": [
      "UNTIL_BEING_ATTACKED",
      "N_TURNS"
     ],
     "turns": 3
    },
    "generalDamageReduction": {
     "type": "GENERAL_DAMAGE_REDUCTION",
     "val": -20,
     "duration": [
      "UNTIL_BEING_ATTACKED",
      "N_TURNS"
     ],
     "turns": 3
    }
   }
  },
  "basic": {
   "description": "{寒冷药水}\r\n\r\n使敌人被冻结，3个回合无法行动。可以对除战争机器外的任何生物使用，且可以无视水系抗性。"
  },
  "advanced": {
   "description": "{寒冷药水}\r\n\r\n使敌人被冻结，3个回合无法行动。可以对除战争机器外的任何生物使用，且可以无视水系抗性。"
  },
  "expert": {
   "description": "{纯化寒冷药水}\r\n\r\n使敌人被冻结，5个回合无法行动。可以对除战争机器外的任何生物使用，且可以无视水系抗性。",
   "effects": {
    "notActive": {
     "val": 0,
     "type": "NOT_ACTIVE",
     "subtype": "spell.stoneGaze",
     "duration": [
      "UNTIL_BEING_ATTACKED",
      "N_TURNS"
     ],
     "turns": 5
    },
    "noRetaliation": {
     "val": 0,
     "type": "NO_RETALIATION",
     "duration": [
      "UNTIL_BEING_ATTACKED",
      "N_TURNS"
     ],
     "turns": 5
    },
    "generalDamageReduction": {
     "type": "GENERAL_DAMAGE_REDUCTION",
     "val": -20,
     "duration": [
      "UNTIL_BEING_ATTACKED",
      "N_TURNS"
     ],
     "turns": 5
    }
   }
  }
 }
}
```

</details>

##### 保护药水（`courtyard:invPotion`）

- 文件：[Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/invPotion.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/invPotion.json>)


<details>
<summary>普通与纯化药剂完整效果、目标与说明</summary>

```json
{
 "type": "combat",
 "targetType": "CREATURE",
 "name": "保护药水",
 "school": {
  "courtPotion": true
 },
 "level": 5,
 "power": 1,
 "defaultGainChance": 0,
 "gainChance": {},
 "animation": {
  "affect": [
   "courtyard/spells/Potions/icePotion/invP.def"
  ]
 },
 "flags": {
  "positive": true,
  "special": true
 },
 "targetCondition": {
  "noneOf": {
   "bonus.SIEGE_WEAPON": "absolute",
   "bonus.UNDEAD": "normal"
  }
 },
 "graphics": {
  "iconBook": "courtyard/spells/Potions/icePotion/invP_Book1.png",
  "iconEffect": "courtyard/spells/Potions/icePotion/invP_effect1",
  "iconScroll": "courtyard/spells/Potions/icePotion/invP_Scroll1.png",
  "iconScenarioBonus": "courtyard/spells/Potions/icePotion/invP_bonus1"
 },
 "sounds": {
  "cast": "courtyard/spells/TOUGHNESSPOT.wav"
 },
 "levels": {
  "base": {
   "cost": 0,
   "range": "0",
   "power": 1,
   "targetModifier": {
    "smart": true
   }
  },
  "none": {
   "description": "{保护药水}\n\n选定部队三个回合内受到伤害不超过当前生命值的40%。",
   "effects": {
    "increaseDamage": {
     "val": 40,
     "type": "DAMAGE_RECEIVED_CAP",
     "valueType": "INDEPENDENT_MIN",
     "duration": "N_TURNS",
     "turns": 3
    }
   }
  },
  "basic": {
   "description": "{保护药水}\n\n选定部队三个回合内受到伤害不超过当前生命值的40%。",
   "effects": {
    "increaseDamage": {
     "val": 40,
     "type": "DAMAGE_RECEIVED_CAP",
     "valueType": "INDEPENDENT_MIN",
     "duration": "N_TURNS",
     "turns": 3
    }
   }
  },
  "advanced": {
   "description": "{保护药水}\n\n选定部队三个回合内受到伤害不超过当前生命值的40%。",
   "effects": {
    "increaseDamage": {
     "val": 40,
     "type": "DAMAGE_RECEIVED_CAP",
     "valueType": "INDEPENDENT_MIN",
     "duration": "N_TURNS",
     "turns": 3
    }
   }
  },
  "expert": {
   "description": "{保护药水}\n\n选定部队三个回合内受到伤害不超过当前生命值的40%。",
   "effects": {
    "increaseDamage": {
     "val": 40,
     "type": "DAMAGE_RECEIVED_CAP",
     "valueType": "INDEPENDENT_MIN",
     "duration": "N_TURNS",
     "turns": 3
    }
   }
  }
 }
}
```

</details>


### 叶卡捷琳娜（`courtyard:14Ekaterina`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：保留和平之歌与祈祷特长；开场全体祈祷50回合，野怪加入判定+4（不是直接+4%必加入）。祈祷的额外兵种分档增强来自 spellsNew.json，不能用英雄specialty#override抹掉已有bonus。

- 可用性：`special=false`（缺省按非特殊）；职业：`philosopher`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"logistics"}]。
- 初始法术书：["pacifism","prayer"]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `diplomaBonus` | 野怪加入判定加成 `WANDERING_CREATURES_JOIN_BONUS`；值：`4`；数值方式：`"BASE_NUMBER"` |
| `pacifism` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.pacifism"`；值：`3`；数值方式：`"INDEPENDENT_MAX"` |
| `prayer` | 特殊附魔特长 `SPECIAL_PECULIAR_ENCHANT`；对象／属性：`"spell.prayer"`；附加参数：`0` |
| `choir` | 开场自动施法 `OPENING_BATTLE_SPELL`；对象／属性：`"spell.prayer"`；值：`50`；数值方式：`"BASE_NUMBER"` |
| `bind` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.prayer"`；值：`1`；数值方式：`"BASE_NUMBER"` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：和平之歌。
- 当前说明：可以施放最高等级的和平之歌魔法；保留祈祷特长，开场为己方全体施放祈祷，持续50回合。游荡生物加入判定额外+4（不是加入概率提高4%）。
- 当前tooltip：Spell Bonus: Pacifism

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/heroes/heroTexts.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/heroes/heroTexts.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/specialtyDescriptions.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/specialtyDescriptions.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-06-14 `3731a2cd`：`skills`：`[{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"scouting"}]` → `[{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"logistics"}]`；`specialty.bonuses.bind.subtype`：`null` → `"spell.prayer"`；`specialty.bonuses.bind.type`：`null` → `"SPELL"`；`specialty.bonuses.bind.val`：`null` → `1`；`specialty.bonuses.bind.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.choir.subtype`：`null` → `"spell.prayer"`；`specialty.bonuses.choir.type`：`null` → `"OPENING_BATTLE_SPELL"`；`specialty.bonuses.choir.val`：`null` → `10`；`specialty.bonuses.choir.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.prayer.addInfo`：`null` → `0`；`specialty.bonuses.prayer.subtype`：`null` → `"spell.prayer"`；`specialty.bonuses.prayer.type`：`null` → `"SPECIAL_PECULIAR_ENCHANT"`；`spellbook`：`["pacifism"]` → `["pacifism","prayer"]`。
- 2026-08-09 `0fdf143e`：`specialty.bonuses.choir.val`：`10` → `50`；`specialty.bonuses.diplomaBonus.type`：`null` → `"WANDERING_CREATURES_JOIN_BONUS"`；`specialty.bonuses.diplomaBonus.val`：`null` → `4`；`specialty.bonuses.diplomaBonus.valueType`：`null` → `"BASE_NUMBER"`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "army": [
  {
   "creature": "cupid",
   "max": 18,
   "min": 10
  },
  {
   "creature": "homunculus",
   "max": 11,
   "min": 5
  },
  {
   "creature": "fireNewt",
   "max": 5,
   "min": 3
  }
 ],
 "class": "philosopher",
 "female": true,
 "images": {
  "large": "courtyard/heroes/Ekaterina/portraitlarge.png",
  "small": "courtyard/heroes/Ekaterina/portraitsmall.png",
  "specialtyLarge": "courtyard/heroes/Ekaterina/speclarge1.png",
  "specialtySmall": "courtyard/heroes/Ekaterina/specsmall1.png"
 },
 "skills": [
  {
   "level": "basic",
   "skill": "wisdom"
  },
  {
   "level": "basic",
   "skill": "logistics"
  }
 ],
 "spellbook": [
  "pacifism",
  "prayer"
 ],
 "battleImage": "courtyard/heroes/CourtAlice.def",
 "specialty": {
  "bonuses": {
   "diplomaBonus": {
    "type": "WANDERING_CREATURES_JOIN_BONUS",
    "val": 4,
    "valueType": "BASE_NUMBER"
   },
   "pacifism": {
    "subtype": "spell.pacifism",
    "type": "SPELL",
    "val": 3,
    "valueType": "INDEPENDENT_MAX"
   },
   "prayer": {
    "addInfo": 0,
    "subtype": "spell.prayer",
    "type": "SPECIAL_PECULIAR_ENCHANT"
   },
   "choir": {
    "type": "OPENING_BATTLE_SPELL",
    "subtype": "spell.prayer",
    "val": 50,
    "valueType": "BASE_NUMBER"
   },
   "bind": {
    "subtype": "spell.prayer",
    "type": "SPELL",
    "val": 1,
    "valueType": "BASE_NUMBER"
   }
  }
 },
 "texts": {
  "biography": "作为一个铁匠的女儿，叶卡捷琳娜自小就对炼金术感兴趣。为了追寻神奇的力量，她离开了父母和年幼的妹妹，来到布拉卡达学习炼金术。但当她认为布拉卡达人并不看重炼金术的成果的时候，她离开了。最终她来到了拉尼亚追寻自己的梦想。",
  "name": "叶卡捷琳娜",
  "specialty": {
   "description": "可以施放最高等级的和平之歌魔法；保留祈祷特长，开场为己方全体施放祈祷，持续50回合。游荡生物加入判定额外+4（不是加入概率提高4%）。",
   "name": "和平之歌",
   "tooltip": "Spell Bonus: Pacifism"
  }
 }
}
```

</details>

### 爱丽丝（`courtyard:16aliceCourt`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：开放普通英雄池；开场全体魔法神镜50回合；限定女战士、女神、女祭司等组及升级形态生命+20、攻防+20、杀伤+10、速度+3。最终以限制器清单为准。

- 可用性：`special=false`（缺省按非特殊）；职业：`philosopher`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"luck"}]。
- 初始法术书：["magicMirror"]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `mirror` | 法术特长随等级增强 `SPECIAL_SPELL_LEV`；对象／属性：`"spell.magicMirror"`；附加参数：`5` |
| `magicMirrorMass` | 特殊附魔特长 `SPECIAL_PECULIAR_ENCHANT`；对象／属性：`"spell.magicMirror"`；附加参数：`0` |
| `openingMagicMirror` | 开场自动施法 `OPENING_BATTLE_SPELL`；对象／属性：`"spell.magicMirror"`；值：`50`；数值方式：`"BASE_NUMBER"` |
| `amazonHighHealth` | 生命值 `STACK_HEALTH`；值：`20`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"amazonHigh","includeUpgrades":true}]` |
| `amazonHighAttack` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.attack"`；值：`20`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"amazonHigh","includeUpgrades":true}]` |
| `amazonHighDefence` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.defence"`；值：`20`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"amazonHigh","includeUpgrades":true}]` |
| `amazonHighDamage` | 最小／最大杀伤 `CREATURE_DAMAGE`；对象／属性：`"creatureDamageBoth"`；值：`10`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"amazonHigh","includeUpgrades":true}]` |
| `amazonHighSpeed` | 部队速度 `STACKS_SPEED`；值：`3`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"amazonHigh","includeUpgrades":true}]` |
| `ftDrag1Health` | 生命值 `STACK_HEALTH`；值：`20`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"ftDrag1","includeUpgrades":true}]` |
| `ftDrag1Attack` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.attack"`；值：`20`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"ftDrag1","includeUpgrades":true}]` |
| `ftDrag1Defence` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.defence"`；值：`20`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"ftDrag1","includeUpgrades":true}]` |
| `ftDrag1Damage` | 最小／最大杀伤 `CREATURE_DAMAGE`；对象／属性：`"creatureDamageBoth"`；值：`10`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"ftDrag1","includeUpgrades":true}]` |
| `ftDrag1Speed` | 部队速度 `STACKS_SPEED`；值：`3`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"ftDrag1","includeUpgrades":true}]` |
| `ftHatchling1Health` | 生命值 `STACK_HEALTH`；值：`20`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"ftHatchling1","includeUpgrades":true}]` |
| `ftHatchling1Attack` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.attack"`；值：`20`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"ftHatchling1","includeUpgrades":true}]` |
| `ftHatchling1Defence` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.defence"`；值：`20`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"ftHatchling1","includeUpgrades":true}]` |
| `ftHatchling1Damage` | 最小／最大杀伤 `CREATURE_DAMAGE`；对象／属性：`"creatureDamageBoth"`；值：`10`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"ftHatchling1","includeUpgrades":true}]` |
| `ftHatchling1Speed` | 部队速度 `STACKS_SPEED`；值：`3`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"ftHatchling1","includeUpgrades":true}]` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：魔法神镜。
- 当前说明：开局释放全体魔法神镜。爱丽丝带领的女战士/女武神、彩虹女神/极光女神和女祭司/女术士生命+20，攻防+20，杀伤+10，速度+3。
- 当前tooltip：Special Bonus: Interference

**额外校对结论**：tooltip仍写干扰术，与当前魔法神镜及女兵强化不一致；主描述未明确开场魔法神镜持续50回合。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/heroes/heroTexts.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/heroes/heroTexts.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/courtyard.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/courtyard.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-08-02 `b9376775`：`special`：`true` → `false`。
- 2026-08-02 `b9376775`：`specialty.bonuses.amazonHighAttack.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"amazonHigh","includeUpgrades":true}]`；`specialty.bonuses.amazonHighAttack.subtype`：`null` → `"primarySkill.attack"`；`specialty.bonuses.amazonHighAttack.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.amazonHighAttack.val`：`null` → `20`；`specialty.bonuses.amazonHighDamage.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"amazonHigh","includeUpgrades":true}]`；`specialty.bonuses.amazonHighDamage.subtype`：`null` → `"creatureDamageBoth"`；`specialty.bonuses.amazonHighDamage.type`：`null` → `"CREATURE_DAMAGE"`；`specialty.bonuses.amazonHighDamage.val`：`null` → `10`；`specialty.bonuses.amazonHighDefence.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"amazonHigh","includeUpgrades":true}]`；`specialty.bonuses.amazonHighDefence.subtype`：`null` → `"primarySkill.defence"`；`specialty.bonuses.amazonHighDefence.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.amazonHighDefence.val`：`null` → `20`；`specialty.bonuses.amazonHighHealth.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"amazonHigh","includeUpgrades":true}]`；`specialty.bonuses.amazonHighHealth.type`：`null` → `"STACK_HEALTH"`；`specialty.bonuses.amazonHighHealth.val`：`null` → `20`；`specialty.bonuses.amazonHighSpeed.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"amazonHigh","includeUpgrades":true}]`；`specialty.bonuses.amazonHighSpeed.type`：`null` → `"STACKS_SPEED"`；`specialty.bonuses.amazonHighSpeed.val`：`null` → `3`；`specialty.bonuses.ftDrag1Attack.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"ftDrag1","includeUpgrades":true}]`；`specialty.bonuses.ftDrag1Attack.subtype`：`null` → `"primarySkill.attack"`；`specialty.bonuses.ftDrag1Attack.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.ftDrag1Attack.val`：`null` → `20`；`specialty.bonuses.ftDrag1Damage.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"ftDrag1","includeUpgrades":true}]`；`specialty.bonuses.ftDrag1Damage.subtype`：`null` → `"creatureDamageBoth"`；`specialty.bonuses.ftDrag1Damage.type`：`null` → `"CREATURE_DAMAGE"`；`specialty.bonuses.ftDrag1Damage.val`：`null` → `10`；`specialty.bonuses.ftDrag1Defence.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"ftDrag1","includeUpgrades":true}]`；`specialty.bonuses.ftDrag1Defence.subtype`：`null` → `"primarySkill.defence"`；`specialty.bonuses.ftDrag1Defence.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.ftDrag1Defence.val`：`null` → `20`；`specialty.bonuses.ftDrag1Health.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"ftDrag1","includeUpgrades":true}]`；`specialty.bonuses.ftDrag1Health.type`：`null` → `"STACK_HEALTH"`；`specialty.bonuses.ftDrag1Health.val`：`null` → `20`；`specialty.bonuses.ftDrag1Speed.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"ftDrag1","includeUpgrades":true}]`；`specialty.bonuses.ftDrag1Speed.type`：`null` → `"STACKS_SPEED"`；`specialty.bonuses.ftDrag1Speed.val`：`null` → `3`；`specialty.bonuses.ftHatchling1Attack.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"ftHatchling1","includeUpgrades":true}]`；`specialty.bonuses.ftHatchling1Attack.subtype`：`null` → `"primarySkill.attack"`；`specialty.bonuses.ftHatchling1Attack.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.ftHatchling1Attack.val`：`null` → `20`；`specialty.bonuses.ftHatchling1Damage.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"ftHatchling1","includeUpgrades":true}]`；`specialty.bonuses.ftHatchling1Damage.subtype`：`null` → `"creatureDamageBoth"`；`specialty.bonuses.ftHatchling1Damage.type`：`null` → `"CREATURE_DAMAGE"`；`specialty.bonuses.ftHatchling1Damage.val`：`null` → `10`；`specialty.bonuses.ftHatchling1Defence.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"ftHatchling1","includeUpgrades":true}]`；`specialty.bonuses.ftHatchling1Defence.subtype`：`null` → `"primarySkill.defence"`；`specialty.bonuses.ftHatchling1Defence.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.ftHatchling1Defence.val`：`null` → `20`；`specialty.bonuses.ftHatchling1Health.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"ftHatchling1","includeUpgrades":true}]`；`specialty.bonuses.ftHatchling1Health.type`：`null` → `"STACK_HEALTH"`；`specialty.bonuses.ftHatchling1Health.val`：`null` → `20`；`specialty.bonuses.ftHatchling1Speed.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"ftHatchling1","includeUpgrades":true}]`；`specialty.bonuses.ftHatchling1Speed.type`：`null` → `"STACKS_SPEED"`；`specialty.bonuses.ftHatchling1Speed.val`：`null` → `3`；`specialty.bonuses.magicMirrorMass.addInfo`：`null` → `0`；`specialty.bonuses.magicMirrorMass.subtype`：`null` → `"spell.magicMirror"`；`specialty.bonuses.magicMirrorMass.type`：`null` → `"SPECIAL_PECULIAR_ENCHANT"`；`specialty.bonuses.openingMagicMirror.subtype`：`null` → `"spell.magicMirror"`；`specialty.bonuses.openingMagicMirror.type`：`null` → `"OPENING_BATTLE_SPELL"`；`specialty.bonuses.openingMagicMirror.val`：`null` → `50`；`specialty.bonuses.openingMagicMirror.valueType`：`null` → `"BASE_NUMBER"`；`texts.specialty.description`：`null` → `"开局释放全体魔法神镜。爱丽丝带领的女战士/女武神、彩虹女神/极光女神和女祭司/女术士生命+20，攻防+20，杀伤+10，速度+3。"`；`texts.specialty.name`：`null` → `"魔法神镜"`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "army": [
  {
   "creature": "cupid",
   "max": 18,
   "min": 10
  },
  {
   "creature": "homunculus",
   "max": 11,
   "min": 5
  },
  {
   "creature": "fireNewt",
   "max": 5,
   "min": 3
  }
 ],
 "class": "philosopher",
 "special": false,
 "female": true,
 "images": {
  "large": "courtyard/heroes/AliceCourt/AliceWonder.bmp",
  "small": "courtyard/heroes/AliceCourt/AliceWonderS.bmp",
  "specialtyLarge": "courtyard/heroes/AliceCourt/speclarge1.png",
  "specialtySmall": "courtyard/heroes/AliceCourt/specsmall1.png"
 },
 "skills": [
  {
   "level": "basic",
   "skill": "wisdom"
  },
  {
   "level": "basic",
   "skill": "luck"
  }
 ],
 "spellbook": [
  "magicMirror"
 ],
 "battleImage": "courtyard/heroes/CourtAlice.def",
 "specialty": {
  "bonuses": {
   "mirror": {
    "type": "SPECIAL_SPELL_LEV",
    "subtype": "spell.magicMirror",
    "addInfo": 5
   },
   "magicMirrorMass": {
    "type": "SPECIAL_PECULIAR_ENCHANT",
    "subtype": "spell.magicMirror",
    "addInfo": 0
   },
   "openingMagicMirror": {
    "type": "OPENING_BATTLE_SPELL",
    "subtype": "spell.magicMirror",
    "val": 50,
    "valueType": "BASE_NUMBER"
   },
   "amazonHighHealth": {
    "type": "STACK_HEALTH",
    "val": 20,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "amazonHigh",
      "includeUpgrades": true
     }
    ]
   },
   "amazonHighAttack": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.attack",
    "val": 20,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "amazonHigh",
      "includeUpgrades": true
     }
    ]
   },
   "amazonHighDefence": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.defence",
    "val": 20,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "amazonHigh",
      "includeUpgrades": true
     }
    ]
   },
   "amazonHighDamage": {
    "type": "CREATURE_DAMAGE",
    "subtype": "creatureDamageBoth",
    "val": 10,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "amazonHigh",
      "includeUpgrades": true
     }
    ]
   },
   "amazonHighSpeed": {
    "type": "STACKS_SPEED",
    "val": 3,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "amazonHigh",
      "includeUpgrades": true
     }
    ]
   },
   "ftDrag1Health": {
    "type": "STACK_HEALTH",
    "val": 20,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "ftDrag1",
      "includeUpgrades": true
     }
    ]
   },
   "ftDrag1Attack": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.attack",
    "val": 20,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "ftDrag1",
      "includeUpgrades": true
     }
    ]
   },
   "ftDrag1Defence": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.defence",
    "val": 20,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "ftDrag1",
      "includeUpgrades": true
     }
    ]
   },
   "ftDrag1Damage": {
    "type": "CREATURE_DAMAGE",
    "subtype": "creatureDamageBoth",
    "val": 10,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "ftDrag1",
      "includeUpgrades": true
     }
    ]
   },
   "ftDrag1Speed": {
    "type": "STACKS_SPEED",
    "val": 3,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "ftDrag1",
      "includeUpgrades": true
     }
    ]
   },
   "ftHatchling1Health": {
    "type": "STACK_HEALTH",
    "val": 20,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "ftHatchling1",
      "includeUpgrades": true
     }
    ]
   },
   "ftHatchling1Attack": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.attack",
    "val": 20,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "ftHatchling1",
      "includeUpgrades": true
     }
    ]
   },
   "ftHatchling1Defence": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.defence",
    "val": 20,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "ftHatchling1",
      "includeUpgrades": true
     }
    ]
   },
   "ftHatchling1Damage": {
    "type": "CREATURE_DAMAGE",
    "subtype": "creatureDamageBoth",
    "val": 10,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "ftHatchling1",
      "includeUpgrades": true
     }
    ]
   },
   "ftHatchling1Speed": {
    "type": "STACKS_SPEED",
    "val": 3,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "ftHatchling1",
      "includeUpgrades": true
     }
    ]
   }
  }
 },
 "texts": {
  "biography": "爱丽丝在奇幻的世界中已经旅行了很久，虽然只是偶然间进入奇幻的世界，但她现在已经迷恋这里了。她的奇幻旅程也让无数人为之赞叹和羡慕。",
  "name": "爱丽丝",
  "specialty": {
   "description": "开局释放全体魔法神镜。爱丽丝带领的女战士/女武神、彩虹女神/极光女神和女祭司/女术士生命+20，攻防+20，杀伤+10，速度+3。",
   "name": "魔法神镜",
   "tooltip": "Special Bonus: Interference"
  }
 }
}
```

</details>

### 诺曼（`deathvalley:m_dvNorman`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：毁灭丧心：保留丧心病狂法术特长，战场取消天然魔法免疫，并通过INDEPENDENT_MIN将抗性／抗魔光环压至0；不能突破法术 targetCondition 的absolute绝对限制。

- 可用性：`special=false`（缺省按非特殊）；职业：`dvNecropriest`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"dv_necromancy"}]。
- 初始法术书：["berserk"]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `berserk` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.berserk"`；值：`2`；数值方式：`"INDEPENDENT_MAX"` |
| `magicVulnerability` | 取消天然法术免疫 `NEGATE_ALL_NATURAL_IMMUNITIES`；对象／属性：`"immunityBattleWide"`；值：`0`；数值方式：`"BASE_NUMBER"`；传播范围：`"BATTLE_WIDE"` |
| `magicVulnerability2` | 取消天然法术免疫 `NEGATE_ALL_NATURAL_IMMUNITIES`；对象／属性：`"immunityEnemyHero"`；值：`0`；数值方式：`"BASE_NUMBER"`；传播范围：`"BATTLE_WIDE"` |
| `noResistance` | 魔法抗性 `MAGIC_RESISTANCE`；值：`0`；数值方式：`"INDEPENDENT_MIN"`；传播范围：`"BATTLE_WIDE"` |
| `noResistanceAura` | 抗魔光环 `SPELL_RESISTANCE_AURA`；值：`0`；数值方式：`"INDEPENDENT_MIN"`；传播范围：`"BATTLE_WIDE"` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：毁灭丧心。
- 当前说明：使用丧心病狂魔法时初始为中级火系效果。战斗中获得毁灭之球的完整效果，消除所有生物的普通魔法免疫、心智免疫、魔法抗性与抗魔光环，但无法突破法术的绝对目标限制。
- 当前tooltip：丧心病狂与魔法易伤

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/Deathvalley/Content/config/deathvalley/heroes/M_norman.json](<Hero3Expasion1.83/Mods/Deathvalley/Content/config/deathvalley/heroes/M_norman.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-09-14 `12be5fe6`：`specialty.bonuses.magicVulnerability.propagator`：`null` → `"BATTLE_WIDE"`；`specialty.bonuses.magicVulnerability.subtype`：`null` → `"immunityBattleWide"`；`specialty.bonuses.magicVulnerability.type`：`null` → `"NEGATE_ALL_NATURAL_IMMUNITIES"`；`specialty.bonuses.magicVulnerability.val`：`null` → `0`；`specialty.bonuses.magicVulnerability.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.magicVulnerability2.propagator`：`null` → `"BATTLE_WIDE"`；`specialty.bonuses.magicVulnerability2.subtype`：`null` → `"immunityEnemyHero"`；`specialty.bonuses.magicVulnerability2.type`：`null` → `"NEGATE_ALL_NATURAL_IMMUNITIES"`；`specialty.bonuses.magicVulnerability2.val`：`null` → `0`；`specialty.bonuses.magicVulnerability2.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.noResistance.propagator`：`null` → `"BATTLE_WIDE"`；`specialty.bonuses.noResistance.type`：`null` → `"MAGIC_RESISTANCE"`；`specialty.bonuses.noResistance.val`：`null` → `0`；`specialty.bonuses.noResistance.valueType`：`null` → `"INDEPENDENT_MIN"`；`specialty.bonuses.noResistanceAura.propagator`：`null` → `"BATTLE_WIDE"`；`specialty.bonuses.noResistanceAura.type`：`null` → `"SPELL_RESISTANCE_AURA"`；`specialty.bonuses.noResistanceAura.val`：`null` → `0`；`specialty.bonuses.noResistanceAura.valueType`：`null` → `"INDEPENDENT_MIN"`；`texts.specialty.description`：`"使用丧心病狂魔法时初始为中级火系效果。"` → `"使用丧心病狂魔法时初始为中级火系效果。战斗中获得毁灭之球的完整效果，消除所有生物的普通魔法免疫、心智免疫、魔法抗性与抗魔光环，但无法突破法术的绝对目标限制。"`；`texts.specialty.name`：`"丧心病狂"` → `"魔法易伤丧心病狂"`；`texts.specialty.tooltip`：`"Spell bonus: Berserk"` → `"丧心病狂与魔法易伤"`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "class": "dvNecropriest",
 "female": false,
 "images": {
  "specialtySmall": "deathvalley/heroes/norman/Un32Bers.png",
  "specialtyLarge": "deathvalley/heroes/norman/Un44Bers.png",
  "large": "deathvalley/heroes/norman/NormanL.png",
  "small": "deathvalley/heroes/norman/NormanS.png"
 },
 "army": [
  {
   "creature": "skelh",
   "max": 25,
   "min": 15
  },
  {
   "creature": "boneHook",
   "max": 10,
   "min": 5
  },
  {
   "creature": "mums",
   "max": 7,
   "min": 5
  }
 ],
 "spellbook": [
  "berserk"
 ],
 "skills": [
  {
   "level": "basic",
   "skill": "wisdom"
  },
  {
   "level": "basic",
   "skill": "dv_necromancy"
  }
 ],
 "specialty": {
  "bonuses": {
   "berserk": {
    "type": "SPELL",
    "subtype": "spell.berserk",
    "val": 2,
    "valueType": "INDEPENDENT_MAX"
   },
   "magicVulnerability": {
    "type": "NEGATE_ALL_NATURAL_IMMUNITIES",
    "subtype": "immunityBattleWide",
    "val": 0,
    "valueType": "BASE_NUMBER",
    "propagator": "BATTLE_WIDE"
   },
   "magicVulnerability2": {
    "type": "NEGATE_ALL_NATURAL_IMMUNITIES",
    "subtype": "immunityEnemyHero",
    "val": 0,
    "valueType": "BASE_NUMBER",
    "propagator": "BATTLE_WIDE"
   },
   "noResistance": {
    "type": "MAGIC_RESISTANCE",
    "val": 0,
    "valueType": "INDEPENDENT_MIN",
    "propagator": "BATTLE_WIDE"
   },
   "noResistanceAura": {
    "type": "SPELL_RESISTANCE_AURA",
    "val": 0,
    "valueType": "INDEPENDENT_MIN",
    "propagator": "BATTLE_WIDE"
   }
  }
 },
 "texts": {
  "biography": "诺曼曾经是一位虔诚的牧师，他在反抗亡灵入侵时被邪恶巫师注入了死灵魔法契约，想让他成为仆从。虽然找到机会消灭了巫师，但他却不得不继续保留死灵魔法的契约，来维持自己只剩一半的生命。",
  "name": "诺曼",
  "specialty": {
   "description": "使用丧心病狂魔法时初始为中级火系效果。战斗中获得毁灭之球的完整效果，消除所有生物的普通魔法免疫、心智免疫、魔法抗性与抗魔光环，但无法突破法术的绝对目标限制。",
   "tooltip": "丧心病狂与魔法易伤",
   "name": "毁灭丧心"
  }
 }
}
```

</details>

### 美瑞（`fairy:ftB_ftMeri`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：美瑞：保留每级5%相对后勤成长，初始魔法书含回城术、飞行奇术和异次元之门。这里的显式5%不应被全局10%的通用技能特长成长覆盖。

- 可用性：`special=false`（缺省按非特殊）；职业：`hunter`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"basic","skill":"logistics"},{"level":"basic","skill":"pathfinding"}]。
- 初始法术书：["townPortal","fly","dimensionDoor"]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `logistics` | 移动力 `MOVEMENT`；对象／属性：`"heroMovementLand"`；值：`5`；数值方式：`"PERCENT_TO_TARGET_TYPE"`；成长规则：`"TIMES_HERO_LEVEL"` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：后勤学。
- 当前说明：在后勤学上每级得到5%的额外奖励。
- 当前tooltip：{Logistics}<br><br>Receives a 5% per level bonus too Logistics skill percentage.

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft2_meri.json](<Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft2_meri.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-06-13 `f9592afc`：`spellbook`：`null` → `["townPortal","dimensionDoor"]`。
- 2026-07-06 `2029faf8`：`spellbook`：`["townPortal","dimensionDoor"]` → `["townPortal","fly","dimensionDoor"]`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "class": "hunter",
 "female": true,
 "texts": {
  "name": "美瑞",
  "biography": "美瑞是有史以来最优秀的探险家之一，每次都能够轻松找到最快的路线。在冒险中，她有时会驻足凝视花草或树木，有人说是自然的力量在帮助她更好地找寻道路。",
  "specialty": {
   "description": "在后勤学上每级得到5%的额外奖励。",
   "tooltip": "{Logistics}\r\n\r\nReceives a 5% per level bonus too Logistics skill percentage.",
   "name": "后勤学"
  }
 },
 "images": {
  "specialtySmall": "fairy/heroes/mrimm/un32logi.png",
  "specialtyLarge": "fairy/heroes/mrimm/un44logi.png",
  "large": "fairy/heroes/mrimm/hpl285.png",
  "small": "fairy/heroes/mrimm/hps285.png"
 },
 "spellbook": [
  "townPortal",
  "fly",
  "dimensionDoor"
 ],
 "army": [
  {
   "creature": "ftFairy1",
   "max": 30,
   "min": 20
  },
  {
   "creature": "ftFench1",
   "max": 10,
   "min": 8
  },
  {
   "creature": "ftNoble1",
   "max": 6,
   "min": 4
  }
 ],
 "skills": [
  {
   "level": "basic",
   "skill": "logistics"
  },
  {
   "level": "basic",
   "skill": "pathfinding"
  }
 ],
 "specialty": {
  "bonuses": {
   "logistics": {
    "targetSourceType": "SECONDARY_SKILL",
    "subtype": "heroMovementLand",
    "type": "MOVEMENT",
    "updater": "TIMES_HERO_LEVEL",
    "val": 5,
    "valueType": "PERCENT_TO_TARGET_TYPE"
   }
  }
 }
}
```

</details>

### 赛丽娃（`fairy:ftL_Seliva`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：幻影射手特长保留杀伤+2与多兵种转幻影射手，追加生命+10、吸血100、不受近身阻挡射击、无限反击、远程反击、先攻、忽略敌方防御100和攻城能力。只强化本英雄带领的幻影射手，不修改兵种本体。

- 可用性：`special=false`（缺省按非特殊）；职业：`warden`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"learning"}]。
- 初始法术书：["bloodlust"]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `damage` | 最小／最大杀伤 `CREATURE_DAMAGE`；对象／属性：`"creatureDamageBoth"`；值：`2`；数值方式：`"BASE_NUMBER"`；附加参数：`"creature.sharpshooter"` |
| `archer2sharpshooter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.archer"`；附加参数：`"creature.sharpshooter"` |
| `grandElf2sharpshooter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.grandElf"`；附加参数：`"creature.sharpshooter"` |
| `marksman2sharpshooter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.marksman"`；附加参数：`"creature.sharpshooter"` |
| `woodElf2sharpshooter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.woodElf"`；附加参数：`"creature.sharpshooter"` |
| `skel12sharpshooter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"deathvalley:boneHook"`；附加参数：`"creature.sharpshooter"` |
| `skel22sharpshooter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"deathvalley:flayer"`；附加参数：`"creature.sharpshooter"` |
| `shadowhunter2sharpshooter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"asylum:shadowhunter"`；附加参数：`"creature.sharpshooter"` |
| `phantomhunter2sharpshooter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"asylum:phantomhunter"`；附加参数：`"creature.sharpshooter"` |
| `lifeDrain` | 吸血 `LIFE_DRAIN`；值：`100`；附加参数：`"creature.sharpshooter"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]` |
| `freeShooting` | 不受近身阻挡射击 `FREE_SHOOTING`；值：`0`；数值方式：`"ADDITIVE_VALUE"`；附加参数：`"creature.sharpshooter"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]` |
| `endlessRetaliation` | 无限反击 `UNLIMITED_RETALIATIONS`；附加参数：`"creature.sharpshooter"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]` |
| `rangedRetaliation` | 远程反击 `RANGED_RETALIATION`；附加参数：`"creature.sharpshooter"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]` |
| `firstStrike` | 先攻 `FIRST_STRIKE`；对象／属性：`"damageTypeRanged"`；附加参数：`"creature.sharpshooter"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]` |
| `ignoreDefence` | 忽略敌方防御比例 `ENEMY_DEFENCE_REDUCTION`；值：`100`；附加参数：`"creature.sharpshooter"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]` |
| `wallAttack` | 攻城器械能力 `CATAPULT`；对象／属性：`"spell.cyclopsShot"`；附加参数：`"creature.sharpshooter"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]` |
| `wallAttackExtraShot` | 特殊效果 `CATAPULT_EXTRA_SHOTS`；对象／属性：`"spell.cyclopsShot"`；值：`1`；数值方式：`"BASE_NUMBER"`；附加参数：`"creature.sharpshooter"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]` |
| `healthBonus` | 生命值 `STACK_HEALTH`；值：`10`；附加参数：`"creature.sharpshooter"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：战争狂人。
- 当前说明：所带领生物的最小伤害值和最大伤害值各增加2。可将弓箭手、神射手、木精灵、大精灵、白骨弓箭手、剥皮者、暗影猎手、魔影猎手升级为幻影射手。赛丽娃麾下的幻影射手获得吸取生命、贴身射击、无限反击、远程反击、抢先反击、无视防御、可攻击城墙与生命值+10。
- 当前tooltip：{战争狂人}<br><br>All creatures get +2 damage. Archers can be upgraded into Sharpshooters. Seliva's Sharpshooters gain Life Drain, Point-Blank Shot, Unlimited Retaliations, Ranged Retaliation, First Strike, Ignore Defense, Wall Attack and +10 HP.

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft12_seliva.json](<Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft12_seliva.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-06-12 `15186f5b`：`specialty.base.addInfo`：`null` → `"creature.sharpshooter"`；`specialty.base.type`：`null` → `"SPECIAL_UPGRADE"`；`specialty.bonuses.archer2sharpshooter.subtype`：`null` → `"creature.archer"`；`specialty.bonuses.grandElf2sharpshooter.subtype`：`null` → `"creature.grandElf"`；`specialty.bonuses.marksman2sharpshooter.subtype`：`null` → `"creature.marksman"`；`specialty.bonuses.woodElf2sharpshooter.subtype`：`null` → `"creature.woodElf"`；`texts.specialty.description`：`"所带领生物的最小伤害值和最大伤害值各增加1。"` → `"所带领生物的最小伤害值和最大伤害值各增加1。隐藏技能:幻影射手升级"`。
- 2026-06-12 `0d300d72`：`specialty.bonuses.damage.val`：`1` → `2`；`texts.specialty.description`：`"所带领生物的最小伤害值和最大伤害值各增加1。隐藏技能:幻影射手升级"` → `"所带领生物的最小伤害值和最大伤害值各增加2。隐藏技能:幻影射手升级"`；`texts.specialty.tooltip`：`"{Rage}\r\n\r\nAll creatures get +1 to damage."` → `"{Rage}\r\n\r\nAll creatures get +2 to damage."`。
- 2026-06-19 `353602c5`：`specialty.bonuses.freeShooting.limiters`：`null` → `["SHOOTER_ONLY"]`；`specialty.bonuses.freeShooting.type`：`null` → `"FREE_SHOOTING"`；`specialty.bonuses.freeShooting.val`：`null` → `0`；`specialty.bonuses.freeShooting.valueType`：`null` → `"ADDITIVE_VALUE"`；`specialty.bonuses.lifeDrain.limiters`：`null` → `["SHOOTER_ONLY"]`；`specialty.bonuses.lifeDrain.type`：`null` → `"LIFE_DRAIN"`；`specialty.bonuses.lifeDrain.val`：`null` → `100`；`specialty.bonuses.phantomhunter2sharpshooter.subtype`：`null` → `"asylum:phantomhunter"`；`specialty.bonuses.shadowhunter2sharpshooter.subtype`：`null` → `"asylum:shadowhunter"`；`specialty.bonuses.skel12sharpshooter.subtype`：`null` → `"deathvalley:boneHook"`；`specialty.bonuses.skel22sharpshooter.subtype`：`null` → `"deathvalley:flayer"`；`texts.specialty.description`：`"所带领生物的最小伤害值和最大伤害值各增加2。隐藏技能:幻影射手升级"` → `"所带领生物的最小伤害值和最大伤害值各增加2。可将弓箭手、神射手、木精灵、大精灵、骷髅护卫、骷髅法师、暗影猎手、魔影猎手升级为幻影射手。麾下远程单位获得吸取生命，并可在近战贴身时继续进行射击。"`；`texts.specialty.tooltip`：`"{Rage}\r\n\r\nAll creatures get +2 to damage."` → `"{战争狂人}\r\n\r\nAll creatures get +2 damage. Archers can be upgraded into Sharpshooters. All shooters gain Life Drain and Point-Blank Shot."`。
- 2026-06-20 `d20e3775`：`specialty.bonuses.endlessRetaliation.description`：`null` → `"{远程无限反击}\n可以无限次反击敌人的远程攻击"`；`specialty.bonuses.endlessRetaliation.icon`：`null` → `"factory/icons/bonuses/e_retail2_bountyhunter.png"`；`specialty.bonuses.endlessRetaliation.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]`；`specialty.bonuses.endlessRetaliation.type`：`null` → `"UNLIMITED_RETALIATIONS"`；`specialty.bonuses.firstStrike.description`：`null` → `"{抢先攻击}\n可以在敌人远程攻击前进行反击"`；`specialty.bonuses.firstStrike.icon`：`null` → `"factory/icons/bonuses/firststrike_ranged.png"`；`specialty.bonuses.firstStrike.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]`；`specialty.bonuses.firstStrike.subtype`：`null` → `"damageTypeRanged"`；`specialty.bonuses.firstStrike.type`：`null` → `"FIRST_STRIKE"`；`specialty.bonuses.freeShooting.limiters`：`["SHOOTER_ONLY"]` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]`；`specialty.bonuses.healthBonus.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]`；`specialty.bonuses.healthBonus.type`：`null` → `"STACK_HEALTH"`；`specialty.bonuses.healthBonus.val`：`null` → `10`；`specialty.bonuses.ignoreDefence.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]`；`specialty.bonuses.ignoreDefence.type`：`null` → `"ENEMY_DEFENCE_REDUCTION"`；`specialty.bonuses.ignoreDefence.val`：`null` → `100`；`specialty.bonuses.lifeDrain.limiters`：`["SHOOTER_ONLY"]` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]`；`specialty.bonuses.rangedRetaliation.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]`；`specialty.bonuses.rangedRetaliation.type`：`null` → `"RANGED_RETALIATION"`；`specialty.bonuses.wallAttack.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]`；`specialty.bonuses.wallAttack.subtype`：`null` → `"spell.cyclopsShot"`；`specialty.bonuses.wallAttack.type`：`null` → `"CATAPULT"`；`specialty.bonuses.wallAttackExtraShot.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"sharpshooter","includeUpgrades":true}]`；`specialty.bonuses.wallAttackExtraShot.subtype`：`null` → `"spell.cyclopsShot"`；`specialty.bonuses.wallAttackExtraShot.type`：`null` → `"CATAPULT_EXTRA_SHOTS"`；`specialty.bonuses.wallAttackExtraShot.val`：`null` → `1`；`specialty.bonuses.wallAttackExtraShot.valueType`：`null` → `"BASE_NUMBER"`；`texts.specialty.description`：`"所带领生物的最小伤害值和最大伤害值各增加2。可将弓箭手、神射手、木精灵、大精灵、骷髅护卫、骷髅法师、暗影猎手、魔影猎手升级为幻影射手。麾下远程单位获得吸取生命，并可在近战贴身时继续进行射击。"` → `"所带领生物的最小伤害值和最大伤害值各增加2。可将弓箭手、神射手、木精灵、大精灵、白骨弓箭手、剥皮者、暗影猎手、魔影猎手升级为幻影射手。赛丽娃麾下的幻影射手获得吸取生命、贴身射击、无限反击、远程反击、抢先反击、无视防御、可攻击城墙与生命值+10。"`；`texts.specialty.tooltip`：`"{战争狂人}\r\n\r\nAll creatures get +2 damage. Archers can be upgraded into Sharpshooters. All shooters gain Life Drain and Point-Blank Shot."` → `"{战争狂人}\r\n\r\nAll creatures get +2 damage. Archers can be upgraded into Sharpshooters. Seliva's Sharpshooters gain Life Drain, Point-Blank Shot, Unlimited Retaliations, Ranged Retaliation, First Strike, Ignore Defense, Wall Attack and +10 HP."`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "class": "warden",
 "female": true,
 "texts": {
  "name": "赛丽娃",
  "biography": "赛丽娃出生之时就充斥着不祥的气息。虽然她长相美丽，但没什么人愿意去接近她。她眼下血红色的印记会让人打消对于花仙善良的幻想。",
  "specialty": {
   "description": "所带领生物的最小伤害值和最大伤害值各增加2。可将弓箭手、神射手、木精灵、大精灵、白骨弓箭手、剥皮者、暗影猎手、魔影猎手升级为幻影射手。赛丽娃麾下的幻影射手获得吸取生命、贴身射击、无限反击、远程反击、抢先反击、无视防御、可攻击城墙与生命值+10。",
   "tooltip": "{战争狂人}\r\n\r\nAll creatures get +2 damage. Archers can be upgraded into Sharpshooters. Seliva's Sharpshooters gain Life Drain, Point-Blank Shot, Unlimited Retaliations, Ranged Retaliation, First Strike, Ignore Defense, Wall Attack and +10 HP.",
   "name": "战争狂人"
  }
 },
 "images": {
  "specialtySmall": "fairy/heroes/velve/UN32Blsh.png",
  "specialtyLarge": "fairy/heroes/velve/UN44Blsh.png",
  "large": "fairy/heroes/velve/hpl298.png",
  "small": "fairy/heroes/velve/hps298.png"
 },
 "spellbook": [
  "bloodlust"
 ],
 "army": [
  {
   "creature": "ftFairy1",
   "max": 30,
   "min": 20
  },
  {
   "creature": "ftFench1",
   "max": 10,
   "min": 8
  },
  {
   "creature": "ftNoble1",
   "max": 6,
   "min": 4
  }
 ],
 "skills": [
  {
   "level": "basic",
   "skill": "wisdom"
  },
  {
   "level": "basic",
   "skill": "learning"
  }
 ],
 "specialty": {
  "base": {
   "addInfo": "creature.sharpshooter",
   "type": "SPECIAL_UPGRADE"
  },
  "bonuses": {
   "damage": {
    "type": "CREATURE_DAMAGE",
    "subtype": "creatureDamageBoth",
    "val": 2,
    "valueType": "BASE_NUMBER"
   },
   "archer2sharpshooter": {
    "subtype": "creature.archer"
   },
   "grandElf2sharpshooter": {
    "subtype": "creature.grandElf"
   },
   "marksman2sharpshooter": {
    "subtype": "creature.marksman"
   },
   "woodElf2sharpshooter": {
    "subtype": "creature.woodElf"
   },
   "skel12sharpshooter": {
    "subtype": "deathvalley:boneHook"
   },
   "skel22sharpshooter": {
    "subtype": "deathvalley:flayer"
   },
   "shadowhunter2sharpshooter": {
    "subtype": "asylum:shadowhunter"
   },
   "phantomhunter2sharpshooter": {
    "subtype": "asylum:phantomhunter"
   },
   "lifeDrain": {
    "type": "LIFE_DRAIN",
    "val": 100,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "sharpshooter",
      "includeUpgrades": true
     }
    ]
   },
   "freeShooting": {
    "type": "FREE_SHOOTING",
    "val": 0,
    "valueType": "ADDITIVE_VALUE",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "sharpshooter",
      "includeUpgrades": true
     }
    ]
   },
   "endlessRetaliation": {
    "type": "UNLIMITED_RETALIATIONS",
    "icon": "factory/icons/bonuses/e_retail2_bountyhunter.png",
    "description": "{远程无限反击}\n可以无限次反击敌人的远程攻击",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "sharpshooter",
      "includeUpgrades": true
     }
    ]
   },
   "rangedRetaliation": {
    "type": "RANGED_RETALIATION",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "sharpshooter",
      "includeUpgrades": true
     }
    ]
   },
   "firstStrike": {
    "type": "FIRST_STRIKE",
    "subtype": "damageTypeRanged",
    "icon": "factory/icons/bonuses/firststrike_ranged.png",
    "description": "{抢先攻击}\n可以在敌人远程攻击前进行反击",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "sharpshooter",
      "includeUpgrades": true
     }
    ]
   },
   "ignoreDefence": {
    "type": "ENEMY_DEFENCE_REDUCTION",
    "val": 100,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "sharpshooter",
      "includeUpgrades": true
     }
    ]
   },
   "wallAttack": {
    "type": "CATAPULT",
    "subtype": "spell.cyclopsShot",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "sharpshooter",
      "includeUpgrades": true
     }
    ]
   },
   "wallAttackExtraShot": {
    "type": "CATAPULT_EXTRA_SHOTS",
    "subtype": "spell.cyclopsShot",
    "valueType": "BASE_NUMBER",
    "val": 1,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "sharpshooter",
      "includeUpgrades": true
     }
    ]
   },
   "healthBonus": {
    "type": "STACK_HEALTH",
    "val": 10,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "sharpshooter",
      "includeUpgrades": true
     }
    ]
   }
  }
 }
}
```

</details>

### 艾瑞莎（`fairy:ftO_Elrisa`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：初始魔法书补至64项，包含普通1—3级冒险／战斗法术和力量光环；排除英雄专属隐形、药剂、符文和内部效果，保留原名单中的4级反戈一击与5级牺牲。因此描述“所有1—3级魔法”不是对全部注册法术无条件全收录。

- 可用性：`special=false`（缺省按非特殊）；职业：`warden`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"advanced","skill":"wisdom"},{"level":"basic","skill":"scholar"}]。
- 初始法术书：["core:summonBoat","core:scuttleBoat","core:visions","core:viewEarth","core:disguise","core:viewAir","core:forceField","courtyard:quagmire","courtyard:immolationCurse","manaFlarePyra","primeMissile","timeStasis","sacrifice","mdtAuraOfPower","curseOfPharaoh","magicArrow","haste","curse","precision","bloodlust","protectFire","slow","disruptingRay","lightningBolt","fortune","shield","stoneSkin","cure","protectAir","fireWall","blind","dispel","bless","protectWater","mdtCancel","waspSwarm","quicksand","deathRipple","iceBolt","removeObstacle","fireball","landMine","quickStrikes","weakness","airShield","counterstrike","earthquake","hypnotize","levitation","mdtDivineArrows","mdtBind","animateDead","antiMagic","destroyUndead","misfortune","reconstruction","protectEarth","enchantedWeapon","frostRing","mirth","regeneration","confusion","forgetfulness","teleport"]。授予整系施法权限与显式已学法术书不是同一件事。

**描述检查**：“所有1—3级”未说明专属隐形等排除项及保留的4／5级法术

- 当前特长名：神秘先知。
- 当前说明：初始即掌握所有1-3级魔法。
- 当前tooltip：{Scribe}<br><br>All 1-3 level spells in spellbook.

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft15_elrisa.json](<Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft15_elrisa.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-06-07 `654fd487`：`skills`：`[{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"waterMagic"}]` → `[{"level":"advanced","skill":"wisdom"},{"level":"basic","skill":"scholar"}]`；`spellbook`：`["viewEarth","bloodlust","cure","curse","dispel","bless","haste","magicArrow","protectFire","protectWater","shield","slow","summonBoat","stoneSkin","viewAir"]` → `["magicArrow","haste","curse","manaFlarePyra","primeMissile","precision","bloodlust","protectFire","slow","disruptingRay","lightningBolt","luck","shield","stoneSkin","cure","protectAir","fireWall","blind","dispel","bless","protectWater","mdtCancel","waspSwarm","quicksand","deathRipple","iceBolt","removeObstacle","fireball","landMine","quickStrikes","weakness","timeStasis","airShield","sacrifice","counterstrike","earthquake","hypnotize","levitation","mdtDivineArrows","mdtBind","animateDead","antiMagic","destroyUndead","misfortune","mdtAuraOfPower","reconstruction","protectEarth","enchantedWeapon","frostRing","mirth","regeneration","confusion","forgetfulness","teleport","curseOfPharaoh"]`；`texts.specialty.description`：`"初始即掌握所有1级魔法。"` → `"初始即掌握所有1-3级魔法。"`；`texts.specialty.tooltip`：`"{Scribe}\r\n\r\nAll 1st level spells in spellbook."` → `"{Scribe}\r\n\r\nAll 1-3 level spells in spellbook."`。
- 2026-06-07 `72600897`：`spellbook`：`["magicArrow","haste","curse","manaFlarePyra","primeMissile","precision","bloodlust","protectFire","slow","disruptingRay","lightningBolt","luck","shield","stoneSkin","cure","protectAir","fireWall","blind","dispel","bless","protectWater","mdtCancel","waspSwarm","quicksand","deathRipple","iceBolt","removeObstacle","fireball","landMine","quickStrikes","weakness","timeStasis","airShield","sacrifice","counterstrike","earthquake","hypnotize","levitation","mdtDivineArrows","mdtBind","animateDead","antiMagic","destroyUndead","misfortune","mdtAuraOfPower","reconstruction","protectEarth","enchantedWeapon","frostRing","mirth","regeneration","confusion","forgetfulness","teleport","curseOfPharaoh"]` → `["manaFlarePyra","primeMissile","timeStasis","sacrifice","mdtAuraOfPower","curseOfPharaoh","magicArrow","haste","curse","precision","bloodlust","protectFire","slow","disruptingRay","lightningBolt","fortune","shield","stoneSkin","cure","protectAir","fireWall","blind","dispel","bless","protectWater","mdtCancel","waspSwarm","quicksand","deathRipple","iceBolt","removeObstacle","fireball","landMine","quickStrikes","weakness","airShield","counterstrike","earthquake","hypnotize","levitation","mdtDivineArrows","mdtBind","animateDead","antiMagic","destroyUndead","misfortune","reconstruction","protectEarth","enchantedWeapon","frostRing","mirth","regeneration","confusion","forgetfulness","teleport"]`。
- 2026-11-08 `138763f2`：`spellbook`：`["manaFlarePyra","primeMissile","timeStasis","sacrifice","mdtAuraOfPower","curseOfPharaoh","magicArrow","haste","curse","precision","bloodlust","protectFire","slow","disruptingRay","lightningBolt","fortune","shield","stoneSkin","cure","protectAir","fireWall","blind","dispel","bless","protectWater","mdtCancel","waspSwarm","quicksand","deathRipple","iceBolt","removeObstacle","fireball","landMine","quickStrikes","weakness","airShield","counterstrike","earthquake","hypnotize","levitation","mdtDivineArrows","mdtBind","animateDead","antiMagic","destroyUndead","misfortune","reconstruction","protectEarth","enchantedWeapon","frostRing","mirth","regeneration","confusion","forgetfulness","teleport"]` → `["core:summonBoat","core:scuttleBoat","core:visions","core:viewEarth","core:disguise","core:viewAir","core:forceField","courtyard:quagmire","courtyard:immolationCurse","manaFlarePyra","primeMissile","timeStasis","sacrifice","mdtAuraOfPower","curseOfPharaoh","magicArrow","haste","curse","precision","bloodlust","protectFire","slow","disruptingRay","lightningBolt","fortune","shield","stoneSkin","cure","protectAir","fireWall","blind","dispel","bless","protectWater","mdtCancel","waspSwarm","quicksand","deathRipple","iceBolt","removeObstacle","fireball","landMine","quickStrikes","weakness","airShield","counterstrike","earthquake","hypnotize","levitation","mdtDivineArrows","mdtBind","animateDead","antiMagic","destroyUndead","misfortune","reconstruction","protectEarth","enchantedWeapon","frostRing","mirth","regeneration","confusion","forgetfulness","teleport"]`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "class": "warden",
 "female": true,
 "texts": {
  "name": "艾瑞莎",
  "biography": "在和平时期，艾瑞莎加入了元素魔法师公会，并且凭借出色的记忆力掌握了所有的初级魔法。然而随着花仙和元素城的分离，她很快成为了花仙王国中最出色的魔法教师。",
  "specialty": {
   "description": "初始即掌握所有1-3级魔法。",
   "tooltip": "{Scribe}\r\n\r\nAll 1-3 level spells in spellbook.",
   "name": "神秘先知"
  }
 },
 "images": {
  "specialtySmall": "fairy/heroes/suliss/Un32Scri.png",
  "specialtyLarge": "fairy/heroes/suliss/Un44Scri.png",
  "large": "fairy/heroes/suliss/hpl293.png",
  "small": "fairy/heroes/suliss/hps293.png"
 },
 "spellbook": [
  "core:summonBoat",
  "core:scuttleBoat",
  "core:visions",
  "core:viewEarth",
  "core:disguise",
  "core:viewAir",
  "core:forceField",
  "courtyard:quagmire",
  "courtyard:immolationCurse",
  "manaFlarePyra",
  "primeMissile",
  "timeStasis",
  "sacrifice",
  "mdtAuraOfPower",
  "curseOfPharaoh",
  "magicArrow",
  "haste",
  "curse",
  "precision",
  "bloodlust",
  "protectFire",
  "slow",
  "disruptingRay",
  "lightningBolt",
  "fortune",
  "shield",
  "stoneSkin",
  "cure",
  "protectAir",
  "fireWall",
  "blind",
  "dispel",
  "bless",
  "protectWater",
  "mdtCancel",
  "waspSwarm",
  "quicksand",
  "deathRipple",
  "iceBolt",
  "removeObstacle",
  "fireball",
  "landMine",
  "quickStrikes",
  "weakness",
  "airShield",
  "counterstrike",
  "earthquake",
  "hypnotize",
  "levitation",
  "mdtDivineArrows",
  "mdtBind",
  "animateDead",
  "antiMagic",
  "destroyUndead",
  "misfortune",
  "reconstruction",
  "protectEarth",
  "enchantedWeapon",
  "frostRing",
  "mirth",
  "regeneration",
  "confusion",
  "forgetfulness",
  "teleport"
 ],
 "army": [
  {
   "creature": "ftFairy1",
   "max": 30,
   "min": 20
  },
  {
   "creature": "ftFench1",
   "max": 10,
   "min": 8
  },
  {
   "creature": "ftNoble1",
   "max": 6,
   "min": 4
  }
 ],
 "skills": [
  {
   "level": "advanced",
   "skill": "wisdom"
  },
  {
   "level": "basic",
   "skill": "scholar"
  }
 ],
 "specialty": {
  "bonuses": {}
 }
}
```

</details>

### 埃莉娜（`fairy:ftP_Arina`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：保留外交特长与初始高级智慧；增加死亡之握及英雄可用隐形。隐形是独立注册英雄法术，不直接把生物能力加入魔法书。

- 通用技能特长：`diplomacy`；每级相对成长参数：`10`，不是每级直接加相同百分点。
- 可用性：`special=false`（缺省按非特殊）；职业：`warden`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"basic","skill":"diplomacy"},{"level":"expert","skill":"wisdom"},{"level":"basic","skill":"logistics"}]。
- 初始法术书：["counterstrike","courtyard:deathGrasp","heroBansheeInvisibility"]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `diplomaBonus` | 野怪加入判定加成 `WANDERING_CREATURES_JOIN_BONUS`；值：`3`；数值方式：`"BASE_NUMBER"` |
| `specialSlow` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"new pavilion.new pavillion:spell.specialSlowPav"` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：外交术。
- 当前说明：在外交术技能上每级减少10%的投降费用，并降低野外中立生物的敌意值3点。
- 当前tooltip：{Loyalty}<br><br>Cost of Surrender -40%<br>Neutral creature hostility -3

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json](<Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/specialtyDescriptions.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/specialtyDescriptions.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-06-07 `7cca90ae`：`specialty.bonuses.diplomaBonus.type`：`null` → `"WANDERING_CREATURES_JOIN_BONUS"`；`specialty.bonuses.diplomaBonus.val`：`null` → `3`；`specialty.bonuses.diplomaBonus.valueType`：`null` → `"BASE_NUMBER"`；`texts.specialty.description`：`"在外交术技能上每级减少5%的投降费用。"` → `"在外交术技能上每级减少5%的投降费用，并降低野外中立生物的敌意值3点。"`；`texts.specialty.tooltip`：`"{Loyalty}\r\n\r\nCost of Surrender -40%"` → `"{Loyalty}\r\n\r\nCost of Surrender -40%\r\nNeutral creature hostility -3"`。
- 2026-06-12 `15186f5b`：`skills`：`[{"level":"basic","skill":"diplomacy"},{"level":"basic","skill":"wisdom"}]` → `[{"level":"basic","skill":"diplomacy"},{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"logistics"}]`。
- 2026-06-13 `f9592afc`：`skills`：`[{"level":"basic","skill":"diplomacy"},{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"logistics"}]` → `[{"level":"basic","skill":"diplomacy"},{"level":"expert","skill":"wisdom"},{"level":"basic","skill":"logistics"}]`。
- 2026-08-02 `f51e1724`：`specialty.bonuses.specialSlow.subtype`：`null` → `"new pavilion.new pavillion:spell.specialSlowPav"`；`specialty.bonuses.specialSlow.type`：`null` → `"SPELL"`；`spellbook`：`["counterstrike"]` → `["counterstrike","courtyard:deathGrasp","heroBansheeInvisibility"]`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "class": "warden",
 "female": true,
 "texts": {
  "name": "埃莉娜",
  "biography": "据说埃莉娜是如此善良和友善，这片土地上这个时代的每一个将军都受过她的恩惠。因此在交战时许多人会比给其他人更容易给予她安全逃生的通道。",
  "specialty": {
   "description": "在外交术技能上每级减少10%的投降费用，并降低野外中立生物的敌意值3点。",
   "tooltip": "{Loyalty}\r\n\r\nCost of Surrender -40%\r\nNeutral creature hostility -3",
   "name": "外交术"
  }
 },
 "images": {
  "specialtySmall": "fairy/heroes/tuidhana/Un32Fmag.png",
  "specialtyLarge": "fairy/heroes/tuidhana/Un44Fmag.png",
  "large": "fairy/heroes/tuidhana/hpl300.png",
  "small": "fairy/heroes/tuidhana/hps300.png"
 },
 "spellbook": [
  "counterstrike",
  "courtyard:deathGrasp",
  "heroBansheeInvisibility"
 ],
 "army": [
  {
   "creature": "ftFairy1",
   "max": 30,
   "min": 20
  },
  {
   "creature": "ftFench1",
   "max": 10,
   "min": 8
  },
  {
   "creature": "ftNoble1",
   "max": 6,
   "min": 4
  }
 ],
 "skills": [
  {
   "level": "basic",
   "skill": "diplomacy"
  },
  {
   "level": "expert",
   "skill": "wisdom"
  },
  {
   "level": "basic",
   "skill": "logistics"
  }
 ],
 "specialty": {
  "secondary": "diplomacy",
  "bonuses": {
   "diplomaBonus": {
    "type": "WANDERING_CREATURES_JOIN_BONUS",
    "val": 3,
    "valueType": "BASE_NUMBER"
   },
   "specialSlow": {
    "subtype": "new pavilion.new pavillion:spell.specialSlowPav",
    "type": "SPELL"
   }
  }
 }
}
```

</details>

### 艾伦（`haven:havAhero05special`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：艾伦：保留每级5%相对航海术成长；上下船保留移动力、海上额外移动5000、视野+5、每日金币5000／木材5／矿石5、漩涡免疫、每日固定恢复50魔法及最大值30%，全部水系施法权限与专家水系。注意视野、资源、法力恢复并未加“必须在海上”限制，陆地也有效；只有MOVEMENT的heroMovementSea明确限定海上。

- 可用性：`special=true`（缺省按非特殊）；职业：`havAhero`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"basic","skill":"offence"},{"level":"basic","skill":"navigation"}]。
- 初始法术书：[]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `navigation` | 移动力 `MOVEMENT`；对象／属性：`"heroMovementSea"`；值：`5`；数值方式：`"PERCENT_TO_TARGET_TYPE"`；成长规则：`"TIMES_HERO_LEVEL"` |
| `freeShipBoarding` | 上下船保留移动力 `FREE_SHIP_BOARDING`；值：`0`；数值方式：`"BASE_NUMBER"` |
| `seaSight` | 视野半径 `SIGHT_RADIUS`；值：`5`；数值方式：`"BASE_NUMBER"` |
| `seaMoveFlat` | 移动力 `MOVEMENT`；对象／属性：`"heroMovementSea"`；值：`5000`；数值方式：`"ADDITIVE_VALUE"` |
| `goldIncome` | 每日资源 `GENERATE_RESOURCE`；对象／属性：`"resource.gold"`；值：`5000`；数值方式：`"BASE_NUMBER"` |
| `woodIncome` | 每日资源 `GENERATE_RESOURCE`；对象／属性：`"resource.wood"`；值：`5`；数值方式：`"BASE_NUMBER"` |
| `oreIncome` | 每日资源 `GENERATE_RESOURCE`；对象／属性：`"resource.ore"`；值：`5`；数值方式：`"BASE_NUMBER"` |
| `whirlpoolProtection` | 漩涡兵力损失免疫 `WHIRLPOOL_PROTECTION`；值：`0`；数值方式：`"BASE_NUMBER"` |
| `manaFlatRegen` | 每日固定魔法恢复 `MANA_REGENERATION`；值：`50`；数值方式：`"BASE_NUMBER"` |
| `manaPercentRegen` | 每日百分比魔法恢复 `MANA_PERCENTAGE_REGENERATION`；值：`30`；数值方式：`"BASE_NUMBER"` |
| `waterSpellsBook` | 授予整系法术权限 `SPELLS_OF_SCHOOL`；对象／属性：`"water"`；值：`0`；数值方式：`"BASE_NUMBER"` |
| `waterMagicExpert` | 学派熟练度 `MAGIC_SCHOOL_SKILL`；对象／属性：`"spellSchool.water"`；值：`3`；数值方式：`"BASE_NUMBER"` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：航海术。
- 当前说明：提升航海术效果，并额外获得上下船不耗移动力、海上移动力 +5000、视野 +5、每日金币 +5000、木材 +5、矿石 +5、漩涡免疫、每日恢复 50 点与 30% 最大值法力，并直接掌握全部水系魔法且视为专家水系魔法。
- 当前tooltip：No description

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/haven/content/config/haven/heroes/havAhero05B.json](<Hero3Expasion1.83/Mods/haven/content/config/haven/heroes/havAhero05B.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-07-05 `c319c49b`：`specialty.bonuses.freeShipBoarding.type`：`null` → `"FREE_SHIP_BOARDING"`；`specialty.bonuses.freeShipBoarding.val`：`null` → `0`；`specialty.bonuses.freeShipBoarding.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.goldIncome.subtype`：`null` → `"resource.gold"`；`specialty.bonuses.goldIncome.type`：`null` → `"GENERATE_RESOURCE"`；`specialty.bonuses.goldIncome.val`：`null` → `5000`；`specialty.bonuses.goldIncome.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.manaFlatRegen.type`：`null` → `"MANA_REGENERATION"`；`specialty.bonuses.manaFlatRegen.val`：`null` → `50`；`specialty.bonuses.manaFlatRegen.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.manaPercentRegen.type`：`null` → `"MANA_PERCENTAGE_REGENERATION"`；`specialty.bonuses.manaPercentRegen.val`：`null` → `30`；`specialty.bonuses.manaPercentRegen.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.oreIncome.subtype`：`null` → `"resource.ore"`；`specialty.bonuses.oreIncome.type`：`null` → `"GENERATE_RESOURCE"`；`specialty.bonuses.oreIncome.val`：`null` → `5`；`specialty.bonuses.oreIncome.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.seaMoveFlat.subtype`：`null` → `"heroMovementSea"`；`specialty.bonuses.seaMoveFlat.type`：`null` → `"MOVEMENT"`；`specialty.bonuses.seaMoveFlat.val`：`null` → `5000`；`specialty.bonuses.seaMoveFlat.valueType`：`null` → `"ADDITIVE_VALUE"`；`specialty.bonuses.seaSight.type`：`null` → `"SIGHT_RADIUS"`；`specialty.bonuses.seaSight.val`：`null` → `5`；`specialty.bonuses.seaSight.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.waterMagicExpert.subtype`：`null` → `"spellSchool.water"`；`specialty.bonuses.waterMagicExpert.type`：`null` → `"MAGIC_SCHOOL_SKILL"`；`specialty.bonuses.waterMagicExpert.val`：`null` → `3`；`specialty.bonuses.waterMagicExpert.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.waterSpellsBook.subtype`：`null` → `"water"`；`specialty.bonuses.waterSpellsBook.type`：`null` → `"SPELLS_OF_SCHOOL"`；`specialty.bonuses.waterSpellsBook.val`：`null` → `0`；`specialty.bonuses.waterSpellsBook.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.whirlpoolProtection.type`：`null` → `"WHIRLPOOL_PROTECTION"`；`specialty.bonuses.whirlpoolProtection.val`：`null` → `0`；`specialty.bonuses.whirlpoolProtection.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.woodIncome.subtype`：`null` → `"resource.wood"`；`specialty.bonuses.woodIncome.type`：`null` → `"GENERATE_RESOURCE"`；`specialty.bonuses.woodIncome.val`：`null` → `5`；`specialty.bonuses.woodIncome.valueType`：`null` → `"BASE_NUMBER"`；`texts.biography`：`"曾经还是孩童的艾伦就迷上了海上生活。他从小以打渔为生，后来加入了海盗团队。但没人知道艾伦为什么会以海盗的身份生活。他看起来并不出众，但却总是能出其不意地展现他的才能。"` → `"艾伦自幼迷上海上生活，从打渔少年成长为经验老到的海上冒险者。他熟悉潮汐、航线与风暴，也擅长把一支舰队维持在最适合作战的状态。"`；`texts.specialty.description`：`"在航海术上每级得到5%的奖励。"` → `"提升航海术效果，并额外获得上下船不耗移动力、海上移动力 +5000、视野 +5、每日金币 +5000、木材 +5、矿石 +5、漩涡免疫、每日恢复 50 点与 30% 最大值法力，并直接掌握全部水系魔法且视为专家水系魔法。"`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "class": "havAhero",
 "female": false,
 "special": true,
 "texts": {
  "name": "艾伦",
  "biography": "艾伦自幼迷上海上生活，从打渔少年成长为经验老到的海上冒险者。他熟悉潮汐、航线与风暴，也擅长把一支舰队维持在最适合作战的状态。",
  "specialty": {
   "tooltip": "No description",
   "name": "航海术",
   "description": "提升航海术效果，并额外获得上下船不耗移动力、海上移动力 +5000、视野 +5、每日金币 +5000、木材 +5、矿石 +5、漩涡免疫、每日恢复 50 点与 30% 最大值法力，并直接掌握全部水系魔法且视为专家水系魔法。"
  }
 },
 "images": {
  "specialtySmall": "haven/heroes/havHero5/skill5BS.bmp",
  "specialtyLarge": "haven/heroes/havHero5/skill5B.bmp",
  "large": "haven/heroes/havHero5/Allen.bmp",
  "small": "haven/heroes/havHero5/Allens.bmp"
 },
 "army": [
  {
   "creature": "havC01",
   "max": 30,
   "min": 20
  },
  {
   "creature": "havC03",
   "max": 10,
   "min": 8
  },
  {
   "creature": "havC05",
   "max": 6,
   "min": 4
  }
 ],
 "skills": [
  {
   "level": "basic",
   "skill": "offence"
  },
  {
   "level": "basic",
   "skill": "navigation"
  }
 ],
 "specialty": {
  "bonuses": {
   "navigation": {
    "targetSourceType": "SECONDARY_SKILL",
    "subtype": "heroMovementSea",
    "type": "MOVEMENT",
    "updater": "TIMES_HERO_LEVEL",
    "val": 5,
    "valueType": "PERCENT_TO_TARGET_TYPE"
   },
   "freeShipBoarding": {
    "type": "FREE_SHIP_BOARDING",
    "val": 0,
    "valueType": "BASE_NUMBER"
   },
   "seaSight": {
    "type": "SIGHT_RADIUS",
    "val": 5,
    "valueType": "BASE_NUMBER"
   },
   "seaMoveFlat": {
    "type": "MOVEMENT",
    "subtype": "heroMovementSea",
    "val": 5000,
    "valueType": "ADDITIVE_VALUE"
   },
   "goldIncome": {
    "type": "GENERATE_RESOURCE",
    "subtype": "resource.gold",
    "val": 5000,
    "valueType": "BASE_NUMBER"
   },
   "woodIncome": {
    "type": "GENERATE_RESOURCE",
    "subtype": "resource.wood",
    "val": 5,
    "valueType": "BASE_NUMBER"
   },
   "oreIncome": {
    "type": "GENERATE_RESOURCE",
    "subtype": "resource.ore",
    "val": 5,
    "valueType": "BASE_NUMBER"
   },
   "whirlpoolProtection": {
    "type": "WHIRLPOOL_PROTECTION",
    "val": 0,
    "valueType": "BASE_NUMBER"
   },
   "manaFlatRegen": {
    "type": "MANA_REGENERATION",
    "val": 50,
    "valueType": "BASE_NUMBER"
   },
   "manaPercentRegen": {
    "type": "MANA_PERCENTAGE_REGENERATION",
    "val": 30,
    "valueType": "BASE_NUMBER"
   },
   "waterSpellsBook": {
    "type": "SPELLS_OF_SCHOOL",
    "subtype": "water",
    "val": 0,
    "valueType": "BASE_NUMBER"
   },
   "waterMagicExpert": {
    "type": "MAGIC_SCHOOL_SKILL",
    "subtype": "spellSchool.water",
    "val": 3,
    "valueType": "BASE_NUMBER"
   }
  }
 }
}
```

</details>

### 亚撒利雅（`new pavilion.new pavillion:azarias`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：蛇类特长扩展为9组并覆盖升级形态，显式攻防系数9，按英雄等级／兵种等级成长，速度+2。原沙蛇通用特长仍保留：默认40%攻防成长与速度+1，故沙蛇叠加两套效果、速度合计+3；其他蛇类不是统一20%公式。

- 通用兵种特长目标：`wadjet`；成长参数：`40`；未显式重写时按通用机制给予目标及升级形态速度+1。
- 可用性：`special=false`（缺省按非特殊）；职业：`vizier`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"basic","skill":"armorer"},{"level":"basic","skill":"luck"}]。
- 初始法术书：[]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `wadjetAttack` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.attack"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"wadjet","includeUpgrades":true}]` |
| `wadjetDefence` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.defence"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"wadjet","includeUpgrades":true}]` |
| `wadjetSpeed` | 部队速度 `STACKS_SPEED`；值：`2`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"wadjet","includeUpgrades":true}]` |
| `serpentFlyAttack` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.attack"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"serpentFly","includeUpgrades":true}]` |
| `serpentFlyDefence` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.defence"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"serpentFly","includeUpgrades":true}]` |
| `serpentFlySpeed` | 部队速度 `STACKS_SPEED`；值：`2`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"serpentFly","includeUpgrades":true}]` |
| `nagaAttack` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.attack"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"naga","includeUpgrades":true}]` |
| `nagaDefence` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.defence"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"naga","includeUpgrades":true}]` |
| `nagaSpeed` | 部队速度 `STACKS_SPEED`；值：`2`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"naga","includeUpgrades":true}]` |
| `medusaAttack` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.attack"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"medusa","includeUpgrades":true}]` |
| `medusaDefence` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.defence"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"medusa","includeUpgrades":true}]` |
| `medusaSpeed` | 部队速度 `STACKS_SPEED`；值：`2`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"medusa","includeUpgrades":true}]` |
| `cathedralCouatlAttack` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.attack"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"cthCouatl","includeUpgrades":true}]` |
| `cathedralCouatlDefence` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.defence"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"cthCouatl","includeUpgrades":true}]` |
| `cathedralCouatlSpeed` | 部队速度 `STACKS_SPEED`；值：`2`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"cthCouatl","includeUpgrades":true}]` |
| `tidesCouatlAttack` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.attack"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"mdtCouatl","includeUpgrades":true}]` |
| `tidesCouatlDefence` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.defence"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"mdtCouatl","includeUpgrades":true}]` |
| `tidesCouatlSpeed` | 部队速度 `STACKS_SPEED`；值：`2`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"mdtCouatl","includeUpgrades":true}]` |
| `seaSerpentAttack` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.attack"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"seaserpent","includeUpgrades":true}]` |
| `seaSerpentDefence` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.defence"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"seaserpent","includeUpgrades":true}]` |
| `seaSerpentSpeed` | 部队速度 `STACKS_SPEED`；值：`2`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"seaserpent","includeUpgrades":true}]` |
| `leviathanAttack` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.attack"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"havC13","includeUpgrades":true}]` |
| `leviathanDefence` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.defence"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"havC13","includeUpgrades":true}]` |
| `leviathanSpeed` | 部队速度 `STACKS_SPEED`；值：`2`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"havC13","includeUpgrades":true}]` |
| `factoryCouatlAttack` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.attack"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"hotaCouatl","includeUpgrades":true}]` |
| `factoryCouatlDefence` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.defence"`；值：`9`；成长规则：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"hotaCouatl","includeUpgrades":true}]` |
| `factoryCouatlSpeed` | 部队速度 `STACKS_SPEED`；值：`2`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"hotaCouatl","includeUpgrades":true}]` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：沙蛇。
- 当前说明：强化沙蛇、飞龙蝇、蛇妖、美杜莎、圣堂飞蛇、风蛇、海蛇、利维坦、工厂羽蛇及其升级形态：额外攻防按英雄等级与目标生物等级计算，系数9；速度+2。沙蛇一系另保留通用兵种特长，每达到基础沙蛇等级的整数倍，基础攻防再提高40%，速度额外+1（合计+3）。实际数值按引擎取整。
- 当前tooltip：Creature Bonus: Wadjets

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Azarias.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Azarias.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/pavilion.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/pavilion.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/specialtyDescriptions.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/specialtyDescriptions.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-07-20 `df8983f9`：`specialty.bonuses.cathedralCouatlAttack.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"cthCouatl","includeUpgrades":true}]`；`specialty.bonuses.cathedralCouatlAttack.subtype`：`null` → `"primarySkill.attack"`；`specialty.bonuses.cathedralCouatlAttack.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.cathedralCouatlAttack.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.cathedralCouatlAttack.val`：`null` → `2`；`specialty.bonuses.cathedralCouatlDefence.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"cthCouatl","includeUpgrades":true}]`；`specialty.bonuses.cathedralCouatlDefence.subtype`：`null` → `"primarySkill.defence"`；`specialty.bonuses.cathedralCouatlDefence.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.cathedralCouatlDefence.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.cathedralCouatlDefence.val`：`null` → `2`；`specialty.bonuses.cathedralCouatlSpeed.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"cthCouatl","includeUpgrades":true}]`；`specialty.bonuses.cathedralCouatlSpeed.type`：`null` → `"STACKS_SPEED"`；`specialty.bonuses.cathedralCouatlSpeed.val`：`null` → `1`；`specialty.bonuses.factoryCouatlAttack.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"hotaCouatl","includeUpgrades":true}]`；`specialty.bonuses.factoryCouatlAttack.subtype`：`null` → `"primarySkill.attack"`；`specialty.bonuses.factoryCouatlAttack.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.factoryCouatlAttack.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.factoryCouatlAttack.val`：`null` → `2`；`specialty.bonuses.factoryCouatlDefence.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"hotaCouatl","includeUpgrades":true}]`；`specialty.bonuses.factoryCouatlDefence.subtype`：`null` → `"primarySkill.defence"`；`specialty.bonuses.factoryCouatlDefence.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.factoryCouatlDefence.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.factoryCouatlDefence.val`：`null` → `2`；`specialty.bonuses.factoryCouatlSpeed.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"hotaCouatl","includeUpgrades":true}]`；`specialty.bonuses.factoryCouatlSpeed.type`：`null` → `"STACKS_SPEED"`；`specialty.bonuses.factoryCouatlSpeed.val`：`null` → `1`；`specialty.bonuses.leviathanAttack.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"havC13","includeUpgrades":true}]`；`specialty.bonuses.leviathanAttack.subtype`：`null` → `"primarySkill.attack"`；`specialty.bonuses.leviathanAttack.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.leviathanAttack.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.leviathanAttack.val`：`null` → `2`；`specialty.bonuses.leviathanDefence.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"havC13","includeUpgrades":true}]`；`specialty.bonuses.leviathanDefence.subtype`：`null` → `"primarySkill.defence"`；`specialty.bonuses.leviathanDefence.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.leviathanDefence.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.leviathanDefence.val`：`null` → `2`；`specialty.bonuses.leviathanSpeed.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"havC13","includeUpgrades":true}]`；`specialty.bonuses.leviathanSpeed.type`：`null` → `"STACKS_SPEED"`；`specialty.bonuses.leviathanSpeed.val`：`null` → `1`；`specialty.bonuses.nagaAttack.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"naga","includeUpgrades":true}]`；`specialty.bonuses.nagaAttack.subtype`：`null` → `"primarySkill.attack"`；`specialty.bonuses.nagaAttack.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.nagaAttack.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.nagaAttack.val`：`null` → `2`；`specialty.bonuses.nagaDefence.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"naga","includeUpgrades":true}]`；`specialty.bonuses.nagaDefence.subtype`：`null` → `"primarySkill.defence"`；`specialty.bonuses.nagaDefence.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.nagaDefence.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.nagaDefence.val`：`null` → `2`；`specialty.bonuses.nagaOfficerAttack.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"twArcane06","includeUpgrades":true}]`；`specialty.bonuses.nagaOfficerAttack.subtype`：`null` → `"primarySkill.attack"`；`specialty.bonuses.nagaOfficerAttack.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.nagaOfficerAttack.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.nagaOfficerAttack.val`：`null` → `2`；`specialty.bonuses.nagaOfficerDefence.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"twArcane06","includeUpgrades":true}]`；`specialty.bonuses.nagaOfficerDefence.subtype`：`null` → `"primarySkill.defence"`；`specialty.bonuses.nagaOfficerDefence.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.nagaOfficerDefence.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.nagaOfficerDefence.val`：`null` → `2`；`specialty.bonuses.nagaOfficerSpeed.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"twArcane06","includeUpgrades":true}]`；`specialty.bonuses.nagaOfficerSpeed.type`：`null` → `"STACKS_SPEED"`；`specialty.bonuses.nagaOfficerSpeed.val`：`null` → `1`；`specialty.bonuses.nagaSpeed.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"naga","includeUpgrades":true}]`；`specialty.bonuses.nagaSpeed.type`：`null` → `"STACKS_SPEED"`；`specialty.bonuses.nagaSpeed.val`：`null` → `1`；`specialty.bonuses.seaSerpentAttack.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"seaserpent","includeUpgrades":true}]`；`specialty.bonuses.seaSerpentAttack.subtype`：`null` → `"primarySkill.attack"`；`specialty.bonuses.seaSerpentAttack.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.seaSerpentAttack.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.seaSerpentAttack.val`：`null` → `2`；`specialty.bonuses.seaSerpentDefence.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"seaserpent","includeUpgrades":true}]`；`specialty.bonuses.seaSerpentDefence.subtype`：`null` → `"primarySkill.defence"`；`specialty.bonuses.seaSerpentDefence.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.seaSerpentDefence.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.seaSerpentDefence.val`：`null` → `2`；`specialty.bonuses.seaSerpentSpeed.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"seaserpent","includeUpgrades":true}]`；`specialty.bonuses.seaSerpentSpeed.type`：`null` → `"STACKS_SPEED"`；`specialty.bonuses.seaSerpentSpeed.val`：`null` → `1`；`specialty.bonuses.serpentFlyAttack.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"serpentFly","includeUpgrades":true}]`；`specialty.bonuses.serpentFlyAttack.subtype`：`null` → `"primarySkill.attack"`；`specialty.bonuses.serpentFlyAttack.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.serpentFlyAttack.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.serpentFlyAttack.val`：`null` → `2`；`specialty.bonuses.serpentFlyDefence.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"serpentFly","includeUpgrades":true}]`；`specialty.bonuses.serpentFlyDefence.subtype`：`null` → `"primarySkill.defence"`；`specialty.bonuses.serpentFlyDefence.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.serpentFlyDefence.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.serpentFlyDefence.val`：`null` → `2`；`specialty.bonuses.serpentFlySpeed.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"serpentFly","includeUpgrades":true}]`；`specialty.bonuses.serpentFlySpeed.type`：`null` → `"STACKS_SPEED"`；`specialty.bonuses.serpentFlySpeed.val`：`null` → `1`；`specialty.bonuses.tidesCouatlAttack.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"mdtCouatl","includeUpgrades":true}]`；`specialty.bonuses.tidesCouatlAttack.subtype`：`null` → `"primarySkill.attack"`；`specialty.bonuses.tidesCouatlAttack.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.tidesCouatlAttack.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.tidesCouatlAttack.val`：`null` → `2`；`specialty.bonuses.tidesCouatlDefence.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"mdtCouatl","includeUpgrades":true}]`；`specialty.bonuses.tidesCouatlDefence.subtype`：`null` → `"primarySkill.defence"`；`specialty.bonuses.tidesCouatlDefence.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.tidesCouatlDefence.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.tidesCouatlDefence.val`：`null` → `2`；`specialty.bonuses.tidesCouatlSpeed.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"mdtCouatl","includeUpgrades":true}]`；`specialty.bonuses.tidesCouatlSpeed.type`：`null` → `"STACKS_SPEED"`；`specialty.bonuses.tidesCouatlSpeed.val`：`null` → `1`；`specialty.bonuses.wadjetAttack.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"wadjet","includeUpgrades":true}]`；`specialty.bonuses.wadjetAttack.subtype`：`null` → `"primarySkill.attack"`；`specialty.bonuses.wadjetAttack.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.wadjetAttack.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.wadjetAttack.val`：`null` → `2`；`specialty.bonuses.wadjetDefence.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"wadjet","includeUpgrades":true}]`；`specialty.bonuses.wadjetDefence.subtype`：`null` → `"primarySkill.defence"`；`specialty.bonuses.wadjetDefence.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.wadjetDefence.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.wadjetDefence.val`：`null` → `2`；`specialty.bonuses.wadjetSpeed.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"wadjet","includeUpgrades":true}]`；`specialty.bonuses.wadjetSpeed.type`：`null` → `"STACKS_SPEED"`；`specialty.bonuses.wadjetSpeed.val`：`null` → `1`；`texts.specialty.description`：`"每2级提高沙蛇及其升级形态20%的攻击力和防御力。速度+1。"` → `"每对应生物等级提高所有蛇类生物及其升级形态20%的攻击力和防御力。速度+1。"`。
- 2026-08-02 `b9376775`：`specialty.bonuses.cathedralCouatlAttack.val`：`2` → `9`；`specialty.bonuses.cathedralCouatlDefence.val`：`2` → `9`；`specialty.bonuses.cathedralCouatlSpeed.val`：`1` → `2`；`specialty.bonuses.factoryCouatlAttack.val`：`2` → `9`；`specialty.bonuses.factoryCouatlDefence.val`：`2` → `9`；`specialty.bonuses.factoryCouatlSpeed.val`：`1` → `2`；`specialty.bonuses.leviathanAttack.val`：`2` → `9`；`specialty.bonuses.leviathanDefence.val`：`2` → `9`；`specialty.bonuses.leviathanSpeed.val`：`1` → `2`；`specialty.bonuses.nagaAttack.val`：`2` → `9`；`specialty.bonuses.nagaDefence.val`：`2` → `9`；`specialty.bonuses.nagaOfficerAttack.val`：`2` → `9`；`specialty.bonuses.nagaOfficerDefence.val`：`2` → `9`；`specialty.bonuses.nagaOfficerSpeed.val`：`1` → `2`；`specialty.bonuses.nagaSpeed.val`：`1` → `2`；`specialty.bonuses.seaSerpentAttack.val`：`2` → `9`；`specialty.bonuses.seaSerpentDefence.val`：`2` → `9`；`specialty.bonuses.seaSerpentSpeed.val`：`1` → `2`；`specialty.bonuses.serpentFlyAttack.val`：`2` → `9`；`specialty.bonuses.serpentFlyDefence.val`：`2` → `9`；`specialty.bonuses.serpentFlySpeed.val`：`1` → `2`；`specialty.bonuses.tidesCouatlAttack.val`：`2` → `9`；`specialty.bonuses.tidesCouatlDefence.val`：`2` → `9`；`specialty.bonuses.tidesCouatlSpeed.val`：`1` → `2`；`specialty.bonuses.wadjetAttack.val`：`2` → `9`；`specialty.bonuses.wadjetDefence.val`：`2` → `9`；`specialty.bonuses.wadjetSpeed.val`：`1` → `2`。
- 2026-09-30 `ba5717ca`：`specialty.bonuses.medusaAttack.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"medusa","includeUpgrades":true}]`；`specialty.bonuses.medusaAttack.subtype`：`null` → `"primarySkill.attack"`；`specialty.bonuses.medusaAttack.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.medusaAttack.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.medusaAttack.val`：`null` → `9`；`specialty.bonuses.medusaDefence.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"medusa","includeUpgrades":true}]`；`specialty.bonuses.medusaDefence.subtype`：`null` → `"primarySkill.defence"`；`specialty.bonuses.medusaDefence.type`：`null` → `"PRIMARY_SKILL"`；`specialty.bonuses.medusaDefence.updater`：`null` → `"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"`；`specialty.bonuses.medusaDefence.val`：`null` → `9`；`specialty.bonuses.medusaSpeed.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"medusa","includeUpgrades":true}]`；`specialty.bonuses.medusaSpeed.type`：`null` → `"STACKS_SPEED"`；`specialty.bonuses.medusaSpeed.val`：`null` → `2`；`specialty.bonuses.nagaOfficerAttack.limiters`：`[{"type":"CREATURE_TYPE_LIMITER","creature":"twArcane06","includeUpgrades":true}]` → `null`；`specialty.bonuses.nagaOfficerAttack.subtype`：`"primarySkill.attack"` → `null`；`specialty.bonuses.nagaOfficerAttack.type`：`"PRIMARY_SKILL"` → `null`；`specialty.bonuses.nagaOfficerAttack.updater`：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"` → `null`；`specialty.bonuses.nagaOfficerAttack.val`：`9` → `null`；`specialty.bonuses.nagaOfficerDefence.limiters`：`[{"type":"CREATURE_TYPE_LIMITER","creature":"twArcane06","includeUpgrades":true}]` → `null`；`specialty.bonuses.nagaOfficerDefence.subtype`：`"primarySkill.defence"` → `null`；`specialty.bonuses.nagaOfficerDefence.type`：`"PRIMARY_SKILL"` → `null`；`specialty.bonuses.nagaOfficerDefence.updater`：`"TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL"` → `null`；`specialty.bonuses.nagaOfficerDefence.val`：`9` → `null`；`specialty.bonuses.nagaOfficerSpeed.limiters`：`[{"type":"CREATURE_TYPE_LIMITER","creature":"twArcane06","includeUpgrades":true}]` → `null`；`specialty.bonuses.nagaOfficerSpeed.type`：`"STACKS_SPEED"` → `null`；`specialty.bonuses.nagaOfficerSpeed.val`：`2` → `null`；`texts.specialty.description`：`"每对应生物等级提高所有蛇类生物及其升级形态20%的攻击力和防御力。速度+1。"` → `"每对应生物等级提高所有蛇类生物及其升级形态20%的攻击力和防御力。速度+2。"`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "class": "vizier",
 "female": false,
 "texts": {
  "name": "亚撒利雅",
  "biography": "亚撒利雅致力于研究世界各地的野生动物。当变得痴迷于此时，他把大部分时间花在荒野探险上，结果是，他对许多野兽长处和弱点的洞察力变得无可比拟。",
  "specialty": {
   "description": "强化沙蛇、飞龙蝇、蛇妖、美杜莎、圣堂飞蛇、风蛇、海蛇、利维坦、工厂羽蛇及其升级形态：额外攻防按英雄等级与目标生物等级计算，系数9；速度+2。沙蛇一系另保留通用兵种特长，每达到基础沙蛇等级的整数倍，基础攻防再提高40%，速度额外+1（合计+3）。实际数值按引擎取整。",
   "tooltip": "Creature Bonus: Wadjets",
   "name": "沙蛇"
  }
 },
 "images": {
  "specialtySmall": "speciconsPavilion/UN32Wadj.bmp",
  "specialtyLarge": "speciconsPavilion/UN44Wadj.bmp",
  "large": "heroportraitsPavilion/Azarias.png",
  "small": "heroportraitsPavilion/AzariasSmall.png"
 },
 "army": [
  {
   "creature": "wadjet",
   "max": 9,
   "min": 7
  },
  {
   "creature": "wadjet",
   "max": 9,
   "min": 7
  },
  {
   "creature": "wadjet",
   "max": 9,
   "min": 7
  }
 ],
 "skills": [
  {
   "level": "basic",
   "skill": "armorer"
  },
  {
   "level": "basic",
   "skill": "luck"
  }
 ],
 "specialty": {
  "creature": "wadjet",
  "bonuses": {
   "wadjetAttack": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.attack",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "wadjet",
      "includeUpgrades": true
     }
    ]
   },
   "wadjetDefence": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.defence",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "wadjet",
      "includeUpgrades": true
     }
    ]
   },
   "wadjetSpeed": {
    "type": "STACKS_SPEED",
    "val": 2,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "wadjet",
      "includeUpgrades": true
     }
    ]
   },
   "serpentFlyAttack": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.attack",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "serpentFly",
      "includeUpgrades": true
     }
    ]
   },
   "serpentFlyDefence": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.defence",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "serpentFly",
      "includeUpgrades": true
     }
    ]
   },
   "serpentFlySpeed": {
    "type": "STACKS_SPEED",
    "val": 2,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "serpentFly",
      "includeUpgrades": true
     }
    ]
   },
   "nagaAttack": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.attack",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "naga",
      "includeUpgrades": true
     }
    ]
   },
   "nagaDefence": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.defence",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "naga",
      "includeUpgrades": true
     }
    ]
   },
   "nagaSpeed": {
    "type": "STACKS_SPEED",
    "val": 2,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "naga",
      "includeUpgrades": true
     }
    ]
   },
   "medusaAttack": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.attack",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "medusa",
      "includeUpgrades": true
     }
    ]
   },
   "medusaDefence": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.defence",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "medusa",
      "includeUpgrades": true
     }
    ]
   },
   "medusaSpeed": {
    "type": "STACKS_SPEED",
    "val": 2,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "medusa",
      "includeUpgrades": true
     }
    ]
   },
   "cathedralCouatlAttack": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.attack",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "cthCouatl",
      "includeUpgrades": true
     }
    ]
   },
   "cathedralCouatlDefence": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.defence",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "cthCouatl",
      "includeUpgrades": true
     }
    ]
   },
   "cathedralCouatlSpeed": {
    "type": "STACKS_SPEED",
    "val": 2,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "cthCouatl",
      "includeUpgrades": true
     }
    ]
   },
   "tidesCouatlAttack": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.attack",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "mdtCouatl",
      "includeUpgrades": true
     }
    ]
   },
   "tidesCouatlDefence": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.defence",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "mdtCouatl",
      "includeUpgrades": true
     }
    ]
   },
   "tidesCouatlSpeed": {
    "type": "STACKS_SPEED",
    "val": 2,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "mdtCouatl",
      "includeUpgrades": true
     }
    ]
   },
   "seaSerpentAttack": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.attack",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "seaserpent",
      "includeUpgrades": true
     }
    ]
   },
   "seaSerpentDefence": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.defence",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "seaserpent",
      "includeUpgrades": true
     }
    ]
   },
   "seaSerpentSpeed": {
    "type": "STACKS_SPEED",
    "val": 2,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "seaserpent",
      "includeUpgrades": true
     }
    ]
   },
   "leviathanAttack": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.attack",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "havC13",
      "includeUpgrades": true
     }
    ]
   },
   "leviathanDefence": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.defence",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "havC13",
      "includeUpgrades": true
     }
    ]
   },
   "leviathanSpeed": {
    "type": "STACKS_SPEED",
    "val": 2,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "havC13",
      "includeUpgrades": true
     }
    ]
   },
   "factoryCouatlAttack": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.attack",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "hotaCouatl",
      "includeUpgrades": true
     }
    ]
   },
   "factoryCouatlDefence": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.defence",
    "val": 9,
    "updater": "TIMES_HERO_LEVEL_DIVIDE_STACK_LEVEL",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "hotaCouatl",
      "includeUpgrades": true
     }
    ]
   },
   "factoryCouatlSpeed": {
    "type": "STACKS_SPEED",
    "val": 2,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "hotaCouatl",
      "includeUpgrades": true
     }
    ]
   }
  }
 }
}
```

</details>

### 埃尔斯佩思（`new pavilion.new pavillion:elspeth`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：生命之源：全军固定生命+1，并按每英雄等级增加基础生命4%；开场全体巨龙之力 dragonStrength 持续50回合。授予全部火系法术的使用权限及专家火系，并以覆盖层显式写入27项已学法术供学术传授。注意巨龙之力不是 slayer（屠龙）。

- 可用性：`special=false`（缺省按非特殊）；职业：`vizier`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"basic","skill":"logistics"},{"level":"basic","skill":"pathfinding"}]。
- 初始法术书：["core:armageddon","core:berserk","core:blind","core:bloodlust","core:curse","core:fireElemental","core:fireShield","core:fireWall","core:fireball","core:frenzy","core:inferno","core:landMine","core:magicArrow","core:misfortune","core:protectFire","core:sacrifice","core:slayer","core:visions","asylum:summonJester","cathedral:confessionSong","cathedral:divineRevenge","courtyard:immolationCurse","courtyard:quickStrikes","newoldspells+:dragonStrength","newoldspells+:vigour","reinforcements:reinforcements","tidesofwar:mdtAuraOfPower"]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `health1` | 生命值 `STACK_HEALTH`；值：`1`；数值方式：`"BASE_NUMBER"` |
| `health2` | 生命值 `STACK_HEALTH`；值：`4`；数值方式：`"PERCENT_TO_BASE"`；成长规则：`"TIMES_HERO_LEVEL"` |
| `openingSlayer` | 开场自动施法 `OPENING_BATTLE_SPELL`；对象／属性：`"spell.dragonStrength"`；值：`50`；数值方式：`"BASE_NUMBER"` |
| `fireSpellsBook` | 授予整系法术权限 `SPELLS_OF_SCHOOL`；对象／属性：`"fire"`；值：`0`；数值方式：`"BASE_NUMBER"` |
| `fireMagicExpert` | 学派熟练度 `MAGIC_SCHOOL_SKILL`；对象／属性：`"spellSchool.fire"`；值：`3`；数值方式：`"BASE_NUMBER"` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：生命之源。
- 当前说明：所带领的所有部队生命值上限获得成长加成，战斗开始时自动施放巨龙之力，并直接掌握全部火系魔法且视为专家火系魔法。
- 当前tooltip：HP growth + opening dragon strength + expert fire magic

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Elspeth.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Elspeth.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/pavilion.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/pavilion.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-07-05 `c319c49b`：`specialty.bonuses.fireMagicExpert.subtype`：`null` → `"spellSchool.fire"`；`specialty.bonuses.fireMagicExpert.type`：`null` → `"MAGIC_SCHOOL_SKILL"`；`specialty.bonuses.fireMagicExpert.val`：`null` → `3`；`specialty.bonuses.fireMagicExpert.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.fireSpellsBook.subtype`：`null` → `"fire"`；`specialty.bonuses.fireSpellsBook.type`：`null` → `"SPELLS_OF_SCHOOL"`；`specialty.bonuses.fireSpellsBook.val`：`null` → `0`；`specialty.bonuses.fireSpellsBook.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.health2.updater`：`null` → `"TIMES_HERO_LEVEL"`；`specialty.bonuses.openingSlayer.subtype`：`null` → `"spell.slayer"`；`specialty.bonuses.openingSlayer.type`：`null` → `"OPENING_BATTLE_SPELL"`；`specialty.bonuses.openingSlayer.val`：`null` → `10`；`specialty.bonuses.openingSlayer.valueType`：`null` → `"BASE_NUMBER"`；`texts.biography`：`"埃尔斯佩思无数次地穿越无尽的沙漠，似乎对那里的一切秘密都了如指掌。他的所有部队似乎总是充满活力，洋溢着不为人知的力量，也许要感谢传说中那神圣的生命之泉？"` → `"埃尔斯佩思无数次地穿越无尽的沙漠，似乎对那里的一切秘密都了如指掌。他的部队总是充满活力，洋溢着不为人知的力量，也许要感谢传说中那神圣的生命之泉。"`；`texts.specialty.description`：`"所带领的所有部队生命值上限增加5%，最小提升为1点。"` → `"所带领的所有部队生命值上限获得成长加成，战斗开始时自动施放巨龙之力，并直接掌握全部火系魔法且视为专家火系魔法。"`；`texts.specialty.tooltip`：`"Hit points + 5%"` → `"HP growth + opening slayer + expert fire magic"`。
- 2026-07-05 `ba4545a7`：`specialty.bonuses.openingSlayer.subtype`：`"spell.slayer"` → `"spell.dragonStrength"`；`texts.specialty.tooltip`：`"HP growth + opening slayer + expert fire magic"` → `"HP growth + opening dragon strength + expert fire magic"`。
- 2026-07-20 `df8983f9`：`specialty.bonuses.openingSlayer.val`：`10` → `50`。
- 2026-11-06 `29bcb8b8`：`spellbook`：`null` → `["core:armageddon","core:berserk","core:blind","core:bloodlust","core:curse","core:fireElemental","core:fireShield","core:fireWall","core:fireball","core:frenzy","core:inferno","core:landMine","core:magicArrow","core:misfortune","core:protectFire","core:sacrifice","core:slayer","core:visions","newoldspells+:dragonStrength","newoldspells+:vigour"]`。
- 2026-11-06 `29bcb8b8`：`spellbook#override`：`null` → `["core:armageddon","core:berserk","core:blind","core:bloodlust","core:curse","core:fireElemental","core:fireShield","core:fireWall","core:fireball","core:frenzy","core:inferno","core:landMine","core:magicArrow","core:misfortune","core:protectFire","core:sacrifice","core:slayer","core:visions","asylum:summonJester","cathedral:confessionSong","cathedral:divineRevenge","courtyard:immolationCurse","courtyard:quickStrikes","newoldspells+:dragonStrength","newoldspells+:vigour","reinforcements:reinforcements","tidesofwar:mdtAuraOfPower"]`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "class": "vizier",
 "female": false,
 "texts": {
  "name": "埃尔斯佩思",
  "biography": "埃尔斯佩思无数次地穿越无尽的沙漠，似乎对那里的一切秘密都了如指掌。他的部队总是充满活力，洋溢着不为人知的力量，也许要感谢传说中那神圣的生命之泉。",
  "specialty": {
   "description": "所带领的所有部队生命值上限获得成长加成，战斗开始时自动施放巨龙之力，并直接掌握全部火系魔法且视为专家火系魔法。",
   "tooltip": "HP growth + opening dragon strength + expert fire magic",
   "name": "生命之源"
  }
 },
 "images": {
  "specialtySmall": "speciconsPavilion/32Ankh.bmp",
  "specialtyLarge": "speciconsPavilion/44Ankh.bmp",
  "large": "heroportraitsPavilion/Elspeth.png",
  "small": "heroportraitsPavilion/ElspethSmall.png"
 },
 "army": [
  {
   "creature": "whipper",
   "max": 25,
   "min": 18
  },
  {
   "creature": "wadjet",
   "max": 9,
   "min": 7
  },
  {
   "creature": "camelRider",
   "max": 7,
   "min": 4
  }
 ],
 "skills": [
  {
   "level": "basic",
   "skill": "logistics"
  },
  {
   "level": "basic",
   "skill": "pathfinding"
  }
 ],
 "spellbook": [
  "core:armageddon",
  "core:berserk",
  "core:blind",
  "core:bloodlust",
  "core:curse",
  "core:fireElemental",
  "core:fireShield",
  "core:fireWall",
  "core:fireball",
  "core:frenzy",
  "core:inferno",
  "core:landMine",
  "core:magicArrow",
  "core:misfortune",
  "core:protectFire",
  "core:sacrifice",
  "core:slayer",
  "core:visions",
  "asylum:summonJester",
  "cathedral:confessionSong",
  "cathedral:divineRevenge",
  "courtyard:immolationCurse",
  "courtyard:quickStrikes",
  "newoldspells+:dragonStrength",
  "newoldspells+:vigour",
  "reinforcements:reinforcements",
  "tidesofwar:mdtAuraOfPower"
 ],
 "specialty": {
  "bonuses": {
   "health1": {
    "type": "STACK_HEALTH",
    "val": 1,
    "valueType": "BASE_NUMBER"
   },
   "health2": {
    "type": "STACK_HEALTH",
    "updater": "TIMES_HERO_LEVEL",
    "val": 4,
    "valueType": "PERCENT_TO_BASE"
   },
   "openingSlayer": {
    "subtype": "spell.dragonStrength",
    "type": "OPENING_BATTLE_SPELL",
    "val": 50,
    "valueType": "BASE_NUMBER"
   },
   "fireSpellsBook": {
    "type": "SPELLS_OF_SCHOOL",
    "subtype": "fire",
    "val": 0,
    "valueType": "BASE_NUMBER"
   },
   "fireMagicExpert": {
    "type": "MAGIC_SCHOOL_SKILL",
    "subtype": "spellSchool.fire",
    "val": 3,
    "valueType": "BASE_NUMBER"
   }
  }
 }
}
```

</details>

### 萨蒙特（`new pavilion.new pavillion:zamonth`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：保留木乃伊特殊升级／转化特长，额外给予木乃伊及升级形态速度+4，并开放普通英雄池。

- 可用性：`special=false`（缺省按非特殊）；职业：`embalmer`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"basic","skill":"necromancy"},{"level":"basic","skill":"wisdom"}]。
- 初始法术书：["animateDead"]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `skeleton2mummy` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.skeleton"`；附加参数：`"creature.mummy"` |
| `skeletonWarrior2mummy` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.skeletonWarrior"`；附加参数：`"creature.mummy"` |
| `walkingDead2mummy` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.walkingDead"`；附加参数：`"creature.mummy"` |
| `zombie2mummy` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.zombieLord"`；附加参数：`"creature.mummy"` |
| `wight2mummy` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.wight"`；附加参数：`"creature.mummy"` |
| `wraith2mummy` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.wraith"`；附加参数：`"creature.mummy"` |
| `vampire2mummy` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.vampire"`；附加参数：`"creature.mummy"` |
| `vampireLord2mummy` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.vampireLord"`；附加参数：`"creature.mummy"` |
| `lich2mummy` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.lich"`；附加参数：`"creature.mummy"` |
| `powerLich2mummy` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.powerLich"`；附加参数：`"creature.mummy"` |
| `blackKnight2mummy` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.blackKnight"`；附加参数：`"creature.mummy"` |
| `dreadKnight2mummy` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.dreadKnight"`；附加参数：`"creature.mummy"` |
| `boneDragon2mummy` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.boneDragon"`；附加参数：`"creature.mummy"` |
| `zombie2ghostDragon` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.ghostDragon"`；附加参数：`"creature.mummy"` |
| `mummySpeed` | 部队速度 `STACKS_SPEED`；值：`4`；附加参数：`"creature.mummy"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"mummy","includeUpgrades":true}]` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：木乃伊。
- 当前说明：可将指定墓园亡灵转化为木乃伊：骷髅、行尸、阴魂、吸血鬼、巫妖、黑暗骑士、骨龙及其对应原版升级兵种。所率木乃伊及其升级形态速度+4。
- 当前tooltip：Special Upgrade: Mummies

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Zamonth.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Zamonth.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/specialtyDescriptions.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/specialtyDescriptions.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-07-20 `df8983f9`：`special`：`true` → `false`；`specialty.bonuses.mummySpeed.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"mummy","includeUpgrades":true}]`；`specialty.bonuses.mummySpeed.type`：`null` → `"STACKS_SPEED"`；`specialty.bonuses.mummySpeed.val`：`null` → `4`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "class": "embalmer",
 "female": false,
 "special": false,
 "texts": {
  "name": "萨蒙特",
  "biography": "萨蒙特永远不会接受圣职者们禁止亡灵巫术的定规，学界也只是因为他几乎具有圣者的地位才允许他留下记录有禁忌知识的黑暗文献。",
  "specialty": {
   "description": "可将指定墓园亡灵转化为木乃伊：骷髅、行尸、阴魂、吸血鬼、巫妖、黑暗骑士、骨龙及其对应原版升级兵种。所率木乃伊及其升级形态速度+4。",
   "tooltip": "Special Upgrade: Mummies",
   "name": "木乃伊"
  }
 },
 "images": {
  "specialtySmall": "speciconsPavilion/un32Mummy.bmp",
  "specialtyLarge": "speciconsPavilion/un44Mummy.bmp",
  "large": "heroportraitsPavilion/Zamonth.png",
  "small": "heroportraitsPavilion/ZamonthSmall.png"
 },
 "spellbook": [
  "animateDead"
 ],
 "army": [
  {
   "creature": "mummy",
   "max": 2,
   "min": 1
  },
  {
   "creature": "mummy",
   "max": 2,
   "min": 1
  },
  {
   "creature": "mummy",
   "max": 2,
   "min": 1
  }
 ],
 "skills": [
  {
   "level": "basic",
   "skill": "necromancy"
  },
  {
   "level": "basic",
   "skill": "wisdom"
  }
 ],
 "specialty": {
  "base": {
   "type": "SPECIAL_UPGRADE",
   "addInfo": "creature.mummy"
  },
  "bonuses": {
   "skeleton2mummy": {
    "subtype": "creature.skeleton"
   },
   "skeletonWarrior2mummy": {
    "subtype": "creature.skeletonWarrior"
   },
   "walkingDead2mummy": {
    "subtype": "creature.walkingDead"
   },
   "zombie2mummy": {
    "subtype": "creature.zombieLord"
   },
   "wight2mummy": {
    "subtype": "creature.wight"
   },
   "wraith2mummy": {
    "subtype": "creature.wraith"
   },
   "vampire2mummy": {
    "subtype": "creature.vampire"
   },
   "vampireLord2mummy": {
    "subtype": "creature.vampireLord"
   },
   "lich2mummy": {
    "subtype": "creature.lich"
   },
   "powerLich2mummy": {
    "subtype": "creature.powerLich"
   },
   "blackKnight2mummy": {
    "subtype": "creature.blackKnight"
   },
   "dreadKnight2mummy": {
    "subtype": "creature.dreadKnight"
   },
   "boneDragon2mummy": {
    "subtype": "creature.boneDragon"
   },
   "zombie2ghostDragon": {
    "subtype": "creature.ghostDragon"
   },
   "mummySpeed": {
    "type": "STACKS_SPEED",
    "val": 4,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "mummy",
      "includeUpgrades": true
     }
    ]
   }
  }
 }
}
```

</details>

### 蕾娜塔（`preserve:p_renata`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：灵魂战栗：开场专家大难临头和悲痛欲绝，持续50回合，敌方符合条件全体幸运-8／士气-8；bind两项均专家。保留黑骑士系强化和多种骑士转恐怖骑士。开场项显式 addInfo=3，防止继承 specialty.base 的兵种字符串。特长图标改为灵魂烈焰图案，不表示会自动施放灵魂烈焰。

- 可用性：`special=false`（缺省按非特殊）；职业：`bard`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"runes"},{"level":"basic","skill":"airMagic"}]。
- 初始法术书：["misfortune","sorrow"]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `champion2dreadKnight` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.champion"`；附加参数：`"creature.dreadKnight"` |
| `cavalier2dreadKnight` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.cavalier"`；附加参数：`"creature.dreadKnight"` |
| `ffpaladin2dreadKnight` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"tidesofwar:ffpaladin"`；附加参数：`"creature.dreadKnight"` |
| `cthPaladin2dreadKnight` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"cathedral:cthPaladin"`；附加参数：`"creature.dreadKnight"` |
| `cthTemplar2dreadKnight` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"cathedral:cthTemplar"`；附加参数：`"creature.dreadKnight"` |
| `blackKnight2dreadKnight` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.blackKnight"`；附加参数：`"creature.dreadKnight"` |
| `gNec06toDreadKnight` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"tidesofwar:gNec06"`；附加参数：`"creature.dreadKnight"` |
| `ftWyvern12dreadKnight` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"fairy:ftWyvern1"`；附加参数：`"creature.dreadKnight"` |
| `ftWyvern22dreadKnight` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"fairy:ftWyvern2"`；附加参数：`"creature.dreadKnight"` |
| `choir` | 开场自动施法 `OPENING_BATTLE_SPELL`；对象／属性：`"spell.misfortune"`；值：`50`；数值方式：`"BASE_NUMBER"`；附加参数：`3` |
| `unchoir` | 开场自动施法 `OPENING_BATTLE_SPELL`；对象／属性：`"spell.sorrow"`；值：`50`；数值方式：`"BASE_NUMBER"`；附加参数：`3` |
| `bind` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.misfortune"`；值：`3`；数值方式：`"BASE_NUMBER"`；附加参数：`"creature.dreadKnight"` |
| `bind2` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.sorrow"`；值：`3`；数值方式：`"BASE_NUMBER"`；附加参数：`"creature.dreadKnight"` |
| `damage` | 最小／最大杀伤 `CREATURE_DAMAGE`；对象／属性：`"creatureDamageBoth"`；值：`15`；附加参数：`"creature.dreadKnight"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]` |
| `attack` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.attack"`；值：`15`；附加参数：`"creature.dreadKnight"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]` |
| `defence` | 主属性加成 `PRIMARY_SKILL`；对象／属性：`"primarySkill.defence"`；值：`15`；附加参数：`"creature.dreadKnight"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]` |
| `speed` | 部队速度 `STACKS_SPEED`；值：`3`；附加参数：`"creature.dreadKnight"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]` |
| `noRetaliation` | 攻击不受反击 `BLOCKS_RETALIATION`；附加参数：`"creature.dreadKnight"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]` |
| `unlimitedRetaliations` | 无限反击 `UNLIMITED_RETALIATIONS`；附加参数：`"creature.dreadKnight"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]` |
| `lifeDrain` | 吸血 `LIFE_DRAIN`；值：`100`；附加参数：`"creature.dreadKnight"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]` |
| `attackAllAdjacent` | 攻击相邻敌人 `ATTACKS_ALL_ADJACENT`；附加参数：`"creature.dreadKnight"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]` |
| `jousting` | 冲锋伤害 `JOUSTING`；值：`5`；附加参数：`"creature.dreadKnight"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]` |
| `additionalAttack` | 额外攻击 `ADDITIONAL_ATTACK`；值：`1`；附加参数：`"creature.dreadKnight"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]` |
| `ferocity` | 凶暴 `FEROCITY`；值：`1`；附加参数：`"creature.dreadKnight"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]` |
| `destruction` | 毁灭杀戮 `DESTRUCTION`；对象／属性：`"destructionKillPercentage"`；值：`25`；附加参数：`20`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]` |
| `firstStrike` | 先攻 `FIRST_STRIKE`；附加参数：`"creature.dreadKnight"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]` |
| `ignoreDefence` | 忽略敌方防御比例 `ENEMY_DEFENCE_REDUCTION`；值：`100`；附加参数：`"creature.dreadKnight"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：灵魂战栗。
- 当前说明：战斗开始时自动施放专家级大难临头与悲痛欲绝，分别使符合条件的敌方全体幸运-8、士气-8，持续50回合；实际幸运与士气受游戏上下限限制。自身也可按专家级施放这两种法术。黑骑士及其升级形态攻击、防御、杀伤提高15，速度+3，并额外获得抢先反击、无反击、无限反击、吸取生命、环击、勇士冲锋、死亡凝视、双击、顺势攻击、无视防御与毁灭。同时可将多种骑士系兵种升级为恐怖骑士。
- 当前tooltip：开场自动施放专家级大难临头与悲痛欲绝，持续50回合。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json](<Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-06-14 `2ad83746`：`skills`：`[{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"airMagic"}]` → `[{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"runes"},{"level":"basic","skill":"airMagic"}]`；`specialty.bonuses.blackKnight2dreadKnight.subtype`：`null` → `"blackKnight"`。
- 2026-06-14 `53b7b675`：`specialty.bonuses.blackKnight2dreadKnight.subtype`：`"blackKnight"` → `"creature.blackKnight"`。
- 2026-06-17 `597ad94e`：`specialty.bonuses.additionalAttack.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]`；`specialty.bonuses.additionalAttack.type`：`null` → `"ADDITIONAL_ATTACK"`；`specialty.bonuses.additionalAttack.val`：`null` → `1`；`specialty.bonuses.attackAllAdjacent.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]`；`specialty.bonuses.attackAllAdjacent.type`：`null` → `"ATTACKS_ALL_ADJACENT"`；`specialty.bonuses.deathStare.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]`；`specialty.bonuses.deathStare.subtype`：`null` → `"deathStareGorgon"`；`specialty.bonuses.deathStare.type`：`null` → `"DEATH_STARE"`；`specialty.bonuses.deathStare.val`：`null` → `10`；`specialty.bonuses.destruction.addInfo`：`null` → `20`；`specialty.bonuses.destruction.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]`；`specialty.bonuses.destruction.subtype`：`null` → `"destructionKillPercentage"`；`specialty.bonuses.destruction.type`：`null` → `"DESTRUCTION"`；`specialty.bonuses.destruction.val`：`null` → `25`；`specialty.bonuses.ferocity.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]`；`specialty.bonuses.ferocity.type`：`null` → `"FEROCITY"`；`specialty.bonuses.ferocity.val`：`null` → `1`；`specialty.bonuses.firstStrike.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]`；`specialty.bonuses.firstStrike.type`：`null` → `"FIRST_STRIKE"`；`specialty.bonuses.gNec06toDreadKnight.subtype`：`null` → `"tidesofwar:gNec06"`；`specialty.bonuses.jousting.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]`；`specialty.bonuses.jousting.type`：`null` → `"JOUSTING"`；`specialty.bonuses.jousting.val`：`null` → `5`；`specialty.bonuses.lifeDrain.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]`；`specialty.bonuses.lifeDrain.type`：`null` → `"LIFE_DRAIN"`；`specialty.bonuses.lifeDrain.val`：`null` → `100`；`specialty.bonuses.noRetaliation.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]`；`specialty.bonuses.noRetaliation.type`：`null` → `"BLOCKS_RETALIATION"`；`specialty.bonuses.speed.val`：`2` → `3`；`texts.biography`：`"外表看起来蕾娜塔是个乐观而又活泼的女子，和她在一起的人都会觉得她能够带来喜悦的气氛。但是这并不代表她没有悲伤的往事。夜深之时有人会看到她独自弹奏曲子，寄予思念她从小失散的妹妹，琳恩。"` → `"蕾娜塔外表开朗活泼，总能为同伴带来轻松愉快的气氛。但在她的乐声背后，也藏着不愿轻易提起的往事。每当夜深人静时，她仍会独自奏起旧日的旋律，怀念那段再也无法回去的过去。"`；`texts.specialty.description`：`"作战开场即施放欢欣鼓舞魔法。隐藏技能:死骑攻防杀伤+15速度+2,同时可以把骑士升级为死骑"` → `"战斗开始时自动施放欢欣鼓舞。黑骑士及其升级形态攻击、防御、杀伤提高15，速度+3，并额外获得抢先反击、无反击、吸取生命、环击、勇士冲锋、死亡凝视、双击、顺势攻击与毁灭。同时可将多种骑士系兵种升级为恐怖骑士。"`。
- 2026-06-17 `c5381542`：`specialty.bonuses.deathStare.limiters`：`[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]` → `null`；`specialty.bonuses.deathStare.subtype`：`"deathStareGorgon"` → `null`；`specialty.bonuses.deathStare.type`：`"DEATH_STARE"` → `null`；`specialty.bonuses.deathStare.val`：`10` → `null`。
- 2026-06-20 `d20e3775`：`specialty.bonuses.bind2.subtype`：`null` → `"spell.sorrow"`；`specialty.bonuses.bind2.type`：`null` → `"SPELL"`；`specialty.bonuses.bind2.val`：`null` → `1`；`specialty.bonuses.bind2.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.ignoreDefence.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]`；`specialty.bonuses.ignoreDefence.type`：`null` → `"ENEMY_DEFENCE_REDUCTION"`；`specialty.bonuses.ignoreDefence.val`：`null` → `100`；`specialty.bonuses.unchoir.subtype`：`null` → `"spell.sorrow"`；`specialty.bonuses.unchoir.type`：`null` → `"OPENING_BATTLE_SPELL"`；`specialty.bonuses.unchoir.val`：`null` → `10`；`specialty.bonuses.unchoir.valueType`：`null` → `"BASE_NUMBER"`；`specialty.bonuses.unlimitedRetaliations.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"blackKnight","includeUpgrades":true}]`；`specialty.bonuses.unlimitedRetaliations.type`：`null` → `"UNLIMITED_RETALIATIONS"`；`spellbook`：`["mirth"]` → `["mirth","sorrow"]`；`texts.specialty.description`：`"战斗开始时自动施放欢欣鼓舞。黑骑士及其升级形态攻击、防御、杀伤提高15，速度+3，并额外获得抢先反击、无反击、吸取生命、环击、勇士冲锋、死亡凝视、双击、顺势攻击与毁灭。同时可将多种骑士系兵种升级为恐怖骑士。"` → `"战斗开始时自动施放欢欣鼓舞。黑骑士及其升级形态攻击、防御、杀伤提高15，速度+3，并额外获得抢先反击、无反击、无限反击、吸取生命、环击、勇士冲锋、死亡凝视、双击、顺势攻击、无视防御与毁灭。同时可将多种骑士系兵种升级为恐怖骑士。"`。
- 2026-06-22 `797ce8ad`：`specialty.bonuses.ftWyvern12dreadKnight.subtype`：`null` → `"fairy:ftWyvern1"`；`specialty.bonuses.ftWyvern22dreadKnight.subtype`：`null` → `"fairy:ftWyvern2"`。
- 2026-11-12 `498be78f`：`images.specialtyLarge`：`"preserve/heroes/renata/UN44T195.bmp"` → `"preserve/heroes/renata/soulConflagration44.png"`；`images.specialtySmall`：`"preserve/heroes/renata/UN32T195.bmp"` → `"preserve/heroes/renata/soulConflagration32.png"`；`specialty.bonuses.bind.subtype`：`"spell.mirth"` → `"spell.misfortune"`；`specialty.bonuses.bind.val`：`1` → `3`；`specialty.bonuses.bind2.val`：`1` → `3`；`specialty.bonuses.choir.addInfo`：`null` → `3`；`specialty.bonuses.choir.subtype`：`"spell.mirth"` → `"spell.misfortune"`；`specialty.bonuses.choir.val`：`10` → `50`；`specialty.bonuses.unchoir.addInfo`：`null` → `3`；`specialty.bonuses.unchoir.val`：`10` → `50`；`spellbook`：`["mirth","sorrow"]` → `["misfortune","sorrow"]`；`texts.specialty.description`：`"战斗开始时自动施放欢欣鼓舞。黑骑士及其升级形态攻击、防御、杀伤提高15，速度+3，并额外获得抢先反击、无反击、无限反击、吸取生命、环击、勇士冲锋、死亡凝视、双击、顺势攻击、无视防御与毁灭。同时可将多种骑士系兵种升级为恐怖骑士。"` → `"战斗开始时自动施放专家级大难临头与悲痛欲绝，分别使符合条件的敌方全体幸运-8、士气-8，持续50回合；实际幸运与士气受游戏上下限限制。自身也可按专家级施放这两种法术。黑骑士及其升级形态攻击、防御、杀伤提高15，速度+3，并额外获得抢先反击、无反击、无限反击、吸取生命、环击、勇士冲锋、死亡凝视、双击、顺势攻击、无视防御与毁灭。同时可将多种骑士系兵种升级为恐怖骑士。"`；`texts.specialty.name`：`"欢欣鼓舞"` → `"灵魂战栗"`；`texts.specialty.tooltip`：`"Bonus: spell"` → `"开场自动施放专家级大难临头与悲痛欲绝，持续50回合。"`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "class": "bard",
 "female": true,
 "texts": {
  "name": "蕾娜塔",
  "biography": "蕾娜塔外表开朗活泼，总能为同伴带来轻松愉快的气氛。但在她的乐声背后，也藏着不愿轻易提起的往事。每当夜深人静时，她仍会独自奏起旧日的旋律，怀念那段再也无法回去的过去。",
  "specialty": {
   "description": "战斗开始时自动施放专家级大难临头与悲痛欲绝，分别使符合条件的敌方全体幸运-8、士气-8，持续50回合；实际幸运与士气受游戏上下限限制。自身也可按专家级施放这两种法术。黑骑士及其升级形态攻击、防御、杀伤提高15，速度+3，并额外获得抢先反击、无反击、无限反击、吸取生命、环击、勇士冲锋、死亡凝视、双击、顺势攻击、无视防御与毁灭。同时可将多种骑士系兵种升级为恐怖骑士。",
   "tooltip": "开场自动施放专家级大难临头与悲痛欲绝，持续50回合。",
   "name": "灵魂战栗"
  }
 },
 "images": {
  "specialtySmall": "preserve/heroes/renata/soulConflagration32.png",
  "specialtyLarge": "preserve/heroes/renata/soulConflagration44.png",
  "large": "preserve/heroes/renata/HPL007BR.bmp",
  "small": "preserve/heroes/renata/HPS007BR.bmp"
 },
 "spellbook": [
  "misfortune",
  "sorrow"
 ],
 "army": [
  {
   "creature": "halflings",
   "max": 30,
   "min": 20
  },
  {
   "creature": "treelurker",
   "max": 7,
   "min": 6
  },
  {
   "creature": "magiceye",
   "max": 5,
   "min": 3
  }
 ],
 "skills": [
  {
   "level": "basic",
   "skill": "wisdom"
  },
  {
   "level": "basic",
   "skill": "runes"
  },
  {
   "level": "basic",
   "skill": "airMagic"
  }
 ],
 "specialty": {
  "base": {
   "addInfo": "creature.dreadKnight",
   "type": "SPECIAL_UPGRADE"
  },
  "bonuses": {
   "champion2dreadKnight": {
    "subtype": "creature.champion"
   },
   "cavalier2dreadKnight": {
    "subtype": "creature.cavalier"
   },
   "ffpaladin2dreadKnight": {
    "subtype": "tidesofwar:ffpaladin"
   },
   "cthPaladin2dreadKnight": {
    "subtype": "cathedral:cthPaladin"
   },
   "cthTemplar2dreadKnight": {
    "subtype": "cathedral:cthTemplar"
   },
   "blackKnight2dreadKnight": {
    "subtype": "creature.blackKnight"
   },
   "gNec06toDreadKnight": {
    "subtype": "tidesofwar:gNec06"
   },
   "ftWyvern12dreadKnight": {
    "subtype": "fairy:ftWyvern1"
   },
   "ftWyvern22dreadKnight": {
    "subtype": "fairy:ftWyvern2"
   },
   "choir": {
    "type": "OPENING_BATTLE_SPELL",
    "subtype": "spell.misfortune",
    "val": 50,
    "addInfo": 3,
    "valueType": "BASE_NUMBER"
   },
   "unchoir": {
    "type": "OPENING_BATTLE_SPELL",
    "subtype": "spell.sorrow",
    "val": 50,
    "addInfo": 3,
    "valueType": "BASE_NUMBER"
   },
   "bind": {
    "subtype": "spell.misfortune",
    "type": "SPELL",
    "val": 3,
    "valueType": "BASE_NUMBER"
   },
   "bind2": {
    "subtype": "spell.sorrow",
    "type": "SPELL",
    "val": 3,
    "valueType": "BASE_NUMBER"
   },
   "damage": {
    "type": "CREATURE_DAMAGE",
    "subtype": "creatureDamageBoth",
    "val": 15,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "blackKnight",
      "includeUpgrades": true
     }
    ]
   },
   "attack": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.attack",
    "val": 15,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "blackKnight",
      "includeUpgrades": true
     }
    ]
   },
   "defence": {
    "type": "PRIMARY_SKILL",
    "subtype": "primarySkill.defence",
    "val": 15,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "blackKnight",
      "includeUpgrades": true
     }
    ]
   },
   "speed": {
    "type": "STACKS_SPEED",
    "val": 3,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "blackKnight",
      "includeUpgrades": true
     }
    ]
   },
   "noRetaliation": {
    "type": "BLOCKS_RETALIATION",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "blackKnight",
      "includeUpgrades": true
     }
    ]
   },
   "unlimitedRetaliations": {
    "type": "UNLIMITED_RETALIATIONS",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "blackKnight",
      "includeUpgrades": true
     }
    ]
   },
   "lifeDrain": {
    "type": "LIFE_DRAIN",
    "val": 100,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "blackKnight",
      "includeUpgrades": true
     }
    ]
   },
   "attackAllAdjacent": {
    "type": "ATTACKS_ALL_ADJACENT",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "blackKnight",
      "includeUpgrades": true
     }
    ]
   },
   "jousting": {
    "type": "JOUSTING",
    "val": 5,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "blackKnight",
      "includeUpgrades": true
     }
    ]
   },
   "additionalAttack": {
    "type": "ADDITIONAL_ATTACK",
    "val": 1,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "blackKnight",
      "includeUpgrades": true
     }
    ]
   },
   "ferocity": {
    "type": "FEROCITY",
    "val": 1,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "blackKnight",
      "includeUpgrades": true
     }
    ]
   },
   "destruction": {
    "type": "DESTRUCTION",
    "subtype": "destructionKillPercentage",
    "val": 25,
    "addInfo": 20,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "blackKnight",
      "includeUpgrades": true
     }
    ]
   },
   "firstStrike": {
    "type": "FIRST_STRIKE",
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "blackKnight",
      "includeUpgrades": true
     }
    ]
   },
   "ignoreDefence": {
    "type": "ENEMY_DEFENCE_REDUCTION",
    "val": 100,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "blackKnight",
      "includeUpgrades": true
     }
    ]
   }
  }
 }
}
```

</details>

### 艾格瑞奈尔（`preserve:s_Agrenal`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：开放普通英雄池；开场吟唱持续17回合。每回合基础2次施法，每满15英雄等级再加1次：1—14级2次，15—29级3次，30—44级4次。固定与成长bonus使用独立stacking标识，防止被同组覆盖。

- 可用性：`special=false`（缺省按非特殊）；职业：`bard`。地图英雄白名单仍可能禁用。
- 初始技能：[{"level":"basic","skill":"wisdom"},{"level":"basic","skill":"leadership"}]。
- 初始法术书：["choir"]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `choir` | 开场自动施法 `OPENING_BATTLE_SPELL`；对象／属性：`"spell.choir"`；值：`17`；数值方式：`"BASE_NUMBER"` |
| `bind` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.choir"`；值：`1`；数值方式：`"BASE_NUMBER"` |
| `extraCast` | 每回合额外英雄施法次数 `HERO_SPELL_CASTS_PER_COMBAT_TURN`；值：`1`；数值方式：`"ADDITIVE_VALUE"`；叠加组：`"aegrenalExtraCast"` |
| `levelCast` | 每回合额外英雄施法次数 `HERO_SPELL_CASTS_PER_COMBAT_TURN`；值：`1`；数值方式：`"ADDITIVE_VALUE"`；成长规则：`{"type":"TIMES_HERO_LEVEL","stepSize":15}`；叠加组：`"aegrenalLevelCast"` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：吟唱。
- 当前说明：拥有吟唱魔法并在开场施放，持续17回合。每回合可施放2次魔法，此后每满15级再增加1次。
- 当前tooltip：Bonus: spell

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/S_Aegrenal.json](<Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/S_Aegrenal.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-09-20 `5f3feea3`：`special`：`true` → `false`；`specialty.bonuses.choir.val`：`7` → `17`；`specialty.bonuses.extraCast.type`：`null` → `"HERO_SPELL_CASTS_PER_COMBAT_TURN"`；`specialty.bonuses.extraCast.val`：`null` → `1`；`specialty.bonuses.extraCast.valueType`：`null` → `"ADDITIVE_VALUE"`；`specialty.bonuses.levelCast.type`：`null` → `"HERO_SPELL_CASTS_PER_COMBAT_TURN"`；`specialty.bonuses.levelCast.updater.stepSize`：`null` → `15`；`specialty.bonuses.levelCast.updater.type`：`null` → `"TIMES_HERO_LEVEL"`；`specialty.bonuses.levelCast.val`：`null` → `1`；`specialty.bonuses.levelCast.valueType`：`null` → `"ADDITIVE_VALUE"`；`texts.specialty.description`：`"拥有吟唱魔法并且开场可以施放。"` → `"拥有吟唱魔法并在开场施放，持续17回合。每回合可施放2次魔法，此后每满15级再增加1次。"`。
- 2026-09-26 `b7d871f9`：`specialty.bonuses.extraCast.stacking`：`null` → `"aegrenalExtraCast"`；`specialty.bonuses.levelCast.stacking`：`null` → `"aegrenalLevelCast"`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "class": "bard",
 "female": true,
 "special": false,
 "texts": {
  "name": "艾格瑞奈尔",
  "biography": "艾格瑞奈尔出生在一个位于森林边缘的村庄里，她的父母丧生于邻国发动的一次袭击中。为了通过歌曲让自己关于父母的记忆永存，也为了能够从普通人民的立场记载历史，艾格瑞奈尔选择成为一位吟游诗人。",
  "specialty": {
   "description": "拥有吟唱魔法并在开场施放，持续17回合。每回合可施放2次魔法，此后每满15级再增加1次。",
   "tooltip": "Bonus: spell",
   "name": "吟唱"
  }
 },
 "images": {
  "specialtySmall": "preserve/heroes/s_agrenal/UN32Mo.bmp",
  "specialtyLarge": "preserve/heroes/s_agrenal/UN44Mo.bmp",
  "large": "preserve/heroes/s_agrenal/HPL009BR.bmp",
  "small": "preserve/heroes/s_agrenal/HPS009BR.bmp"
 },
 "battleImage": "preserve/battle/heroes/CH21SP",
 "spellbook": [
  "choir"
 ],
 "army": [
  {
   "creature": "halflings",
   "max": 30,
   "min": 20
  },
  {
   "creature": "treelurker",
   "max": 7,
   "min": 6
  },
  {
   "creature": "magiceye",
   "max": 5,
   "min": 3
  }
 ],
 "skills": [
  {
   "level": "basic",
   "skill": "wisdom"
  },
  {
   "level": "basic",
   "skill": "leadership"
  }
 ],
 "specialty": {
  "bonuses": {
   "choir": {
    "type": "OPENING_BATTLE_SPELL",
    "subtype": "spell.choir",
    "val": 17,
    "valueType": "BASE_NUMBER"
   },
   "bind": {
    "subtype": "spell.choir",
    "type": "SPELL",
    "val": 1,
    "valueType": "BASE_NUMBER"
   },
   "extraCast": {
    "type": "HERO_SPELL_CASTS_PER_COMBAT_TURN",
    "val": 1,
    "valueType": "ADDITIVE_VALUE",
    "stacking": "aegrenalExtraCast"
   },
   "levelCast": {
    "type": "HERO_SPELL_CASTS_PER_COMBAT_TURN",
    "val": 1,
    "valueType": "ADDITIVE_VALUE",
    "stacking": "aegrenalLevelCast",
    "updater": {
     "type": "TIMES_HERO_LEVEL",
     "stepSize": 15
    }
   }
  }
 }
}
```

</details>

### 埃瓦希斯（`hota.cove:10eovacius`）

**改动性质**：配置／能力或可用性调整（含后续文字维护）。

**最终效果与范围**：使用镜像大法魔法时效果大增，战斗中首次施放镜像可以召唤2个镜像。隐藏技能:将赏金猎人、宝藏猎人、快枪猎手、火枪手、步枪手、猎巫人、猎魔人、海贼、海盗和皇家海盗升级为快枪猎头者。

- 可用性：`special=false`（缺省按非特殊）；职业：`navigator`。地图英雄白名单仍可能禁用。
- 初始技能：[{"skill":"wisdom","level":"basic"},{"skill":"mysticism","level":"basic"},{"skill":"waterMagic","level":"basic"}]。
- 初始法术书：["clone"]。授予整系施法权限与显式已学法术书不是同一件事。

| 特长项 | 中文解释与最终参数 |
|---|---|
| `bountyHunter2headhunter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"haven:havC05"`；附加参数：`"hota.factory:hotaGunmanUp"` |
| `treasureHunter2headhunter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"haven:havC06"`；附加参数：`"hota.factory:hotaGunmanUp"` |
| `gunman2headhunter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"hota.factory:hotaGunman"`；附加参数：`"hota.factory:hotaGunmanUp"` |
| `arquebusier2headhunter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"courtyard:arquebusier"`；附加参数：`"hota.factory:hotaGunmanUp"` |
| `musketeer2headhunter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"courtyard:musketeer"`；附加参数：`"hota.factory:hotaGunmanUp"` |
| `witchHunter2headhunter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"cathedral:cthWitchHunter"`；附加参数：`"hota.factory:hotaGunmanUp"` |
| `evilHunter2headhunter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"cathedral:cthEvilHunter"`；附加参数：`"hota.factory:hotaGunmanUp"` |
| `pirate2headhunter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.pirate"`；附加参数：`"hota.factory:hotaGunmanUp"` |
| `corsair2headhunter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.corsair"`；附加参数：`"hota.factory:hotaGunmanUp"` |
| `seadog2headhunter` | 特殊兵种升级 `SPECIAL_UPGRADE`；对象／属性：`"creature.seadog"`；附加参数：`"hota.factory:hotaGunmanUp"` |
| `clone` | 授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.specialClone"`；附加参数：`"hota.factory:hotaGunmanUp"` |
| `damage` | 最小／最大杀伤 `CREATURE_DAMAGE`；对象／属性：`"creatureDamageBoth"`；值：`10`；附加参数：`"hota.factory:hotaGunmanUp"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"hota.factory:hotaGunmanUp","includeUpgrades":true}]` |
| `speed` | 部队速度 `STACKS_SPEED`；值：`2`；附加参数：`"hota.factory:hotaGunmanUp"`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"hota.factory:hotaGunman","includeUpgrades":true}]` |

**描述检查**：已读取最终覆盖后的描述；未发现本轮规则可确认的通用成长／文本缺失问题。复杂自定义特长仍需对照下方参数和实战，不等于完整验收。

- 当前特长名：镜像大法。
- 当前说明：使用镜像大法魔法时效果大增，战斗中首次施放镜像可以召唤2个镜像。隐藏技能:将赏金猎人、宝藏猎人、快枪猎手、火枪手、步枪手、猎巫人、猎魔人、海贼、海盗和皇家海盗升级为快枪猎头者。
- 当前tooltip：Spell Bonus: Clone

**额外校对结论**：主描述已补升级兵种清单，但没有写快枪猎头者杀伤+10及快枪猎手系速度+2；当前完整bonus见上表。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/hota/Mods/cove/Content/config/hota/cove/heroes/eovacius.json](<Hero3Expasion1.83/Mods/hota/Mods/cove/Content/config/hota/cove/heroes/eovacius.json>)

<details>
<summary>该英雄的逐次改动详情（旧值 → 新值）</summary>

- 2026-06-14 `2ad83746`：`skills`：`[{"skill":"wisdom","level":"basic"},{"skill":"mysticism","level":"basic"}]` → `[{"skill":"wisdom","level":"basic"},{"skill":"mysticism","level":"basic"},{"skill":"waterMagic","level":"basic"}]`；`specialty.base.addInfo`：`"creature.hotaGunmanUp"` → `"hota.factory:hotaGunmanUp"`；`specialty.bonuses.damage.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"hota.factory:hotaGunmanUp","includeUpgrades":true}]`；`specialty.bonuses.damage.subtype`：`null` → `"creatureDamageBoth"`；`specialty.bonuses.damage.type`：`null` → `"CREATURE_DAMAGE"`；`specialty.bonuses.damage.val`：`null` → `10`；`specialty.bonuses.speed.limiters`：`null` → `[{"type":"CREATURE_TYPE_LIMITER","creature":"hota.factory:hotaGunman","includeUpgrades":true}]`；`specialty.bonuses.speed.type`：`null` → `"STACKS_SPEED"`；`specialty.bonuses.speed.val`：`null` → `2`；`texts.biography`：`"埃瓦希斯的魔法才能不是来自学习，而是来自天赋。他少年时期就用自己独特的替身法术在闹市中做小偷。他不断地变得富有和强势，直到被当地统治者所厌恶。自此这位天赋极强的法师离开了他的家乡并且选择成为海盗。"` → `"埃瓦希斯的魔法才能不是来自学习，而是来自天赋。他少年时期就用自己独特的替身法术在闹市中做小偷。他不断地变得富有和强势，直到被当地统治者所厌恶。自此这位天赋极强的法师离开了他的家乡并且选择成为海盗。(智慧/神秘/气/土/水/后勤/箭术/战术)"`。

</details>


<details>
<summary>改动后的完整英雄静态定义（合并快照）</summary>

```json
{
 "class": "navigator",
 "images": {
  "large": "hota/cove/heroes/eovacius/icons/portraitLarge.png",
  "small": "hota/cove/heroes/eovacius/icons/portraitSmall.png",
  "specialtyLarge": "hota/cove/heroes/eovacius/icons/specialtyLarge.png",
  "specialtySmall": "hota/cove/heroes/eovacius/icons/specialtySmall.png"
 },
 "army": [
  {
   "creature": "nymph",
   "min": 20,
   "max": 30
  },
  {
   "creature": "crewmate",
   "min": 6,
   "max": 9
  },
  {
   "creature": "pirate",
   "min": 4,
   "max": 7
  }
 ],
 "skills": [
  {
   "skill": "wisdom",
   "level": "basic"
  },
  {
   "skill": "mysticism",
   "level": "basic"
  },
  {
   "skill": "waterMagic",
   "level": "basic"
  }
 ],
 "specialty": {
  "base": {
   "type": "SPECIAL_UPGRADE",
   "addInfo": "hota.factory:hotaGunmanUp"
  },
  "bonuses": {
   "bountyHunter2headhunter": {
    "subtype": "haven:havC05"
   },
   "treasureHunter2headhunter": {
    "subtype": "haven:havC06"
   },
   "gunman2headhunter": {
    "subtype": "hota.factory:hotaGunman"
   },
   "arquebusier2headhunter": {
    "subtype": "courtyard:arquebusier"
   },
   "musketeer2headhunter": {
    "subtype": "courtyard:musketeer"
   },
   "witchHunter2headhunter": {
    "subtype": "cathedral:cthWitchHunter"
   },
   "evilHunter2headhunter": {
    "subtype": "cathedral:cthEvilHunter"
   },
   "pirate2headhunter": {
    "subtype": "creature.pirate"
   },
   "corsair2headhunter": {
    "subtype": "creature.corsair"
   },
   "seadog2headhunter": {
    "subtype": "creature.seadog"
   },
   "clone": {
    "type": "SPELL",
    "subtype": "spell.specialClone"
   },
   "damage": {
    "type": "CREATURE_DAMAGE",
    "subtype": "creatureDamageBoth",
    "val": 10,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "hota.factory:hotaGunmanUp",
      "includeUpgrades": true
     }
    ]
   },
   "speed": {
    "type": "STACKS_SPEED",
    "val": 2,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "hota.factory:hotaGunman",
      "includeUpgrades": true
     }
    ]
   }
  }
 },
 "spellbook": [
  "clone"
 ],
 "texts": {
  "biography": "埃瓦希斯的魔法才能不是来自学习，而是来自天赋。他少年时期就用自己独特的替身法术在闹市中做小偷。他不断地变得富有和强势，直到被当地统治者所厌恶。自此这位天赋极强的法师离开了他的家乡并且选择成为海盗。(智慧/神秘/气/土/水/后勤/箭术/战术)",
  "name": "埃瓦希斯",
  "specialty": {
   "tooltip": "Spell Bonus: Clone",
   "name": "镜像大法",
   "description": "使用镜像大法魔法时效果大增，战斗中首次施放镜像可以召唤2个镜像。隐藏技能:将赏金猎人、宝藏猎人、快枪猎手、火枪手、步枪手、猎巫人、猎魔人、海贼、海盗和皇家海盗升级为快枪猎头者。"
  }
 }
}
```

</details>

## 放开默认特殊英雄限制

以下4位英雄的非文字改动只有`special: true → false`，因此合并记录，不在“英雄改动”逐人展开，也不把其原有特长计作新增能力。

| 英雄 | ID | 配置文件 |
|---|---|---|
| 德肯 | `core:dracon` | [special.json](Hero3Expasion1.83/config/heroes/special.json) |
| 姆拉克爵士 | `core:sirMullich` | [special.json](Hero3Expasion1.83/config/heroes/special.json) |
| 巴斯特 | `new pavilion.new pavillion:bast` | [Bast.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Bast.json>) |
| 凡恩 | `new pavilion.new pavillion:varn` | [Varn.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Varn.json>) |

共同来源：2026-07-20，提交`df8983f9`。效果是取消英雄定义中的默认特殊英雄限制，不改变兵种升级、属性、技能或初始魔法；固定地图的英雄白名单、禁用规则等仍可能限制其出现。

凯瑟琳、萨蒙特、艾格瑞奈尔等同时有特长或其他能力变化的英雄，继续在“英雄改动”各自条目中记录，不混入这个“仅放开限制”列表。

## 技能改动

只收录本项目有功能字段变化且进入当前有效加载链的技能；原有机制、关联资料、未注册方案和被覆盖而不生效的改值不单列。

每个熟练度先继承base再应用该级effects；以下表格已展开继承。核心旧中文还可能来自H3bitmap.lod的SSTRAITS，不应只搜索JSON就断言“没有描述”。

### 箭术（`core:archery`）

**最终效果**：远程杀伤加成为15%／30%／60%。潮汐leadership.json中的魔杖联动没有进入当前注册链，不能据此宣称会自动开场护体神盾。

| 熟练度 | 最终效果 | 当前描述 |
|---|---|---|
| 初级 | `main`：百分比伤害加成 `PERCENTAGE_DAMAGE_BOOST`；对象／属性：`"damageTypeRanged"`；值：`15`；数值方式：`"BASE_NUMBER"` | {初级箭术}<br><br>初级箭术可使远程攻击部队的杀伤力提高10%。<br> |
| 中级 | `main`：百分比伤害加成 `PERCENTAGE_DAMAGE_BOOST`；对象／属性：`"damageTypeRanged"`；值：`30`；数值方式：`"BASE_NUMBER"` | {中级箭术}<br><br>中级箭术可使远程攻击部队的杀伤力提高25%。<br> |
| 专家 | `main`：百分比伤害加成 `PERCENTAGE_DAMAGE_BOOST`；对象／属性：`"damageTypeRanged"`；值：`60`；数值方式：`"BASE_NUMBER"` | {高级箭术}<br><br>高级箭术可使远程攻击部队的杀伤力提高50%。<br> |

**描述检查**：未同步：LOD描述仍为10%／25%／50%，配置15%／30%／60%。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/skills.json](<Hero3Expasion1.83/config/skills.json>)

<details>
<summary>技能改动历史</summary>

- 2026-06-19 `353602c5`：`advanced.effects.main.val`：`25` → `30`；`basic.effects.main.val`：`10` → `15`；`expert.effects.main.val`：`50` → `60`。

</details>


<details>
<summary>当前完整技能定义</summary>

```json
{
 "index": 1,
 "specialty": [
  "main"
 ],
 "base": {
  "effects": {
   "main": {
    "type": "PERCENTAGE_DAMAGE_BOOST",
    "subtype": "damageTypeRanged",
    "valueType": "BASE_NUMBER"
   }
  }
 },
 "basic": {
  "effects": {
   "main": {
    "val": 15
   }
  },
  "description": "{初级箭术}\n\n初级箭术可使远程攻击部队的杀伤力提高10%。\n"
 },
 "advanced": {
  "effects": {
   "main": {
    "val": 30
   }
  },
  "description": "{中级箭术}\n\n中级箭术可使远程攻击部队的杀伤力提高25%。\n"
 },
 "expert": {
  "effects": {
   "main": {
    "val": 60
   }
  },
  "description": "{高级箭术}\n\n高级箭术可使远程攻击部队的杀伤力提高50%。\n"
 },
 "name": "箭术"
}
```

</details>

### 外交术（`core:diplomacy`）

**最终效果**：见下列分级效果。

| 熟练度 | 最终效果 | 当前描述 |
|---|---|---|
| 初级 | `main`：野怪加入判定加成 `WANDERING_CREATURES_JOIN_BONUS`；值：`1`；数值方式：`"BASE_NUMBER"`<br>`surr`：特殊效果 `SURRENDER_DISCOUNT`；值：`20`；数值方式：`"BASE_NUMBER"` | {初级外交术}<br><br>初级外交术可说服比你弱小的怪物加入你的阵营，投降时的费用也可降低20%。 |
| 中级 | `main`：野怪加入判定加成 `WANDERING_CREATURES_JOIN_BONUS`；值：`2`；数值方式：`"BASE_NUMBER"`<br>`surr`：特殊效果 `SURRENDER_DISCOUNT`；值：`40`；数值方式：`"BASE_NUMBER"` | {中级外交术}<br><br>中级外交术可说服比你弱小的怪物加入你的阵营，投降时的费用也可降低40%。 |
| 专家 | `main`：野怪加入判定加成 `WANDERING_CREATURES_JOIN_BONUS`；值：`4`；数值方式：`"BASE_NUMBER"`<br>`surr`：特殊效果 `SURRENDER_DISCOUNT`；值：`60`；数值方式：`"BASE_NUMBER"` | {高级外交术}<br><br>高级外交术可说服比你弱小的怪物加入你的阵营，投降时的费用也可降低60%。 |

**描述检查**：需复核：核心改动包含多项外交参数，野怪加入并非简单固定百分比；保留实际三级描述与效果供对照。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/skills.json](<Hero3Expasion1.83/config/skills.json>)

<details>
<summary>技能改动历史</summary>

- 2026-06-07 `7cca90ae`：`expert.effects.main.val`：`3` → `4`。

</details>


<details>
<summary>当前完整技能定义</summary>

```json
{
 "index": 4,
 "specialty": [
  "surr"
 ],
 "base": {
  "effects": {
   "main": {
    "type": "WANDERING_CREATURES_JOIN_BONUS",
    "valueType": "BASE_NUMBER"
   },
   "surr": {
    "type": "SURRENDER_DISCOUNT",
    "val": 20,
    "valueType": "BASE_NUMBER"
   }
  }
 },
 "basic": {
  "effects": {
   "main": {
    "val": 1
   },
   "surr": {
    "val": 20
   }
  },
  "description": "{初级外交术}\n\n初级外交术可说服比你弱小的怪物加入你的阵营，投降时的费用也可降低20%。"
 },
 "advanced": {
  "effects": {
   "main": {
    "val": 2
   },
   "surr": {
    "val": 40
   }
  },
  "description": "{中级外交术}\n\n中级外交术可说服比你弱小的怪物加入你的阵营，投降时的费用也可降低40%。"
 },
 "expert": {
  "effects": {
   "main": {
    "val": 4
   },
   "surr": {
    "val": 60
   }
  },
  "description": "{高级外交术}\n\n高级外交术可说服比你弱小的怪物加入你的阵营，投降时的费用也可降低60%。"
 },
 "name": "外交术"
}
```

</details>

### 学习能力（`core:learning`）

**最终效果**：经验加成50%／100%／200%；保留源力飞弹、魔力闪光及中／高等级的时间停滞、法老诅咒，专家光子炮联动保留。

| 熟练度 | 最终效果 | 当前描述 |
|---|---|---|
| 初级 | `main`：经验获取加成 `HERO_EXPERIENCE_GAIN_PERCENT`；值：`50`；数值方式：`"PERCENT_TO_BASE"`<br>`addPrimeMissile`：授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.primeMissile"`；值：`1`；传播范围：`"HERO"`<br>`addManaFlare`：授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.manaFlarePyra"`；值：`1`；传播范围：`"HERO"` | {初级学习能力}<br><br>掌握初级学习能力的英雄可以提升获得的经验值50%，并且学会源力系魔法{源力飞弹}和{魔力闪光}。 |
| 中级 | `main`：经验获取加成 `HERO_EXPERIENCE_GAIN_PERCENT`；值：`100`；数值方式：`"PERCENT_TO_BASE"`<br>`addPrimeMissile`：授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.primeMissile"`；值：`2`；传播范围：`"HERO"`<br>`addManaFlare`：授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.manaFlarePyra"`；值：`2`；传播范围：`"HERO"`<br>`addTimeStasis`：授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.timeStasis"`；值：`2`；传播范围：`"HERO"`<br>`addCurseOfPharaoh`：授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.curseOfPharaoh"`；值：`2`；传播范围：`"HERO"` | {中级学习能力}<br><br>掌握中级学习能力的英雄可以提升获得的经验值100%，并且学会源力系魔法{中级源力飞弹}，{中级魔力闪光}，{时间停滞}和{法老诅咒}。 |
| 专家 | `main`：经验获取加成 `HERO_EXPERIENCE_GAIN_PERCENT`；值：`200`；数值方式：`"PERCENT_TO_BASE"`<br>`addPrimeMissile`：授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.primeMissile"`；值：`3`；传播范围：`"HERO"`<br>`addManaFlare`：授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.manaFlarePyra"`；值：`3`；传播范围：`"HERO"`<br>`addTimeStasis`：授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.timeStasis"`；值：`3`；传播范围：`"HERO"`<br>`addCurseOfPharaoh`：授予施法权限／熟练度 `SPELL`；对象／属性：`"spell.curseOfPharaoh"`；值：`3`；传播范围：`"HERO"`<br>`pyramids01Extra`：自动附加法术状态 `ENCHANTED`；对象／属性：`"spell.pyramid01Supply"`；值：`3`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"photonCannon1"}]`<br>`pyramids02Extra`：自动附加法术状态 `ENCHANTED`；对象／属性：`"spell.pyramid02Supply"`；值：`3`；作用限制：`[{"type":"CREATURE_TYPE_LIMITER","creature":"photonCannon2"}]`<br>`pyramidActive01`：法术联动标记／易伤 `MORE_DAMAGE_FROM_SPELL`；对象／属性：`"spell.soulConflagration"`；值：`0`；传播范围：`"HERO"`<br>`pyramidActive02`：法术联动标记／易伤 `MORE_DAMAGE_FROM_SPELL`；对象／属性：`"spell.hourglassOfDeath"`；值：`0`；传播范围：`"HERO"`<br>`activeDemonic01`：法术联动标记／易伤 `MORE_DAMAGE_FROM_SPELL`；对象／属性：`"spell.tarspell3"`；值：`0`；传播范围：`"HERO"`<br>`activeDemonic02`：法术联动标记／易伤 `MORE_DAMAGE_FROM_SPELL`；对象／属性：`"spell.tarspell4"`；值：`0`；传播范围：`"HERO"` | {高级学习能力}<br><br>掌握高级学习能力的英雄可以提升获得的经验值200%，并且学会源力系魔法{高级源力飞弹}，{高级魔力闪光}，{高级时间停滞}和{高级法老诅咒}。在有{光子炮}的情况下，可以学会{灵魂烈焰}或{死亡时计}。 |

**描述检查**：主要数值和源力赠法已同步；专家还保留恶魔血统联动标记，不能仅凭文案判定全部跨模组联动。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/skills.json](<Hero3Expasion1.83/config/skills.json>)
- [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/spellsPavilion/learningPyramids.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/spellsPavilion/learningPyramids.json>)
- [Hero3Expasion1.83/Mods/tartarus/content/config/tartarus/skills/demonicBlood/demonicBlood.json](<Hero3Expasion1.83/Mods/tartarus/content/config/tartarus/skills/demonicBlood/demonicBlood.json>)

<details>
<summary>技能改动历史</summary>

- 2026-06-19 `353602c5`：`advanced.description`：`"{中级学习能力}\n\n掌握中级学习能力的英雄可以提升获得的经验值35%，并且学会源力系魔法{中级源力飞弹}，{中级魔力闪光}，{时间停滞}和{法老诅咒}。"` → `"{中级学习能力}\n\n掌握中级学习能力的英雄可以提升获得的经验值100%，并且学会源力系魔法{中级源力飞弹}，{中级魔力闪光}，{时间停滞}和{法老诅咒}。"`；`advanced.effects.main.val`：`35` → `100`；`basic.description`：`"{初级学习能力}\n\n掌握初级学习能力的英雄可以提升获得的经验值25%，并且学会源力系魔法{源力飞弹}和{魔力闪光}。"` → `"{初级学习能力}\n\n掌握初级学习能力的英雄可以提升获得的经验值50%，并且学会源力系魔法{源力飞弹}和{魔力闪光}。"`；`basic.effects.main.val`：`25` → `50`；`expert.description`：`"{高级学习能力}\n\n掌握高级学习能力的英雄可以提升获得的经验值50%，并且学会源力系魔法{高级源力飞弹}，{高级魔力闪光}，{高级时间停滞}和{高级法老诅咒}。在有{光子炮}的情况下，可以学会{灵魂烈焰}或{死亡时计}。"` → `"{高级学习能力}\n\n掌握高级学习能力的英雄可以提升获得的经验值200%，并且学会源力系魔法{高级源力飞弹}，{高级魔力闪光}，{高级时间停滞}和{高级法老诅咒}。在有{光子炮}的情况下，可以学会{灵魂烈焰}或{死亡时计}。"`；`expert.effects.main.val`：`50` → `200`。

</details>


<details>
<summary>当前完整技能定义</summary>

```json
{
 "index": 21,
 "specialty": [
  "main"
 ],
 "base": {
  "effects": {
   "main": {
    "type": "HERO_EXPERIENCE_GAIN_PERCENT",
    "valueType": "PERCENT_TO_BASE"
   },
   "addPrimeMissile": {
    "propagator": "HERO",
    "sourceType": "CREATURE_ABILITY",
    "type": "SPELL",
    "subtype": "spell.primeMissile",
    "val": 1
   },
   "addManaFlare": {
    "propagator": "HERO",
    "sourceType": "CREATURE_ABILITY",
    "type": "SPELL",
    "subtype": "spell.manaFlarePyra",
    "val": 1
   }
  }
 },
 "basic": {
  "effects": {
   "main": {
    "val": 50
   },
   "addPrimeMissile": {
    "propagator": "HERO",
    "sourceType": "CREATURE_ABILITY",
    "type": "SPELL",
    "subtype": "spell.primeMissile",
    "val": 1
   },
   "addManaFlare": {
    "propagator": "HERO",
    "sourceType": "CREATURE_ABILITY",
    "type": "SPELL",
    "subtype": "spell.manaFlarePyra",
    "val": 1
   }
  },
  "description": "{初级学习能力}\n\n掌握初级学习能力的英雄可以提升获得的经验值50%，并且学会源力系魔法{源力飞弹}和{魔力闪光}。"
 },
 "advanced": {
  "effects": {
   "main": {
    "val": 100
   },
   "addPrimeMissile": {
    "propagator": "HERO",
    "sourceType": "CREATURE_ABILITY",
    "type": "SPELL",
    "subtype": "spell.primeMissile",
    "val": 2
   },
   "addManaFlare": {
    "propagator": "HERO",
    "sourceType": "CREATURE_ABILITY",
    "type": "SPELL",
    "subtype": "spell.manaFlarePyra",
    "val": 2
   },
   "addTimeStasis": {
    "propagator": "HERO",
    "sourceType": "CREATURE_ABILITY",
    "type": "SPELL",
    "subtype": "spell.timeStasis",
    "val": 2
   },
   "addCurseOfPharaoh": {
    "propagator": "HERO",
    "sourceType": "CREATURE_ABILITY",
    "type": "SPELL",
    "subtype": "spell.curseOfPharaoh",
    "val": 2
   }
  },
  "description": "{中级学习能力}\n\n掌握中级学习能力的英雄可以提升获得的经验值100%，并且学会源力系魔法{中级源力飞弹}，{中级魔力闪光}，{时间停滞}和{法老诅咒}。"
 },
 "expert": {
  "effects": {
   "main": {
    "val": 200
   },
   "addPrimeMissile": {
    "propagator": "HERO",
    "sourceType": "CREATURE_ABILITY",
    "type": "SPELL",
    "subtype": "spell.primeMissile",
    "val": 3
   },
   "addManaFlare": {
    "propagator": "HERO",
    "sourceType": "CREATURE_ABILITY",
    "type": "SPELL",
    "subtype": "spell.manaFlarePyra",
    "val": 3
   },
   "addTimeStasis": {
    "propagator": "HERO",
    "sourceType": "CREATURE_ABILITY",
    "type": "SPELL",
    "subtype": "spell.timeStasis",
    "val": 3
   },
   "addCurseOfPharaoh": {
    "propagator": "HERO",
    "sourceType": "CREATURE_ABILITY",
    "type": "SPELL",
    "subtype": "spell.curseOfPharaoh",
    "val": 3
   },
   "pyramids01Extra": {
    "type": "ENCHANTED",
    "subtype": "spell.pyramid01Supply",
    "val": 3,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "photonCannon1"
     }
    ]
   },
   "pyramids02Extra": {
    "type": "ENCHANTED",
    "subtype": "spell.pyramid02Supply",
    "val": 3,
    "limiters": [
     {
      "type": "CREATURE_TYPE_LIMITER",
      "creature": "photonCannon2"
     }
    ]
   },
   "pyramidActive01": {
    "propagator": "HERO",
    "type": "MORE_DAMAGE_FROM_SPELL",
    "subtype": "spell.soulConflagration",
    "val": 0,
    "hidden": true
   },
   "pyramidActive02": {
    "propagator": "HERO",
    "type": "MORE_DAMAGE_FROM_SPELL",
    "subtype": "spell.hourglassOfDeath",
    "val": 0,
    "hidden": true
   },
   "activeDemonic01": {
    "propagator": "HERO",
    "type": "MORE_DAMAGE_FROM_SPELL",
    "subtype": "spell.tarspell3",
    "val": 0,
    "hidden": true
   },
   "activeDemonic02": {
    "propagator": "HERO",
    "type": "MORE_DAMAGE_FROM_SPELL",
    "subtype": "spell.tarspell4",
    "val": 0,
    "hidden": true
   }
  },
  "description": "{高级学习能力}\n\n掌握高级学习能力的英雄可以提升获得的经验值200%，并且学会源力系魔法{高级源力飞弹}，{高级魔力闪光}，{高级时间停滞}和{高级法老诅咒}。在有{光子炮}的情况下，可以学会{灵魂烈焰}或{死亡时计}。"
 },
 "name": "学习能力"
}
```

</details>

### 后勤学（`core:logistics`）

**最终效果**：陆地移动加成为20%／40%／80%。未注册的hotaGameBalance/skills.json不是最终来源；当前核心定义本身已给出该数值。

| 熟练度 | 最终效果 | 当前描述 |
|---|---|---|
| 初级 | `main`：移动力 `MOVEMENT`；对象／属性：`"heroMovementLand"`；值：`20`；数值方式：`"PERCENT_TO_BASE"` | {初级后勤学}<br><br>初级后勤学可使英雄在陆地上的移动点数增加10%。 |
| 中级 | `main`：移动力 `MOVEMENT`；对象／属性：`"heroMovementLand"`；值：`40`；数值方式：`"PERCENT_TO_BASE"` | {中级后勤学}<br><br>中级后勤学可使英雄在陆地上的移动点数增加20%。 |
| 专家 | `main`：移动力 `MOVEMENT`；对象／属性：`"heroMovementLand"`；值：`80`；数值方式：`"PERCENT_TO_BASE"` | {高级后勤学}<br><br>高级后勤学可使英雄在陆地上的移动点数增加30%。 |

**描述检查**：未同步：LOD描述仍为10%／20%／30%，配置20%／40%／80%。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/skills.json](<Hero3Expasion1.83/config/skills.json>)

<details>
<summary>技能改动历史</summary>

- 2026-06-07 `dc12b4ec`：`basic.effects.main.val`：`10` → `20`；`expert.effects.main.val`：`30` → `40`。
- 2026-06-19 `353602c5`：`advanced.effects.main.val`：`20` → `40`；`expert.effects.main.val`：`40` → `80`。

</details>


<details>
<summary>当前完整技能定义</summary>

```json
{
 "index": 2,
 "specialty": [
  "main"
 ],
 "base": {
  "effects": {
   "main": {
    "subtype": "heroMovementLand",
    "type": "MOVEMENT",
    "valueType": "PERCENT_TO_BASE"
   }
  }
 },
 "basic": {
  "effects": {
   "main": {
    "val": 20
   }
  },
  "description": "{初级后勤学}\n\n初级后勤学可使英雄在陆地上的移动点数增加10%。"
 },
 "advanced": {
  "effects": {
   "main": {
    "val": 40
   }
  },
  "description": "{中级后勤学}\n\n中级后勤学可使英雄在陆地上的移动点数增加20%。"
 },
 "expert": {
  "effects": {
   "main": {
    "val": 80
   }
  },
  "description": "{高级后勤学}\n\n高级后勤学可使英雄在陆地上的移动点数增加30%。"
 },
 "name": "后勤学"
}
```

</details>

### 招魂术（`core:necromancy`）

**最终效果**：实际招魂比例15%／30%／60%，并保留强化招魂等级；当前描述10%／15%／20%已过时。

| 熟练度 | 最终效果 | 当前描述 |
|---|---|---|
| 初级 | `main`：招魂比例 `UNDEAD_RAISE_PERCENTAGE`；值：`15`；数值方式：`"BASE_NUMBER"`<br>`power`：强化招魂等级 `IMPROVED_NECROMANCY`；对象／属性：`"creature.skeleton"`；值：`1`；附加参数：`0` | {初级招魂术}<br><br>初级招魂术可使英雄将10%在战斗中死亡的生物变成骷髅兵。 |
| 中级 | `main`：招魂比例 `UNDEAD_RAISE_PERCENTAGE`；值：`30`；数值方式：`"BASE_NUMBER"`<br>`power`：强化招魂等级 `IMPROVED_NECROMANCY`；对象／属性：`"creature.skeleton"`；值：`2`；附加参数：`0` | {中级招魂术}<br><br>中级招魂术可使英雄将15%在战斗中死亡的生物变成骷髅兵。 |
| 专家 | `main`：招魂比例 `UNDEAD_RAISE_PERCENTAGE`；值：`60`；数值方式：`"BASE_NUMBER"`<br>`power`：强化招魂等级 `IMPROVED_NECROMANCY`；对象／属性：`"creature.skeleton"`；值：`3`；附加参数：`0` | {高级招魂术}<br><br>高级招魂术可使英雄将20%在战斗中死亡的生物变成骷髅兵。 |

**描述检查**：未同步：描述10%／15%／20%，配置15%／30%／60%。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/skills.json](<Hero3Expasion1.83/config/skills.json>)
- [Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/scouting.json](<Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/scouting.json>)

<details>
<summary>技能改动历史</summary>

- 2026-06-19 `353602c5`：`advanced.effects.main.val`：`15` → `30`；`basic.effects.main.val`：`10` → `15`；`expert.effects.main.val`：`20` → `60`。

</details>


<details>
<summary>当前完整技能定义</summary>

```json
{
 "index": 12,
 "specialty": [
  "main"
 ],
 "base": {
  "effects": {
   "main": {
    "type": "UNDEAD_RAISE_PERCENTAGE",
    "valueType": "BASE_NUMBER"
   },
   "power": {
    "type": "IMPROVED_NECROMANCY",
    "subtype": "creature.skeleton",
    "addInfo": 0
   }
  }
 },
 "basic": {
  "effects": {
   "main": {
    "val": 15
   },
   "power": {
    "val": 1
   }
  },
  "description": "{初级招魂术}\n\n初级招魂术可使英雄将10%在战斗中死亡的生物变成骷髅兵。"
 },
 "advanced": {
  "effects": {
   "main": {
    "val": 30
   },
   "power": {
    "val": 2
   }
  },
  "description": "{中级招魂术}\n\n中级招魂术可使英雄将15%在战斗中死亡的生物变成骷髅兵。"
 },
 "expert": {
  "effects": {
   "main": {
    "val": 60
   },
   "power": {
    "val": 3
   }
  },
  "description": "{高级招魂术}\n\n高级招魂术可使英雄将20%在战斗中死亡的生物变成骷髅兵。"
 },
 "special": true,
 "name": "招魂术"
}
```

</details>

### 寻路术（`core:pathfinding`）

**最终效果**：最终不是核心文件的50／75／100：HotA已注册的skillHotA覆写为地形惩罚减免25／50／75，基础地格消耗修正-15／-20／-35。中级描述“最低70”与基准100及-20的配置推算80不符，需运行时复核。

| 熟练度 | 最终效果 | 当前描述 |
|---|---|---|
| 初级 | `main`：崎岖地形移动惩罚减免 `ROUGH_TERRAIN_DISCOUNT`；值：`25`；数值方式：`"BASE_NUMBER"`<br>`main2`：基础地格移动消耗修正 `BASE_TILE_MOVEMENT_COST`；值：`-15`；数值方式：`"BASE_NUMBER"` | {初级寻路术}<br><br>初级寻路术可以降低英雄对于崎岖地形的移动惩罚，移动一格降低25点移动力，最低消耗为85点。 |
| 中级 | `main`：崎岖地形移动惩罚减免 `ROUGH_TERRAIN_DISCOUNT`；值：`50`；数值方式：`"BASE_NUMBER"`<br>`main2`：基础地格移动消耗修正 `BASE_TILE_MOVEMENT_COST`；值：`-20`；数值方式：`"BASE_NUMBER"` | {中级寻路术}<br><br>中级寻路术可以降低英雄对于崎岖地形的移动惩罚，移动一格降低50点移动力，最低消耗为70点。 |
| 专家 | `main`：崎岖地形移动惩罚减免 `ROUGH_TERRAIN_DISCOUNT`；值：`75`；数值方式：`"BASE_NUMBER"`<br>`main2`：基础地格移动消耗修正 `BASE_TILE_MOVEMENT_COST`；值：`-35`；数值方式：`"BASE_NUMBER"` | {高级寻路术}<br><br>高级寻路术可以降低英雄对于崎岖地形的移动惩罚，移动一格降低75点移动力，最低消耗为65点。 |

**描述检查**：部分不符：中级最低消耗文案70与main2=-20不一致，基准100下应为80；需实机验证结算。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/skills.json](<Hero3Expasion1.83/config/skills.json>)
- [Hero3Expasion1.83/Mods/hota/Mods/gameBalance/content/config/hotaGameBalance/skillHotA.json](<Hero3Expasion1.83/Mods/hota/Mods/gameBalance/content/config/hotaGameBalance/skillHotA.json>)

<details>
<summary>技能改动历史</summary>

- 2026-06-19 `353602c5`：`advanced.description`：`"{中级寻路术}\n\n中级寻路术可以降低英雄对于崎岖地形的移动惩罚，移动一格降低50点移动力，最低消耗为90点。"` → `"{中级寻路术}\n\n中级寻路术可以降低英雄对于崎岖地形的移动惩罚，移动一格降低50点移动力，最低消耗为70点。"`；`advanced.effects.main2.val`：`-10` → `-20`；`basic.description`：`"{初级寻路术}\n\n初级寻路术可以降低英雄对于崎岖地形的移动惩罚，移动一格降低25点移动力，最低消耗为95点。"` → `"{初级寻路术}\n\n初级寻路术可以降低英雄对于崎岖地形的移动惩罚，移动一格降低25点移动力，最低消耗为85点。"`；`basic.effects.main2.val`：`-5` → `-15`；`expert.description`：`"{高级寻路术}\n\n高级寻路术可以降低英雄对于崎岖地形的移动惩罚，移动一格降低75点移动力，最低消耗为85点。"` → `"{高级寻路术}\n\n高级寻路术可以降低英雄对于崎岖地形的移动惩罚，移动一格降低75点移动力，最低消耗为65点。"`；`expert.effects.main2.val`：`-15` → `-35`。
- 2026-06-19 `353602c5`：`advanced.effects.main.val`：`50` → `75`；`basic.effects.main.val`：`25` → `50`；`expert.effects.main.val`：`75` → `100`。

</details>


<details>
<summary>当前完整技能定义</summary>

```json
{
 "index": 0,
 "specialty": [],
 "base": {
  "effects": {
   "main": {
    "type": "ROUGH_TERRAIN_DISCOUNT",
    "valueType": "BASE_NUMBER"
   },
   "main2": {
    "type": "BASE_TILE_MOVEMENT_COST",
    "valueType": "BASE_NUMBER"
   }
  }
 },
 "basic": {
  "effects": {
   "main": {
    "val": 25
   },
   "main2": {
    "val": -15
   }
  },
  "description": "{初级寻路术}\n\n初级寻路术可以降低英雄对于崎岖地形的移动惩罚，移动一格降低25点移动力，最低消耗为85点。"
 },
 "advanced": {
  "effects": {
   "main": {
    "val": 50
   },
   "main2": {
    "val": -20
   }
  },
  "description": "{中级寻路术}\n\n中级寻路术可以降低英雄对于崎岖地形的移动惩罚，移动一格降低50点移动力，最低消耗为70点。"
 },
 "expert": {
  "effects": {
   "main": {
    "val": 75
   },
   "main2": {
    "val": -35
   }
  },
  "description": "{高级寻路术}\n\n高级寻路术可以降低英雄对于崎岖地形的移动惩罚，移动一格降低75点移动力，最低消耗为65点。"
 },
 "name": "寻路术"
}
```

</details>

### 学术（`core:scholar`）

**最终效果**：传法上限3／4／5级；新增同一玩家英雄经验追赶：严格高等级者为老师，只看老师学术，将学生总经验补到老师20%／40%／80%，不足才发，不扣老师经验，不对本次再乘学习／经验光环，不要求学生魔法书智慧，受等级上限限制。相遇交换或城镇处理后延迟结算；专家原有玩家经验光环保留。需要已部署的新引擎，描述已更新。

| 熟练度 | 最终效果 | 当前描述 |
|---|---|---|
| 初级 | `main`：学术传法等级上限 `LEARN_MEETING_SPELL_LIMIT`；值：`3`；数值方式：`"BASE_NUMBER"` | {初级学术}<br><br>掌握初级学术的英雄可以从其他英雄处学习1-3级魔法，或将已学会的1-3级魔法传授给其他英雄。<br><br>与同一玩家的较低等级英雄相遇，可将对方总经验补足到自身总经验的20%，不扣除自身经验。学生已达到该值时不再获得；不要求魔法书或智慧，不受额外经验加成影响，仍受地图等级上限限制。<br><br>掌握学术是学习{符文学}的必要条件。 |
| 中级 | `main`：学术传法等级上限 `LEARN_MEETING_SPELL_LIMIT`；值：`4`；数值方式：`"BASE_NUMBER"` | {中级学术}<br><br>掌握中级学术的英雄可以从其他英雄处学习1-4级魔法，或将已学会的1-4级魔法传授给其他英雄。<br><br>与同一玩家的较低等级英雄相遇，可将对方总经验补足到自身总经验的40%，不扣除自身经验。学生已达到该值时不再获得；不要求魔法书或智慧，不受额外经验加成影响，仍受地图等级上限限制。<br><br>掌握学术是学习{符文学}的必要条件。 |
| 专家 | `main`：学术传法等级上限 `LEARN_MEETING_SPELL_LIMIT`；值：`5`；数值方式：`"BASE_NUMBER"`<br>`xpAura`：经验获取加成 `HERO_EXPERIENCE_GAIN_PERCENT`；值：`100`；数值方式：`"PERCENT_TO_BASE"`；传播范围：`"PLAYER"` | {高级学术}<br><br>掌握高级学术的英雄可以从其他英雄处学习1-5级魔法，或将已学会的1-5级魔法传授给其他英雄。<br><br>与同一玩家的较低等级英雄相遇，可将对方总经验补足到自身总经验的80%，不扣除自身经验。学生已达到该值时不再获得；不要求魔法书或智慧，不受额外经验加成影响，仍受地图等级上限限制。<br><br>掌握学术是学习{符文学}的必要条件。 |

**描述检查**：传法3／4／5、经验20%／40%／80%及符文学前提已同步；专家保留的玩家经验光环100%未写入本段描述。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/skills.json](<Hero3Expasion1.83/config/skills.json>)
- [Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/scholar.json](<Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/scholar.json>)

<details>
<summary>技能改动历史</summary>

- 2026-06-07 `7cca90ae`：`advanced.effects.main.val`：`3` → `4`；`basic.effects.main.val`：`2` → `3`；`expert.effects.main.val`：`4` → `5`。
- 2026-06-20 `d20e3775`：`expert.effects.xpAura.propagator`：`null` → `"PLAYER"`；`expert.effects.xpAura.type`：`null` → `"HERO_EXPERIENCE_GAIN_PERCENT"`；`expert.effects.xpAura.val`：`null` → `100`；`expert.effects.xpAura.valueType`：`null` → `"PERCENT_TO_BASE"`。
- 2026-11-06 `bed82f2f`：`advanced.description`：`"{中级学术}\n\n掌握中级学术的英雄可以从其他英雄处学习1-3级魔法，或将已学会的1-3级魔法传授给其他英雄。\n\n掌握学术是学习{符文学}的必要条件。"` → `"{中级学术}\n\n掌握中级学术的英雄可以从其他英雄处学习1-3级魔法，或将已学会的1-3级魔法传授给其他英雄。\n\n与同一玩家的较低等级英雄相遇，可将对方总经验补足到自身总经验的40%，不扣除自身经验。学生已达到该值时不再获得；不要求魔法书或智慧，不受额外经验加成影响，仍受地图等级上限限制。\n\n掌握学术是学习{符文学}的必要条件。"`；`basic.description`：`"{初级学术}\n\n掌握初级学术的英雄可以从其他英雄处学习1-2级魔法，或将已学会的1-2级魔法传授给其他英雄。\n\n掌握学术是学习{符文学}的必要条件。"` → `"{初级学术}\n\n掌握初级学术的英雄可以从其他英雄处学习1-2级魔法，或将已学会的1-2级魔法传授给其他英雄。\n\n与同一玩家的较低等级英雄相遇，可将对方总经验补足到自身总经验的20%，不扣除自身经验。学生已达到该值时不再获得；不要求魔法书或智慧，不受额外经验加成影响，仍受地图等级上限限制。\n\n掌握学术是学习{符文学}的必要条件。"`；`expert.description`：`"{高级学术}\n\n掌握高级学术的英雄可以从其他英雄处学习1-4级魔法，或将已学会的1-4级魔法传授给其他英雄。\n\n掌握学术是学习{符文学}的必要条件。"` → `"{高级学术}\n\n掌握高级学术的英雄可以从其他英雄处学习1-4级魔法，或将已学会的1-4级魔法传授给其他英雄。\n\n与同一玩家的较低等级英雄相遇，可将对方总经验补足到自身总经验的80%，不扣除自身经验。学生已达到该值时不再获得；不要求魔法书或智慧，不受额外经验加成影响，仍受地图等级上限限制。\n\n掌握学术是学习{符文学}的必要条件。"`。
- 2026-11-06 `29bcb8b8`：`advanced.description`：`"{中级学术}\n\n掌握中级学术的英雄可以从其他英雄处学习1-3级魔法，或将已学会的1-3级魔法传授给其他英雄。\n\n与同一玩家的较低等级英雄相遇，可将对方总经验补足到自身总经验的40%，不扣除自身经验。学生已达到该值时不再获得；不要求魔法书或智慧，不受额外经验加成影响，仍受地图等级上限限制。\n\n掌握学术是学习{符文学}的必要条件。"` → `"{中级学术}\n\n掌握中级学术的英雄可以从其他英雄处学习1-4级魔法，或将已学会的1-4级魔法传授给其他英雄。\n\n与同一玩家的较低等级英雄相遇，可将对方总经验补足到自身总经验的40%，不扣除自身经验。学生已达到该值时不再获得；不要求魔法书或智慧，不受额外经验加成影响，仍受地图等级上限限制。\n\n掌握学术是学习{符文学}的必要条件。"`；`basic.description`：`"{初级学术}\n\n掌握初级学术的英雄可以从其他英雄处学习1-2级魔法，或将已学会的1-2级魔法传授给其他英雄。\n\n与同一玩家的较低等级英雄相遇，可将对方总经验补足到自身总经验的20%，不扣除自身经验。学生已达到该值时不再获得；不要求魔法书或智慧，不受额外经验加成影响，仍受地图等级上限限制。\n\n掌握学术是学习{符文学}的必要条件。"` → `"{初级学术}\n\n掌握初级学术的英雄可以从其他英雄处学习1-3级魔法，或将已学会的1-3级魔法传授给其他英雄。\n\n与同一玩家的较低等级英雄相遇，可将对方总经验补足到自身总经验的20%，不扣除自身经验。学生已达到该值时不再获得；不要求魔法书或智慧，不受额外经验加成影响，仍受地图等级上限限制。\n\n掌握学术是学习{符文学}的必要条件。"`；`expert.description`：`"{高级学术}\n\n掌握高级学术的英雄可以从其他英雄处学习1-4级魔法，或将已学会的1-4级魔法传授给其他英雄。\n\n与同一玩家的较低等级英雄相遇，可将对方总经验补足到自身总经验的80%，不扣除自身经验。学生已达到该值时不再获得；不要求魔法书或智慧，不受额外经验加成影响，仍受地图等级上限限制。\n\n掌握学术是学习{符文学}的必要条件。"` → `"{高级学术}\n\n掌握高级学术的英雄可以从其他英雄处学习1-5级魔法，或将已学会的1-5级魔法传授给其他英雄。\n\n与同一玩家的较低等级英雄相遇，可将对方总经验补足到自身总经验的80%，不扣除自身经验。学生已达到该值时不再获得；不要求魔法书或智慧，不受额外经验加成影响，仍受地图等级上限限制。\n\n掌握学术是学习{符文学}的必要条件。"`。
- 2026-11-06 `29bcb8b8`：`advanced.description`：`null` → `"{Advanced Scholar}\n\nExchanges level 1-4 spells under the normal Scholar rules. When meeting a lower-level hero of the same player, raises their total experience to 40% of the teacher's total experience if it is below that target. The teacher loses no experience. Experience instruction does not require spellbooks or Wisdom, receives no extra XP multiplier, and respects the map level cap."`；`basic.description`：`null` → `"{Basic Scholar}\n\nExchanges level 1-3 spells under the normal Scholar rules. When meeting a lower-level hero of the same player, raises their total experience to 20% of the teacher's total experience if it is below that target. The teacher loses no experience. Experience instruction does not require spellbooks or Wisdom, receives no extra XP multiplier, and respects the map level cap."`；`expert.description`：`null` → `"{Expert Scholar}\n\nExchanges level 1-5 spells under the normal Scholar rules. When meeting a lower-level hero of the same player, raises their total experience to 80% of the teacher's total experience if it is below that target. The teacher loses no experience. Experience instruction does not require spellbooks or Wisdom, receives no extra XP multiplier, and respects the map level cap."`。

</details>


<details>
<summary>当前完整技能定义</summary>

```json
{
 "index": 18,
 "specialty": [],
 "base": {
  "effects": {
   "main": {
    "type": "LEARN_MEETING_SPELL_LIMIT",
    "valueType": "BASE_NUMBER"
   }
  }
 },
 "basic": {
  "description": "{初级学术}\n\n掌握初级学术的英雄可以从其他英雄处学习1-3级魔法，或将已学会的1-3级魔法传授给其他英雄。\n\n与同一玩家的较低等级英雄相遇，可将对方总经验补足到自身总经验的20%，不扣除自身经验。学生已达到该值时不再获得；不要求魔法书或智慧，不受额外经验加成影响，仍受地图等级上限限制。\n\n掌握学术是学习{符文学}的必要条件。",
  "effects": {
   "main": {
    "val": 3
   }
  }
 },
 "advanced": {
  "description": "{中级学术}\n\n掌握中级学术的英雄可以从其他英雄处学习1-4级魔法，或将已学会的1-4级魔法传授给其他英雄。\n\n与同一玩家的较低等级英雄相遇，可将对方总经验补足到自身总经验的40%，不扣除自身经验。学生已达到该值时不再获得；不要求魔法书或智慧，不受额外经验加成影响，仍受地图等级上限限制。\n\n掌握学术是学习{符文学}的必要条件。",
  "effects": {
   "main": {
    "val": 4
   }
  }
 },
 "expert": {
  "description": "{高级学术}\n\n掌握高级学术的英雄可以从其他英雄处学习1-5级魔法，或将已学会的1-5级魔法传授给其他英雄。\n\n与同一玩家的较低等级英雄相遇，可将对方总经验补足到自身总经验的80%，不扣除自身经验。学生已达到该值时不再获得；不要求魔法书或智慧，不受额外经验加成影响，仍受地图等级上限限制。\n\n掌握学术是学习{符文学}的必要条件。",
  "effects": {
   "main": {
    "val": 5
   },
   "xpAura": {
    "type": "HERO_EXPERIENCE_GAIN_PERCENT",
    "val": 100,
    "valueType": "PERCENT_TO_BASE",
    "propagator": "PLAYER"
   }
  }
 },
 "name": "学术"
}
```

</details>

### 魔力（`core:sorcery`）

**最终效果**：法术伤害+10%／25%／50%，数值与当前三级描述同步。

| 熟练度 | 最终效果 | 当前描述 |
|---|---|---|
| 初级 | `main`：法术伤害增幅 `SPELL_DAMAGE`；对象／属性：`"spellSchool.any"`；值：`10`；数值方式：`"BASE_NUMBER"` | {初级魔力}<br><br>初级魔力使魔法的杀伤力提升10%。 |
| 中级 | `main`：法术伤害增幅 `SPELL_DAMAGE`；对象／属性：`"spellSchool.any"`；值：`25`；数值方式：`"BASE_NUMBER"` | {中级魔力}<br><br>中级魔力使魔法的杀伤力提升25%。 |
| 专家 | `main`：法术伤害增幅 `SPELL_DAMAGE`；对象／属性：`"spellSchool.any"`；值：`50`；数值方式：`"BASE_NUMBER"` | {高级魔力}<br><br>高级魔力使魔法的杀伤力提升50%。 |

**描述检查**：三级伤害10%／25%／50%与描述一致。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/skills.json](<Hero3Expasion1.83/config/skills.json>)
- [Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/sorcery.json](<Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/sorcery.json>)

<details>
<summary>技能改动历史</summary>

- 2026-09-20 `659ed492`：`advanced.description`：`"{中级魔力}\n\n中级魔力使魔法的杀伤力提升20%。"` → `"{中级魔力}\n\n中级魔力使魔法的杀伤力提升25%。"`；`advanced.effects.main.val`：`20` → `25`；`expert.description`：`"{高级魔力}\n\n高级魔力使魔法的杀伤力提升30%。"` → `"{高级魔力}\n\n高级魔力使魔法的杀伤力提升50%。"`；`expert.effects.main.val`：`30` → `50`。

</details>


<details>
<summary>当前完整技能定义</summary>

```json
{
 "index": 25,
 "specialty": [
  "main"
 ],
 "base": {
  "effects": {
   "main": {
    "type": "SPELL_DAMAGE",
    "subtype": "spellSchool.any",
    "valueType": "BASE_NUMBER"
   }
  }
 },
 "basic": {
  "effects": {
   "main": {
    "val": 10
   }
  },
  "description": "{初级魔力}\n\n初级魔力使魔法的杀伤力提升10%。"
 },
 "advanced": {
  "effects": {
   "main": {
    "val": 25
   }
  },
  "description": "{中级魔力}\n\n中级魔力使魔法的杀伤力提升25%。"
 },
 "expert": {
  "effects": {
   "main": {
    "val": 50
   }
  },
  "description": "{高级魔力}\n\n高级魔力使魔法的杀伤力提升50%。"
 },
 "name": "魔力"
}
```

</details>

## 魔法改动

法术以ID为准，不用相近中文名猜测。下表熟练度效果已继承levels.base；普通等级为none，初级为basic。原版名称、基础消耗和说明同时读取SPTRAITS；快照保留实际目标条件、效果类型、范围、音效与图标。

### 召唤夺心魔（`asylum:hornSpell`）

**最终效果与范围**：梦境本地深渊号角：5级水系战斗法术，core:demonSummon将尸体转为夺心魔，永久保留；仍受尸体生命总额和目标条件约束，不是“200具尸体固定换200只”。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 0 | 400000 | `0` | {"effects":{},"battleEffects":{"demonSummon":{"id":"hota.neutralcreatures:fangarm","permanent":true,"type":"core:demonSummon"}}} | {召唤夺心魔}<br><br>从尸体上召唤一队夺心魔，战后不会消失。对亡灵和无生命的生物无效。 |
| 初级 | 0 | 400000 | `0` | {"effects":{},"battleEffects":{"demonSummon":{"id":"hota.neutralcreatures:fangarm","permanent":true,"type":"core:demonSummon"}}} | {召唤夺心魔}<br><br>从尸体上召唤一队夺心魔，战后不会消失。对亡灵和无生命的生物无效。 |
| 中级 | 0 | 400000 | `0` | {"effects":{},"battleEffects":{"demonSummon":{"id":"hota.neutralcreatures:fangarm","permanent":true,"type":"core:demonSummon"}}} | {召唤夺心魔}<br><br>从尸体上召唤一队夺心魔，战后不会消失。对亡灵和无生命的生物无效。 |
| 专家 | 0 | 400000 | `0` | {"effects":{},"battleEffects":{"demonSummon":{"id":"hota.neutralcreatures:fangarm","permanent":true,"type":"core:demonSummon"}}} | {召唤夺心魔}<br><br>从尸体上召唤一队夺心魔，战后不会消失。对亡灵和无生命的生物无效。 |

**目标条件**：`{"noneOf":{"bonus.NON_LIVING":"absolute","bonus.SIEGE_WEAPON":"absolute","bonus.UNDEAD":"absolute","bonus.GARGOYLE":"absolute","bonus.MECHANICAL":"absolute"}}`。

**描述检查**：永久召唤及亡灵／非生命无效已描述，但0耗魔与极高额度未写明。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/asylum/content/config/asylum/NewOldSpells/hornSpell.json](<Hero3Expasion1.83/Mods/asylum/content/config/asylum/NewOldSpells/hornSpell.json>)

<details>
<summary>法术改动历史</summary>

- 2026-06-13 `892590ca`：`animation.affect`：`null` → `["C13SPF0"]`；`defaultGainChance`：`null` → `0`；`flags.rising`：`null` → `true`；`graphics.iconBook`：`null` → `"artifactsCampaignBonuses/fakehornEffect.png"`；`graphics.iconEffect`：`null` → `"artifactsCampaignBonuses/fakehornEffect.png"`；`graphics.iconImmune`：`null` → `"artifactsCampaignBonuses/fakehornImmune.png"`；`graphics.iconScenarioBonus`：`null` → `"artifactsCampaignBonuses/fakehornScene.png"`；`graphics.iconScroll`：`null` → `"artifactsCampaignBonuses/fakehornScroll.png"`；`level`：`null` → `5`；`levels.base.battleEffects.demonSummon.id`：`null` → `"hota.neutralcreatures:fangarm"`；`levels.base.battleEffects.demonSummon.permanent`：`null` → `true`；`levels.base.battleEffects.demonSummon.type`：`null` → `"core:demonSummon"`；`levels.base.cost`：`null` → `0`；`levels.base.description`：`null` → `"{召唤夺心魔}\r\n\r\n从尸体上召唤一队夺心魔，战后不会消失。对亡灵和无生命的生物无效。"`；`levels.base.power`：`null` → `80`；`levels.base.range`：`null` → `"0"`；`levels.base.targetModifier.smart`：`null` → `false`；`name`：`null` → `"召唤夺心魔"`；`power`：`null` → `30`；`school.air`：`null` → `false`；`school.earth`：`null` → `false`；`school.fire`：`null` → `false`；`school.water`：`null` → `true`；`sounds.cast`：`null` → `"ANIMDEAD"`；`targetCondition.noneOf.bonus.GARGOYLE`：`null` → `"absolute"`；`targetCondition.noneOf.bonus.MECHANICAL`：`null` → `"absolute"`；`targetCondition.noneOf.bonus.NON_LIVING`：`null` → `"absolute"`；`targetCondition.noneOf.bonus.SIEGE_WEAPON`：`null` → `"absolute"`；`targetCondition.noneOf.bonus.UNDEAD`：`null` → `"absolute"`；`targetType`：`null` → `"CREATURE"`；`type`：`null` → `"combat"`。
- 2026-06-22 `797ce8ad`：`levels.base.power`：`80` → `400000`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "type": "combat",
 "targetType": "CREATURE",
 "name": "召唤夺心魔",
 "school": {
  "air": false,
  "earth": false,
  "fire": false,
  "water": true
 },
 "level": 5,
 "power": 30,
 "defaultGainChance": 0,
 "gainChance": {},
 "animation": {
  "affect": [
   "C13SPF0"
  ]
 },
 "sounds": {
  "cast": "ANIMDEAD"
 },
 "flags": {
  "rising": true
 },
 "graphics": {
  "iconBook": "artifactsCampaignBonuses/fakehornEffect.png",
  "iconScroll": "artifactsCampaignBonuses/fakehornScroll.png",
  "iconScenarioBonus": "artifactsCampaignBonuses/fakehornScene.png",
  "iconImmune": "artifactsCampaignBonuses/fakehornImmune.png",
  "iconEffect": "artifactsCampaignBonuses/fakehornEffect.png"
 },
 "targetCondition": {
  "noneOf": {
   "bonus.NON_LIVING": "absolute",
   "bonus.SIEGE_WEAPON": "absolute",
   "bonus.UNDEAD": "absolute",
   "bonus.GARGOYLE": "absolute",
   "bonus.MECHANICAL": "absolute"
  }
 },
 "levels": {
  "base": {
   "description": "{召唤夺心魔}\r\n\r\n从尸体上召唤一队夺心魔，战后不会消失。对亡灵和无生命的生物无效。",
   "power": 400000,
   "cost": 0,
   "targetModifier": {
    "smart": false
   },
   "range": "0",
   "battleEffects": {
    "demonSummon": {
     "id": "hota.neutralcreatures:fangarm",
     "permanent": true,
     "type": "core:demonSummon"
    }
   }
  },
  "none": {},
  "basic": {},
  "advanced": {},
  "expert": {}
 }
}
```

</details>

### 抗魔大法（`core:antiMagic`）

**最终效果与范围**：抗魔大法：当前专家range=X，原历史的小写x已修正，不应继续列为当前缺陷。专家等级法术免疫阈值5，完整条件见快照。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 15 | 4 | `0` | {"effects":{},"battleEffects":{"spellImmunity":{"type":"core:timed","bonus":{"levelSpellImmunity":{"val":3,"type":"LEVEL_SPELL_IMMUNITY","valueType":"INDEPENDENT_MAX","duration":"N_TURNS"}}},"dispel":{"type":"core:dispel","optional":true,"dispelNegative":true,"dispelNeutral":true,"dispelPositive":false}}} | {抗魔大法}<br><br>使被选中的部队能防御所有低级魔法的攻击。 |
| 初级 | 12 | 4 | `0` | {"effects":{},"battleEffects":{"spellImmunity":{"type":"core:timed","bonus":{"levelSpellImmunity":{"val":3,"type":"LEVEL_SPELL_IMMUNITY","valueType":"INDEPENDENT_MAX","duration":"N_TURNS"}}},"dispel":{"type":"core:dispel","optional":true,"dispelNegative":true,"dispelNeutral":true,"dispelPositive":false}}} | {初级抗魔大法}<br><br>使被选中的部队能防御所有低级魔法的攻击。 |
| 中级 | 12 | 5 | `0` | {"effects":{},"battleEffects":{"spellImmunity":{"type":"core:timed","bonus":{"levelSpellImmunity":{"val":4,"type":"LEVEL_SPELL_IMMUNITY","valueType":"INDEPENDENT_MAX","duration":"N_TURNS"}}},"dispel":{"type":"core:dispel","optional":true,"dispelNegative":true,"dispelNeutral":true,"dispelPositive":false}}} | {中级抗魔大法}<br><br>使被选中的部队能防御除最高级魔法外所有魔法的攻击。<br> |
| 专家 | 12 | 6 | `X` | {"effects":{},"battleEffects":{"spellImmunity":{"type":"core:timed","bonus":{"levelSpellImmunity":{"val":5,"type":"LEVEL_SPELL_IMMUNITY","valueType":"INDEPENDENT_MAX","duration":"N_TURNS"}}},"dispel":{"type":"core:dispel","optional":true,"dispelNegative":true,"dispelNeutral":true,"dispelPositive":false}}} | {高级抗魔大法}<br><br>使被选中的部队能防御所有魔法的攻击。 |

**目标条件**：`{}`。

**描述检查**：当前专家范围X结构已修复；继承说明仍按原版单目标表述，需同步群体范围。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/spells/timed.json](<Hero3Expasion1.83/config/spells/timed.json>)
- [Hero3Expasion1.83/Mods/vcmi-extras/Mods/bonusIcons/mods/Immunity Icons/Content/spellImmunityIcons.json](<Hero3Expasion1.83/Mods/vcmi-extras/Mods/bonusIcons/mods/Immunity Icons/Content/spellImmunityIcons.json>)

<details>
<summary>法术改动历史</summary>

- 2026-06-13 `9ae0792d`：`levels.expert.range`：`null` → `"X"`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "index": 34,
 "targetType": "CREATURE",
 "animation": {
  "affect": [
   "C02SPE0"
  ]
 },
 "sounds": {
  "cast": "ANTIMAGK"
 },
 "levels": {
  "base": {
   "range": "0",
   "targetModifier": {
    "smart": true
   },
   "battleEffects": {
    "spellImmunity": {
     "type": "core:timed",
     "bonus": {
      "levelSpellImmunity": {
       "val": 3,
       "type": "LEVEL_SPELL_IMMUNITY",
       "valueType": "INDEPENDENT_MAX",
       "duration": "N_TURNS"
      }
     }
    },
    "dispel": {
     "type": "core:dispel",
     "optional": true,
     "dispelNegative": true,
     "dispelNeutral": true,
     "dispelPositive": false
    }
   }
  },
  "advanced": {
   "battleEffects": {
    "spellImmunity": {
     "bonus": {
      "levelSpellImmunity": {
       "val": 4
      }
     }
    }
   },
   "cost": 12,
   "power": 5,
   "description": "{中级抗魔大法}\n\n使被选中的部队能防御除最高级魔法外所有魔法的攻击。\n"
  },
  "expert": {
   "range": "X",
   "battleEffects": {
    "spellImmunity": {
     "bonus": {
      "levelSpellImmunity": {
       "val": 5
      }
     }
    }
   },
   "cost": 12,
   "power": 6,
   "description": "{高级抗魔大法}\n\n使被选中的部队能防御所有魔法的攻击。"
  },
  "none": {
   "cost": 15,
   "power": 4,
   "description": "{抗魔大法}\n\n使被选中的部队能防御所有低级魔法的攻击。"
  },
  "basic": {
   "cost": 12,
   "power": 4,
   "description": "{初级抗魔大法}\n\n使被选中的部队能防御所有低级魔法的攻击。"
  }
 },
 "flags": {
  "positive": true
 },
 "graphics": {
  "iconImmune": "spellimmunity/imm_antimagic.png"
 },
 "name": "抗魔大法",
 "level": 3,
 "power": 0
}
```

</details>

### 异次元之门（`core:dimensionDoor`）

**最终效果与范围**：已改动字段和实际分级效果见下表及历史；不从中文名称推断隐藏规则。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 25 | 1 | `X` | {"effects":{},"battleEffects":{}} | {异次元之门}<br><br>把英雄送到一个未被占领但可见的目的地。每天可以使用1次。 |
| 初级 | 20 | 2 | `X` | {"effects":{},"battleEffects":{}} | {初级异次元之门}<br><br>把英雄送到一个未被占领但可见的目的地。每天可以使用2次。<br> |
| 中级 | 20 | 3 | `X` | {"effects":{},"battleEffects":{}} | {中级异次元之门}<br><br>把英雄送到一个未被占领但可见的目的地。每天可以使用3次。<br> |
| 专家 | 20 | 4 | `X` | {"effects":{},"battleEffects":{}} | {高级异次元之门}<br><br>把英雄送到一个未被占领但可见的目的地。每天可以使用4次。<br> |

**目标条件**：`{}`。

**描述检查**：已保留各级最终说明与效果对照；本轮未自动证明所有自定义效果说明完全一致，需人工／游戏内复核。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/spells/adventure.json](<Hero3Expasion1.83/config/spells/adventure.json>)

<details>
<summary>法术改动历史</summary>

- 2026-06-06 `61f68f5b`：`levels.advanced.adventureEffect.castsPerDay`：`3` → `4`；`levels.expert.adventureEffect.castsPerDay`：`4` → `8`。
- 2026-06-22 `797ce8ad`：`levels.expert.adventureEffect.movementPointsTaken`：`200` → `100`；`levels.expert.adventureEffect.rangeX`：`null` → `20`；`levels.expert.adventureEffect.rangeY`：`null` → `15`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "index": 8,
 "targetType": "LOCATION",
 "sounds": {
  "cast": "TELPTOUT"
 },
 "levels": {
  "base": {
   "range": "X",
   "adventureEffect": {
    "type": "dimensionDoor",
    "movementPointsRequired": 0,
    "movementPointsTaken": 300,
    "waterLandFailureTakesPoints": true,
    "cursor": "mapDimensionDoor",
    "cursorGuarded": "mapTurn1Attack",
    "castsPerDay": 1,
    "rangeX": 9,
    "rangeY": 8,
    "ignoreFow": true,
    "exposeFow": true
   }
  },
  "basic": {
   "adventureEffect": {
    "castsPerDay": 2
   },
   "cost": 20,
   "power": 2,
   "description": "{初级异次元之门}\n\n把英雄送到一个未被占领但可见的目的地。每天可以使用2次。\n"
  },
  "advanced": {
   "adventureEffect": {
    "castsPerDay": 4
   },
   "cost": 20,
   "power": 3,
   "description": "{中级异次元之门}\n\n把英雄送到一个未被占领但可见的目的地。每天可以使用3次。\n"
  },
  "expert": {
   "adventureEffect": {
    "rangeX": 20,
    "rangeY": 15,
    "castsPerDay": 8,
    "movementPointsTaken": 100
   },
   "cost": 20,
   "power": 4,
   "description": "{高级异次元之门}\n\n把英雄送到一个未被占领但可见的目的地。每天可以使用4次。\n"
  },
  "none": {
   "cost": 25,
   "power": 1,
   "description": "{异次元之门}\n\n把英雄送到一个未被占领但可见的目的地。每天可以使用1次。"
  }
 },
 "flags": {
  "indifferent": true
 },
 "name": "异次元之门",
 "level": 5,
 "power": 0
}
```

</details>

### 毁灭之光（`core:disruptingRay`）

**最终效果与范围**：毁灭之光专家range=X，改为群体永久累积减防；低级仍为单体。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 10 | 3 | `0` | {"effects":{},"battleEffects":{}} | {毁灭之光}<br><br>降低被选中敌军的防御力。魔法可以对同一部队重复使用。 |
| 初级 | 8 | 3 | `0` | {"effects":{},"battleEffects":{}} | {初级毁灭之光}<br><br>降低被选中敌军的防御力。魔法可以对同一部队重复使用。<br> |
| 中级 | 8 | 4 | `0` | {"effects":{},"battleEffects":{}} | {中级毁灭之光}<br><br>降低被选中敌军的防御力。魔法可以对同一部队重复使用。<br><br>比初级更有效。 |
| 专家 | 8 | 5 | `X` | {"effects":{},"battleEffects":{}} | {高级毁灭之光}<br><br>降低被选中敌军的防御力。魔法可以对同一部队重复使用。<br><br>比中级更有效。 |

**目标条件**：`{}`。

**描述检查**：不完整：专家群体范围需要同步到说明；效果仍是永久累积减防。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/spells/timed.json](<Hero3Expasion1.83/config/spells/timed.json>)
- [Hero3Expasion1.83/Mods/vcmi-extras/Mods/bonusIcons/mods/Immunity Icons/Content/spellImmunityIcons.json](<Hero3Expasion1.83/Mods/vcmi-extras/Mods/bonusIcons/mods/Immunity Icons/Content/spellImmunityIcons.json>)

<details>
<summary>法术改动历史</summary>

- 2026-06-09 `21646fe0`：`levels.expert.range`：`null` → `"X"`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "index": 47,
 "targetType": "CREATURE",
 "animation": {
  "affect": [
   {
    "defName": "C07SPA1",
    "transparency": 0.5
   }
  ],
  "projectile": [
   {
    "defName": "C07SPA0"
   }
  ]
 },
 "sounds": {
  "cast": "DISRUPTR"
 },
 "levels": {
  "base": {
   "range": "0",
   "targetModifier": {
    "smart": true
   },
   "cumulativeEffects": {
    "primarySkill": {
     "type": "PRIMARY_SKILL",
     "subtype": "primarySkill.defence",
     "val": -3,
     "valueType": "ADDITIVE_VALUE",
     "duration": "PERMANENT"
    }
   }
  },
  "advanced": {
   "cumulativeEffects": {
    "primarySkill": {
     "val": -4
    }
   },
   "cost": 8,
   "power": 4,
   "description": "{中级毁灭之光}\n\n降低被选中敌军的防御力。魔法可以对同一部队重复使用。\n\n比初级更有效。"
  },
  "expert": {
   "range": "X",
   "cumulativeEffects": {
    "primarySkill": {
     "val": -5
    }
   },
   "cost": 8,
   "power": 5,
   "description": "{高级毁灭之光}\n\n降低被选中敌军的防御力。魔法可以对同一部队重复使用。\n\n比中级更有效。"
  },
  "none": {
   "cost": 10,
   "power": 3,
   "description": "{毁灭之光}\n\n降低被选中敌军的防御力。魔法可以对同一部队重复使用。"
  },
  "basic": {
   "cost": 8,
   "power": 3,
   "description": "{初级毁灭之光}\n\n降低被选中敌军的防御力。魔法可以对同一部队重复使用。\n"
  }
 },
 "flags": {
  "negative": true,
  "persistent": true
 },
 "graphics": {
  "iconImmune": "spellimmunity/imm_disrupt.png"
 },
 "name": "毁灭之光",
 "level": 2,
 "power": 0
}
```

</details>

### 幸运之神（`core:fortune`）

**最终效果与范围**：幸运之神：普通／初级幸运+2，中级+4，专家+8，专家群体。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 7 | 1 | `0` | {"effects":{"luck":{"type":"LUCK","val":2,"duration":"N_TURNS"}},"battleEffects":{}} | {幸运之神}<br><br>提高被选中的部队的幸运值。 |
| 初级 | 5 | 1 | `0` | {"effects":{"luck":{"type":"LUCK","val":2,"duration":"N_TURNS"}},"battleEffects":{}} | {初级幸运之神}<br><br>提高被选中的部队的幸运值。 |
| 中级 | 5 | 2 | `0` | {"effects":{"luck":{"type":"LUCK","val":4,"duration":"N_TURNS"}},"battleEffects":{}} | {中级幸运之神}<br><br>提高被选中的部队的幸运值。<br><br>效果是初级的2倍。 |
| 专家 | 5 | 2 | `X` | {"effects":{"luck":{"type":"LUCK","val":8,"duration":"N_TURNS"}},"battleEffects":{}} | {高级幸运之神}<br><br>提高所有部队的幸运值。<br><br>效果是初级的2倍。 |

**目标条件**：`{}`。

**描述检查**：未同步：继承描述与2／4／8的新数值不一致。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/spells/timed.json](<Hero3Expasion1.83/config/spells/timed.json>)

<details>
<summary>法术改动历史</summary>

- 2026-06-17 `3a4ab7cf`：`levels.advanced.effects.luck.val`：`2` → `4`；`levels.base.effects.luck.val`：`1` → `2`；`levels.expert.effects.luck.val`：`2` → `8`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "index": 51,
 "targetType": "CREATURE",
 "animation": {
  "affect": [
   "C09SPA0"
  ]
 },
 "sounds": {
  "cast": "FORTUNE"
 },
 "levels": {
  "base": {
   "range": "0",
   "targetModifier": {
    "smart": true
   },
   "effects": {
    "luck": {
     "type": "LUCK",
     "val": 2,
     "duration": "N_TURNS"
    }
   }
  },
  "advanced": {
   "effects": {
    "luck": {
     "val": 4
    }
   },
   "cost": 5,
   "power": 2,
   "description": "{中级幸运之神}\n\n提高被选中的部队的幸运值。\n\n效果是初级的2倍。"
  },
  "expert": {
   "range": "X",
   "effects": {
    "luck": {
     "val": 8
    }
   },
   "cost": 5,
   "power": 2,
   "description": "{高级幸运之神}\n\n提高所有部队的幸运值。\n\n效果是初级的2倍。"
  },
  "none": {
   "cost": 7,
   "power": 1,
   "description": "{幸运之神}\n\n提高被选中的部队的幸运值。"
  },
  "basic": {
   "cost": 5,
   "power": 1,
   "description": "{初级幸运之神}\n\n提高被选中的部队的幸运值。"
  }
 },
 "counters": {
  "spell.misfortune": true
 },
 "flags": {
  "positive": true
 },
 "name": "幸运之神",
 "level": 2,
 "power": 0
}
```

</details>

### 魔法神镜（`core:magicMirror`）

**最终效果与范围**：魔法神镜：当前advanced和expert与base同级，分别power80／99并群体X；原“expert嵌在base内”的历史错误已不存在。描述仍需与当前概率和群体范围同步核对。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 25 | 20 | `0` | {"effects":{"magicMirror":{"type":"MAGIC_MIRROR","valueType":"INDEPENDENT_MAX","duration":"N_TURNS"}},"battleEffects":{}} | {魔法神镜}<br><br>有20%的机会使敌军使用的魔法反射到任意一支敌军身上。<br> |
| 初级 | 20 | 20 | `0` | {"effects":{"magicMirror":{"type":"MAGIC_MIRROR","valueType":"INDEPENDENT_MAX","duration":"N_TURNS"}},"battleEffects":{}} | {初级魔法神镜}<br><br>有20%的机会使敌军使用的魔法反射到任意一支敌军身上。<br> |
| 中级 | 20 | 80 | `X` | {"effects":{"magicMirror":{"type":"MAGIC_MIRROR","valueType":"INDEPENDENT_MAX","duration":"N_TURNS"}},"battleEffects":{}} | {中级魔法神镜}<br><br>有30%的机会使敌军使用的魔法反射到任意一支敌军身上。<br> |
| 专家 | 20 | 99 | `X` | {"effects":{"magicMirror":{"type":"MAGIC_MIRROR","valueType":"INDEPENDENT_MAX","duration":"N_TURNS"}},"battleEffects":{}} | {高级魔法神镜}<br><br>有40%的机会使敌军使用的魔法反射到任意一支敌军身上。<br> |

**目标条件**：`{}`。

**描述检查**：未同步：当前中级80／专家99且群体，原说明不能代表新效果。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/spells/timed.json](<Hero3Expasion1.83/config/spells/timed.json>)

<details>
<summary>法术改动历史</summary>

- 2026-06-13 `e69928e3`：`levels.advanced.range`：`null` → `"X"`；`levels.expert.range`：`null` → `"X"`。
- 2026-06-14 `3731a2cd`：`levels.advanced.val`：`null` → `80`；`levels.expert.val`：`null` → `99`。
- 2026-06-20 `d20e3775`：`levels.advanced.power`：`null` → `80`；`levels.advanced.val`：`80` → `null`；`levels.expert.power`：`null` → `99`；`levels.expert.val`：`99` → `null`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "index": 36,
 "targetType": "CREATURE",
 "animation": {
  "affect": [
   "C02SPA0"
  ]
 },
 "sounds": {
  "cast": "BACKLASH"
 },
 "levels": {
  "base": {
   "range": "0",
   "targetModifier": {
    "smart": true
   },
   "effects": {
    "magicMirror": {
     "type": "MAGIC_MIRROR",
     "valueType": "INDEPENDENT_MAX",
     "duration": "N_TURNS"
    }
   }
  },
  "advanced": {
   "power": 80,
   "range": "X",
   "cost": 20,
   "description": "{中级魔法神镜}\n\n有30%的机会使敌军使用的魔法反射到任意一支敌军身上。\n"
  },
  "expert": {
   "power": 99,
   "range": "X",
   "cost": 20,
   "description": "{高级魔法神镜}\n\n有40%的机会使敌军使用的魔法反射到任意一支敌军身上。\n"
  },
  "none": {
   "cost": 25,
   "power": 20,
   "description": "{魔法神镜}\n\n有20%的机会使敌军使用的魔法反射到任意一支敌军身上。\n"
  },
  "basic": {
   "cost": 20,
   "power": 20,
   "description": "{初级魔法神镜}\n\n有20%的机会使敌军使用的魔法反射到任意一支敌军身上。\n"
  }
 },
 "flags": {
  "positive": true
 },
 "name": "魔法神镜",
 "level": 5,
 "power": 0
}
```

</details>

### 欢欣鼓舞（`core:mirth`）

**最终效果与范围**：欢欣鼓舞：普通／初级士气+2，中级+4，专家+8，专家群体；最终士气仍受全局上下限与其他来源影响。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 12 | 1 | `0` | {"effects":{"morale":{"type":"MORALE","val":2,"duration":"N_TURNS"}},"battleEffects":{}} | {欢欣鼓舞}<br><br>令被选中的部队士气大增。 |
| 初级 | 9 | 1 | `0` | {"effects":{"morale":{"type":"MORALE","val":2,"duration":"N_TURNS"}},"battleEffects":{}} | {初级欢欣鼓舞}<br><br>令被选中的部队士气大增。 |
| 中级 | 9 | 2 | `0` | {"effects":{"morale":{"type":"MORALE","val":4,"duration":"N_TURNS"}},"battleEffects":{}} | {中级欢欣鼓舞}<br><br>令被选中的部队士气大增。<br><br>效果是初级的2倍。 |
| 专家 | 9 | 2 | `X` | {"effects":{"morale":{"type":"MORALE","val":8,"duration":"N_TURNS"}},"battleEffects":{}} | {高级欢欣鼓舞}<br><br>令所有部队士气大增。<br><br>效果是初级的2倍。 |

**目标条件**：`{"noneOf":{"bonus.MIND_IMMUNITY":"normal","bonus.NON_LIVING":"normal","bonus.MECHANICAL":"normal","bonus.SIEGE_WEAPON":"absolute","bonus.UNDEAD":"absolute"}}`。

**描述检查**：未同步：继承描述与2／4／8的新数值不一致。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/spells/timed.json](<Hero3Expasion1.83/config/spells/timed.json>)

<details>
<summary>法术改动历史</summary>

- 2026-06-17 `3a4ab7cf`：`levels.advanced.effects.morale.val`：`2` → `4`；`levels.base.effects.morale.val`：`1` → `2`；`levels.expert.effects.morale.val`：`2` → `8`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "index": 49,
 "targetType": "CREATURE",
 "animation": {
  "affect": [
   "C09SPW0"
  ]
 },
 "sounds": {
  "cast": "MIRTH"
 },
 "levels": {
  "base": {
   "range": "0",
   "targetModifier": {
    "smart": true
   },
   "effects": {
    "morale": {
     "type": "MORALE",
     "val": 2,
     "duration": "N_TURNS"
    }
   }
  },
  "advanced": {
   "effects": {
    "morale": {
     "val": 4
    }
   },
   "cost": 9,
   "power": 2,
   "description": "{中级欢欣鼓舞}\n\n令被选中的部队士气大增。\n\n效果是初级的2倍。"
  },
  "expert": {
   "range": "X",
   "effects": {
    "morale": {
     "val": 8
    }
   },
   "cost": 9,
   "power": 2,
   "description": "{高级欢欣鼓舞}\n\n令所有部队士气大增。\n\n效果是初级的2倍。"
  },
  "none": {
   "cost": 12,
   "power": 1,
   "description": "{欢欣鼓舞}\n\n令被选中的部队士气大增。"
  },
  "basic": {
   "cost": 9,
   "power": 1,
   "description": "{初级欢欣鼓舞}\n\n令被选中的部队士气大增。"
  }
 },
 "counters": {
  "spell.sorrow": true
 },
 "flags": {
  "positive": true
 },
 "targetCondition": {
  "noneOf": {
   "bonus.MIND_IMMUNITY": "normal",
   "bonus.NON_LIVING": "normal",
   "bonus.MECHANICAL": "normal",
   "bonus.SIEGE_WEAPON": "absolute",
   "bonus.UNDEAD": "absolute"
  }
 },
 "name": "欢欣鼓舞",
 "level": 3,
 "power": 0
}
```

</details>

### 大难临头（`core:misfortune`）

**最终效果与范围**：大难临头：普通／初级幸运-2，中级-4，专家-8，专家群体。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 12 | 1 | `0` | {"effects":{"luck":{"type":"LUCK","val":-2,"duration":"N_TURNS"}},"battleEffects":{}} | {大难临头}<br><br>降低被选中敌军的幸运值。 |
| 初级 | 9 | 1 | `0` | {"effects":{"luck":{"type":"LUCK","val":-2,"duration":"N_TURNS"}},"battleEffects":{}} | {初级大难临头}<br><br>降低被选中敌军的幸运值。<br><br>魔法消耗是普通的1/2。 |
| 中级 | 9 | 2 | `0` | {"effects":{"luck":{"type":"LUCK","val":-4,"duration":"N_TURNS"}},"battleEffects":{}} | {中级大难临头}<br><br>降低被选中敌军的幸运值。<br><br>效果是初级的2倍。 |
| 专家 | 9 | 2 | `X` | {"effects":{"luck":{"type":"LUCK","val":-8,"duration":"N_TURNS"}},"battleEffects":{}} | {高级大难临头}<br><br>降低所有敌军的幸运值。<br><br>效果是初级的2倍。 |

**目标条件**：`{}`。

**描述检查**：未同步：继承描述与-2／-4／-8的新数值不一致。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/spells/timed.json](<Hero3Expasion1.83/config/spells/timed.json>)
- [Hero3Expasion1.83/Mods/vcmi-extras/Mods/bonusIcons/mods/Immunity Icons/Content/spellImmunityIcons.json](<Hero3Expasion1.83/Mods/vcmi-extras/Mods/bonusIcons/mods/Immunity Icons/Content/spellImmunityIcons.json>)

<details>
<summary>法术改动历史</summary>

- 2026-06-17 `3a4ab7cf`：`levels.advanced.effects.luck.val`：`-2` → `-4`；`levels.base.effects.luck.val`：`-1` → `-2`；`levels.expert.effects.luck.val`：`-2` → `-8`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "index": 52,
 "targetType": "CREATURE",
 "animation": {
  "affect": [
   "C10SPF0"
  ]
 },
 "sounds": {
  "cast": "MISFORT"
 },
 "levels": {
  "base": {
   "range": "0",
   "targetModifier": {
    "smart": true
   },
   "effects": {
    "luck": {
     "type": "LUCK",
     "val": -2,
     "duration": "N_TURNS"
    }
   }
  },
  "advanced": {
   "effects": {
    "luck": {
     "val": -4
    }
   },
   "cost": 9,
   "power": 2,
   "description": "{中级大难临头}\n\n降低被选中敌军的幸运值。\n\n效果是初级的2倍。"
  },
  "expert": {
   "range": "X",
   "effects": {
    "luck": {
     "val": -8
    }
   },
   "cost": 9,
   "power": 2,
   "description": "{高级大难临头}\n\n降低所有敌军的幸运值。\n\n效果是初级的2倍。"
  },
  "none": {
   "cost": 12,
   "power": 1,
   "description": "{大难临头}\n\n降低被选中敌军的幸运值。"
  },
  "basic": {
   "cost": 9,
   "power": 1,
   "description": "{初级大难临头}\n\n降低被选中敌军的幸运值。\n\n魔法消耗是普通的1/2。"
  }
 },
 "counters": {
  "spell.fortune": true
 },
 "flags": {
  "negative": true
 },
 "graphics": {
  "iconImmune": "spellimmunity/imm_misfortune.png"
 },
 "name": "大难临头",
 "level": 3,
 "power": 0
}
```

</details>

### 御气奇术（`core:protectAir`）

**最终效果与范围**：四系御魔之一：专家对应学派法术伤害减免提高至90%，群体X；中级及以下以表格为准。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 7 | 70 | `0` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.air","duration":"N_TURNS","val":30}},"battleEffects":{}} | {御气奇术}<br><br>能对被选中的部队起到保护作用，降低气系魔法的杀伤力。<br> |
| 初级 | 5 | 70 | `0` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.air","duration":"N_TURNS","val":30}},"battleEffects":{}} | {初级御气奇术}<br><br>能对被选中的部队起到保护作用，降低气系魔法的杀伤力。<br> |
| 中级 | 5 | 50 | `0` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.air","duration":"N_TURNS","val":50}},"battleEffects":{}} | {中级御气奇术}<br><br>能对被选中的部队起到保护作用，降低气系魔法的杀伤力。<br><br>防御效果接近初级的2倍。 |
| 专家 | 5 | 50 | `X` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.air","duration":"N_TURNS","val":90}},"battleEffects":{}} | {高级御气奇术}<br><br>能对所有的部队起到保护作用，降低气系魔法的杀伤力。<br><br>防御效果接近初级的2倍。 |

**目标条件**：`{}`。

**描述检查**：未同步：专家实际90%，继承描述仍为50%。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/spells/timed.json](<Hero3Expasion1.83/config/spells/timed.json>)

<details>
<summary>法术改动历史</summary>

- 2026-08-03 `7e77dfde`：`levels.expert.effects.spellDamageReduction.val`：`50` → `90`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "index": 30,
 "targetType": "CREATURE",
 "animation": {
  "affect": [
   "C11SPE0"
  ]
 },
 "sounds": {
  "cast": "PROTECTA"
 },
 "levels": {
  "base": {
   "range": "0",
   "targetModifier": {
    "smart": true
   },
   "effects": {
    "spellDamageReduction": {
     "type": "SPELL_DAMAGE_REDUCTION",
     "subtype": "spellSchool.air",
     "duration": "N_TURNS",
     "val": 30
    }
   }
  },
  "advanced": {
   "effects": {
    "spellDamageReduction": {
     "val": 50
    }
   },
   "cost": 5,
   "power": 50,
   "description": "{中级御气奇术}\n\n能对被选中的部队起到保护作用，降低气系魔法的杀伤力。\n\n防御效果接近初级的2倍。"
  },
  "expert": {
   "effects": {
    "spellDamageReduction": {
     "val": 90
    }
   },
   "range": "X",
   "cost": 5,
   "power": 50,
   "description": "{高级御气奇术}\n\n能对所有的部队起到保护作用，降低气系魔法的杀伤力。\n\n防御效果接近初级的2倍。"
  },
  "none": {
   "cost": 7,
   "power": 70,
   "description": "{御气奇术}\n\n能对被选中的部队起到保护作用，降低气系魔法的杀伤力。\n"
  },
  "basic": {
   "cost": 5,
   "power": 70,
   "description": "{初级御气奇术}\n\n能对被选中的部队起到保护作用，降低气系魔法的杀伤力。\n"
  }
 },
 "flags": {
  "positive": true
 },
 "name": "御气奇术",
 "level": 2,
 "power": 0
}
```

</details>

### 御土奇术（`core:protectEarth`）

**最终效果与范围**：四系御魔之一：专家对应学派法术伤害减免提高至90%，群体X；中级及以下以表格为准。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 12 | 70 | `0` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.earth","duration":"N_TURNS","val":30}},"battleEffects":{}} | {御土奇术}<br><br>能对被选中的部队起到保护作用，降低土系魔法的杀伤力。<br> |
| 初级 | 9 | 70 | `0` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.earth","duration":"N_TURNS","val":30}},"battleEffects":{}} | {初级御土奇术}<br><br>能对被选中的部队起到保护作用，降低土系魔法的杀伤力。<br> |
| 中级 | 9 | 50 | `0` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.earth","duration":"N_TURNS","val":50}},"battleEffects":{}} | {中级御土奇术}<br><br>能对被选中的部队起到保护作用，降低土系魔法的杀伤力。<br><br>防御效果接近初级的2倍。 |
| 专家 | 9 | 50 | `X` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.earth","duration":"N_TURNS","val":90}},"battleEffects":{}} | {高级御土奇术}<br><br>能对所有的部队起到保护作用，降低土系魔法的杀伤力。<br><br>防御效果接近初级的2倍。 |

**目标条件**：`{}`。

**描述检查**：未同步：专家实际90%，继承描述仍为50%。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/spells/timed.json](<Hero3Expasion1.83/config/spells/timed.json>)

<details>
<summary>法术改动历史</summary>

- 2026-08-03 `7e77dfde`：`levels.expert.effects.spellDamageReduction.val`：`50` → `90`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "index": 33,
 "targetType": "CREATURE",
 "animation": {
  "affect": [
   "C13SPA0"
  ]
 },
 "sounds": {
  "cast": "PROTECTE"
 },
 "levels": {
  "base": {
   "range": "0",
   "targetModifier": {
    "smart": true
   },
   "effects": {
    "spellDamageReduction": {
     "type": "SPELL_DAMAGE_REDUCTION",
     "subtype": "spellSchool.earth",
     "duration": "N_TURNS",
     "val": 30
    }
   }
  },
  "advanced": {
   "effects": {
    "spellDamageReduction": {
     "val": 50
    }
   },
   "cost": 9,
   "power": 50,
   "description": "{中级御土奇术}\n\n能对被选中的部队起到保护作用，降低土系魔法的杀伤力。\n\n防御效果接近初级的2倍。"
  },
  "expert": {
   "effects": {
    "spellDamageReduction": {
     "val": 90
    }
   },
   "range": "X",
   "cost": 9,
   "power": 50,
   "description": "{高级御土奇术}\n\n能对所有的部队起到保护作用，降低土系魔法的杀伤力。\n\n防御效果接近初级的2倍。"
  },
  "none": {
   "cost": 12,
   "power": 70,
   "description": "{御土奇术}\n\n能对被选中的部队起到保护作用，降低土系魔法的杀伤力。\n"
  },
  "basic": {
   "cost": 9,
   "power": 70,
   "description": "{初级御土奇术}\n\n能对被选中的部队起到保护作用，降低土系魔法的杀伤力。\n"
  }
 },
 "flags": {
  "positive": true
 },
 "name": "御土奇术",
 "level": 3,
 "power": 0
}
```

</details>

### 御火奇术（`core:protectFire`）

**最终效果与范围**：四系御魔之一：专家对应学派法术伤害减免提高至90%，群体X；中级及以下以表格为准。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 5 | 70 | `0` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.fire","duration":"N_TURNS","val":30}},"battleEffects":{}} | {御火奇术}<br><br>能对被选中的部队起到保护作用，降低火系魔法的杀伤力。<br> |
| 初级 | 4 | 70 | `0` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.fire","duration":"N_TURNS","val":30}},"battleEffects":{}} | {初级御火奇术}<br><br>能对被选中的部队起到保护作用，降低火系魔法的杀伤力。<br> |
| 中级 | 4 | 50 | `0` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.fire","duration":"N_TURNS","val":50}},"battleEffects":{}} | {中级御火奇术}<br><br>能对被选中的部队起到保护作用，降低火系魔法的杀伤力。<br><br>防御效果接近初级的2倍。 |
| 专家 | 4 | 50 | `X` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.fire","duration":"N_TURNS","val":90}},"battleEffects":{}} | {高级御火奇术}<br><br>能对所有的部队起到保护作用，降低火系魔法的杀伤力。<br><br>防御效果接近初级的2倍。 |

**目标条件**：`{}`。

**描述检查**：未同步：专家实际90%，继承描述仍为50%。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/spells/timed.json](<Hero3Expasion1.83/config/spells/timed.json>)

<details>
<summary>法术改动历史</summary>

- 2026-08-03 `7e77dfde`：`levels.expert.effects.spellDamageReduction.val`：`50` → `90`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "index": 31,
 "targetType": "CREATURE",
 "animation": {
  "affect": [
   "C11SPW0"
  ]
 },
 "sounds": {
  "cast": "PROTECTF"
 },
 "levels": {
  "base": {
   "range": "0",
   "targetModifier": {
    "smart": true
   },
   "effects": {
    "spellDamageReduction": {
     "type": "SPELL_DAMAGE_REDUCTION",
     "subtype": "spellSchool.fire",
     "duration": "N_TURNS",
     "val": 30
    }
   }
  },
  "advanced": {
   "effects": {
    "spellDamageReduction": {
     "val": 50
    }
   },
   "cost": 4,
   "power": 50,
   "description": "{中级御火奇术}\n\n能对被选中的部队起到保护作用，降低火系魔法的杀伤力。\n\n防御效果接近初级的2倍。"
  },
  "expert": {
   "effects": {
    "spellDamageReduction": {
     "val": 90
    }
   },
   "range": "X",
   "cost": 4,
   "power": 50,
   "description": "{高级御火奇术}\n\n能对所有的部队起到保护作用，降低火系魔法的杀伤力。\n\n防御效果接近初级的2倍。"
  },
  "none": {
   "cost": 5,
   "power": 70,
   "description": "{御火奇术}\n\n能对被选中的部队起到保护作用，降低火系魔法的杀伤力。\n"
  },
  "basic": {
   "cost": 4,
   "power": 70,
   "description": "{初级御火奇术}\n\n能对被选中的部队起到保护作用，降低火系魔法的杀伤力。\n"
  }
 },
 "flags": {
  "positive": true
 },
 "name": "御火奇术",
 "level": 1,
 "power": 0
}
```

</details>

### 御水奇术（`core:protectWater`）

**最终效果与范围**：四系御魔之一：专家对应学派法术伤害减免提高至90%，群体X；中级及以下以表格为准。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 5 | 70 | `0` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.water","duration":"N_TURNS","val":30}},"battleEffects":{}} | {御水奇术}<br><br>能对被选中的部队起到保护作用，降低水系魔法的杀伤力。<br> |
| 初级 | 4 | 70 | `0` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.water","duration":"N_TURNS","val":30}},"battleEffects":{}} | {初级御水奇术}<br><br>能对被选中的部队起到保护作用，降低水系魔法的杀伤力。<br> |
| 中级 | 4 | 50 | `0` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.water","duration":"N_TURNS","val":50}},"battleEffects":{}} | {中级御水奇术}<br><br>能对被选中的部队起到保护作用，降低水系魔法的杀伤力。<br><br>防御效果接近初级的2倍。 |
| 专家 | 4 | 50 | `X` | {"effects":{"spellDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.water","duration":"N_TURNS","val":90}},"battleEffects":{}} | {高级御水奇术}<br><br>能对所有的部队起到保护作用，降低水系魔法的杀伤力。<br><br>防御效果接近初级的2倍。 |

**目标条件**：`{}`。

**描述检查**：未同步：专家实际90%，继承描述仍为50%。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/spells/timed.json](<Hero3Expasion1.83/config/spells/timed.json>)

<details>
<summary>法术改动历史</summary>

- 2026-08-03 `7e77dfde`：`levels.expert.effects.spellDamageReduction.val`：`50` → `90`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "index": 32,
 "targetType": "CREATURE",
 "animation": {
  "affect": [
   "C11SPF0"
  ]
 },
 "sounds": {
  "cast": "PROTECTW"
 },
 "levels": {
  "base": {
   "range": "0",
   "targetModifier": {
    "smart": true
   },
   "effects": {
    "spellDamageReduction": {
     "type": "SPELL_DAMAGE_REDUCTION",
     "subtype": "spellSchool.water",
     "duration": "N_TURNS",
     "val": 30
    }
   }
  },
  "advanced": {
   "effects": {
    "spellDamageReduction": {
     "val": 50
    }
   },
   "cost": 4,
   "power": 50,
   "description": "{中级御水奇术}\n\n能对被选中的部队起到保护作用，降低水系魔法的杀伤力。\n\n防御效果接近初级的2倍。"
  },
  "expert": {
   "effects": {
    "spellDamageReduction": {
     "val": 90
    }
   },
   "range": "X",
   "cost": 4,
   "power": 50,
   "description": "{高级御水奇术}\n\n能对所有的部队起到保护作用，降低水系魔法的杀伤力。\n\n防御效果接近初级的2倍。"
  },
  "none": {
   "cost": 5,
   "power": 70,
   "description": "{御水奇术}\n\n能对被选中的部队起到保护作用，降低水系魔法的杀伤力。\n"
  },
  "basic": {
   "cost": 4,
   "power": 70,
   "description": "{初级御水奇术}\n\n能对被选中的部队起到保护作用，降低水系魔法的杀伤力。\n"
  }
 },
 "flags": {
  "positive": true
 },
 "name": "御水奇术",
 "level": 1,
 "power": 0
}
```

</details>

### 牺牲（`core:sacrifice`）

**最终效果与范围**：当前为0耗魔单体百分比直接伤害版，不再用原版献祭友军复活另一队的双目标逻辑。保留原牺牲动画音效，killByPercentage=true、power=10000、ignoreImmunity=true；flags当前positive=true、negative=false。重要：覆盖文件虽删去NON_LIVING与UNDEAD两键，但普通对象合并不会删除核心原有键，最终仍排除亡灵及非生命。这正是“单文件看着放开，实际仍点不了亡灵”的配置原因；需要#override替换条件或显式删除机制才能真正移除，本次只记录不修配置。smart也不是独立友军硬锁。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 0 | 10000 | `0` | {"effects":{},"battleEffects":{"sacrifice":{"type":"core:damage","healLevel":"resurrect","healPower":"permanent","minFullUnits":0,"ignoreImmunity":true,"killByPercentage":true}}} | {牺牲}<br><br>牺牲一支部队来复活另一支死亡的部队。复活的数量取决于你的魔法能力和被摧毁部队的生命值。 |
| 初级 | 0 | 10000 | `0` | {"effects":{},"battleEffects":{"sacrifice":{"type":"core:damage","healLevel":"resurrect","healPower":"permanent","minFullUnits":0,"ignoreImmunity":true,"killByPercentage":true}}} | {初级牺牲}<br><br>牺牲一支部队来复活另一支死亡的部队。复活的数量取决于你的魔法能力和被摧毁部队的生命值。 |
| 中级 | 0 | 10000 | `0` | {"effects":{},"battleEffects":{"sacrifice":{"type":"core:damage","healLevel":"resurrect","healPower":"permanent","minFullUnits":0,"ignoreImmunity":true,"killByPercentage":true}}} | {中级牺牲}<br><br>牺牲一支部队来复活另一支死亡的部队。复活的数量取决于你的魔法能力和被摧毁部队的生命值。<br><br>比初级复活更多的部队。 |
| 专家 | 0 | 10000 | `0` | {"effects":{},"battleEffects":{"sacrifice":{"type":"core:damage","healLevel":"resurrect","healPower":"permanent","minFullUnits":0,"ignoreImmunity":true,"killByPercentage":true}}} | {高级牺牲}<br><br>牺牲一支部队来复活另一支死亡的部队。复活的数量取决于你的魔法能力和被摧毁部队的生命值。<br><br>比中级复活更多的部队。 |

**目标条件**：`{"noneOf":{"bonus.NON_LIVING":"absolute","bonus.MECHANICAL":"absolute","bonus.SIEGE_WEAPON":"absolute","bonus.UNDEAD":"absolute","bonus.GARGOYLE":"absolute"}}`。

**描述检查**：未同步：仍继承原版牺牲复活说明，实际改为百分比伤害；亡灵限制也仍存在。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/spells/other.json](<Hero3Expasion1.83/config/spells/other.json>)
- [Hero3Expasion1.83/Mods/hota/Mods/artifacts/Content/config/artiSpells/fakeHornSpell.json](<Hero3Expasion1.83/Mods/hota/Mods/artifacts/Content/config/artiSpells/fakeHornSpell.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/sacrificeSelfkill.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/sacrificeSelfkill.json>)

<details>
<summary>法术改动历史</summary>

- 2026-06-15 `2b79bc80`：`animation.affect`：`null` → `[{"defName":"C03SPA0","verticalPosition":"bottom"},"C11SPA1"]`；`flags.damage`：`null` → `true`；`flags.negative`：`null` → `true`；`flags.positive`：`null` → `false`；`flags.rising`：`null` → `false`；`index`：`null` → `40`；`levels.advanced.cost`：`null` → `0`；`levels.advanced.power`：`null` → `10000`；`levels.base.battleEffects.sacrifice.killByPercentage`：`null` → `true`；`levels.base.battleEffects.sacrifice.type`：`null` → `"core:damage"`；`levels.base.cost`：`null` → `0`；`levels.base.power`：`null` → `10000`；`levels.base.range`：`null` → `"0"`；`levels.base.targetModifier.smart`：`null` → `false`；`levels.basic.cost`：`null` → `0`；`levels.basic.power`：`null` → `10000`；`levels.expert.cost`：`null` → `0`；`levels.expert.power`：`null` → `10000`；`levels.none.cost`：`null` → `0`；`levels.none.power`：`null` → `10000`；`sounds.cast`：`null` → `"LIGHTBLT"`；`targetCondition.noneOf.bonus.GARGOYLE`：`null` → `"absolute"`；`targetCondition.noneOf.bonus.MECHANICAL`：`null` → `"absolute"`；`targetCondition.noneOf.bonus.NON_LIVING`：`null` → `"absolute"`；`targetCondition.noneOf.bonus.SIEGE_WEAPON`：`null` → `"absolute"`；`targetCondition.noneOf.bonus.UNDEAD`：`null` → `"absolute"`；`targetType`：`null` → `"CREATURE"`。
- 2026-06-28 `3b04a75d`：`flags.negative`：`true` → `false`；`flags.positive`：`false` → `true`；`levels.base.targetModifier.smart`：`false` → `true`。
- 2026-06-29 `78f6d0bb`：`targetCondition.noneOf.bonus.GARGOYLE`：`"absolute"` → `null`；`targetCondition.noneOf.bonus.MECHANICAL`：`"absolute"` → `null`；`targetCondition.noneOf.bonus.NON_LIVING`：`"absolute"` → `null`；`targetCondition.noneOf.bonus.SIEGE_WEAPON`：`"absolute"` → `null`；`targetCondition.noneOf.bonus.UNDEAD`：`"absolute"` → `null`。
- 2026-07-03 `4f7ffbb6`：`targetCondition.noneOf.bonus.GARGOYLE`：`null` → `"absolute"`；`targetCondition.noneOf.bonus.MECHANICAL`：`null` → `"absolute"`；`targetCondition.noneOf.bonus.NON_LIVING`：`null` → `"absolute"`；`targetCondition.noneOf.bonus.SIEGE_WEAPON`：`null` → `"absolute"`；`targetCondition.noneOf.bonus.UNDEAD`：`null` → `"absolute"`。
- 2026-07-03 `74f7de15`：`targetCondition.noneOf.bonus.NON_LIVING`：`"absolute"` → `null`；`targetCondition.noneOf.bonus.UNDEAD`：`"absolute"` → `null`。
- 2026-07-20 `df8983f9`：`animation.affect`：`[{"defName":"C03SPA0","verticalPosition":"bottom"},"C11SPA1"]` → `[{"defName":"C01SPE0","transparency":0.5}]`；`sounds.cast`：`"LIGHTBLT"` → `"SACRIF1"`。
- 2026-08-02 `b9376775`：`levels.base.battleEffects.sacrifice.ignoreImmunity`：`null` → `true`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "index": 40,
 "targetType": "CREATURE",
 "animation": {
  "affect": [
   {
    "defName": "C01SPE0",
    "transparency": 0.5
   }
  ]
 },
 "sounds": {
  "cast": "SACRIF1"
 },
 "levels": {
  "base": {
   "range": "0",
   "battleEffects": {
    "sacrifice": {
     "type": "core:damage",
     "healLevel": "resurrect",
     "healPower": "permanent",
     "minFullUnits": 0,
     "ignoreImmunity": true,
     "killByPercentage": true
    }
   },
   "targetModifier": {
    "smart": true
   },
   "cost": 0,
   "power": 10000
  },
  "none": {
   "cost": 0,
   "power": 10000,
   "description": "{牺牲}\n\n牺牲一支部队来复活另一支死亡的部队。复活的数量取决于你的魔法能力和被摧毁部队的生命值。"
  },
  "basic": {
   "cost": 0,
   "power": 10000,
   "description": "{初级牺牲}\n\n牺牲一支部队来复活另一支死亡的部队。复活的数量取决于你的魔法能力和被摧毁部队的生命值。"
  },
  "advanced": {
   "cost": 0,
   "power": 10000,
   "description": "{中级牺牲}\n\n牺牲一支部队来复活另一支死亡的部队。复活的数量取决于你的魔法能力和被摧毁部队的生命值。\n\n比初级复活更多的部队。"
  },
  "expert": {
   "cost": 0,
   "power": 10000,
   "description": "{高级牺牲}\n\n牺牲一支部队来复活另一支死亡的部队。复活的数量取决于你的魔法能力和被摧毁部队的生命值。\n\n比中级复活更多的部队。"
  }
 },
 "flags": {
  "rising": false,
  "positive": true,
  "damage": true,
  "negative": false
 },
 "targetCondition": {
  "noneOf": {
   "bonus.NON_LIVING": "absolute",
   "bonus.MECHANICAL": "absolute",
   "bonus.SIEGE_WEAPON": "absolute",
   "bonus.UNDEAD": "absolute",
   "bonus.GARGOYLE": "absolute"
  }
 },
 "graphics": {
  "iconImmune": "hota/artifactsCampaignBonuses/immuneSacriface"
 },
 "name": "牺牲",
 "level": 5,
 "power": 1
}
```

</details>

### 屠戮成性（`core:slayer`）

**最终效果与范围**：屠龙，不是巨龙之力；当前专家范围以快照为准。历史曾用小写x，后改为大写X。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 16 | 8 | `0` | {"effects":{"slayer":{"type":"SLAYER","duration":"N_TURNS","val":0}},"battleEffects":{}} | {屠戮成性}<br><br>被选中的我方部队在进攻比蒙、龙和九头怪时攻击力提高。 |
| 初级 | 12 | 8 | `0` | {"effects":{"slayer":{"type":"SLAYER","duration":"N_TURNS","val":1}},"battleEffects":{}} | {初级屠戮成性}<br><br>被选中的我方部队在进攻比蒙、龙和九头怪时攻击力提高。<br> |
| 中级 | 12 | 8 | `0` | {"effects":{"slayer":{"type":"SLAYER","duration":"N_TURNS","val":2}},"battleEffects":{}} | {中级屠戮成性}<br><br>被选中的我方部队在进攻比蒙、龙、九头怪、恶魔和天使时攻击力提高。<br> |
| 专家 | 12 | 8 | `X` | {"effects":{"slayer":{"type":"SLAYER","duration":"N_TURNS","val":3}},"battleEffects":{}} | {高级屠戮成性}<br><br>被选中的我方部队在进攻比蒙、龙、九头怪、恶魔、天使及泰坦巨人时攻击力提高。<br> |

**目标条件**：`{}`。

**描述检查**：范围已修为X；专家群体范围未在原版说明完整体现。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/spells/timed.json](<Hero3Expasion1.83/config/spells/timed.json>)

<details>
<summary>法术改动历史</summary>

- 2026-06-12 `15186f5b`：`levels.expert.range`：`null` → `"x"`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "index": 55,
 "targetType": "CREATURE",
 "animation": {
  "affect": [
   "C13SPW0"
  ]
 },
 "sounds": {
  "cast": "SLAYER"
 },
 "levels": {
  "base": {
   "range": "0",
   "targetModifier": {
    "smart": true
   },
   "effects": {
    "slayer": {
     "type": "SLAYER",
     "duration": "N_TURNS"
    }
   }
  },
  "none": {
   "effects": {
    "slayer": {
     "val": 0
    }
   },
   "cost": 16,
   "power": 8,
   "description": "{屠戮成性}\n\n被选中的我方部队在进攻比蒙、龙和九头怪时攻击力提高。"
  },
  "basic": {
   "effects": {
    "slayer": {
     "val": 1
    }
   },
   "cost": 12,
   "power": 8,
   "description": "{初级屠戮成性}\n\n被选中的我方部队在进攻比蒙、龙和九头怪时攻击力提高。\n"
  },
  "advanced": {
   "effects": {
    "slayer": {
     "val": 2
    }
   },
   "cost": 12,
   "power": 8,
   "description": "{中级屠戮成性}\n\n被选中的我方部队在进攻比蒙、龙、九头怪、恶魔和天使时攻击力提高。\n"
  },
  "expert": {
   "range": "X",
   "effects": {
    "slayer": {
     "val": 3
    }
   },
   "cost": 12,
   "power": 8,
   "description": "{高级屠戮成性}\n\n被选中的我方部队在进攻比蒙、龙、九头怪、恶魔、天使及泰坦巨人时攻击力提高。\n"
  }
 },
 "flags": {
  "positive": true
 },
 "name": "屠戮成性",
 "level": 4,
 "power": 0
}
```

</details>

### 悲痛欲绝（`core:sorrow`）

**最终效果与范围**：悲痛欲绝：普通／初级士气-2，中级-4，专家-8，专家群体。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 16 | 1 | `0` | {"effects":{"morale":{"type":"MORALE","val":-2,"duration":"N_TURNS"}},"battleEffects":{}} | {悲痛欲绝}<br><br>降低被选中的敌军士气。 |
| 初级 | 12 | 1 | `0` | {"effects":{"morale":{"type":"MORALE","val":-2,"duration":"N_TURNS"}},"battleEffects":{}} | {初级悲痛欲绝}<br><br>降低被选中的敌军的士气。 |
| 中级 | 12 | 2 | `0` | {"effects":{"morale":{"type":"MORALE","val":-4,"duration":"N_TURNS"}},"battleEffects":{}} | {中级悲痛欲绝}<br><br>降低被选中的敌军的士气。<br><br>效果是初级的2倍。 |
| 专家 | 12 | 2 | `X` | {"effects":{"morale":{"type":"MORALE","val":-8,"duration":"N_TURNS"}},"battleEffects":{}} | {高级悲痛欲绝}<br><br>降低所有敌军的士气。<br><br>效果是初级的2倍。 |

**目标条件**：`{"noneOf":{"bonus.MIND_IMMUNITY":"normal","bonus.NON_LIVING":"normal","bonus.MECHANICAL":"normal","bonus.SIEGE_WEAPON":"absolute","bonus.UNDEAD":"absolute"}}`。

**描述检查**：未同步：继承描述与-2／-4／-8的新数值不一致。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/spells/timed.json](<Hero3Expasion1.83/config/spells/timed.json>)
- [Hero3Expasion1.83/Mods/vcmi-extras/Mods/bonusIcons/mods/Immunity Icons/Content/spellImmunityIcons.json](<Hero3Expasion1.83/Mods/vcmi-extras/Mods/bonusIcons/mods/Immunity Icons/Content/spellImmunityIcons.json>)

<details>
<summary>法术改动历史</summary>

- 2026-06-17 `3a4ab7cf`：`levels.advanced.effects.morale.val`：`-2` → `-4`；`levels.base.effects.morale.val`：`-1` → `-2`；`levels.expert.effects.morale.val`：`-2` → `-8`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "index": 50,
 "targetType": "CREATURE",
 "animation": {
  "affect": [
   "C14SPE0"
  ]
 },
 "sounds": {
  "cast": "SORROW"
 },
 "levels": {
  "base": {
   "range": "0",
   "targetModifier": {
    "smart": true
   },
   "effects": {
    "morale": {
     "type": "MORALE",
     "val": -2,
     "duration": "N_TURNS"
    }
   }
  },
  "advanced": {
   "effects": {
    "morale": {
     "val": -4
    }
   },
   "cost": 12,
   "power": 2,
   "description": "{中级悲痛欲绝}\n\n降低被选中的敌军的士气。\n\n效果是初级的2倍。"
  },
  "expert": {
   "range": "X",
   "effects": {
    "morale": {
     "val": -8
    }
   },
   "cost": 12,
   "power": 2,
   "description": "{高级悲痛欲绝}\n\n降低所有敌军的士气。\n\n效果是初级的2倍。"
  },
  "none": {
   "cost": 16,
   "power": 1,
   "description": "{悲痛欲绝}\n\n降低被选中的敌军士气。"
  },
  "basic": {
   "cost": 12,
   "power": 1,
   "description": "{初级悲痛欲绝}\n\n降低被选中的敌军的士气。"
  }
 },
 "counters": {
  "spell.mirth": true
 },
 "flags": {
  "negative": true
 },
 "targetCondition": {
  "noneOf": {
   "bonus.MIND_IMMUNITY": "normal",
   "bonus.NON_LIVING": "normal",
   "bonus.MECHANICAL": "normal",
   "bonus.SIEGE_WEAPON": "absolute",
   "bonus.UNDEAD": "absolute"
  }
 },
 "graphics": {
  "iconImmune": "spellimmunity/imm_sorrow.png"
 },
 "name": "悲痛欲绝",
 "level": 4,
 "power": 0
}
```

</details>

### 透视大气（`core:viewAir`）

**最终效果与范围**：专家透视大气在宝物／英雄／城镇外加入11类属性相关建筑，模组对象筛选移到带依赖的覆盖文件；新引擎用独立属性建筑图标，缺失回退中立宝物。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 2 | 0 | `X` | {"effects":{},"battleEffects":{}} | {透视大气}<br><br>显示所有宝物的方位。<br> |
| 初级 | 1 | 0 | `X` | {"effects":{},"battleEffects":{}} | {初级透视大气}<br><br>显示所有宝物的方位。 |
| 中级 | 1 | 0 | `X` | {"effects":{},"battleEffects":{}} | {中级透视大气}<br><br>显示所有宝物和敌方英雄的方位。 |
| 专家 | 1 | 0 | `X` | {"effects":{},"battleEffects":{}} | {高级透视大气}<br><br>显示所有宝物、敌方英雄和城镇的方位。<br> |

**目标条件**：`{}`。

**描述检查**：已保留各级最终说明与效果对照；本轮未自动证明所有自定义效果说明完全一致，需人工／游戏内复核。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/config/spells/adventure.json](<Hero3Expasion1.83/config/spells/adventure.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/viewAirStatBuildings.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/viewAirStatBuildings.json>)

<details>
<summary>法术改动历史</summary>

- 2026-10-27 `fd572c8c`：`levels.expert.adventureEffect.objects.arena`：`null` → `true`；`levels.expert.adventureEffect.objects.fairy:divinationHut`：`null` → `true`；`levels.expert.adventureEffect.objects.gardenOfRevelation`：`null` → `true`；`levels.expert.adventureEffect.objects.hota.mapobjects:colosseumOfTheMagi`：`null` → `true`；`levels.expert.adventureEffect.objects.libraryOfEnlightenment`：`null` → `true`；`levels.expert.adventureEffect.objects.marlettoTower`：`null` → `true`；`levels.expert.adventureEffect.objects.mercenaryCamp`：`null` → `true`；`levels.expert.adventureEffect.objects.scholar`：`null` → `true`；`levels.expert.adventureEffect.objects.schoolOfMagic`：`null` → `true`；`levels.expert.adventureEffect.objects.schoolOfWar`：`null` → `true`；`levels.expert.adventureEffect.objects.starAxis`：`null` → `true`。
- 2026-11-06 `29bcb8b8`：`levels.expert.adventureEffect.objects.fairy:divinationHut`：`null` → `true`；`levels.expert.adventureEffect.objects.hota.mapobjects:colosseumOfTheMagi`：`null` → `true`。
- 2026-11-06 `29bcb8b8`：`levels.expert.adventureEffect.objects.fairy:divinationHut`：`true` → `null`；`levels.expert.adventureEffect.objects.hota.mapobjects:colosseumOfTheMagi`：`true` → `null`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "index": 5,
 "targetType": "NO_TARGET",
 "sounds": {
  "cast": "VIEW"
 },
 "levels": {
  "base": {
   "range": "X",
   "adventureEffect": {
    "type": "viewWorld",
    "castsPerDay": 0,
    "objects": {
     "artifact": true
    }
   }
  },
  "advanced": {
   "adventureEffect": {
    "objects": {
     "artifact": true,
     "hero": true
    }
   },
   "cost": 1,
   "power": 0,
   "description": "{中级透视大气}\n\n显示所有宝物和敌方英雄的方位。"
  },
  "expert": {
   "adventureEffect": {
    "objects": {
     "artifact": true,
     "hero": true,
     "town": true,
     "mercenaryCamp": true,
     "marlettoTower": true,
     "starAxis": true,
     "gardenOfRevelation": true,
     "arena": true,
     "libraryOfEnlightenment": true,
     "schoolOfWar": true,
     "schoolOfMagic": true,
     "scholar": true,
     "hota.mapobjects:colosseumOfTheMagi": true,
     "fairy:divinationHut": true
    }
   },
   "cost": 1,
   "power": 0,
   "description": "{高级透视大气}\n\n显示所有宝物、敌方英雄和城镇的方位。\n"
  },
  "none": {
   "cost": 2,
   "power": 0,
   "description": "{透视大气}\n\n显示所有宝物的方位。\n"
  },
  "basic": {
   "cost": 1,
   "power": 0,
   "description": "{初级透视大气}\n\n显示所有宝物的方位。"
  }
 },
 "flags": {
  "indifferent": true
 },
 "name": "透视大气",
 "level": 1,
 "power": 0
}
```

</details>

### 禁足（`courtyard:quagmire`）

**最终效果与范围**：禁足：实际持续3／6／12回合，描述仍写1／2／3回合，需要同步；不同熟练度以快照为准。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 13 | 10 | `X` | {"effects":{"noFleeing":{"type":"BATTLE_NO_FLEEING","duration":"N_TURNS","turns":1,"propagator":"BATTLE_WIDE"}},"battleEffects":{}} | {禁足}<br><br>使英雄1回合内无法离开战场（逃跑或投降）。<br><br>若敌方有单位在禁足期间被击杀，魔法将失效。 |
| 初级 | 12 | 10 | `X` | {"effects":{"noFleeing":{"type":"BATTLE_NO_FLEEING","duration":"N_TURNS","turns":3,"propagator":"BATTLE_WIDE"}},"battleEffects":{}} | {初级禁足}<br><br>使英雄1回合内无法离开战场（逃跑或投降）。<br><br>若敌方有单位在禁足期间被击杀，魔法将失效。 |
| 中级 | 10 | 10 | `X` | {"effects":{"noFleeing":{"type":"BATTLE_NO_FLEEING","duration":"N_TURNS","turns":6,"propagator":"BATTLE_WIDE"}},"battleEffects":{}} | {中级禁足}<br><br>使英雄2回合内无法离开战场（逃跑或投降）。<br><br>若敌方有单位在禁足期间被击杀，魔法将失效。 |
| 专家 | 10 | 10 | `X` | {"effects":{"noFleeing":{"type":"BATTLE_NO_FLEEING","duration":"N_TURNS","turns":12,"propagator":"BATTLE_WIDE"}},"battleEffects":{}} | {高级禁足}<br><br>使英雄3回合内无法离开战场（逃跑或投降）。<br><br>若敌方有单位在禁足期间被击杀，魔法将失效。 |

**目标条件**：`{}`。

**描述检查**：未同步：效果持续3／6／12回合，描述1／2／3回合。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/quagmire.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/quagmire.json>)

<details>
<summary>法术改动历史</summary>

- 2026-06-19 `353602c5`：`levels.advanced.effects.noFleeing.turns`：`2` → `6`；`levels.basic.effects.noFleeing.turns`：`1` → `3`；`levels.expert.effects.noFleeing.turns`：`3` → `12`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "type": "combat",
 "name": "禁足",
 "school": {
  "air": false,
  "earth": true,
  "fire": false,
  "water": false
 },
 "targetType": "NO_TARGET",
 "level": 3,
 "power": 10,
 "defaultGainChance": 7,
 "gainChance": {
  "rampart": 6,
  "fortress": 20,
  "necropolis": 20,
  "stronghold": 15,
  "tower": 3,
  "dungeon": 15,
  "castle": 0,
  "conflux": 15,
  "courtyard": 15
 },
 "animation": {
  "hit": [
   "courtyard/spells/Quagmire/Quagmire.def"
  ]
 },
 "flags": {
  "positive": false,
  "negative": false,
  "indifferent": true
 },
 "immunity": {},
 "sounds": {
  "cast": "courtyard/spells/SPELLQUAGMIRE.wav"
 },
 "graphics": {
  "iconBook": "courtyard/spells/Quagmire/Quagmire_book1",
  "iconEffect": "courtyard/spells/Quagmire/Quagmire_effect1",
  "iconScroll": "courtyard/spells/Quagmire/Quagmire_scroll1",
  "iconScenarioBonus": "courtyard/spells/Quagmire/Quagmire_bonus1"
 },
 "levels": {
  "base": {
   "power": 10,
   "range": "X",
   "targetModifier": {
    "smart": false
   },
   "cost": 13,
   "effects": {
    "noFleeing": {
     "type": "BATTLE_NO_FLEEING",
     "duration": "N_TURNS",
     "turns": 1,
     "propagator": "BATTLE_WIDE"
    }
   }
  },
  "none": {
   "description": "{禁足}\n\n使英雄1回合内无法离开战场（逃跑或投降）。\n\n若敌方有单位在禁足期间被击杀，魔法将失效。",
   "effects": {
    "noFleeing": {
     "type": "BATTLE_NO_FLEEING",
     "duration": "N_TURNS",
     "turns": 1,
     "propagator": "BATTLE_WIDE"
    }
   }
  },
  "basic": {
   "description": "{初级禁足}\n\n使英雄1回合内无法离开战场（逃跑或投降）。\n\n若敌方有单位在禁足期间被击杀，魔法将失效。",
   "cost": 12,
   "effects": {
    "noFleeing": {
     "type": "BATTLE_NO_FLEEING",
     "duration": "N_TURNS",
     "turns": 3,
     "propagator": "BATTLE_WIDE"
    }
   }
  },
  "advanced": {
   "description": "{中级禁足}\n\n使英雄2回合内无法离开战场（逃跑或投降）。\n\n若敌方有单位在禁足期间被击杀，魔法将失效。",
   "cost": 10,
   "effects": {
    "noFleeing": {
     "type": "BATTLE_NO_FLEEING",
     "duration": "N_TURNS",
     "turns": 6,
     "propagator": "BATTLE_WIDE"
    }
   }
  },
  "expert": {
   "description": "{高级禁足}\n\n使英雄3回合内无法离开战场（逃跑或投降）。\n\n若敌方有单位在禁足期间被击杀，魔法将失效。",
   "cost": 10,
   "effects": {
    "noFleeing": {
     "type": "BATTLE_NO_FLEEING",
     "duration": "N_TURNS",
     "turns": 12,
     "propagator": "BATTLE_WIDE"
    }
   }
  }
 }
}
```

</details>

### 驱役白骨弓箭手（`extra creatures specialty:summonBoneHook`）

**最终效果与范围**：已改动字段和实际分级效果见下表及历史；不从中文名称推断隐藏规则。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 1 | 40 | `0` | {"effects":{},"battleEffects":{"demonSummon":{"id":"deathvalley:boneHook","permanent":true,"type":"core:demonSummon"}}} | {驱役白骨弓箭手}<br><br>从一队死去的我方单位上召唤白骨弓箭手，可以包括死灵单位。 |
| 初级 | 1 | 40 | `0` | {"effects":{},"battleEffects":{"demonSummon":{"id":"deathvalley:boneHook","permanent":true,"type":"core:demonSummon"}}} | {驱役白骨弓箭手}<br><br>从一队死去的我方单位上召唤白骨弓箭手，可以包括死灵单位。 |
| 中级 | 1 | 40 | `0` | {"effects":{},"battleEffects":{"demonSummon":{"id":"deathvalley:boneHook","permanent":true,"type":"core:demonSummon"}}} | {驱役白骨弓箭手}<br><br>从一队死去的我方单位上召唤白骨弓箭手，可以包括死灵单位。 |
| 专家 | 1 | 40 | `0` | {"effects":{},"battleEffects":{"demonSummon":{"id":"deathvalley:boneHook","permanent":true,"type":"core:demonSummon"}}} | {驱役白骨弓箭手}<br><br>从一队死去的我方单位上召唤白骨弓箭手，可以包括死灵单位。 |

**目标条件**：`{"noneOf":{"bonus.NON_LIVING":"absolute","bonus.MECHANICAL":"absolute","bonus.SIEGE_WEAPON":"absolute","bonus.GARGOYLE":"absolute"}}`。

**描述检查**：需复核：描述允许死灵，但仍排除NON_LIVING；对同时带此标记的死灵目标不可用。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/summonBoneHook.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/summonBoneHook.json>)

<details>
<summary>法术改动历史</summary>

- 2026-06-20 `d20e3775`：`animation.affect`：`null` → `[{"defName":"C01SPE0","transparency":0.5}]`；`defaultGainChance`：`null` → `0`；`flags.positive`：`null` → `true`；`flags.rising`：`null` → `true`；`graphics.iconEffect`：`null` → `"asylum/NewOldSpells/spp.png"`；`level`：`null` → `2`；`levels.base.battleEffects.demonSummon.id`：`null` → `"deathvalley:boneHook"`；`levels.base.battleEffects.demonSummon.permanent`：`null` → `true`；`levels.base.battleEffects.demonSummon.type`：`null` → `"core:demonSummon"`；`levels.base.cost`：`null` → `1`；`levels.base.description`：`null` → `"{驱役白骨弓箭手}\r\n\r\n从一队死去的我方单位上召唤白骨弓箭手，可以包括死灵单位。"`；`levels.base.power`：`null` → `40`；`levels.base.range`：`null` → `"0"`；`levels.base.targetModifier.smart`：`null` → `true`；`name`：`null` → `"驱役白骨弓箭手"`；`power`：`null` → `50`；`sounds.cast`：`null` → `"ANIMDEAD"`；`targetCondition.noneOf.bonus.GARGOYLE`：`null` → `"absolute"`；`targetCondition.noneOf.bonus.MECHANICAL`：`null` → `"absolute"`；`targetCondition.noneOf.bonus.NON_LIVING`：`null` → `"absolute"`；`targetCondition.noneOf.bonus.SIEGE_WEAPON`：`null` → `"absolute"`；`targetType`：`null` → `"CREATURE"`；`type`：`null` → `"ability"`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "type": "ability",
 "targetType": "CREATURE",
 "name": "驱役白骨弓箭手",
 "school": {},
 "level": 2,
 "power": 50,
 "defaultGainChance": 0,
 "gainChance": {},
 "animation": {
  "affect": [
   {
    "defName": "C01SPE0",
    "transparency": 0.5
   }
  ]
 },
 "graphics": {
  "iconEffect": "asylum/NewOldSpells/spp.png"
 },
 "sounds": {
  "cast": "ANIMDEAD"
 },
 "levels": {
  "base": {
   "description": "{驱役白骨弓箭手}\r\n\r\n从一队死去的我方单位上召唤白骨弓箭手，可以包括死灵单位。",
   "power": 40,
   "cost": 1,
   "targetModifier": {
    "smart": true
   },
   "range": "0",
   "battleEffects": {
    "demonSummon": {
     "id": "deathvalley:boneHook",
     "permanent": true,
     "type": "core:demonSummon"
    }
   }
  },
  "none": {},
  "basic": {},
  "advanced": {},
  "expert": {}
 },
 "flags": {
  "rising": true,
  "positive": true
 },
 "targetCondition": {
  "noneOf": {
   "bonus.NON_LIVING": "absolute",
   "bonus.MECHANICAL": "absolute",
   "bonus.SIEGE_WEAPON": "absolute",
   "bonus.GARGOYLE": "absolute"
  }
 }
}
```

</details>

### 隐形（`fairy:heroBansheeInvisibility`）

**最终效果与范围**：英雄可用隐形：持续3回合或主动攻击结束，限制战争机器；配置无敌、物理及法术伤害减免100%、不受反击。需实战核验效果与可选目标，艾瑞莎不加入此专属魔法。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 8 | 1 | `0` | {"effects":{"invincible":{"type":"INVINCIBLE","duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"damageReduction":{"type":"GENERAL_DAMAGE_REDUCTION","subtype":"damageTypeMelee","val":100,"duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"damageReductionShots":{"type":"GENERAL_DAMAGE_REDUCTION","subtype":"damageTypeRanged","val":100,"duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"magicDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.any","val":100,"duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"noRetaliation":{"type":"NO_RETALIATION","duration":["UNTIL_ATTACK","N_TURNS"],"turns":3}},"battleEffects":{}} | {隐形}<br><br>使目标部队隐形3回合，不受近战、远程和法术伤害，攻击不受反击。主动攻击后效果消失。 |
| 初级 | 8 | 1 | `0` | {"effects":{"invincible":{"type":"INVINCIBLE","duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"damageReduction":{"type":"GENERAL_DAMAGE_REDUCTION","subtype":"damageTypeMelee","val":100,"duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"damageReductionShots":{"type":"GENERAL_DAMAGE_REDUCTION","subtype":"damageTypeRanged","val":100,"duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"magicDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.any","val":100,"duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"noRetaliation":{"type":"NO_RETALIATION","duration":["UNTIL_ATTACK","N_TURNS"],"turns":3}},"battleEffects":{}} | {初级隐形}<br><br>使目标部队隐形3回合，不受近战、远程和法术伤害，攻击不受反击。主动攻击后效果消失。 |
| 中级 | 8 | 1 | `0` | {"effects":{"invincible":{"type":"INVINCIBLE","duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"damageReduction":{"type":"GENERAL_DAMAGE_REDUCTION","subtype":"damageTypeMelee","val":100,"duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"damageReductionShots":{"type":"GENERAL_DAMAGE_REDUCTION","subtype":"damageTypeRanged","val":100,"duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"magicDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.any","val":100,"duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"noRetaliation":{"type":"NO_RETALIATION","duration":["UNTIL_ATTACK","N_TURNS"],"turns":3}},"battleEffects":{}} | {中级隐形}<br><br>使目标部队隐形3回合，不受近战、远程和法术伤害，攻击不受反击。主动攻击后效果消失。 |
| 专家 | 8 | 1 | `0` | {"effects":{"invincible":{"type":"INVINCIBLE","duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"damageReduction":{"type":"GENERAL_DAMAGE_REDUCTION","subtype":"damageTypeMelee","val":100,"duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"damageReductionShots":{"type":"GENERAL_DAMAGE_REDUCTION","subtype":"damageTypeRanged","val":100,"duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"magicDamageReduction":{"type":"SPELL_DAMAGE_REDUCTION","subtype":"spellSchool.any","val":100,"duration":["UNTIL_ATTACK","N_TURNS"],"turns":3},"noRetaliation":{"type":"NO_RETALIATION","duration":["UNTIL_ATTACK","N_TURNS"],"turns":3}},"battleEffects":{}} | {高级隐形}<br><br>使目标部队隐形3回合，不受近战、远程和法术伤害，攻击不受反击。主动攻击后效果消失。 |

**目标条件**：`{"noneOf":{"bonus.SIEGE_WEAPON":"absolute"}}`。

**描述检查**：已保留各级最终说明与效果对照；本轮未自动证明所有自定义效果说明完全一致，需人工／游戏内复核。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/spells/heroBansheeInvisibility.json](<Hero3Expasion1.83/Mods/Fairy/content/config/fairy/spells/heroBansheeInvisibility.json>)

<details>
<summary>法术改动历史</summary>

- 2026-08-02 `f51e1724`：`animation.affect`：`null` → `["deathvalley/spells/skill4.def"]`；`defaultGainChance`：`null` → `0`；`flags.indifferent`：`null` → `true`；`flags.nonMagical`：`null` → `true`；`flags.positive`：`null` → `true`；`graphics.iconBook`：`null` → `"deathvalley/spells/invisible.png"`；`graphics.iconEffect`：`null` → `"deathvalley/spells/skill 4-2.bmp"`；`graphics.iconScenarioBonus`：`null` → `"deathvalley/spells/invisible.png"`；`graphics.iconScroll`：`null` → `"deathvalley/spells/invisible.png"`；`level`：`null` → `1`；`levels.advanced.description`：`null` → `"{中级隐形}\n\n使目标部队隐形3回合，不受近战、远程和法术伤害，攻击不受反击。主动攻击后效果消失。"`；`levels.base.cost`：`null` → `8`；`levels.base.effects.damageReduction.duration`：`null` → `["UNTIL_ATTACK","N_TURNS"]`；`levels.base.effects.damageReduction.subtype`：`null` → `"damageTypeMelee"`；`levels.base.effects.damageReduction.turns`：`null` → `3`；`levels.base.effects.damageReduction.type`：`null` → `"GENERAL_DAMAGE_REDUCTION"`；`levels.base.effects.damageReduction.val`：`null` → `100`；`levels.base.effects.damageReductionShots.duration`：`null` → `["UNTIL_ATTACK","N_TURNS"]`；`levels.base.effects.damageReductionShots.subtype`：`null` → `"damageTypeRanged"`；`levels.base.effects.damageReductionShots.turns`：`null` → `3`；`levels.base.effects.damageReductionShots.type`：`null` → `"GENERAL_DAMAGE_REDUCTION"`；`levels.base.effects.damageReductionShots.val`：`null` → `100`；`levels.base.effects.invincible.duration`：`null` → `["UNTIL_ATTACK","N_TURNS"]`；`levels.base.effects.invincible.turns`：`null` → `3`；`levels.base.effects.invincible.type`：`null` → `"INVINCIBLE"`；`levels.base.effects.magicDamageReduction.duration`：`null` → `["UNTIL_ATTACK","N_TURNS"]`；`levels.base.effects.magicDamageReduction.subtype`：`null` → `"spellSchool.any"`；`levels.base.effects.magicDamageReduction.turns`：`null` → `3`；`levels.base.effects.magicDamageReduction.type`：`null` → `"SPELL_DAMAGE_REDUCTION"`；`levels.base.effects.magicDamageReduction.val`：`null` → `100`；`levels.base.effects.noRetaliation.duration`：`null` → `["UNTIL_ATTACK","N_TURNS"]`；`levels.base.effects.noRetaliation.turns`：`null` → `3`；`levels.base.effects.noRetaliation.type`：`null` → `"NO_RETALIATION"`；`levels.base.power`：`null` → `1`；`levels.base.range`：`null` → `"0"`；`levels.base.targetModifier.smart`：`null` → `true`；`levels.basic.description`：`null` → `"{初级隐形}\n\n使目标部队隐形3回合，不受近战、远程和法术伤害，攻击不受反击。主动攻击后效果消失。"`；`levels.expert.description`：`null` → `"{高级隐形}\n\n使目标部队隐形3回合，不受近战、远程和法术伤害，攻击不受反击。主动攻击后效果消失。"`；`levels.none.description`：`null` → `"{隐形}\n\n使目标部队隐形3回合，不受近战、远程和法术伤害，攻击不受反击。主动攻击后效果消失。"`；`name`：`null` → `"隐形"`；`power`：`null` → `1`；`school.air`：`null` → `false`；`school.earth`：`null` → `false`；`school.fire`：`null` → `false`；`school.water`：`null` → `false`；`sounds.cast`：`null` → `"SACBRETH"`；`targetCondition.noneOf.bonus.SIEGE_WEAPON`：`null` → `"absolute"`；`targetType`：`null` → `"CREATURE"`；`type`：`null` → `"combat"`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "type": "combat",
 "targetType": "CREATURE",
 "name": "隐形",
 "school": {
  "air": false,
  "earth": false,
  "fire": false,
  "water": false
 },
 "level": 1,
 "power": 1,
 "defaultGainChance": 0,
 "gainChance": {},
 "animation": {
  "affect": [
   "deathvalley/spells/skill4.def"
  ]
 },
 "targetCondition": {
  "noneOf": {
   "bonus.SIEGE_WEAPON": "absolute"
  }
 },
 "flags": {
  "positive": true,
  "nonMagical": true,
  "indifferent": true
 },
 "graphics": {
  "iconBook": "deathvalley/spells/invisible.png",
  "iconEffect": "deathvalley/spells/skill 4-2.bmp",
  "iconScroll": "deathvalley/spells/invisible.png",
  "iconScenarioBonus": "deathvalley/spells/invisible.png"
 },
 "sounds": {
  "cast": "SACBRETH"
 },
 "levels": {
  "base": {
   "cost": 8,
   "range": "0",
   "power": 1,
   "targetModifier": {
    "smart": true
   },
   "effects": {
    "invincible": {
     "type": "INVINCIBLE",
     "duration": [
      "UNTIL_ATTACK",
      "N_TURNS"
     ],
     "turns": 3
    },
    "damageReduction": {
     "type": "GENERAL_DAMAGE_REDUCTION",
     "subtype": "damageTypeMelee",
     "val": 100,
     "duration": [
      "UNTIL_ATTACK",
      "N_TURNS"
     ],
     "turns": 3
    },
    "damageReductionShots": {
     "type": "GENERAL_DAMAGE_REDUCTION",
     "subtype": "damageTypeRanged",
     "val": 100,
     "duration": [
      "UNTIL_ATTACK",
      "N_TURNS"
     ],
     "turns": 3
    },
    "magicDamageReduction": {
     "type": "SPELL_DAMAGE_REDUCTION",
     "subtype": "spellSchool.any",
     "val": 100,
     "duration": [
      "UNTIL_ATTACK",
      "N_TURNS"
     ],
     "turns": 3
    },
    "noRetaliation": {
     "type": "NO_RETALIATION",
     "duration": [
      "UNTIL_ATTACK",
      "N_TURNS"
     ],
     "turns": 3
    }
   }
  },
  "none": {
   "description": "{隐形}\n\n使目标部队隐形3回合，不受近战、远程和法术伤害，攻击不受反击。主动攻击后效果消失。"
  },
  "basic": {
   "description": "{初级隐形}\n\n使目标部队隐形3回合，不受近战、远程和法术伤害，攻击不受反击。主动攻击后效果消失。"
  },
  "advanced": {
   "description": "{中级隐形}\n\n使目标部队隐形3回合，不受近战、远程和法术伤害，攻击不受反击。主动攻击后效果消失。"
  },
  "expert": {
   "description": "{高级隐形}\n\n使目标部队隐形3回合，不受近战、远程和法术伤害，攻击不受反击。主动攻击后效果消失。"
  }
 }
}
```

</details>

### 召唤夺心魔（`hota.neutralcreatures:hornSpell`）

**最终效果与范围**：已改动字段和实际分级效果见下表及历史；不从中文名称推断隐藏规则。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 0 | 400000 | `0` | {"effects":{},"battleEffects":{"demonSummon":{"id":"hota.neutralcreatures:fangarm","permanent":true,"type":"core:demonSummon"}}} | {召唤夺心魔}<br><br>从尸体上召唤一队夺心魔，战后不会消失。对亡灵和无生命的生物无效。 |
| 初级 | 0 | 400000 | `0` | {"effects":{},"battleEffects":{"demonSummon":{"id":"hota.neutralcreatures:fangarm","permanent":true,"type":"core:demonSummon"}}} | {召唤夺心魔}<br><br>从尸体上召唤一队夺心魔，战后不会消失。对亡灵和无生命的生物无效。 |
| 中级 | 0 | 400000 | `0` | {"effects":{},"battleEffects":{"demonSummon":{"id":"hota.neutralcreatures:fangarm","permanent":true,"type":"core:demonSummon"}}} | {召唤夺心魔}<br><br>从尸体上召唤一队夺心魔，战后不会消失。对亡灵和无生命的生物无效。 |
| 专家 | 0 | 400000 | `0` | {"effects":{},"battleEffects":{"demonSummon":{"id":"hota.neutralcreatures:fangarm","permanent":true,"type":"core:demonSummon"}}} | {召唤夺心魔}<br><br>从尸体上召唤一队夺心魔，战后不会消失。对亡灵和无生命的生物无效。 |

**目标条件**：`{"noneOf":{"bonus.NON_LIVING":"absolute","bonus.SIEGE_WEAPON":"absolute","bonus.UNDEAD":"absolute","bonus.GARGOYLE":"absolute","bonus.MECHANICAL":"absolute"}}`。

**描述检查**：已保留各级最终说明与效果对照；本轮未自动证明所有自定义效果说明完全一致，需人工／游戏内复核。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/hota/Mods/neutralCreatures/content/config/artiSpells/HornSpell.json](<Hero3Expasion1.83/Mods/hota/Mods/neutralCreatures/content/config/artiSpells/HornSpell.json>)

<details>
<summary>法术改动历史</summary>

- 2026-06-22 `797ce8ad`：`levels.base.power`：`40` → `400000`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "type": "combat",
 "targetType": "CREATURE",
 "name": "召唤夺心魔",
 "school": {
  "air": false,
  "earth": false,
  "fire": false,
  "water": true
 },
 "level": 5,
 "power": 30,
 "defaultGainChance": 0,
 "gainChance": {},
 "animation": {
  "affect": [
   "C13SPF0"
  ]
 },
 "sounds": {
  "cast": "ANIMDEAD"
 },
 "flags": {
  "rising": true,
  "special": true
 },
 "graphics": {
  "iconBook": "artifactsCampaignBonuses/fakehornEffect.png",
  "iconScroll": "artifactsCampaignBonuses/fakehornScroll.png",
  "iconScenarioBonus": "artifactsCampaignBonuses/fakehornScene.png",
  "iconImmune": "artifactsCampaignBonuses/fakehornImmune.png",
  "iconEffect": "artifactsCampaignBonuses/fakehornEffect.png"
 },
 "targetCondition": {
  "noneOf": {
   "bonus.NON_LIVING": "absolute",
   "bonus.SIEGE_WEAPON": "absolute",
   "bonus.UNDEAD": "absolute",
   "bonus.GARGOYLE": "absolute",
   "bonus.MECHANICAL": "absolute"
  }
 },
 "levels": {
  "base": {
   "description": "{召唤夺心魔}\r\n\r\n从尸体上召唤一队夺心魔，战后不会消失。对亡灵和无生命的生物无效。",
   "power": 400000,
   "cost": 0,
   "targetModifier": {
    "smart": false
   },
   "range": "0",
   "battleEffects": {
    "demonSummon": {
     "id": "hota.neutralcreatures:fangarm",
     "permanent": true,
     "type": "core:demonSummon"
    }
   }
  },
  "none": {},
  "basic": {},
  "advanced": {},
  "expert": {}
 }
}
```

</details>

### 活力重生（`newoldspells+:regeneration`）

**最终效果与范围**：活力重生：新增施放当下core:heal，healLevel=resurrect、healPower=permanent，只有本次施法即时治疗可以复活。后续每回合仍用HP_REGENERATION，普通／初级25、中级50、专家75，专家群体；只是治疗存活个体，不会每回合反复复活。现有描述没写即时永久复活这层效果。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 15 | 10 | `0` | {"effects":{"redeem2":{"type":"HP_REGENERATION","val":25,"valueType":"BASE_NUMBER","duration":"N_TURNS"}},"battleEffects":{"healNow":{"type":"core:heal","healLevel":"resurrect","healPower":"permanent"}}} | {活力重生}<br><br>使一个生物获得自我复原能力。<br><br>每回合回复25点生命值。<br> |
| 初级 | 15 | 10 | `0` | {"effects":{"redeem2":{"type":"HP_REGENERATION","val":25,"valueType":"BASE_NUMBER","duration":"N_TURNS"}},"battleEffects":{"healNow":{"type":"core:heal","healLevel":"resurrect","healPower":"permanent"}}} | {初级活力重生}<br><br>使一个生物获得自我复原能力。<br><br>每回合回复25点生命值。<br> |
| 中级 | 12 | 10 | `0` | {"effects":{"redeem2":{"type":"HP_REGENERATION","val":50,"valueType":"BASE_NUMBER","duration":"N_TURNS"}},"battleEffects":{"healNow":{"type":"core:heal","healLevel":"resurrect","healPower":"permanent"}}} | {中级活力重生}<br><br>使一个生物获得自我复原能力。<br><br>每回合回复50点生命值。<br> |
| 专家 | 12 | 10 | `X` | {"effects":{"redeem2":{"type":"HP_REGENERATION","val":75,"valueType":"BASE_NUMBER","duration":"N_TURNS"}},"battleEffects":{"healNow":{"type":"core:heal","healLevel":"resurrect","healPower":"permanent"}}} | {高级活力重生}<br><br>使所有己方生物获得自我复原能力。<br><br>每回合回复75点生命值。<br> |

**目标条件**：`{"allOf":{},"anyOf":{},"noneOf":{"core:bonus.NON_LIVING":"absolute","core:bonus.SIEGE_WEAPON":"absolute","bonus.MECHANICAL":"absolute","core:bonus.HP_REGENERATION":"absolute","core:bonus.UNDEAD":"absolute"}}`。

**描述检查**：不完整：25／50／75再生已描述，漏写施法当下的永久复活治疗，不能误写为每回合复活。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/NewOldSpells+/Content/config/NewOldSpells/Regeneration.json](<Hero3Expasion1.83/Mods/NewOldSpells+/Content/config/NewOldSpells/Regeneration.json>)

<details>
<summary>法术改动历史</summary>

- 2026-07-15 `562fb433`：`levels.base.battleEffects.healNow.healLevel`：`null` → `"resurrect"`；`levels.base.battleEffects.healNow.healPower`：`null` → `"permanent"`；`levels.base.battleEffects.healNow.type`：`null` → `"core:heal"`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "type": "combat",
 "name": "活力重生",
 "school": {
  "air": false,
  "earth": false,
  "fire": false,
  "water": true
 },
 "level": 3,
 "power": 10,
 "defaultGainChance": 10,
 "gainChance": {
  "necropolis": 0,
  "castle": 20,
  "inferno": 10,
  "conflux": 20
 },
 "animation": {
  "affect": [
   {
    "defName": "Sp12_",
    "transparency": 0.5
   }
  ]
 },
 "flags": {
  "positive": true
 },
 "targetCondition": {
  "allOf": {},
  "anyOf": {},
  "noneOf": {
   "core:bonus.NON_LIVING": "absolute",
   "core:bonus.SIEGE_WEAPON": "absolute",
   "bonus.MECHANICAL": "absolute",
   "core:bonus.HP_REGENERATION": "absolute",
   "core:bonus.UNDEAD": "absolute"
  }
 },
 "graphics": {
  "iconBook": "NewOldSpells/regenerationBook.bmp",
  "iconScroll": "NewOldSpells/regenerationScroll.bmp",
  "iconScenarioBonus": "NewOldSpells/regenerationScenario.bmp",
  "iconEffect": "NewOldSpells/regenerationEffect.bmp"
 },
 "sounds": {
  "cast": "REGENER"
 },
 "targetType": "CREATURE",
 "levels": {
  "base": {
   "range": "0",
   "power": 10,
   "battleEffects": {
    "healNow": {
     "type": "core:heal",
     "healLevel": "resurrect",
     "healPower": "permanent"
    }
   },
   "targetModifier": {
    "smart": true
   }
  },
  "none": {
   "description": "{活力重生}\n\n使一个生物获得自我复原能力。\n\n每回合回复25点生命值。\n",
   "cost": 15,
   "effects": {
    "redeem2": {
     "type": "HP_REGENERATION",
     "val": 25,
     "valueType": "BASE_NUMBER",
     "duration": "N_TURNS"
    }
   }
  },
  "basic": {
   "description": "{初级活力重生}\n\n使一个生物获得自我复原能力。\n\n每回合回复25点生命值。\n",
   "cost": 15,
   "effects": {
    "redeem2": {
     "type": "HP_REGENERATION",
     "val": 25,
     "valueType": "BASE_NUMBER",
     "duration": "N_TURNS"
    }
   }
  },
  "advanced": {
   "description": "{中级活力重生}\n\n使一个生物获得自我复原能力。\n\n每回合回复50点生命值。\n",
   "cost": 12,
   "effects": {
    "redeem2": {
     "type": "HP_REGENERATION",
     "val": 50,
     "valueType": "BASE_NUMBER",
     "duration": "N_TURNS"
    }
   }
  },
  "expert": {
   "description": "{高级活力重生}\n\n使所有己方生物获得自我复原能力。\n\n每回合回复75点生命值。\n",
   "cost": 12,
   "range": "X",
   "effects": {
    "redeem2": {
     "type": "HP_REGENERATION",
     "val": 75,
     "valueType": "BASE_NUMBER",
     "duration": "N_TURNS"
    }
   }
  }
 }
}
```

</details>

### 吟唱（`preserve:choir`）

**最终效果与范围**：吟唱：己方全体永久士气+1、幸运+1，友方英雄施法固定减耗5，不重复叠加。所有熟练度统一core:timed，描述已同步。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 0 | 10 | `X` | {"effects":{},"battleEffects":{"choirBuff":{"type":"core:timed","cumulative":false,"bonus":{"morale":{"type":"MORALE","val":1,"duration":"PERMANENT"},"luck":{"type":"LUCK","val":1,"duration":"PERMANENT"},"reduceSpellCost":{"type":"CHANGES_SPELL_COST_FOR_ALLY","val":5,"duration":"PERMANENT"}}}}} | {吟唱}<br><br>提升全员1点士气和运气，并使己方英雄施法消耗减少5点。 |
| 初级 | 0 | 10 | `X` | {"effects":{},"battleEffects":{"choirBuff":{"type":"core:timed","cumulative":false,"bonus":{"morale":{"type":"MORALE","val":1,"duration":"PERMANENT"},"luck":{"type":"LUCK","val":1,"duration":"PERMANENT"},"reduceSpellCost":{"type":"CHANGES_SPELL_COST_FOR_ALLY","val":5,"duration":"PERMANENT"}}}}} | {初级吟唱}<br><br>提升全员1点士气和运气，并使己方英雄施法消耗减少5点。 |
| 中级 | 0 | 10 | `X` | {"effects":{},"battleEffects":{"choirBuff":{"type":"core:timed","cumulative":false,"bonus":{"morale":{"type":"MORALE","val":1,"duration":"PERMANENT"},"luck":{"type":"LUCK","val":1,"duration":"PERMANENT"},"reduceSpellCost":{"type":"CHANGES_SPELL_COST_FOR_ALLY","val":5,"duration":"PERMANENT"}}}}} | {中级吟唱}<br><br>提升全员1点士气和运气，并使己方英雄施法消耗减少5点。 |
| 专家 | 0 | 10 | `X` | {"effects":{},"battleEffects":{"choirBuff":{"type":"core:timed","cumulative":false,"bonus":{"morale":{"type":"MORALE","val":1,"duration":"PERMANENT"},"luck":{"type":"LUCK","val":1,"duration":"PERMANENT"},"reduceSpellCost":{"type":"CHANGES_SPELL_COST_FOR_ALLY","val":5,"duration":"PERMANENT"}}}}} | {高级吟唱}<br><br>提升全员1点士气和运气，并使己方英雄施法消耗减少5点。 |

**目标条件**：`{"noneOf":{"bonus.MIND_IMMUNITY":"normal","bonus.NON_LIVING":"normal","bonus.MECHANICAL":"normal","bonus.SIEGE_WEAPON":"absolute","bonus.UNDEAD":"absolute"}}`。

**描述检查**：各熟练度已同步为士气+1、幸运+1、减耗5。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/preserve/content/config/preserve/NewOldSpells/choir.json](<Hero3Expasion1.83/Mods/preserve/content/config/preserve/NewOldSpells/choir.json>)

<details>
<summary>法术改动历史</summary>

- 2026-09-20 `5f3feea3`：`levels.advanced.battleEffects.choirBuff.bonus.luck.duration`：`null` → `"PERMANENT"`；`levels.advanced.battleEffects.choirBuff.bonus.luck.type`：`null` → `"LUCK"`；`levels.advanced.battleEffects.choirBuff.bonus.luck.val`：`null` → `1`；`levels.advanced.battleEffects.choirBuff.bonus.morale.duration`：`null` → `"PERMANENT"`；`levels.advanced.battleEffects.choirBuff.bonus.morale.type`：`null` → `"MORALE"`；`levels.advanced.battleEffects.choirBuff.bonus.morale.val`：`null` → `1`；`levels.advanced.battleEffects.choirBuff.bonus.reduceSpellCost.duration`：`null` → `"PERMANENT"`；`levels.advanced.battleEffects.choirBuff.bonus.reduceSpellCost.type`：`null` → `"CHANGES_SPELL_COST_FOR_ALLY"`；`levels.advanced.battleEffects.choirBuff.bonus.reduceSpellCost.val`：`null` → `5`；`levels.advanced.battleEffects.choirBuff.cumulative`：`null` → `false`；`levels.advanced.battleEffects.choirBuff.type`：`null` → `"core:timed"`；`levels.advanced.description`：`"{中级吟唱}\r\n\r\n提升全员2点士气和运气。"` → `"{中级吟唱}\r\n\r\n提升全员1点士气和运气，并使己方英雄施法消耗减少5点。"`；`levels.advanced.effects.luck.duration`：`"PERMANENT"` → `null`；`levels.advanced.effects.luck.type`：`"LUCK"` → `null`；`levels.advanced.effects.luck.val`：`1` → `null`；`levels.advanced.effects.morale.duration`：`"PERMANENT"` → `null`；`levels.advanced.effects.morale.type`：`"MORALE"` → `null`；`levels.advanced.effects.morale.val`：`1` → `null`；`levels.basic.battleEffects.choirBuff.bonus.luck.duration`：`null` → `"PERMANENT"`；`levels.basic.battleEffects.choirBuff.bonus.luck.type`：`null` → `"LUCK"`；`levels.basic.battleEffects.choirBuff.bonus.luck.val`：`null` → `1`；`levels.basic.battleEffects.choirBuff.bonus.morale.duration`：`null` → `"PERMANENT"`；`levels.basic.battleEffects.choirBuff.bonus.morale.type`：`null` → `"MORALE"`；`levels.basic.battleEffects.choirBuff.bonus.morale.val`：`null` → `1`；`levels.basic.battleEffects.choirBuff.bonus.reduceSpellCost.duration`：`null` → `"PERMANENT"`；`levels.basic.battleEffects.choirBuff.bonus.reduceSpellCost.type`：`null` → `"CHANGES_SPELL_COST_FOR_ALLY"`；`levels.basic.battleEffects.choirBuff.bonus.reduceSpellCost.val`：`null` → `5`；`levels.basic.battleEffects.choirBuff.cumulative`：`null` → `false`；`levels.basic.battleEffects.choirBuff.type`：`null` → `"core:timed"`；`levels.basic.description`：`"{初级吟唱}\r\n\r\n提升全员1点士气和运气。"` → `"{初级吟唱}\r\n\r\n提升全员1点士气和运气，并使己方英雄施法消耗减少5点。"`；`levels.basic.effects.luck.duration`：`"PERMANENT"` → `null`；`levels.basic.effects.luck.type`：`"LUCK"` → `null`；`levels.basic.effects.luck.val`：`1` → `null`；`levels.basic.effects.morale.duration`：`"PERMANENT"` → `null`；`levels.basic.effects.morale.type`：`"MORALE"` → `null`；`levels.basic.effects.morale.val`：`1` → `null`；`levels.expert.battleEffects.choirBuff.bonus.luck.duration`：`null` → `"PERMANENT"`；`levels.expert.battleEffects.choirBuff.bonus.luck.type`：`null` → `"LUCK"`；`levels.expert.battleEffects.choirBuff.bonus.luck.val`：`null` → `1`；`levels.expert.battleEffects.choirBuff.bonus.morale.duration`：`null` → `"PERMANENT"`；`levels.expert.battleEffects.choirBuff.bonus.morale.type`：`null` → `"MORALE"`；`levels.expert.battleEffects.choirBuff.bonus.morale.val`：`null` → `1`；`levels.expert.battleEffects.choirBuff.bonus.reduceSpellCost.duration`：`null` → `"PERMANENT"`；`levels.expert.battleEffects.choirBuff.bonus.reduceSpellCost.type`：`null` → `"CHANGES_SPELL_COST_FOR_ALLY"`；`levels.expert.battleEffects.choirBuff.bonus.reduceSpellCost.val`：`null` → `5`；`levels.expert.battleEffects.choirBuff.cumulative`：`null` → `false`；`levels.expert.battleEffects.choirBuff.type`：`null` → `"core:timed"`；`levels.expert.description`：`"{高级吟唱}\r\n\r\n提升全员3点士气和运气。"` → `"{高级吟唱}\r\n\r\n提升全员1点士气和运气，并使己方英雄施法消耗减少5点。"`；`levels.expert.effects.luck.duration`：`"PERMANENT"` → `null`；`levels.expert.effects.luck.type`：`"LUCK"` → `null`；`levels.expert.effects.luck.val`：`1` → `null`；`levels.expert.effects.morale.duration`：`"PERMANENT"` → `null`；`levels.expert.effects.morale.type`：`"MORALE"` → `null`；`levels.expert.effects.morale.val`：`1` → `null`；`levels.none.battleEffects.choirBuff.bonus.luck.duration`：`null` → `"PERMANENT"`；`levels.none.battleEffects.choirBuff.bonus.luck.type`：`null` → `"LUCK"`；`levels.none.battleEffects.choirBuff.bonus.luck.val`：`null` → `1`；`levels.none.battleEffects.choirBuff.bonus.morale.duration`：`null` → `"PERMANENT"`；`levels.none.battleEffects.choirBuff.bonus.morale.type`：`null` → `"MORALE"`；`levels.none.battleEffects.choirBuff.bonus.morale.val`：`null` → `1`；`levels.none.battleEffects.choirBuff.bonus.reduceSpellCost.duration`：`null` → `"PERMANENT"`；`levels.none.battleEffects.choirBuff.bonus.reduceSpellCost.type`：`null` → `"CHANGES_SPELL_COST_FOR_ALLY"`；`levels.none.battleEffects.choirBuff.bonus.reduceSpellCost.val`：`null` → `5`；`levels.none.battleEffects.choirBuff.cumulative`：`null` → `false`；`levels.none.battleEffects.choirBuff.type`：`null` → `"core:timed"`；`levels.none.description`：`"{吟唱}\r\n\r\n提升全员1点士气和运气。"` → `"{吟唱}\r\n\r\n提升全员1点士气和运气，并使己方英雄施法消耗减少5点。"`；`levels.none.effects.luck.duration`：`"PERMANENT"` → `null`；`levels.none.effects.luck.type`：`"LUCK"` → `null`；`levels.none.effects.luck.val`：`1` → `null`；`levels.none.effects.morale.duration`：`"PERMANENT"` → `null`；`levels.none.effects.morale.type`：`"MORALE"` → `null`；`levels.none.effects.morale.val`：`1` → `null`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "type": "combat",
 "name": "吟唱",
 "school": {
  "air": false,
  "earth": false,
  "fire": false,
  "water": true
 },
 "level": 5,
 "power": 10,
 "defaultGainChance": 0,
 "gainChance": {
  "castle": 0,
  "rampart": 0,
  "conflux": 0
 },
 "animation": {
  "affect": [
   "NewOldSpells/choir.def"
  ]
 },
 "flags": {
  "special": true,
  "positive": true
 },
 "graphics": {
  "iconBook": "NewOldSpells/choir1.bmp",
  "iconScroll": "NewOldSpells/choir2.bmp",
  "iconScenarioBonus": "NewOldSpells/choir4.bmp",
  "iconEffect": "NewOldSpells/IN5.bmp"
 },
 "sounds": {
  "cast": "BLESS"
 },
 "targetType": "CREATURE",
 "targetCondition": {
  "noneOf": {
   "bonus.MIND_IMMUNITY": "normal",
   "bonus.NON_LIVING": "normal",
   "bonus.MECHANICAL": "normal",
   "bonus.SIEGE_WEAPON": "absolute",
   "bonus.UNDEAD": "absolute"
  }
 },
 "levels": {
  "base": {
   "power": 10,
   "range": "X",
   "targetModifier": {
    "smart": true
   }
  },
  "none": {
   "description": "{吟唱}\r\n\r\n提升全员1点士气和运气，并使己方英雄施法消耗减少5点。",
   "cost": 0,
   "battleEffects": {
    "choirBuff": {
     "type": "core:timed",
     "cumulative": false,
     "bonus": {
      "morale": {
       "type": "MORALE",
       "val": 1,
       "duration": "PERMANENT"
      },
      "luck": {
       "type": "LUCK",
       "val": 1,
       "duration": "PERMANENT"
      },
      "reduceSpellCost": {
       "type": "CHANGES_SPELL_COST_FOR_ALLY",
       "val": 5,
       "duration": "PERMANENT"
      }
     }
    }
   }
  },
  "basic": {
   "description": "{初级吟唱}\r\n\r\n提升全员1点士气和运气，并使己方英雄施法消耗减少5点。",
   "cost": 0,
   "battleEffects": {
    "choirBuff": {
     "type": "core:timed",
     "cumulative": false,
     "bonus": {
      "morale": {
       "type": "MORALE",
       "val": 1,
       "duration": "PERMANENT"
      },
      "luck": {
       "type": "LUCK",
       "val": 1,
       "duration": "PERMANENT"
      },
      "reduceSpellCost": {
       "type": "CHANGES_SPELL_COST_FOR_ALLY",
       "val": 5,
       "duration": "PERMANENT"
      }
     }
    }
   }
  },
  "advanced": {
   "description": "{中级吟唱}\r\n\r\n提升全员1点士气和运气，并使己方英雄施法消耗减少5点。",
   "cost": 0,
   "battleEffects": {
    "choirBuff": {
     "type": "core:timed",
     "cumulative": false,
     "bonus": {
      "morale": {
       "type": "MORALE",
       "val": 1,
       "duration": "PERMANENT"
      },
      "luck": {
       "type": "LUCK",
       "val": 1,
       "duration": "PERMANENT"
      },
      "reduceSpellCost": {
       "type": "CHANGES_SPELL_COST_FOR_ALLY",
       "val": 5,
       "duration": "PERMANENT"
      }
     }
    }
   }
  },
  "expert": {
   "description": "{高级吟唱}\r\n\r\n提升全员1点士气和运气，并使己方英雄施法消耗减少5点。",
   "cost": 0,
   "battleEffects": {
    "choirBuff": {
     "type": "core:timed",
     "cumulative": false,
     "bonus": {
      "morale": {
       "type": "MORALE",
       "val": 1,
       "duration": "PERMANENT"
      },
      "luck": {
       "type": "LUCK",
       "val": 1,
       "duration": "PERMANENT"
      },
      "reduceSpellCost": {
       "type": "CHANGES_SPELL_COST_FOR_ALLY",
       "val": 5,
       "duration": "PERMANENT"
      }
     }
    }
   }
  }
 }
}
```

</details>

### 召唤恶灵（`tidesofwar:summonGhost`）

**最终效果与范围**：5级土系召唤恶灵，召唤tidesofwar:mdtGhost，permanent=true、exclusive=false；普通／初级／中级／专家威力1／2／3／4。

| 熟练度 | 消耗 | power | 范围 | 效果 | 当前描述 |
|---|---|---|---|---|---|
| 普通 | 25 | 1 | `X` | {"effects":{},"battleEffects":{"summon":{"exclusive":false,"id":"tidesofwar:mdtGhost","permanent":true,"type":"core:summon"}}} | {召唤恶灵}<br><br>召唤恶灵参战。 |
| 初级 | 20 | 2 | `X` | {"effects":{},"battleEffects":{"summon":{"exclusive":false,"id":"tidesofwar:mdtGhost","permanent":true,"type":"core:summon"}}} | {召唤恶灵}<br><br>召唤恶灵参战。 |
| 中级 | 20 | 3 | `X` | {"effects":{},"battleEffects":{"summon":{"exclusive":false,"id":"tidesofwar:mdtGhost","permanent":true,"type":"core:summon"}}} | {召唤恶灵}<br><br>召唤恶灵参战。 |
| 专家 | 20 | 4 | `X` | {"effects":{},"battleEffects":{"summon":{"exclusive":false,"id":"tidesofwar:mdtGhost","permanent":true,"type":"core:summon"}}} | {召唤恶灵}<br><br>召唤恶灵参战。 |

**目标条件**：`{}`。

**描述检查**：已保留各级最终说明与效果对照；本轮未自动证明所有自定义效果说明完全一致，需人工／游戏内复核。

**文件与覆盖顺序**：

- [Hero3Expasion1.83/Mods/tidesOfWar/content/config/spells/summonGhost/summonGhost.json](<Hero3Expasion1.83/Mods/tidesOfWar/content/config/spells/summonGhost/summonGhost.json>)

<details>
<summary>法术改动历史</summary>

- 2026-06-05 `d43a11e7`：`levels.base.battleEffects.summon.permanent`：`false` → `true`。

</details>


<details>
<summary>当前完整法术定义</summary>

```json
{
 "type": "combat",
 "name": "召唤恶灵",
 "school": {
  "air": false,
  "earth": true,
  "fire": false,
  "water": false
 },
 "animation": {
  "cast": [
   "spells/summonGhost/SP_summonGhost"
  ]
 },
 "sounds": {},
 "level": 5,
 "power": 10,
 "defaultGainChance": 2,
 "gainChance": {
  "necropolis": 12,
  "dungeon": 6,
  "inferno": 6
 },
 "flags": {
  "indifferent": true
 },
 "graphics": {
  "iconBook": "spells/summonGhost/ghostIconBook",
  "iconScroll": "spells/summonGhost/ghostIconScroll",
  "iconScenarioBonus": "spells/summonGhost/ghostIconScenario",
  "iconEffect": "spells/summonGhost/ghostIconEffect"
 },
 "targetType": "NO_TARGET",
 "levels": {
  "base": {
   "description": "{召唤恶灵}\r\n\r\n召唤恶灵参战。",
   "power": 2,
   "cost": 20,
   "range": "X",
   "targetModifier": {
    "smart": true
   },
   "battleEffects": {
    "summon": {
     "exclusive": false,
     "id": "tidesofwar:mdtGhost",
     "permanent": true,
     "type": "core:summon"
    }
   }
  },
  "none": {
   "cost": 25,
   "power": 1
  },
  "basic": {
   "power": 2
  },
  "advanced": {
   "power": 3
  },
  "expert": {
   "power": 4
  }
 }
}
```

</details>

## 全局参数

### 英雄与成长

最终主覆盖：`Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json`；核心默认位于`config/gameConfig.json`，不能只改核心就认定最终生效。

| 项目 | 当前值 | 中文解释 |
|---|---|---|
| specialtyCreatureGrowth | 40 | 通用兵种特长攻防成长参数；不是每升一级所有兵种固定+40% |
| specialtySecondarySkillGrowth | 10 | 通用辅助技能特长每英雄等级相对增强10%，显式例外保留 |
| skillPerHero | 10 | 全局副技能格上限，不是20级才开放两格 |
| levelupTotalSkillsAmount | 16 | 升级候选技能总量参数 |
| levelupUpgradedSkillsAmount | 4 | 已学技能升级候选数量参数 |
| baseScoutingRange | 7 | 英雄基础视野，另加侦察／特长／宝物 |
| tavernInvite | true | 开启酒馆邀请相关规则，地图限制仍在 |
| buildingsPerTurnCap | 5 | 每回合建造上限，城镇／事件条件仍适用 |
| abilityBias | 0 | 能力触发历史概率平滑关闭，改回独立随机 |

### 全局成长的作用范围

- 通用兵种特长成长参数为40%；每N级提高对应兵种及升级形态攻防的效果由全局规则决定，N与基础兵种等级等规则有关，不分别计作英雄独立改动。
- 通用辅助技能特长成长参数为每英雄等级相对增强10%；不把所有受影响英雄逐人重复列出。
- 速度+1属于保留的通用兵种特长效果，不是本轮新增全局速度。金尔与博伊德显式stepSize=10为默认成长的例外，不能用全局40%覆盖。
- 配置入口：Hero3Expasion1.83/config/gameConfig.json、Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json。纯文字同步记录不收录本文件。

### 士气与幸运

`56be0335`（2026-11-11）把当前HotA平衡覆盖改为12档，实际合并后有效范围-12至+12。

- 正士气／正幸运表：`[1,2,3,4,5,6,7,8,9,10,11,24]`。
- 负士气／负幸运表：`[2,4,6,8,10,12,14,16,18,20,22,24]`。
- 两种diceSize均24，bias均8；第12档表值24，基础判定24/24。偏置不为0，连续触发过程不能一概称为独立随机；abilityBias=0针对另一类能力。
- 这些是全局规则，不是琳恩／蕾娜塔专属。±8法术先与其他属性来源合并，再受上下限影响。

### 法术研究与难度

法术研究开启；每级每日99次、重复研究费用倍率均0。1—3级每次1000金币，4—5级每次1000金币加水银／硫磺／水晶／宝石各1。原“每日10次”属于历史旧值。

人类king难度初始资源曾改为六类资源各90、金币999990；不应推广为所有难度／所有玩家。完整当前难度表附后。

<details>
<summary>最终全局heroes参数快照</summary>

```json
{
 "perPlayerOnMapCap": 8,
 "perPlayerTotalCap": 9999,
 "retreatOnWinWithoutTroops": true,
 "startingStackChances": [
  100,
  100,
  100
 ],
 "backpackSize": -1,
 "tavernInvite": true,
 "minimalPrimarySkills": [
  0,
  0,
  1,
  1
 ],
 "movementCostBase": 100,
 "movementPointsLand": [
  1500,
  1500,
  1500,
  1500,
  1560,
  1630,
  1700,
  1760,
  1830,
  1900,
  1960,
  2000
 ],
 "movementPointsSea": [
  1500
 ],
 "skillPerHero": 10,
 "baseScoutingRange": 7,
 "specialtySecondarySkillGrowth": 10,
 "specialtyCreatureGrowth": 40,
 "levelupTotalSkillsAmount": 16,
 "levelupUpgradedSkillsAmount": 4
}
```

</details>


<details>
<summary>最终全局combat参数快照</summary>

```json
{
 "abilityBias": 0,
 "goodMoraleChance": [
  1,
  2,
  3,
  4,
  5,
  6,
  7,
  8,
  9,
  10,
  11,
  24
 ],
 "badMoraleChance": [
  2,
  4,
  6,
  8,
  10,
  12,
  14,
  16,
  18,
  20,
  22,
  24
 ],
 "moraleDiceSize": 24,
 "moraleBias": 8,
 "goodLuckChance": [
  1,
  2,
  3,
  4,
  5,
  6,
  7,
  8,
  9,
  10,
  11,
  24
 ],
 "badLuckChance": [
  2,
  4,
  6,
  8,
  10,
  12,
  14,
  16,
  18,
  20,
  22,
  24
 ],
 "luckDiceSize": 24,
 "luckBias": 8,
 "attackPointDamageFactor": 0.05,
 "attackPointDamageFactorCap": 4.0,
 "defensePointDamageFactor": 0.025,
 "defensePointDamageFactorCap": 0.7,
 "oneHexTriggersObstacles": false,
 "areaShotCanTargetEmptyHex": true,
 "noSpellHitAndRunRounds": 0,
 "layouts": {
  "default": {
   "tacticsAllowed": true,
   "obstaclesAllowed": true,
   "attackerCommander": 88,
   "defenderCommander": 98,
   "attackerWarMachines": [
    52,
    18,
    154,
    120
   ],
   "defenderWarMachines": [
    66,
    32,
    168,
    134
   ],
   "attackerUnitsLoose": [
    [
     86
    ],
    [
     35,
     137
    ],
    [
     35,
     86,
     137
    ],
    [
     1,
     69,
     103,
     171
    ],
    [
     1,
     35,
     86,
     137,
     171
    ],
    [
     1,
     35,
     69,
     103,
     137,
     171
    ],
    [
     1,
     35,
     69,
     86,
     103,
     137,
     171
    ]
   ],
   "defenderUnitsLoose": [
    [
     100
    ],
    [
     49,
     151
    ],
    [
     49,
     100,
     151
    ],
    [
     15,
     83,
     117,
     185
    ],
    [
     15,
     49,
     100,
     151,
     185
    ],
    [
     15,
     49,
     83,
     117,
     151,
     185
    ],
    [
     15,
     49,
     83,
     100,
     117,
     151,
     185
    ]
   ],
   "attackerUnitsTight": [
    [
     86
    ],
    [
     69,
     103
    ],
    [
     69,
     86,
     103
    ],
    [
     35,
     69,
     103,
     137
    ],
    [
     35,
     69,
     86,
     103,
     137
    ],
    [
     1,
     35,
     69,
     103,
     137,
     171
    ],
    [
     1,
     35,
     69,
     86,
     103,
     137,
     171
    ]
   ],
   "defenderUnitsTight": [
    [
     100
    ],
    [
     83,
     117
    ],
    [
     83,
     100,
     117
    ],
    [
     49,
     83,
     117,
     151
    ],
    [
     49,
     83,
     100,
     117,
     151
    ],
    [
     15,
     49,
     83,
     117,
     151,
     185
    ],
    [
     15,
     49,
     83,
     100,
     117,
     151,
     185
    ]
   ]
  },
  "creatureBankNarrow": {
   "tacticsAllowed": false,
   "obstaclesAllowed": false,
   "attackerCommander": 94,
   "defenderCommander": 8,
   "attackerUnits": [
    57,
    61,
    90,
    93,
    96,
    125,
    129
   ],
   "defenderUnits": [
    15,
    185,
    172,
    2,
    100,
    87,
    8
   ]
  },
  "creatureBankWide": {
   "tacticsAllowed": false,
   "obstaclesAllowed": false,
   "attackerCommander": 94,
   "defenderCommander": 8,
   "attackerUnits": [
    57,
    61,
    90,
    93,
    96,
    125,
    129
   ],
   "defenderUnits": [
    15,
    185,
    171,
    1,
    100,
    86,
    8
   ]
  }
 }
}
```

</details>


<details>
<summary>最终全局towns参数快照</summary>

```json
{
 "buildingsPerTurnCap": 5,
 "startingDwellingChances": [
  100,
  100
 ],
 "spellResearch": true,
 "spellResearchCost": [
  {
   "gold": 1000,
   "mercury": 0,
   "sulfur": 0,
   "crystal": 0,
   "gems": 0
  },
  {
   "gold": 1000,
   "mercury": 0,
   "sulfur": 0,
   "crystal": 0,
   "gems": 0
  },
  {
   "gold": 1000,
   "mercury": 0,
   "sulfur": 0,
   "crystal": 0,
   "gems": 0
  },
  {
   "gold": 1000,
   "mercury": 1,
   "sulfur": 1,
   "crystal": 1,
   "gems": 1
  },
  {
   "gold": 1000,
   "mercury": 1,
   "sulfur": 1,
   "crystal": 1,
   "gems": 1
  }
 ],
 "spellResearchPerDay": [
  99,
  99,
  99,
  99,
  99
 ],
 "spellResearchCostMultiplierPerResearch": [
  1,
  1,
  1,
  1,
  1
 ],
 "spellResearchCostMultiplierPerReroll": [
  0,
  0,
  0,
  0,
  0
 ],
 "baseScoutingRange": 5
}
```

</details>


<details>
<summary>最终全局dwellings参数快照</summary>

```json
{
 "accumulateWhenNeutral": false,
 "accumulateWhenOwned": true,
 "mergeOnRecruit": true
}
```

</details>


<details>
<summary>最终全局pathfinder参数快照</summary>

```json
{
 "ignoreGuards": false,
 "useBoat": true,
 "useMonolithTwoWay": true,
 "useMonolithOneWayUnique": true,
 "useMonolithOneWayRandom": true,
 "useWhirlpool": true,
 "originalFlyRules": false
}
```

</details>


### 难度与法术研究的完整当前配置

文件：[Hero3Expasion1.83/config/difficulty.json](<Hero3Expasion1.83/config/difficulty.json>)


<details>
<summary>config/difficulty.json</summary>

```json
{
 "human": {
  "pawn": {
   "resources": {
    "wood": 30,
    "mercury": 15,
    "ore": 30,
    "sulfur": 15,
    "crystal": 15,
    "gems": 15,
    "gold": 30000
   },
   "globalBonuses": [],
   "battleBonuses": []
  },
  "knight": {
   "resources": {
    "wood": 20,
    "mercury": 10,
    "ore": 20,
    "sulfur": 10,
    "crystal": 10,
    "gems": 10,
    "gold": 20000
   },
   "globalBonuses": [],
   "battleBonuses": []
  },
  "rook": {
   "resources": {
    "wood": 15,
    "mercury": 7,
    "ore": 15,
    "sulfur": 7,
    "crystal": 7,
    "gems": 7,
    "gold": 15000
   },
   "globalBonuses": [],
   "battleBonuses": []
  },
  "queen": {
   "resources": {
    "wood": 10,
    "mercury": 4,
    "ore": 10,
    "sulfur": 4,
    "crystal": 4,
    "gems": 4,
    "gold": 10000
   },
   "globalBonuses": [],
   "battleBonuses": []
  },
  "king": {
   "resources": {
    "wood": 90,
    "mercury": 90,
    "ore": 90,
    "sulfur": 90,
    "crystal": 90,
    "gems": 90,
    "gold": 999990
   },
   "globalBonuses": [],
   "battleBonuses": []
  }
 },
 "ai": {
  "pawn": {
   "resources": {
    "wood": 5,
    "mercury": 2,
    "ore": 5,
    "sulfur": 2,
    "crystal": 2,
    "gems": 2,
    "gold": 5000
   },
   "globalBonuses": [],
   "battleBonuses": []
  },
  "knight": {
   "resources": {
    "wood": 10,
    "mercury": 4,
    "ore": 10,
    "sulfur": 4,
    "crystal": 4,
    "gems": 4,
    "gold": 7500
   },
   "globalBonuses": [],
   "battleBonuses": []
  },
  "rook": {
   "resources": {
    "wood": 15,
    "mercury": 7,
    "ore": 15,
    "sulfur": 7,
    "crystal": 7,
    "gems": 7,
    "gold": 10000
   },
   "globalBonuses": [],
   "battleBonuses": []
  },
  "queen": {
   "resources": {
    "wood": 15,
    "mercury": 7,
    "ore": 15,
    "sulfur": 7,
    "crystal": 7,
    "gems": 7,
    "gold": 10000
   },
   "globalBonuses": [],
   "battleBonuses": []
  },
  "king": {
   "resources": {
    "wood": 15,
    "mercury": 7,
    "ore": 15,
    "sulfur": 7,
    "crystal": 7,
    "gems": 7,
    "gold": 10000
   },
   "globalBonuses": [],
   "battleBonuses": []
  }
 }
}
```

</details>

文件：[Hero3Expasion1.83/Mods/hota/Mods/spellResearch/mod.json](<Hero3Expasion1.83/Mods/hota/Mods/spellResearch/mod.json>)


<details>
<summary>Mods/hota/Mods/spellResearch/mod.json</summary>

```json
{
 "name": "Spell Research",
 "description": "This submod enables basic Spell Research system to mimic similar feature in HotA.",
 "modType": "Mechanics",
 "author": "avatar",
 "contact": "https://forum.vcmi.eu/",
 "version": "1.0",
 "compatibility": {
  "min": "1.6"
 },
 "softDepends": [
  "vcmi"
 ],
 "settings": {
  "towns": {
   "spellResearch": true,
   "spellResearchCostMultiplierPerReroll": [
    0,
    0,
    0,
    0,
    0
   ],
   "spellResearchPerDay": [
    99,
    99,
    99,
    99,
    99
   ],
   "spellResearchCost": [
    {
     "gold": 1000,
     "mercury": 0,
     "sulfur": 0,
     "crystal": 0,
     "gems": 0
    },
    {
     "gold": 1000,
     "mercury": 0,
     "sulfur": 0,
     "crystal": 0,
     "gems": 0
    },
    {
     "gold": 1000,
     "mercury": 0,
     "sulfur": 0,
     "crystal": 0,
     "gems": 0
    },
    {
     "gold": 1000,
     "mercury": 1,
     "sulfur": 1,
     "crystal": 1,
     "gems": 1
    },
    {
     "gold": 1000,
     "mercury": 1,
     "sulfur": 1,
     "crystal": 1,
     "gems": 1
    }
   ]
  }
 },
 "chinese": {
  "name": "法术研究",
  "description": "这个子mod启用基本的法术研究，以模仿HotA中的类似功能。"
 },
 "czech": {
  "name": "Výzkum kouzel",
  "description": "Tato modifikace aktivuje základní systém výzkumu kouzel ve věži kouzel"
 },
 "polish": {
  "name": "Badanie zaklęć",
  "description": "Podmoduł włącza podstawowy system badań zaklęć na wzór rozwiązania w HotA"
 },
 "portuguese": {
  "name": "Pesquisa de Feitiços",
  "description": "Este submod ativa o sistema básico de Pesquisa de Feitiços para imitar uma característica semelhante no HotA."
 }
}
```

</details>

## 生物与宝物

尸体转生物能力的高额度不是凭空增加尸体总生命。最终数量同时受施法者额度、尸体可利用生命、目标生物生命与取整限制；不能按尸体个数1:1换算。

### 阴影兽（`asylum:shadowmare`）

**作用范围**：兵种本体／宝物本体配置，区别于仅某位英雄所带部队。历史及完整快照如下。

- [Hero3Expasion1.83/Mods/asylum/content/config/asylum/creatures/shadowmare.json](<Hero3Expasion1.83/Mods/asylum/content/config/asylum/creatures/shadowmare.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/creatures/creatureAbilities.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/creatures/creatureAbilities.json>)

<details>
<summary>逐次改动</summary>

- 2026-06-15 `2b79bc80`：`abilities.summons40HP.val`：`40` → `4000000`。

</details>


<details>
<summary>当前完整定义与描述</summary>

```json
{
 "name": {
  "plural": "阴影兽",
  "singular": "阴影兽"
 },
 "advMapAmount": {
  "max": 10,
  "min": 5
 },
 "faction": "asylum",
 "aiValue": 2755,
 "attack": 18,
 "cost": {
  "gold": 1300
 },
 "damage": {
  "max": 24,
  "min": 18
 },
 "defense": 19,
 "fightValue": 2345,
 "graphics": {
  "animation": "asylum/battle/creatures/CPST6U.def",
  "map": "asylum/map/creatures/AVWC188.def",
  "iconSmall": "asylum/iconsSmall/creatures/CPRS188Y.bmp",
  "iconLarge": "asylum/iconsBig/creatures/CPRL188Y.bmp",
  "timeBetweenFidgets": 1,
  "animationTime": {
   "walk": 1.25,
   "attack": 1,
   "idle": 10
  }
 },
 "doubleWide": true,
 "growth": 2,
 "hitPoints": 95,
 "level": 6,
 "sound": {
  "attack": "asylum/CPU6ATTK.wav",
  "defend": "asylum/CPU6DFND.wav",
  "killed": "asylum/CPU6KILL.wav",
  "move": "asylum/CPU6MOVE.wav",
  "wince": "asylum/CPU6WNCE.wav"
 },
 "speed": 9,
 "abilities": {
  "noMorale": {
   "type": "NO_MORALE"
  },
  "noMind": {
   "type": "MIND_IMMUNITY"
  },
  "petrify": {
   "type": "SPELL_AFTER_ATTACK",
   "subtype": "spell.terrorEye",
   "val": 30,
   "icon": "NewOldSpells/terroreye.png"
  },
  "summons40HP": {
   "type": "SPECIFIC_SPELL_POWER",
   "subtype": "spell.summonApps",
   "val": 4000000
  },
  "resurrects": {
   "type": "SPELLCASTER",
   "subtype": "spell.summonApps",
   "val": 0,
   "icon": "NewOldSpells/summon.png"
  },
  "castsAmount": {
   "type": "CASTS",
   "val": 1
  }
 },
 "stackExperience": [
  {
   "bonus": {
    "subtype": "primarySkill.attack",
    "type": "PRIMARY_SKILL"
   },
   "values": [
    1,
    2,
    3,
    4,
    4,
    5,
    6,
    7,
    8,
    9
   ]
  },
  {
   "bonus": {
    "subtype": "primarySkill.defence",
    "type": "PRIMARY_SKILL"
   },
   "values": [
    1,
    2,
    2,
    3,
    4,
    5,
    6,
    7,
    8,
    9
   ]
  },
  {
   "bonus": {
    "subtype": "creatureDamageBoth",
    "type": "CREATURE_DAMAGE"
   },
   "values": [
    1,
    2,
    3,
    4,
    5,
    6,
    7,
    8,
    8,
    9
   ]
  },
  {
   "bonus": {
    "type": "STACKS_SPEED"
   },
   "values": [
    0,
    0,
    0,
    1,
    1,
    1,
    1,
    1,
    1,
    2
   ]
  },
  {
   "bonus": {
    "type": "DEATH_STARE"
   },
   "values": [
    12,
    14,
    16,
    18,
    20,
    22,
    24,
    26,
    28,
    30
   ]
  },
  {
   "bonus": {
    "type": "ADDITIONAL_RETALIATION"
   },
   "values": [
    0,
    0,
    0,
    0,
    1,
    1,
    1,
    1,
    1,
    2
   ]
  },
  {
   "bonus": {
    "type": "SPELL_BEFORE_ATTACK",
    "subtype": "spell.deathRipple",
    "addInfo": [
     3,
     2
    ]
   },
   "values": [
    5,
    10,
    15,
    20,
    25,
    30,
    35,
    40,
    45,
    50
   ]
  }
 ],
 "description": "梦魇兽出生于扭曲的梦境，据说它们从噩梦中出现，本身就代表着噩梦。阴影兽可以用魔力引燃灵魂，将它们变成灵魂之火。\r\n\r\n梦魇兽（Nightmare）来源于一系列噩梦的传说。mare来源于欧洲的民间传说，代指“恶魔”，它会坐在熟睡者的胸口，带来可怕的梦境。因为mare和表示雌性马的词汇相似，所以有的作品将梦境中的恶魔具象为马的样子。也因此出现了马形态的梦魇兽。\r\n\r\n梦魇兽并没有标准的形象，更像是人们对于噩梦的幻想，日耳曼和斯拉夫民间传说中的母马被认为会在人们睡觉时骑在他们的胸口，从而引发噩梦。但有的传说中，梦魇就是纯粹的恶魔形象。\r\n\r\n每只阴影兽召唤幽影的生命值为40，可以在己方死去的生命实体生物和不死生物身上召唤。"
}
```

</details>

### pitLord（`core:pitLord`）

**作用范围**：兵种本体／宝物本体配置，区别于仅某位英雄所带部队。历史及完整快照如下。

- [Hero3Expasion1.83/config/creatures/inferno.json](<Hero3Expasion1.83/config/creatures/inferno.json>)
- [Hero3Expasion1.83/Mods/hota/Mods/newGraphics/Content/config/hota/newGraphics/inferno/newCreatureAnimations.json](<Hero3Expasion1.83/Mods/hota/Mods/newGraphics/Content/config/hota/newGraphics/inferno/newCreatureAnimations.json>)
- [Hero3Expasion1.83/Mods/tidesOfWar/content/config/creatures/castle/castleCreatures.json](<Hero3Expasion1.83/Mods/tidesOfWar/content/config/creatures/castle/castleCreatures.json>)
- [Hero3Expasion1.83/Mods/vcmi-extras/Mods/bonusIcons/mods/Bonus Icons/Content/config/creatures/inferno.json](<Hero3Expasion1.83/Mods/vcmi-extras/Mods/bonusIcons/mods/Bonus Icons/Content/config/creatures/inferno.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/creatures/creatureAbilities.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/creatures/creatureAbilities.json>)

<details>
<summary>逐次改动</summary>

- 2026-06-15 `2b79bc80`：`abilities.summons50HP.val`：`50` → `5000000`。

</details>


<details>
<summary>当前完整定义与描述</summary>

```json
{
 "index": 51,
 "level": 5,
 "faction": "inferno",
 "abilities": {
  "summons50HP": {
   "type": "SPECIFIC_SPELL_POWER",
   "subtype": "spell.summonDemons",
   "val": 5000000
  },
  "resurrects": {
   "type": "SPELLCASTER",
   "subtype": "spell.summonDemons",
   "val": 0,
   "icon": "zvs/Lib1.res/E_SUMMON"
  },
  "castsAmount": {
   "type": "CASTS",
   "val": 1
  }
 },
 "graphics": {
  "mapAttackFromRight": "AvWattak.def:0:102",
  "mapAttackFromLeft": "AvWattak.def:0:103",
  "animation": "CPFOE.DEF",
  "map": "hota/inferno/adventureMap/creatures/AVWpitx0.def",
  "mapMask": [
   "VVV",
   "VAV"
  ]
 },
 "sound": {
  "attack": "PFOEATTK.wav",
  "defend": "PFOEDFND.wav",
  "killed": "PFOEKILL.wav",
  "move": "PFOEMOVE.wav",
  "wince": "PFOEWNCE.wav"
 },
 "upgrades": [
  "ifDark05"
 ],
 "description": "邪神，邪神王和折磨者以折磨之鞭攻击。邪神王每场战斗可复活己方阵亡部队为恶鬼（替代其他行动）。\r\n\r\nFiend起源于古英语的feond，本义仅指普通的「敌人」或「对手」，在词源上是动词feogan（仇恨）的现在分词，意为「正在仇恨的人」，与表示「爱」的friend（朋友）在构词逻辑上恰好相反。随着基督教在欧洲的传播，该词在公元1000年左右被赋予了强烈的宗教色彩，从泛指的敌人缩窄为专指「人类的公敌」——即撒旦或恶魔。到了13世纪，它开始用来形容极其残暴、具有魔鬼般性格的人。\r\n\r\nPit（坑/渊），可能是指代DND地狱里奈苏斯（Nessus）的巨大深沟、峡谷，以及神话传说中地狱整体如漏斗般的深渊构造。\r\n\r\n邪神王可以复活的恶鬼生命值为50×数量。"
}
```

</details>

### sharpshooter（`core:sharpshooter`）

**作用范围**：兵种本体／宝物本体配置，区别于仅某位英雄所带部队。历史及完整快照如下。

- [Hero3Expasion1.83/config/creatures/neutral.json](<Hero3Expasion1.83/config/creatures/neutral.json>)
- [Hero3Expasion1.83/Mods/hota/Mods/newGraphics/Content/config/hota/newGraphics/neutrals/newCreatureAnimations.json](<Hero3Expasion1.83/Mods/hota/Mods/newGraphics/Content/config/hota/newGraphics/neutrals/newCreatureAnimations.json>)
- [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/creatures/creatureAbilities.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/creatures/creatureAbilities.json>)

<details>
<summary>逐次改动</summary>

- 2026-06-20 `d20e3775`：`abilities.summonBoneHook.icon`：`null` → `"asylum/NewOldSpells/summon.png"`；`abilities.summonBoneHook.subtype`：`null` → `"spell.summonBoneHook"`；`abilities.summonBoneHook.type`：`null` → `"SPELLCASTER"`；`abilities.summonBoneHook.val`：`null` → `0`；`abilities.summonBoneHookCasts.type`：`null` → `"CASTS"`；`abilities.summonBoneHookCasts.val`：`null` → `1`；`abilities.summonBoneHookPower.subtype`：`null` → `"spell.summonBoneHook"`；`abilities.summonBoneHookPower.type`：`null` → `"SPECIFIC_SPELL_POWER"`；`abilities.summonBoneHookPower.val`：`null` → `4000000`。

</details>


<details>
<summary>当前完整定义与描述</summary>

```json
{
 "index": 137,
 "level": 4,
 "compatibilityIdentifiers": [
  "sharpshooters"
 ],
 "faction": "neutral",
 "excludeFromRandomization": true,
 "shots": 32,
 "abilities": {
  "shooter": {
   "type": "SHOOTER"
  },
  "noDistancePenalty": {
   "type": "NO_DISTANCE_PENALTY"
  },
  "noWallPenalty": {
   "type": "NO_WALL_PENALTY"
  },
  "boneArcherSkeleton": {
   "type": "SKELETON_TRANSFORMER_TARGET",
   "subtype": "boneHook",
   "hidden": true
  },
  "summonBoneHookPower": {
   "type": "SPECIFIC_SPELL_POWER",
   "subtype": "spell.summonBoneHook",
   "val": 4000000
  },
  "summonBoneHook": {
   "type": "SPELLCASTER",
   "subtype": "spell.summonBoneHook",
   "val": 0,
   "icon": "asylum/NewOldSpells/summon.png"
  },
  "summonBoneHookCasts": {
   "type": "CASTS",
   "val": 1
  }
 },
 "graphics": {
  "mapAttackFromRight": "AvWattak.def:0:274",
  "mapAttackFromLeft": "AvWattak.def:0:275",
  "animation": "CSHARP.DEF",
  "missile": {
   "projectile": "PELFX.DEF"
  },
  "map": "hota/neutrals/adventureMap/creatures/AVWsharp.def",
  "mapMask": [
   "VVV",
   "VAV"
  ]
 },
 "sound": {
  "attack": "HCRSATTK.wav",
  "defend": "HCRSDFND.wav",
  "killed": "HCRSKILL.wav",
  "move": "HCRSMOVE.wav",
  "shoot": "HCRSSHOT.wav",
  "wince": "HCRSWNCE.wav"
 },
 "description": "幻影射手由游侠英雄格鲁专门训练，从弓箭手、神射手、木精灵与大精灵中选拔。他们使用巨型复合长弓提升威力与精度，训练有素，无视距离与城墙阻碍。\r\n\r\n神射手（Sharpshooter）一词源于19世纪军事领域，专指经过严格训练、使用精密步枪进行超远距离精准射击的精英步兵。他们通常在战斗中扮演狙击与关键目标清除的角色，是技术、耐心与心理素质的结合体。在《英雄无敌》等奇幻战略游戏中，神射手被塑造为远程单位的顶级进阶形态。"
}
```

</details>

### cloakOfTheUndeadKing（`core:cloakOfTheUndeadKing`）

**作用范围**：兵种本体／宝物本体配置，区别于仅某位英雄所带部队。历史及完整快照如下。

- [Hero3Expasion1.83/config/artifacts.json](<Hero3Expasion1.83/config/artifacts.json>)

<details>
<summary>逐次改动</summary>

- 2026-06-04 `a851c5f7`：`bonuses.lich.subtype`：`"creature.lich"` → `"creature.dreadKnight"`；`bonuses.walkingDead.subtype`：`"creature.walkingDead"` → `"creature.wight"`；`bonuses.wight.subtype`：`"creature.wight"` → `"creature.lich"`。

</details>


<details>
<summary>当前完整定义与描述</summary>

```json
{
 "bonuses": {
  "skeleton": {
   "type": "IMPROVED_NECROMANCY",
   "subtype": "creature.skeleton",
   "addInfo": 0
  },
  "walkingDead": {
   "type": "IMPROVED_NECROMANCY",
   "subtype": "creature.wight",
   "addInfo": 1
  },
  "wight": {
   "type": "IMPROVED_NECROMANCY",
   "subtype": "creature.lich",
   "addInfo": 2
  },
  "lich": {
   "type": "IMPROVED_NECROMANCY",
   "subtype": "creature.dreadKnight",
   "addInfo": 3
  }
 },
 "index": 130,
 "type": [
  "HERO"
 ],
 "components": [
  "amuletOfTheUndertaker",
  "vampiresCowl",
  "deadMansBoots"
 ],
 "value": 12000
}
```

</details>


## 建筑与战役

### 禁林学者小屋

- 文件：`Hero3Expasion1.83/Mods/preserve/content/config/preserve/town.json`，`special1.configuration`，提交`498be78f`。
- 原9技能池：气、水、土、寻路、幸运、魔力、智力、学术、神秘术。依次抽skill01／02／03，用noneOf排除已抽结果，三个不同，不从整个技能库重抽。
- 每位英雄在每间小屋选择一次，费用1000金币，学习初级；保留金币足额、可学技能、未学技能限制与拒绝按钮。初始化时决定该小屋课程，不随访问英雄重抽。
- 已会的技能被过滤后可能只有1—2项，已满技能栏／没钱不保证提供可买选项；不能宣传为永远可选3项。
- 删除旧gainedSkill visitLimiter，保留visitMode=hero、selectMode=selectPlayer；建筑、选择、已访问及无课程提示已同步。
- 静态抽取覆盖504种有序组合、84种无序组合；旧存档已初始化建筑不自动迁移，游戏内选择窗口仍待验收。

### 沙堡战役

位置：`Hero3Expasion1.83/Mods/new-menu/Content/Maps/Expansion/campaign-5 - Copy.vcmp`。其中第4关`c504.vmap`的“怨影亚伯拉罕”是实例名，英雄类型继承`new pavilion.new pavillion:ibrahim`；监狱英雄为`new pavilion.new pavillion:varn`，二者不可混淆。

`562fb433`曾添加展开的header.json，`b2d8167b`删除该文件；不要把曾经解包的头文件视为当前必需新增地图。另有战役存档、快速存档及测试备份提交，不代表修改了对应英雄全局定义。

## 引擎与界面

### 右键地图物体预览

- 当前修复版基于`4dd431c45`，先构造完整原生窗口，再附加只读详情；空间不足保留原生窗口。旧初版提前return导致原信息丢失，已被后续版本取代。
- 支持城镇本局法术、大学技能、先知条件／奖励、通用奖励物体内容；区分已建／未建、选中英雄上下文、访问与重置状态。不支持或复杂特殊对象会保留／回退原窗口，不承诺解释全部模组脚本。
- 仅单人类玩家且对象可见时启用，不要求侦察术；开关`adventure.detailedObjectPreview`默认开。不得访问对象、发奖、扣资源或推进随机数。
- 位置：独立源码`client/windows/InfoWindows.cpp`、`ObjectPreview.cpp`及布局／策略测试；主游戏设置schema、冒险选项控件与中英文翻译配套。
- Actions `41791162749`已构建并部署，后续引擎包继续保留；曾有HTTP422无法调度属于当时历史障碍，不是当前仍未编译。完整游戏内回归未记录通过。

### 透视大气属性建筑图标

- 专家增加佣兵营地、马力特塔、占星轴、真理花园、竞技场、启迪图书馆、战争学院、魔法学校、学者，以及模组魔法竞技场／占卜小屋共11类。
- 模组对象引用移至`Mods/extra creatures specialty/content/config/spells/viewAirStatBuildings.json`并补依赖，避免core直接跨模组引用。学者等按对象类型展示，不保证每个实例实际给主属性。
- 引擎`c21ade8df`，图片`Mods/vcmi/Content/Sprites/mapView/statBoost.png`；缺失时回退中立宝物。后续部署保留本地自定义箭头图标，不用构建旧图覆盖。

### 每日随机宝物

引擎`83cda461f`，自定义bonus注册`Mods/extra creatures specialty/content/config/bonuses/dailyArtifacts.json`。先快照当日数量，再按英雄实例ID发放；每英雄每日聚合上限100件，薇奥拉为1。自有正常、驻防、访问、乘船英雄参与，酒馆候选／监狱不参与。

忽略地图禁用、允许普通组合宝物；排除SPECIAL及战争机器。卷轴绑定有效1—5级非特殊英雄法术，无有效法术则不生成无效卷轴。背包满／空池跳过，不补发、不递归发奖、不自动装备。不是“身旁地图生成宝物”，后者未实现。

### 学术经验追赶

引擎`67cc593b2`，核心策略`server/processors/ScholarExperiencePolicy.h`。目标经验向下取整，发放max(0,目标-学生当前经验)；同等级不传、严格同玩家，不向盟友／敌人发放。关闭地图交换窗口或城镇奖励后通过延迟查询结算，再校验英雄ID和归属；复用正常升级流程及等级上限。

旧存档中已会学术可按新引擎流程触发；薇奥拉等新增英雄特长不随读档自动补齐。无需新增师生历史记录，重复相遇由当前总经验自然防重。

### 中文存档名称回填

引擎`acc433e1e`将`client/lobby/SelectionTab.cpp`的`filename.stem().string()`替换为`TextOperations::filesystemPathToUtf8(filename.stem())`，避免Windows本地编码误当UTF-8。新增`test/TextOperationsTest.cpp`及测试注册；不改变存档格式，不批量重命名旧存档。

Actions `42463657558`已构建并部署；历史记录称64个EXE／DLL哈希及x64类型校验通过，备份`temp/deployment-backups/before-acc433e-20261108-015649`。完整单元测试目标当时未开启，不能宣称新增测试已执行；中文名称游戏内回填仍待验证。可迁移补丁`patches/save-name-utf8.patch`。

### BattleAI与构建部署

法术等级抑制下AI反复选择不可施放法术的循环由独立引擎修复，主仓库`0ac2867b`部署BattleAI.dll并保留bak。后续整包必须保留该分支修复，不能单独换回上游旧DLL。

独立Windows工作流`.github/workflows/msvc-x64-only.yml`只构建MSVC x64，不启动完整多平台矩阵。主要部署序列：`41335105647`（初版预览）→`41791162749`（预览修复）→`42177684899`（每日宝物）→`42180943003`（学术）→`42463657558`（UTF-8保存名）。后包包含前述功能；没有把编译成功当作实战验收。

## 加载与维护

### 注册与依赖

- 牺牲伤害版、白骨弓箭手召唤、每日宝物bonus、集中描述及透视建筑补丁均由extra creatures specialty的manifest注册。局部文件存在不代表自动加载。
- 当前潮汐navigation.json和leadership.json未列入其skills加载清单，前者的水上行走／免费上下船、后者的领导术＋箭术魔杖联动都不能记作当前有效技能。艾伦直接拥有的FREE_SHIP_BOARDING不受此文件未注册影响。
- 未注册`hotaGameBalance/skills.json`不是当前后勤80%的加载来源；已注册的是skillHotA.json，当前核心后勤本身就为20／40／80。
- preserve依赖修复、中文UTF-8清理，及Fairy加入asylum／deathvalley、Courtyard加入hota.bulwark／哲学家runes权重4、主教学术权重0→1，均属依赖或职业抽取规则，不是单英雄直接学会技能。
- 埃尔斯佩思原文件保留20项可访问火系法术，完整27项书单移至覆盖模组pavilion.json的spellbook#override，解决跨模组引用依赖；不把原文件20项误记为最终漏7项。

### 参数中文速查

- BASE_NUMBER是基础数值；ADDITIVE_VALUE是追加数值；PERCENT_TO_BASE是按基础值增加百分比；PERCENT_TO_TARGET_TYPE是在指定来源类型上做相对百分比增强。比如珍妮每级20%增强防御术，不是每级减伤20个百分点。
- INDEPENDENT_MIN／MAX用于独立最小／最大值比较，不应当作普通加法。HAS_ANOTHER_BONUS_LIMITER要求目标有指定标记；CREATURE_TYPE_LIMITER只对列出的兵种生效，includeUpgrades才扩展升级链。
- 没有海上限定器的视野、收入、恢复并不会因为bonus名称带sea就仅在海上有效；bonus键名通常只是条目名称，真正效果由type、subtype、limiters和注册链决定。
- 技能基础effects与熟练度effects需要合并；法术levels.base与四级熟练度也需要合并。空对象和缺少键不自动清空父配置，#override才显式替换。

### 作弊、迁移与资料

- `config/cheats.json`将vcmiskill、giveArtifacts、victory相关命令归入localCheats；已有作弊标记不自动清除，是否完全不留下状态仍需分别实测。
- VS Code的search.useIgnoreFiles=false只是搜索范围调整；不会把忽略文件重新加入Git。vcmiskill文档说明0删除、1初级、2中级、3高级及!cheaters查询。
- 主游戏目录由旧`Heroes of Might and Magic III Expansion 1.83`改名为`Hero3Expasion1.83`；本手册统一引用新路径，历史树中旧路径仍用于追溯。
- `temp`包含临时源码、下载构建、部署备份、审计报告，不是游戏运行目录的VCMI文件夹。主仓库clone不会取得这些被忽略内容，也不会自动取得独立引擎历史。
- 必须另补被ignore排除的`Data/H3bitmap.lod`、`Data/Heroes3.vid`、`Mods/new-menu/Content/video/preserveEnd.bik`；只有clone而未补资源，不能保证正常启动。入口`Hero3Expasion1.83/VCMI_launcher.exe`。
- 仓库`My Games/vcmi`与Windows实际用户文档存档目录不同，不要误认提交测试存档就修改了运行中的存档。
- 新建吸取敌方魔法值=自身知识×10、传送到友军身旁等仅讨论可行性，未实现。不把方案或问答当正式更新。

### 禁林学者小屋当前完整奖励配置

文件：[Hero3Expasion1.83/Mods/preserve/content/config/preserve/town.json](<Hero3Expasion1.83/Mods/preserve/content/config/preserve/town.json>)


<details>
<summary>当前禁林城镇定义（含三选一课程、费用、限制与描述）</summary>

```json
{
 "preserve": {
  "alignment": "good",
  "creatureBackground": {
   "120px": "preserve/creature-bg-120px.bmp",
   "130px": "preserve/creature-bg-130px.bmp"
  },
  "name": "禁林",
  "description": "{禁林}\n种族阵营： 正义，混乱\n种族地形： 草地\n所在地： 恩塔格瑞大陆-维尔宁王国\n禁林势力是埃里部分反对战争的精灵离开后自行建立的新联盟。他们分布在曾经的争议之地哈蒙代尔，并在此建立了新的国度，并且不再受格鲁为首的精灵节制。他们爱好和平，但同时也善于利用大自然的力量对来犯的敌人进行攻击。",
  "nativeTerrain": "grass",
  "boat": "boatCastle",
  "puzzleMap": {
   "prefix": "preserve/puzzle-map/PUZ",
   "pieces": [
    {
     "index": 48,
     "x": 6,
     "y": 7
    },
    {
     "index": 47,
     "x": 6,
     "y": 7
    },
    {
     "index": 46,
     "x": 6,
     "y": 7
    },
    {
     "index": 45,
     "x": 6,
     "y": 7
    },
    {
     "index": 44,
     "x": 6,
     "y": 7
    },
    {
     "index": 43,
     "x": 6,
     "y": 7
    },
    {
     "index": 42,
     "x": 6,
     "y": 7
    },
    {
     "index": 41,
     "x": 6,
     "y": 7
    },
    {
     "index": 40,
     "x": 6,
     "y": 7
    },
    {
     "index": 39,
     "x": 6,
     "y": 7
    },
    {
     "index": 38,
     "x": 6,
     "y": 7
    },
    {
     "index": 37,
     "x": 6,
     "y": 7
    },
    {
     "index": 36,
     "x": 6,
     "y": 7
    },
    {
     "index": 35,
     "x": 6,
     "y": 7
    },
    {
     "index": 34,
     "x": 6,
     "y": 7
    },
    {
     "index": 33,
     "x": 6,
     "y": 7
    },
    {
     "index": 32,
     "x": 6,
     "y": 7
    },
    {
     "index": 31,
     "x": 6,
     "y": 7
    },
    {
     "index": 30,
     "x": 6,
     "y": 7
    },
    {
     "index": 29,
     "x": 6,
     "y": 7
    },
    {
     "index": 28,
     "x": 6,
     "y": 7
    },
    {
     "index": 27,
     "x": 6,
     "y": 7
    },
    {
     "index": 26,
     "x": 6,
     "y": 7
    },
    {
     "index": 25,
     "x": 6,
     "y": 7
    },
    {
     "index": 24,
     "x": 6,
     "y": 7
    },
    {
     "index": 23,
     "x": 6,
     "y": 7
    },
    {
     "index": 22,
     "x": 6,
     "y": 7
    },
    {
     "index": 21,
     "x": 6,
     "y": 7
    },
    {
     "index": 20,
     "x": 6,
     "y": 7
    },
    {
     "index": 19,
     "x": 6,
     "y": 7
    },
    {
     "index": 18,
     "x": 6,
     "y": 7
    },
    {
     "index": 17,
     "x": 6,
     "y": 7
    },
    {
     "index": 16,
     "x": 6,
     "y": 7
    },
    {
     "index": 15,
     "x": 6,
     "y": 7
    },
    {
     "index": 14,
     "x": 6,
     "y": 7
    },
    {
     "index": 13,
     "x": 6,
     "y": 7
    },
    {
     "index": 12,
     "x": 6,
     "y": 7
    },
    {
     "index": 11,
     "x": 6,
     "y": 7
    },
    {
     "index": 10,
     "x": 6,
     "y": 7
    },
    {
     "index": 9,
     "x": 6,
     "y": 7
    },
    {
     "index": 8,
     "x": 6,
     "y": 7
    },
    {
     "index": 7,
     "x": 6,
     "y": 7
    },
    {
     "index": 6,
     "x": 6,
     "y": 7
    },
    {
     "index": 5,
     "x": 6,
     "y": 7
    },
    {
     "index": 4,
     "x": 6,
     "y": 7
    },
    {
     "index": 3,
     "x": 6,
     "y": 7
    },
    {
     "index": 2,
     "x": 6,
     "y": 7
    },
    {
     "index": 1,
     "x": 6,
     "y": 7
    }
   ]
  },
  "town": {
   "defaultTavern": 5,
   "buildings": {
    "mageGuild1": {
     "description": "进入魔法行会的来访英雄可以学会其中的魔法。",
     "name": "1级魔法行会",
     "cost": {
      "wood": 5,
      "ore": 5,
      "gold": 2000
     }
    },
    "mageGuild2": {
     "description": "进入魔法行会的来访英雄可以学会其中的魔法。",
     "name": "2级魔法行会",
     "cost": {
      "wood": 5,
      "mercury": 4,
      "ore": 5,
      "sulfur": 4,
      "crystal": 4,
      "gems": 4,
      "gold": 1000
     },
     "upgrades": "mageGuild1"
    },
    "mageGuild3": {
     "description": "进入魔法行会的来访英雄可以学会其中的魔法。",
     "name": "3级魔法行会",
     "cost": {
      "wood": 5,
      "mercury": 6,
      "ore": 5,
      "sulfur": 6,
      "crystal": 6,
      "gems": 6,
      "gold": 1000
     },
     "upgrades": "mageGuild2"
    },
    "mageGuild4": {
     "description": "进入魔法行会的来访英雄可以学会其中的魔法。",
     "name": "4级魔法行会",
     "cost": {
      "wood": 5,
      "mercury": 8,
      "ore": 5,
      "sulfur": 8,
      "crystal": 8,
      "gems": 8,
      "gold": 1000
     },
     "upgrades": "mageGuild3"
    },
    "mageGuild5": {
     "description": "进入魔法行会的来访英雄可以学会其中的魔法。",
     "name": "5级魔法行会",
     "cost": {
      "wood": 5,
      "mercury": 10,
      "ore": 5,
      "sulfur": 10,
      "crystal": 10,
      "gems": 10,
      "gold": 1000
     },
     "upgrades": "mageGuild4"
    },
    "tavern": {
     "name": "酒馆",
     "description": "酒馆可提高驻军的士气。",
     "cost": {
      "wood": 5,
      "gold": 500
     }
    },
    "shipyard": {
     "name": "船坞",
     "description": "船坞允许你购买船只。",
     "cost": {
      "wood": 20,
      "gold": 2000
     }
    },
    "fort": {
     "name": "堡垒",
     "description": "堡垒可为你的城镇提供城墙。",
     "cost": {
      "wood": 20,
      "ore": 20,
      "gold": 5000
     }
    },
    "citadel": {
     "name": "大本营",
     "description": "大本营不仅能使生物的基本生长率提高50%，还能加强城镇的防御设施。",
     "cost": {
      "ore": 5,
      "gold": 2500
     },
     "upgrades": "fort"
    },
    "castle": {
     "name": "城堡",
     "description": "城堡可提供2座箭塔以加强城镇的防御，而且还可使生物的基本生长率加倍。",
     "cost": {
      "wood": 10,
      "ore": 10,
      "gold": 5000
     },
     "upgrades": "citadel"
    },
    "villageHall": {
     "name": "议事堂",
     "description": "议事堂可以让你购买城镇建筑并每天向你提供500个金币。",
     "mode": "auto"
    },
    "townHall": {
     "name": "内政厅",
     "description": "内政厅可以让你购买城镇建筑，并每天向你提供1000个金币。",
     "cost": {
      "gold": 2500
     },
     "requires": [
      "tavern"
     ],
     "upgrades": "villageHall"
    },
    "cityHall": {
     "name": "议会",
     "description": "议会可以让你购买城镇建筑并每天向你提供2000个金币。",
     "cost": {
      "gold": 5000
     },
     "requires": [
      "allOf",
      [
       "mageGuild1"
      ],
      [
       "marketplace"
      ],
      [
       "blacksmith"
      ]
     ],
     "upgrades": "townHall"
    },
    "capitol": {
     "name": "国会",
     "description": "国会每天向你提供4000个金币。",
     "cost": {
      "gold": 10000
     },
     "requires": [
      "castle"
     ],
     "upgrades": "cityHall"
    },
    "marketplace": {
     "name": "市场",
     "description": "市场可让你交换资源(你所拥有的市场越多，价格越便宜)。",
     "cost": {
      "wood": 5,
      "gold": 500
     }
    },
    "resourceSilo": {
     "description": "物资仓库每天为你额外提供1宝石。",
     "name": "物资仓库",
     "produce": {
      "gems": 1
     },
     "cost": {
      "ore": 5,
      "gold": 5000
     },
     "requires": [
      "marketplace"
     ]
    },
    "blacksmith": {
     "description": "铁匠铺可向你的军队提供急救帐篷。",
     "name": "铁匠铺",
     "warMachine": "firstAidTent",
     "cost": {
      "wood": 5,
      "gold": 1000
     }
    },
    "horde1": {
     "description": "岩石拱门使矮妖精的产量每周增加6个。",
     "name": "岩石拱门",
     "cost": {
      "gold": 1000
     },
     "upgrades": "dwellingLvl1",
     "requires": [
      "allOf",
      [
       "fort"
      ],
      [
       "dwellingLvl1"
      ]
     ]
    },
    "horde1Upgr": {
     "description": "岩石拱门使棕妖精的产量每周增加6个。",
     "name": "岩石拱门",
     "cost": {
      "gold": 1000
     },
     "requires": [
      "allOf",
      [
       "horde1"
      ],
      [
       "dwellingUpLvl1"
      ]
     ],
     "upgrades": "dwellingUpLvl1",
     "mode": "auto"
    },
    "ship": {
     "description": "船坞允许你购买船只。",
     "name": "船坞"
    },
    "special1": {
     "description": "学者小屋从气系魔法、水系魔法、土系魔法、寻路术、幸运术、魔力、学术、智力和神秘术中随机提供3种不同技能。每位英雄可支付1000黄金选择学习其中一项；已学会的技能不会显示。",
     "name": "学者小屋",
     "manualHeroVisit": true,
     "configuration": {
      "visitMode": "hero",
      "variables": {
       "secondarySkill": {
        "skill01": {
         "anyOf": [
          "airMagic",
          "waterMagic",
          "earthMagic",
          "pathfinding",
          "luck",
          "sorcery",
          "intelligence",
          "scholar",
          "mysticism"
         ]
        },
        "skill02": {
         "anyOf": [
          "airMagic",
          "waterMagic",
          "earthMagic",
          "pathfinding",
          "luck",
          "sorcery",
          "intelligence",
          "scholar",
          "mysticism"
         ],
         "noneOf": [
          "@skill01"
         ]
        },
        "skill03": {
         "anyOf": [
          "airMagic",
          "waterMagic",
          "earthMagic",
          "pathfinding",
          "luck",
          "sorcery",
          "intelligence",
          "scholar",
          "mysticism"
         ],
         "noneOf": [
          "@skill01",
          "@skill02"
         ]
        }
       }
      },
      "rewards": [
       {
        "secondary": {
         "@skill01": 1
        },
        "resources": {
         "gold": -1000
        },
        "limiter": {
         "canLearnSkills": true,
         "noneOf": [
          {
           "secondary": {
            "@skill01": 1
           }
          }
         ],
         "resources": {
          "gold": 1000
         }
        },
        "message": "{学者小屋}\r\n\r\n你捐助了1000黄金，学者传授给你一项新的技能。"
       },
       {
        "secondary": {
         "@skill02": 1
        },
        "resources": {
         "gold": -1000
        },
        "limiter": {
         "canLearnSkills": true,
         "noneOf": [
          {
           "secondary": {
            "@skill02": 1
           }
          }
         ],
         "resources": {
          "gold": 1000
         }
        },
        "message": "{学者小屋}\r\n\r\n你捐助了1000黄金，学者传授给你一项新的技能。"
       },
       {
        "secondary": {
         "@skill03": 1
        },
        "resources": {
         "gold": -1000
        },
        "limiter": {
         "canLearnSkills": true,
         "noneOf": [
          {
           "secondary": {
            "@skill03": 1
           }
          }
         ],
         "resources": {
          "gold": 1000
         }
        },
        "message": "{学者小屋}\r\n\r\n你捐助了1000黄金，学者传授给你一项新的技能。"
       }
      ],
      "onSelectMessage": "{学者小屋}\r\n\r\n学者们提供了三项不同的技能课程。请选择一项尚未掌握的技能，支付1000黄金学习。每位英雄在本小屋只能学习一次。",
      "onVisitedMessage": "{学者小屋}\r\n\r\n你已经在这间学者小屋学习过一项技能，学者们祝你旅途顺利。",
      "onEmptyMessage": "{学者小屋}\r\n\r\n当前没有可学习的课程：可能你已掌握所提供的技能、技能栏已满，或不足1000黄金。",
      "canRefuse": true,
      "selectMode": "selectPlayer"
     },
     "requires": [
      "mageGuild1"
     ],
     "cost": {
      "gems": 5,
      "crystal": 5,
      "gold": 2000
     }
    },
    "special2": {
     "description": "彩虹使访问过的英雄一周内幸运值+1。",
     "name": "彩虹",
     "configuration": {
      "visitMode": "bonus",
      "rewards": [
       {
        "message": "经过一场大雨之后的密林中空气额外清新，而你也在摇曳的树影中看到了美丽的彩虹，这是好运的征兆。",
        "bonuses": [
         {
          "type": "LUCK",
          "val": 1,
          "duration": "ONE_WEEK"
         }
        ]
       }
      ]
     },
     "cost": {
      "gold": 1000,
      "gems": 2
     }
    },
    "grail": {
     "description": "生命之灵使每周的生物产量提高50%，每天为你的王国额外提供5000个金币，且给所有的英雄提升2点幸运。并且每天为你的王国带来每种稀有资源3个。",
     "name": "生命之灵",
     "produce": {
      "gold": 5000,
      "gems": 3,
      "crystal": 3,
      "sulfur": 3,
      "mercury": 3
     },
     "bonuses": [
      {
       "type": "LUCK",
       "val": 2,
       "propagator": "PLAYER"
      }
     ],
     "mode": "grail"
    },
    "dwellingLvl1": {
     "description": "村落允许你招募矮妖精。",
     "name": "村落",
     "cost": {
      "gold": 500,
      "wood": 10
     },
     "requires": [
      "fort"
     ]
    },
    "dwellingLvl2": {
     "description": "岩洞允许你招募棕狼。",
     "name": "岩洞",
     "cost": {
      "gold": 1000,
      "ore": 5
     },
     "requires": [
      "dwellingLvl1"
     ]
    },
    "dwellingLvl3": {
     "description": "虎穴允许你招募老虎。",
     "name": "虎穴",
     "cost": {
      "gold": 1500,
      "wood": 10,
      "ore": 5
     },
     "requires": [
      "allOf",
      [
       "mageGuild1"
      ],
      [
       "dwellingLvl2"
      ]
     ]
    },
    "dwellingLvl4": {
     "description": "温室允许你招募蜂草。",
     "name": "温室",
     "cost": {
      "gold": 2000,
      "ore": 15
     },
     "requires": [
      "allOf",
      [
       "mageGuild1"
      ],
      [
       "dwellingLvl2"
      ]
     ]
    },
    "dwellingLvl5": {
     "description": "螳螂巢允许你招募螳螂。",
     "name": "螳螂巢",
     "cost": {
      "gold": 3000,
      "wood": 15
     },
     "requires": [
      "allOf",
      [
       "dwellingLvl2"
      ],
      [
       "dwellingLvl3"
      ]
     ]
    },
    "dwellingLvl6": {
     "description": "森林祭坛允许你招募德鲁伊。",
     "name": "森林祭坛",
     "cost": {
      "gold": 4000,
      "ore": 10,
      "sulfur": 5,
      "crystal": 5,
      "gems": 5
     },
     "requires": [
      "dwellingLvl4"
     ]
    },
    "dwellingLvl7": {
     "description": "金色阁楼允许你招募森林龙。",
     "name": "金色阁楼",
     "cost": {
      "gold": 15000,
      "wood": 20,
      "ore": 5,
      "gems": 10
     },
     "requires": [
      "dwellingLvl6"
     ]
    },
    "dwellingUpLvl1": {
     "description": "高级村落允许你招募棕妖精。",
     "name": "高级村落",
     "cost": {
      "gold": 1000
     },
     "requires": [
      "tavern"
     ],
     "upgrades": "dwellingLvl1"
    },
    "dwellingUpLvl2": {
     "description": "高级岩洞允许你招募灰狼。",
     "name": "高级岩洞",
     "cost": {
      "gold": 1500,
      "wood": 2,
      "ore": 2
     },
     "upgrades": "dwellingLvl2"
    },
    "dwellingUpLvl3": {
     "description": "高级虎穴允许你招募白虎。",
     "name": "高级虎穴",
     "cost": {
      "gold": 1000,
      "ore": 5,
      "wood": 5
     },
     "requires": [
      "dwellingUpLvl2"
     ],
     "upgrades": "dwellingLvl3"
    },
    "dwellingUpLvl4": {
     "description": "高级温室允许你招募毒蜂草。",
     "name": "高级温室",
     "cost": {
      "gold": 2000,
      "ore": 5,
      "crystal": 5,
      "mercury": 5
     },
     "upgrades": "dwellingLvl4"
    },
    "dwellingUpLvl5": {
     "description": "高级螳螂巢允许你招募黄金螳螂。",
     "name": "高级螳螂巢",
     "cost": {
      "gold": 2500,
      "wood": 15,
      "gems": 5
     },
     "upgrades": "dwellingLvl5"
    },
    "dwellingUpLvl6": {
     "description": "高级森林祭坛允许你招募高阶德鲁伊。",
     "name": "高级森林祭坛",
     "cost": {
      "gold": 3000,
      "ore": 5,
      "sulfur": 3,
      "crystal": 3,
      "gems": 3
     },
     "upgrades": "dwellingLvl6"
    },
    "dwellingUpLvl7": {
     "description": "高级金色阁楼允许你招募精灵龙。",
     "name": "高级金色阁楼",
     "cost": {
      "gold": 15000,
      "wood": 5,
      "ore": 5,
      "gems": 20
     },
     "requires": [
      "mageGuild2"
     ],
     "upgrades": "dwellingLvl7"
    }
   },
   "buildingsIcons": "preserve/town-hall.def",
   "creatures": [
    [
     "halflings",
     "hobbit"
    ],
    [
     "treelurker",
     "caveleaper"
    ],
    [
     "magiceye",
     "misfortuneeye"
    ],
    [
     "foxdruid",
     "highdruid"
    ],
    [
     "hippogryph",
     "grimhippo"
    ],
    [
     "fairybear",
     "mysticalbear"
    ],
    [
     "treant",
     "ancienttreant"
    ]
   ],
   "guildBackground": "TPMAGE.bmp",
   "guildSpells": {
    "bless": 2,
    "bloodlust": 5,
    "cure": 4,
    "curse": 3,
    "dispel": 10,
    "haste": 10,
    "magicArrow": 25,
    "protectAir": 5,
    "protectFire": 3,
    "shield": 5,
    "slow": 15,
    "stoneSkin": 2,
    "summonBoat": 5,
    "viewAir": 3,
    "viewEarth": 3,
    "disguise": 2,
    "disruptingRay": 8,
    "fireWall": 3,
    "fortune": 10,
    "iceBolt": 10,
    "lightningBolt": 35,
    "precision": 6,
    "quicksand": 4,
    "removeObstacle": 6,
    "scuttleBoat": 4,
    "visions": 2,
    "weakness": 10,
    "airShield": 2,
    "antiMagic": 20,
    "earthquake": 30,
    "fireball": 2,
    "forceField": 4,
    "forgetfulness": 8,
    "frostRing": 10,
    "hypnotize": 2,
    "landMine": 4,
    "mirth": 2,
    "misfortune": 4,
    "protectEarth": 8,
    "protectWater": 2,
    "teleport": 2,
    "berserk": 5,
    "chainLightning": 10,
    "clone": 5,
    "counterstrike": 5,
    "fireShield": 5,
    "frenzy": 5,
    "inferno": 5,
    "meteorShower": 5,
    "prayer": 5,
    "resurrection": 5,
    "slayer": 5,
    "sorrow": 5,
    "townPortal": 15,
    "waterWalk": 5,
    "airElemental": 4,
    "dimensionDoor": 15,
    "earthElemental": 10,
    "fireElemental": 10,
    "fly": 15,
    "implosion": 15,
    "magicMirror": 15,
    "sacrifice": 11,
    "waterElemental": 5
   },
   "guildWindow": "preserve/town-screen/mage-guild.bmp",
   "hallBackground": "TPTHBKFR",
   "horde": [
    0,
    -1
   ],
   "icons": {
    "village": {
     "normal": {
      "small": "preserve/icons/hall-small.bmp",
      "large": "preserve/icons/hall-big.bmp"
     },
     "built": {
      "small": "preserve/icons/hall-builded-small.bmp",
      "large": "preserve/icons/hall-builded-big.bmp"
     }
    },
    "fort": {
     "normal": {
      "small": "preserve/icons/fort-small.bmp",
      "large": "preserve/icons/fort-big.bmp"
     },
     "built": {
      "small": "preserve/icons/fort-builded-small.bmp",
      "large": "preserve/icons/fort-builded-big.bmp"
     }
    }
   },
   "mapObject": {
    "filters": {
     "castle": [
      "allOf",
      [
       "castle"
      ],
      [
       "noneOf",
       [
        "capitol"
       ]
      ]
     ],
     "citadel": [
      "allOf",
      [
       "citadel"
      ],
      [
       "noneOf",
       [
        "castle"
       ]
      ]
     ],
     "fort": [
      "allOf",
      [
       "fort"
      ],
      [
       "noneOf",
       [
        "citadel"
       ]
      ]
     ]
    },
    "templates": {
     "capitol": {
      "animation": "preserve/map/capitol.def"
     },
     "castle": {
      "animation": "preserve/map/castle.def"
     },
     "citadel": {
      "animation": "preserve/map/citadel.def"
     },
     "fort": {
      "animation": "preserve/map/fort.def"
     },
     "village": {
      "animation": "preserve/map/village.def"
     }
    }
   },
   "mageGuild": 5,
   "musicTheme": [
    "music/factions/PreserveTown"
   ],
   "names": [
    "福拜斯",
    "布拉顿",
    "瓦拉塔拉",
    "清泉",
    "莱茵哈姆",
    "诺斯霍顿",
    "艾尔比恩",
    "海格希尔",
    "千橡",
    "艾利斯湾",
    "诺斯菲尔德",
    "斯卡菲尔德",
    "艾伦帕克",
    "格伦海文",
    "布鲁菲尔德",
    "占星谷",
    "水晶崖"
   ],
   "primaryResource": "gems",
   "siege": {
    "shooter": "fairybear",
    "imagePrefix": "preserve/siege/SGPR",
    "towerIconSmall": "preserve/icons/towerSmall",
    "towerIconLarge": "preserve/icons/towerLarge",
    "gate": {
     "arch": {
      "x": 460,
      "y": 200
     },
     "gate": {
      "x": 403,
      "y": 271
     }
    },
    "moat": {
     "bank": {
      "x": 410,
      "y": 77
     },
     "moat": {
      "x": 410,
      "y": 77
     }
    },
    "static": {
     "background": {
      "x": 608,
      "y": 46
     },
     "bottom": {
      "x": 510,
      "y": 334
     },
     "top": {
      "x": 492,
      "y": 83
     }
    },
    "towers": {
     "bottom": {
      "battlement": {
       "x": 594,
       "y": 511
      },
      "creature": {
       "x": 371,
       "y": 297
      },
      "tower": {
       "x": 594,
       "y": 511
      }
     },
     "keep": {
      "battlement": {
       "x": 736,
       "y": 159
      },
      "creature": {
       "x": 539,
       "y": -10
      },
      "tower": {
       "x": 736,
       "y": 159
      }
     },
     "top": {
      "battlement": {
       "x": 566,
       "y": 31
      },
      "creature": {
       "x": 341,
       "y": -181
      },
      "tower": {
       "x": 565,
       "y": 31
      }
     }
    },
    "walls": {
     "bottom": {
      "x": 549,
      "y": 451
     },
     "bottomMid": {
      "x": 469,
      "y": 309
     },
     "upper": {
      "x": 530,
      "y": 57
     },
     "upperMid": {
      "x": 469,
      "y": 186
     }
    }
   },
   "structures": {
    "animatedWater": {
     "animation": "preserve/town-screen/buildings/TBPREXT6",
     "x": 0,
     "y": 0,
     "z": 1
    },
    "animatedWater2": {
     "animation": "preserve/town-screen/buildings/TBPREXT5",
     "x": 0,
     "y": 0,
     "z": 0
    },
    "mageGuild1": {
     "animation": "preserve/town-screen/buildings/TBPRmage",
     "area": "preserve/town-screen/buildings-area/TZPRmage",
     "border": "preserve/town-screen/buildings-border/TOPRmage",
     "x": 0,
     "y": 0,
     "z": -1
    },
    "mageGuild2": {
     "animation": "preserve/town-screen/buildings/TBPRmag2",
     "area": "preserve/town-screen/buildings-area/TZPRmag2",
     "border": "preserve/town-screen/buildings-border/TOPRmag2",
     "x": 0,
     "y": 0,
     "z": -1
    },
    "mageGuild3": {
     "animation": "preserve/town-screen/buildings/TBPRmag3",
     "area": "preserve/town-screen/buildings-area/TZPRmag3",
     "border": "preserve/town-screen/buildings-border/TOPRmag3",
     "x": 0,
     "y": 0,
     "z": -1
    },
    "mageGuild4": {
     "animation": "preserve/town-screen/buildings/TBPRmag4",
     "area": "preserve/town-screen/buildings-area/TZPRmag4",
     "border": "preserve/town-screen/buildings-border/TOPRmag4",
     "x": 0,
     "y": 0,
     "z": -1
    },
    "mageGuild5": {
     "animation": "preserve/town-screen/buildings/TBPRmag5",
     "area": "preserve/town-screen/buildings-area/TZPRmag5",
     "border": "preserve/town-screen/buildings-border/TOPRmag5",
     "x": 0,
     "y": 0,
     "z": -1
    },
    "tavern": {
     "animation": "preserve/town-screen/buildings/TBPRTVRN",
     "area": "preserve/town-screen/buildings-area/TZPRTVRN",
     "border": "preserve/town-screen/buildings-border/TOPRTVRN",
     "x": 0,
     "y": 0,
     "z": 2
    },
    "shipyard": {
     "animation": "preserve/town-screen/buildings/TBPRdock",
     "area": "preserve/town-screen/buildings-area/TZPRdock",
     "border": "preserve/town-screen/buildings-border/TOPRdock",
     "x": 0,
     "y": 0,
     "z": 3
    },
    "fort": {
     "animation": "preserve/town-screen/buildings/TBPRCSTL",
     "area": "preserve/town-screen/buildings-area/TZPRCSTL",
     "border": "preserve/town-screen/buildings-border/TOPRCSTL",
     "x": 0,
     "y": 0
    },
    "citadel": {
     "animation": "preserve/town-screen/buildings/TBPRCAS2",
     "area": "preserve/town-screen/buildings-area/TZPRCAS2",
     "border": "preserve/town-screen/buildings-border/TOPRCAS2",
     "x": 0,
     "y": 0
    },
    "castle": {
     "animation": "preserve/town-screen/buildings/TBPRCAS3",
     "area": "preserve/town-screen/buildings-area/TZPRCAS3",
     "border": "preserve/town-screen/buildings-border/TOPRCAS3",
     "x": 0,
     "y": 0
    },
    "villageHall": {
     "animation": "preserve/town-screen/buildings/TBPRhall",
     "area": "preserve/town-screen/buildings-area/TZPRhall",
     "border": "preserve/town-screen/buildings-border/TOPRhall",
     "x": 0,
     "y": 0
    },
    "townHall": {
     "animation": "preserve/town-screen/buildings/TBPRhal2",
     "area": "preserve/town-screen/buildings-area/TZPRhal2",
     "border": "preserve/town-screen/buildings-border/TOPRhal2",
     "x": 0,
     "y": 0
    },
    "cityHall": {
     "animation": "preserve/town-screen/buildings/TBPRhal3",
     "area": "preserve/town-screen/buildings-area/TZPRhal3",
     "border": "preserve/town-screen/buildings-border/TOPRhal3",
     "x": 0,
     "y": 0
    },
    "capitol": {
     "animation": "preserve/town-screen/buildings/TBPRhal4",
     "area": "preserve/town-screen/buildings-area/TZPRhal4",
     "border": "preserve/town-screen/buildings-border/TOPRhal4",
     "x": 0,
     "y": 0
    },
    "marketplace": {
     "animation": "preserve/town-screen/buildings/TBPRmark",
     "area": "preserve/town-screen/buildings-area/TZPRmark",
     "border": "preserve/town-screen/buildings-border/TOPRmark",
     "x": 0,
     "y": 0,
     "z": 2
    },
    "resourceSilo": {
     "animation": "preserve/town-screen/buildings/TBPRsilo",
     "area": "preserve/town-screen/buildings-area/TZPRsilo",
     "border": "preserve/town-screen/buildings-border/TOPRsilo",
     "x": 0,
     "y": 0,
     "z": 3
    },
    "blacksmith": {
     "animation": "preserve/town-screen/buildings/TBPRBLAK",
     "area": "preserve/town-screen/buildings-area/TZPRBLAK",
     "border": "preserve/town-screen/buildings-border/TOPRBLAK",
     "x": 0,
     "y": 0
    },
    "horde1": {
     "animation": "preserve/town-screen/buildings/TBPRHRD1",
     "area": "preserve/town-screen/buildings-area/TZPRHRD1",
     "border": "preserve/town-screen/buildings-border/TOPRHRD1",
     "x": 0,
     "y": 0,
     "z": 2,
     "hidden": true
    },
    "horde1Upgr": {
     "animation": "preserve/town-screen/buildings/TBPRHRD2",
     "area": "preserve/town-screen/buildings-area/TZPRHRD2",
     "border": "preserve/town-screen/buildings-border/TOPRHRD2",
     "x": 0,
     "y": 0,
     "z": 2,
     "hidden": true
    },
    "ship": {
     "animation": "preserve/town-screen/buildings/TBPRship",
     "area": "preserve/town-screen/buildings-area/TZPRship",
     "border": "preserve/town-screen/buildings-border/TOPRship",
     "x": 0,
     "y": 0,
     "z": 4
    },
    "special1": {
     "animation": "preserve/town-screen/buildings/TBPRSPEC",
     "area": "preserve/town-screen/buildings-area/TZPRSPEC",
     "border": "preserve/town-screen/buildings-border/TOPRSPEC",
     "x": 0,
     "y": 0,
     "z": -1
    },
    "special2": {
     "animation": "preserve/town-screen/buildings/TBPRSpe2",
     "area": "preserve/town-screen/buildings-area/TZPRSpe2",
     "border": "preserve/town-screen/buildings-border/TOPRSpe2",
     "x": 0,
     "y": 0,
     "z": -1
    },
    "grail": {
     "animation": "preserve/town-screen/buildings/TBPRHoly",
     "area": "preserve/town-screen/buildings-area/TZPRHoly",
     "border": "preserve/town-screen/buildings-border/TOPRHoly",
     "x": 0,
     "y": 0
    },
    "dwellingLvl1": {
     "animation": "preserve/town-screen/buildings/TBPRdw_0",
     "area": "preserve/town-screen/buildings-area/TZPRdw_0",
     "border": "preserve/town-screen/buildings-border/TOPRdw_0",
     "x": 0,
     "y": 0
    },
    "dwellingLvl2": {
     "animation": "preserve/town-screen/buildings/TBPRdw_1",
     "area": "preserve/town-screen/buildings-area/TZPRdw_1",
     "border": "preserve/town-screen/buildings-border/TOPRdw_1",
     "x": 0,
     "y": 0
    },
    "dwellingLvl3": {
     "animation": "preserve/town-screen/buildings/TBPRdw_2",
     "area": "preserve/town-screen/buildings-area/TZPRdw_2",
     "border": "preserve/town-screen/buildings-border/TOPRdw_2",
     "x": 0,
     "y": 0
    },
    "dwellingLvl4": {
     "animation": "preserve/town-screen/buildings/TBPRdw_3",
     "area": "preserve/town-screen/buildings-area/TZPRdw_3",
     "border": "preserve/town-screen/buildings-border/TOPRdw_3",
     "x": 0,
     "y": 0,
     "z": 4
    },
    "dwellingLvl5": {
     "animation": "preserve/town-screen/buildings/TBPRdw_4",
     "area": "preserve/town-screen/buildings-area/TZPRdw_4",
     "border": "preserve/town-screen/buildings-border/TOPRdw_4",
     "x": 0,
     "y": 0
    },
    "dwellingLvl6": {
     "animation": "preserve/town-screen/buildings/TBPRdw_5",
     "area": "preserve/town-screen/buildings-area/TZPRdw_5",
     "border": "preserve/town-screen/buildings-border/TOPRdw_5",
     "x": 0,
     "y": 0
    },
    "dwellingLvl7": {
     "animation": "preserve/town-screen/buildings/TBPRdw_6",
     "area": "preserve/town-screen/buildings-area/TZPRdw_6",
     "border": "preserve/town-screen/buildings-border/TOPRdw_6",
     "x": 0,
     "y": 0
    },
    "dwellingUpLvl1": {
     "animation": "preserve/town-screen/buildings/TBPRup_0",
     "area": "preserve/town-screen/buildings-area/TZPRup_0",
     "border": "preserve/town-screen/buildings-border/TOPRup_0",
     "x": 0,
     "y": 0
    },
    "dwellingUpLvl2": {
     "animation": "preserve/town-screen/buildings/TBPRup_1",
     "area": "preserve/town-screen/buildings-area/TZPRup_1",
     "border": "preserve/town-screen/buildings-border/TOPRup_1",
     "x": 0,
     "y": 0
    },
    "dwellingUpLvl3": {
     "animation": "preserve/town-screen/buildings/TBPRup_2",
     "area": "preserve/town-screen/buildings-area/TZPRup_2",
     "border": "preserve/town-screen/buildings-border/TOPRup_2",
     "x": 0,
     "y": 0
    },
    "dwellingUpLvl4": {
     "animation": "preserve/town-screen/buildings/TBPRup_3",
     "area": "preserve/town-screen/buildings-area/TZPRup_3",
     "border": "preserve/town-screen/buildings-border/TOPRup_3",
     "x": 0,
     "y": 0,
     "z": 4
    },
    "dwellingUpLvl5": {
     "animation": "preserve/town-screen/buildings/TBPRup_4",
     "area": "preserve/town-screen/buildings-area/TZPRup_4",
     "border": "preserve/town-screen/buildings-border/TOPRup_4",
     "x": 0,
     "y": 0
    },
    "dwellingUpLvl6": {
     "animation": "preserve/town-screen/buildings/TBPRup_5",
     "area": "preserve/town-screen/buildings-area/TZPRup_5",
     "border": "preserve/town-screen/buildings-border/TOPRup_5",
     "x": 0,
     "y": 0
    },
    "dwellingUpLvl7": {
     "animation": "preserve/town-screen/buildings/TBPRup_6",
     "area": "preserve/town-screen/buildings-area/TZPRup_6",
     "border": "preserve/town-screen/buildings-border/TOPRup_6",
     "x": 0,
     "y": 0
    }
   },
   "tavernVideo": "pretvrn.BIK",
   "townBackground": "preserve/town-screen/background.bmp",
   "moatAbility": "castleMoat",
   "hallSlots": [
    [
     [
      "villageHall",
      "townHall",
      "cityHall",
      "capitol"
     ],
     [
      "fort",
      "citadel",
      "castle"
     ],
     [
      "tavern"
     ],
     [
      "blacksmith"
     ]
    ],
    [
     [
      "marketplace",
      "resourceSilo"
     ],
     [
      "mageGuild1",
      "mageGuild2",
      "mageGuild3",
      "mageGuild4",
      "mageGuild5"
     ],
     [
      "shipyard"
     ]
    ],
    [
     [
      "special1"
     ],
     [
      "special2"
     ],
     [
      "horde1",
      "horde1Upgr"
     ]
    ],
    [
     [
      "dwellingLvl1",
      "dwellingUpLvl1"
     ],
     [
      "dwellingLvl2",
      "dwellingUpLvl2"
     ],
     [
      "dwellingLvl3",
      "dwellingUpLvl3"
     ],
     [
      "dwellingLvl4",
      "dwellingUpLvl4"
     ]
    ],
    [
     [
      "dwellingLvl5",
      "dwellingUpLvl5"
     ],
     [
      "dwellingLvl6",
      "dwellingUpLvl6"
     ],
     [
      "dwellingLvl7",
      "dwellingUpLvl7"
     ]
    ]
   ]
  }
 }
}
```

</details>


## 描述检查汇总

检查范围：最终英雄描述覆盖、三级技能描述、四级法术描述及已知隐藏bonus；原版文字读取本地LOD资源。下列是静态确认的问题／信息遗漏，并非本次已修复；没有列出不代表任意复杂特长均无问题。

| 对象 | 检查结果 |
|---|---|
| 珍尼 `core:gem` | 描述未明确8种药剂均按专家／纯化级施放；防御20%为独立bonus，与急救术通用成长10%并存，不构成数值冲突。 |
| 艾伦 `haven:havAhero05special` | 主描述已覆盖主要新增能力，但tooltip仍为“No description”；视野、资源和法力恢复无海上条件，应避免宣传为只在海上触发。 |
| 埃瓦希斯 `hota.cove:10eovacius` | 描述遗漏快枪猎头者杀伤+10和快枪猎手系速度+2。 |
| 爱丽丝 `courtyard:16aliceCourt` | 主描述遗漏魔法神镜50回合；tooltip仍为干扰术。 |
| 加布里埃尔 `cathedral:sveneld` | 主描述已同步主要效果，tooltip仍为旧兵种“Nerubians”。 |
| 埃莉娜 `fairy:ftP_Arina` | 主描述未写新增死亡之握／隐形及其施法权限；tooltip沿用固定投降费用-40%，不能完整解释当前等级成长配置。 |
| 艾瑞莎 `fairy:ftO_Elrisa` | “所有1—3级”未说明专属隐形等排除项及保留的4／5级法术 |
| 技能 `core:archery` | 未同步：LOD描述仍为10%／25%／50%，配置15%／30%／60%。 |
| 技能 `core:diplomacy` | 需复核：核心改动包含多项外交参数，野怪加入并非简单固定百分比；保留实际三级描述与效果供对照。 |
| 技能 `core:learning` | 主要数值和源力赠法已同步；专家还保留恶魔血统联动标记，不能仅凭文案判定全部跨模组联动。 |
| 技能 `core:logistics` | 未同步：LOD描述仍为10%／20%／30%，配置20%／40%／80%。 |
| 技能 `core:necromancy` | 未同步：描述10%／15%／20%，配置15%／30%／60%。 |
| 技能 `core:pathfinding` | 部分不符：中级最低消耗文案70与main2=-20不一致，基准100下应为80；需实机验证结算。 |
| 技能 `core:scholar` | 传法3／4／5、经验20%／40%／80%及符文学前提已同步；专家保留的玩家经验光环100%未写入本段描述。 |
| 技能 `core:sorcery` | 三级伤害10%／25%／50%与描述一致。 |
| 法术 `asylum:hornSpell` | 永久召唤及亡灵／非生命无效已描述，但0耗魔与极高额度未写明。 |
| 法术 `core:antiMagic` | 当前专家范围X结构已修复；继承说明仍按原版单目标表述，需同步群体范围。 |
| 法术 `core:disruptingRay` | 不完整：专家群体范围需要同步到说明；效果仍是永久累积减防。 |
| 法术 `core:fortune` | 未同步：继承描述与2／4／8的新数值不一致。 |
| 法术 `core:magicMirror` | 未同步：当前中级80／专家99且群体，原说明不能代表新效果。 |
| 法术 `core:mirth` | 未同步：继承描述与2／4／8的新数值不一致。 |
| 法术 `core:misfortune` | 未同步：继承描述与-2／-4／-8的新数值不一致。 |
| 法术 `core:protectAir` | 未同步：专家实际90%，继承描述仍为50%。 |
| 法术 `core:protectEarth` | 未同步：专家实际90%，继承描述仍为50%。 |
| 法术 `core:protectFire` | 未同步：专家实际90%，继承描述仍为50%。 |
| 法术 `core:protectWater` | 未同步：专家实际90%，继承描述仍为50%。 |
| 法术 `core:sacrifice` | 未同步：仍继承原版牺牲复活说明，实际改为百分比伤害；亡灵限制也仍存在。 |
| 法术 `core:slayer` | 范围已修为X；专家群体范围未在原版说明完整体现。 |
| 法术 `core:sorrow` | 未同步：继承描述与-2／-4／-8的新数值不一致。 |
| 法术 `courtyard:quagmire` | 未同步：效果持续3／6／12回合，描述1／2／3回合。 |
| 法术 `extra creatures specialty:summonBoneHook` | 需复核：描述允许死灵，但仍排除NON_LIVING；对同时带此标记的死灵目标不可用。 |
| 法术 `newoldspells+:regeneration` | 不完整：25／50／75再生已描述，漏写施法当下的永久复活治疗，不能误写为每回合复活。 |
| 法术 `preserve:choir` | 各熟练度已同步为士气+1、幸运+1、减耗5。 |

### 已关闭的历史风险


- antiMagic专家小写x与magicMirror嵌套错误已不在当前配置；仍有说明未同步范围／概率的问题，二者分开记。
- 艾格瑞奈尔两个施法次数bonus已分设stacking，修复已提交b7d871f9，不是“当前未提交”。
- 复活帐篷失败试验已删除；未把它和当前的不朽药剂复活混为一谈。

### 仍需验收

- 静态检查／构建成功／部署哈希一致不代表实战通过；英雄开场施法、跨模组图标、旧存档初始化、学者小屋三选一、帐篷／药剂联动和中文保存回填仍需游戏内复核。
- 历史部分配置曾无法按当前JSONC规则解析，详细列表在末尾。已保留对应提交／文件索引，未捏造无法解析时的完整状态。

## 提交与覆盖索引

### 主游戏功能提交溯源

| 日期 | 提交 | 原始说明 |
|---|---|---|
| 2026-11-12 | `da29b328` | update lynn |
| 2026-11-12 | `498be78f` | update renata |
| 2026-11-11 | `56be0335` | Update mod.json |
| 2026-11-08 | `138763f2` | update save issue |
| 2026-11-08 | `7bc983db` | gem |
| 2026-11-08 | `61910a66` | update catherine |
| 2026-11-06 | `29bcb8b8` | update |
| 2026-11-05 | `e6be84b8` | Add daily backpack artifact specialty to Viola |
| 2026-11-03 | `e9ce2f78` | Record preview repair upload and blocked Actions dispatch |
| 2026-11-03 | `100883de` | update |
| 2026-11-01 | `290bd94f` | update |
| 2026-11-01 | `c4cc6cc2` | update |
| 2026-11-01 | `cd56a7dd` | Create statBoost.png |
| 2026-10-27 | `f63bb652` | update saves |
| 2026-10-27 | `fd572c8c` | update |
| 2026-09-30 | `ad3c35fe` | Update Azarias.json |
| 2026-09-30 | `ba5717ca` | update 亚撒利亚 |
| 2026-09-26 | `b7d871f9` | Update S_Aegrenal.json |
| 2026-09-20 | `659ed492` | Update sorcery.json |
| 2026-09-20 | `5f3feea3` | 艾格瑞奈尔 |
| 2026-09-20 | `7fb29ac2` | Update cathedral.json |
| 2026-09-14 | `ae2a7980` | Update M_norman.json |
| 2026-09-14 | `12be5fe6` | Update M_norman.json |
| 2026-09-07 | `807c7503` | update |
| 2026-08-26 | `0ac2867b` | update battle ai |
| 2026-08-25 | `986a4991` | update cheats and saves |
| 2026-08-09 | `0fdf143e` | Update Ekaterina.json |
| 2026-08-03 | `7e77dfde` | Update timed.json |
| 2026-08-02 | `f51e1724` | update |
| 2026-08-02 | `b9376775` | upta |
| 2026-07-20 | `df8983f9` | update |
| 2026-07-19 | `b2d8167b` | update |
| 2026-07-15 | `562fb433` | update |
| 2026-07-06 | `2029faf8` | Update ft2_meri.json |
| 2026-07-05 | `de19c26d` | Update mod.json |
| 2026-07-05 | `ba4545a7` | Update Elspeth.json |
| 2026-07-05 | `c319c49b` | update |
| 2026-07-03 | `74f7de15` | Update sacrificeSelfkill.json |
| 2026-07-03 | `4f7ffbb6` | Update other.json |
| 2026-06-29 | `78f6d0bb` | Update other.json |
| 2026-06-28 | `3b04a75d` | Update sacrificeSelfkill.json |
| 2026-06-22 | `797ce8ad` | uodate |
| 2026-06-20 | `d20e3775` | update |
| 2026-06-19 | `353602c5` | update some |
| 2026-06-17 | `c5381542` | Update P_renata.json |
| 2026-06-17 | `597ad94e` | update P_renata |
| 2026-06-17 | `a932a0c7` | Update mod.json |
| 2026-06-17 | `3a4ab7cf` | update |
| 2026-06-15 | `2b79bc80` | feed pig |
| 2026-06-14 | `53b7b675` | Update P_renata.json |
| 2026-06-14 | `2ad83746` | 10eovacius |
| 2026-06-14 | `8448fdc5` | update log |
| 2026-06-14 | `da0ddbc3` | chang folder name |
| 2026-06-14 | `3731a2cd` | 14Ekaterina |
| 2026-06-13 | `e69928e3` | Update timed.json |
| 2026-06-13 | `9ae0792d` | Update timed.json |
| 2026-06-13 | `69504b0e` | rollback timed.json |
| 2026-06-13 | `f9592afc` | upDATE |
| 2026-06-13 | `892590ca` | update |
| 2026-06-12 | `0d300d72` | Update ft12_seliva.json |
| 2026-06-12 | `15186f5b` | update severals |
| 2026-06-09 | `21646fe0` | update |
| 2026-06-07 | `72600897` | update spells |
| 2026-06-07 | `654fd487` | update |
| 2026-06-07 | `7cca90ae` | update skills |
| 2026-06-07 | `dc12b4ec` | update skills |
| 2026-06-06 | `61f68f5b` | update ciyuanzhimen |
| 2026-06-05 | `d43a11e7` | update ghost |
| 2026-06-04 | `a851c5f7` | update cheats |
| 2026-06-03 | `7b3343ef` | initialized |

### 配置文件改动覆盖索引

以下索引收录初始化后的JSON增删改，重命名提交da0ddbc3单独视为目录迁移，不把全目录搬运算作新玩法。实体项已在正文按ID归并；manifest、职业、图标／设置等归入加载／全局／界面章节。A为新增，M为修改，D为删除。

| 当前或历史文件 | 可见提交 | 当前注册情况 |
|---|---|---|
| [Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/classes/magicHero.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/classes/magicHero.json>) | `3a4ab7cf`(M) | courtyard/heroClasses |
| [Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/heroes/AliceCourt.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/heroes/AliceCourt.json>) | `b9376775`(M) | courtyard/heroes |
| [Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/heroes/Ekaterina.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/heroes/Ekaterina.json>) | `3731a2cd`(M)、`0fdf143e`(M) | courtyard/heroes |
| [Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/heroes/heroTexts.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/heroes/heroTexts.json>) | `3731a2cd`(M) | courtyard/heroes |
| [Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/quagmire.json](<Hero3Expasion1.83/Mods/Courtyard/Content/config/courtyard/spells/quagmire.json>) | `353602c5`(M) | courtyard/spells |
| [Hero3Expasion1.83/Mods/Courtyard/mod.json](<Hero3Expasion1.83/Mods/Courtyard/mod.json>) | `3a4ab7cf`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/Mods/Deathvalley/Content/config/deathvalley/heroes/M_norman.json](<Hero3Expasion1.83/Mods/Deathvalley/Content/config/deathvalley/heroes/M_norman.json>) | `12be5fe6`(M)、`ae2a7980`(M) | deathvalley/heroes |
| [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft12_seliva.json](<Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft12_seliva.json>) | `15186f5b`(M)、`0d300d72`(M)、`353602c5`(M)、`d20e3775`(M) | fairy/heroes |
| [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft15_elrisa.json](<Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft15_elrisa.json>) | `654fd487`(M)、`72600897`(M)、`138763f2`(M) | fairy/heroes |
| [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json](<Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft16_arina.json>) | `7cca90ae`(M)、`15186f5b`(M)、`f9592afc`(M)、`f51e1724`(M) | fairy/heroes |
| [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft2_meri.json](<Hero3Expasion1.83/Mods/Fairy/content/config/fairy/heroes/ft2_meri.json>) | `f9592afc`(M)、`2029faf8`(M) | fairy/heroes |
| [Hero3Expasion1.83/Mods/Fairy/content/config/fairy/spells/heroBansheeInvisibility.json](<Hero3Expasion1.83/Mods/Fairy/content/config/fairy/spells/heroBansheeInvisibility.json>) | `f51e1724`(A) | fairy/spells |
| [Hero3Expasion1.83/Mods/Fairy/mod.json](<Hero3Expasion1.83/Mods/Fairy/mod.json>) | `21646fe0`(M)、`353602c5`(M)、`f51e1724`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Azarias.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Azarias.json>) | `ad3c35fe`(M) | new pavilion.new pavillion/heroes |
| [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Bast.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Bast.json>) | `df8983f9`(M) | new pavilion.new pavillion/heroes |
| [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Elspeth.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Elspeth.json>) | `c319c49b`(M)、`ba4545a7`(M)、`df8983f9`(M)、`29bcb8b8`(M) | new pavilion.new pavillion/heroes |
| [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Varn.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Varn.json>) | `df8983f9`(M) | new pavilion.new pavillion/heroes |
| [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Zamonth.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/heroesPavilion/Zamonth.json>) | `df8983f9`(M) | new pavilion.new pavillion/heroes |
| [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/spellsPavilion/learningPyramids.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/Content/spellsPavilion/learningPyramids.json>) | `353602c5`(M) | new pavilion.new pavillion/skills |
| [Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/mod.json](<Hero3Expasion1.83/Mods/New Pavilion/Mods/New Pavillion/mod.json>) | `de19c26d`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/Mods/NewOldSpells+/Content/config/NewOldSpells/Regeneration.json](<Hero3Expasion1.83/Mods/NewOldSpells+/Content/config/NewOldSpells/Regeneration.json>) | `562fb433`(M) | newoldspells+/spells |
| [Hero3Expasion1.83/Mods/asylum/content/config/asylum/NewOldSpells/hornSpell.json](<Hero3Expasion1.83/Mods/asylum/content/config/asylum/NewOldSpells/hornSpell.json>) | `892590ca`(A)、`797ce8ad`(M) | asylum/spells |
| [Hero3Expasion1.83/Mods/asylum/content/config/asylum/creatures/shadowmare.json](<Hero3Expasion1.83/Mods/asylum/content/config/asylum/creatures/shadowmare.json>) | `2b79bc80`(M) | asylum/creatures |
| [Hero3Expasion1.83/Mods/asylum/content/config/asylum/heroes/K_viola.json](<Hero3Expasion1.83/Mods/asylum/content/config/asylum/heroes/K_viola.json>) | `892590ca`(M) | asylum/heroes |
| [Hero3Expasion1.83/Mods/asylum/content/config/asylum/heroes/P_lynn.json](<Hero3Expasion1.83/Mods/asylum/content/config/asylum/heroes/P_lynn.json>) | `da29b328`(M) | asylum/heroes |
| [Hero3Expasion1.83/Mods/asylum/mod.json](<Hero3Expasion1.83/Mods/asylum/mod.json>) | `892590ca`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/Mods/cathedral/Content/config/cathedral/classes/cthbishop.json](<Hero3Expasion1.83/Mods/cathedral/Content/config/cathedral/classes/cthbishop.json>) | `b9376775`(M) | cathedral/heroClasses |
| [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/bonuses/dailyArtifacts.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/bonuses/dailyArtifacts.json>) | `e6be84b8`(A) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/creatures/creatureAbilities.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/creatures/creatureAbilities.json>) | `d20e3775`(M) | extra creatures specialty/creatures |
| [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/castle.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/castle.json>) | `61910a66`(M) | extra creatures specialty/heroes |
| [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/cathedral.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/cathedral.json>) | `b9376775`(M)、`7fb29ac2`(M) | extra creatures specialty/heroes |
| [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/courtyard.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/courtyard.json>) | `b9376775`(M) | extra creatures specialty/heroes |
| [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/pavilion.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/pavilion.json>) | `df8983f9`(M)、`b9376775`(M)、`ba5717ca`(M)、`29bcb8b8`(M) | extra creatures specialty/heroes |
| [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/rampart.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/rampart.json>) | `7bc983db`(M) | extra creatures specialty/heroes |
| [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/spellHero.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/spellHero.json>) | `e6be84b8`(M) | extra creatures specialty/heroes |
| [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/sacrificeSelfkill.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/sacrificeSelfkill.json>) | `2b79bc80`(A)、`3b04a75d`(M)、`74f7de15`(M)、`df8983f9`(M)、`b9376775`(M) | extra creatures specialty/spells |
| [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/summonBoneHook.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/summonBoneHook.json>) | `d20e3775`(A) | extra creatures specialty/spells |
| [Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/viewAirStatBuildings.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/spells/viewAirStatBuildings.json>) | `29bcb8b8`(A) | extra creatures specialty/spells |
| [Hero3Expasion1.83/Mods/extra creatures specialty/mod.json](<Hero3Expasion1.83/Mods/extra creatures specialty/mod.json>) | `2b79bc80`(M)、`d20e3775`(M)、`e6be84b8`(M)、`29bcb8b8`(M)、`61910a66`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/Mods/haven/content/config/haven/heroes/havAhero05B.json](<Hero3Expasion1.83/Mods/haven/content/config/haven/heroes/havAhero05B.json>) | `c319c49b`(M) | haven/heroes |
| [Hero3Expasion1.83/Mods/hota/Mods/bulwark/content/config/hota/bulwark/heroes/creyle.json](<Hero3Expasion1.83/Mods/hota/Mods/bulwark/content/config/hota/bulwark/heroes/creyle.json>) | `dc12b4ec`(M) | hota.bulwark/heroes |
| [Hero3Expasion1.83/Mods/hota/Mods/bulwark/content/config/hota/bulwark/heroes/dhuin.json](<Hero3Expasion1.83/Mods/hota/Mods/bulwark/content/config/hota/bulwark/heroes/dhuin.json>) | `dc12b4ec`(M) | hota.bulwark/heroes |
| [Hero3Expasion1.83/Mods/hota/Mods/bulwark/content/config/hota/bulwark/heroes/eikthurn.json](<Hero3Expasion1.83/Mods/hota/Mods/bulwark/content/config/hota/bulwark/heroes/eikthurn.json>) | `dc12b4ec`(M) | hota.bulwark/heroes |
| [Hero3Expasion1.83/Mods/hota/Mods/bulwark/content/config/hota/bulwark/heroes/kriv.json](<Hero3Expasion1.83/Mods/hota/Mods/bulwark/content/config/hota/bulwark/heroes/kriv.json>) | `dc12b4ec`(M) | hota.bulwark/heroes |
| [Hero3Expasion1.83/Mods/hota/Mods/bulwark/content/config/hota/bulwark/heroes/kynr.json](<Hero3Expasion1.83/Mods/hota/Mods/bulwark/content/config/hota/bulwark/heroes/kynr.json>) | `dc12b4ec`(M) | hota.bulwark/heroes |
| [Hero3Expasion1.83/Mods/hota/Mods/bulwark/content/config/hota/bulwark/heroes/spadum.json](<Hero3Expasion1.83/Mods/hota/Mods/bulwark/content/config/hota/bulwark/heroes/spadum.json>) | `dc12b4ec`(M) | hota.bulwark/heroes |
| [Hero3Expasion1.83/Mods/hota/Mods/cove/Content/config/hota/cove/heroes/eovacius.json](<Hero3Expasion1.83/Mods/hota/Mods/cove/Content/config/hota/cove/heroes/eovacius.json>) | `2ad83746`(M) | hota.cove/heroes |
| [Hero3Expasion1.83/Mods/hota/Mods/cove/mod.json](<Hero3Expasion1.83/Mods/hota/Mods/cove/mod.json>) | `2ad83746`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/Mods/hota/Mods/gameBalance/content/config/hotaGameBalance/skillHotA.json](<Hero3Expasion1.83/Mods/hota/Mods/gameBalance/content/config/hotaGameBalance/skillHotA.json>) | `353602c5`(M) | hota.gamebalance/skills |
| [Hero3Expasion1.83/Mods/hota/Mods/gameBalance/content/config/hotaGameBalance/skills.json](<Hero3Expasion1.83/Mods/hota/Mods/gameBalance/content/config/hotaGameBalance/skills.json>) | `353602c5`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json](<Hero3Expasion1.83/Mods/hota/Mods/gameBalance/mod.json>) | `dc12b4ec`(M)、`2b79bc80`(M)、`a932a0c7`(M)、`df8983f9`(M)、`56be0335`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/Mods/hota/Mods/heroes/Content/config/necropolis/heroes/boyd.json](<Hero3Expasion1.83/Mods/hota/Mods/heroes/Content/config/necropolis/heroes/boyd.json>) | `dc12b4ec`(M) | hota.heroes/heroes |
| [Hero3Expasion1.83/Mods/hota/Mods/neutralCreatures/content/config/artiSpells/HornSpell.json](<Hero3Expasion1.83/Mods/hota/Mods/neutralCreatures/content/config/artiSpells/HornSpell.json>) | `797ce8ad`(M) | hota.neutralcreatures/spells |
| [Hero3Expasion1.83/Mods/hota/Mods/spellResearch/mod.json](<Hero3Expasion1.83/Mods/hota/Mods/spellResearch/mod.json>) | `21646fe0`(M)、`15186f5b`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/Mods/new-menu/Content/Maps/Expansion/campaign-5 - Copy.vcmp/header.json](<Hero3Expasion1.83/Mods/new-menu/Content/Maps/Expansion/campaign-5 - Copy.vcmp/header.json>) | `562fb433`(A)、`b2d8167b`(D) | 当前文件不存在；历史删除或改名 |
| [Hero3Expasion1.83/Mods/preserve/content/config/preserve/NewOldSpells/choir.json](<Hero3Expasion1.83/Mods/preserve/content/config/preserve/NewOldSpells/choir.json>) | `5f3feea3`(M) | preserve/spells |
| [Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json](<Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json>) | `892590ca`(M)、`f9592afc`(M)、`8448fdc5`(M)、`2ad83746`(M)、`53b7b675`(M)、`597ad94e`(M)、`c5381542`(M)、`d20e3775`(M)、`797ce8ad`(M)、`498be78f`(M) | preserve/heroes |
| [Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/S_Aegrenal.json](<Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/S_Aegrenal.json>) | `5f3feea3`(M)、`b7d871f9`(M) | preserve/heroes |
| [Hero3Expasion1.83/Mods/preserve/content/config/preserve/town.json](<Hero3Expasion1.83/Mods/preserve/content/config/preserve/town.json>) | `498be78f`(M) | preserve/factions |
| [Hero3Expasion1.83/Mods/preserve/mod.json](<Hero3Expasion1.83/Mods/preserve/mod.json>) | `f9592afc`(M)、`2ad83746`(M)、`597ad94e`(M)、`797ce8ad`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/navigation.json](<Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/navigation.json>) | `c319c49b`(M) | 未在当前实体注册表中找到 |
| [Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/scholar.json](<Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/scholar.json>) | `d20e3775`(M)、`bed82f2f`(M)、`29bcb8b8`(M) | tidesofwar/skills |
| [Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/scouting.json](<Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/scouting.json>) | `353602c5`(M) | tidesofwar/skills |
| [Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/sorcery.json](<Hero3Expasion1.83/Mods/tidesOfWar/content/config/skills/sorcery.json>) | `659ed492`(M) | tidesofwar/skills |
| [Hero3Expasion1.83/Mods/tidesOfWar/content/config/spells/summonGhost/summonGhost.json](<Hero3Expasion1.83/Mods/tidesOfWar/content/config/spells/summonGhost/summonGhost.json>) | `d43a11e7`(M) | tidesofwar/spells |
| [Hero3Expasion1.83/Mods/vcmi/Content/config/translations/chinese.json](<Hero3Expasion1.83/Mods/vcmi/Content/config/translations/chinese.json>) | `100883de`(M)、`bed82f2f`(M)、`29bcb8b8`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/Mods/vcmi/Content/config/translations/english.json](<Hero3Expasion1.83/Mods/vcmi/Content/config/translations/english.json>) | `100883de`(M)、`bed82f2f`(M)、`29bcb8b8`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/config/artifacts.json](<Hero3Expasion1.83/config/artifacts.json>) | `a851c5f7`(M) | core/artifacts |
| [Hero3Expasion1.83/config/cheats.json](<Hero3Expasion1.83/config/cheats.json>) | `986a4991`(M)、`fd572c8c`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/config/creatures/inferno.json](<Hero3Expasion1.83/config/creatures/inferno.json>) | `2b79bc80`(M) | core/creatures |
| [Hero3Expasion1.83/config/difficulty.json](<Hero3Expasion1.83/config/difficulty.json>) | `15186f5b`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/config/gameConfig.json](<Hero3Expasion1.83/config/gameConfig.json>) | `654fd487`(M)、`15186f5b`(M)、`3a4ab7cf`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/config/heroes/special.json](<Hero3Expasion1.83/config/heroes/special.json>) | `df8983f9`(M)、`61910a66`(M) | core/heroes |
| [Hero3Expasion1.83/config/schemas/settings.json](<Hero3Expasion1.83/config/schemas/settings.json>) | `100883de`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |
| [Hero3Expasion1.83/config/skills.json](<Hero3Expasion1.83/config/skills.json>) | `dc12b4ec`(M)、`7cca90ae`(M)、`353602c5`(M)、`29bcb8b8`(M) | core/skills |
| [Hero3Expasion1.83/config/spells/adventure.json](<Hero3Expasion1.83/config/spells/adventure.json>) | `61f68f5b`(M)、`797ce8ad`(M)、`fd572c8c`(M)、`29bcb8b8`(M) | core/spells |
| [Hero3Expasion1.83/config/spells/other.json](<Hero3Expasion1.83/config/spells/other.json>) | `78f6d0bb`(M)、`4f7ffbb6`(M) | core/spells |
| [Hero3Expasion1.83/config/spells/timed.json](<Hero3Expasion1.83/config/spells/timed.json>) | `21646fe0`(M)、`15186f5b`(M)、`f9592afc`(M)、`69504b0e`(M)、`9ae0792d`(M)、`e69928e3`(M)、`3731a2cd`(M)、`3a4ab7cf`(M)、`d20e3775`(M)、`7e77dfde`(M) | core/spells |
| [Hero3Expasion1.83/config/widgets/settings/adventureOptionsTab.json](<Hero3Expasion1.83/config/widgets/settings/adventureOptionsTab.json>) | `100883de`(M) | manifest／全局／资源或其他非实体入口，不能直接按实体列表判断 |

### 独立引擎提交

引擎仓库位于temp/vcmi-source-1.7.4，受主仓库ignore排除；分支fix/magic-fader-battle-ai-1.7.4，本地与fork同名跟踪分支均指向acc433e1e。浅克隆只能证明可见历史。

| 提交 | 功能 |
|---|---|
| `4dbf5dc25`、`f5101639a` | BattleAI在法术等级抑制下拒绝／重试循环修复 |
| `6fd273424`、`6dea3d573`、`1d542bfc7` | Windows 2022 runner、独立MSVC x64构建 |
| `c21ade8df` | 透视大气属性建筑独立图标 |
| `a2d42de74` | 初版地图物体只读预览（被后续修复取代） |
| `4dd431c45` | 保留原生窗口后追加详情、布局及回归检查 |
| `83cda461f` | 每日背包随机宝物 |
| `67cc593b2` | 学术经验追赶延迟查询 |
| `acc433e1e` | 中文存档文件名UTF-8回填 |

### 历史解析限制

- `892590ca`，[Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json](<Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json>)：旧版JSONC无法完整解析（`Extra data: line 147 column 1 (char 2658)`）。不影响当前快照；该提交详情需用原始git diff进一步追溯。

<details>
<summary>892590ca 无法结构化的历史原始差异（保留原文）</summary>

```diff
commit 892590cadf060d579d352dc7c1b961eaaaf590e2
Author:     CanadaWorker2025 <vincent2016.liu@gmail.com>
AuthorDate: Sat Jun 13 00:44:52 2026 -0700
Commit:     CanadaWorker2025 <vincent2016.liu@gmail.com>
CommitDate: Sat Jun 13 00:44:52 2026 -0700

    update

diff --git a/Heroes of Might and Magic III Expansion 1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json b/Heroes of Might and Magic III Expansion 1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json
index 58c3b33e..5a43deb8 100644
--- a/Heroes of Might and Magic III Expansion 1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json	
+++ b/Heroes of Might and Magic III Expansion 1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json	
@@ -10,7 +10,7 @@
 			"biography" : "外表看起来蕾娜塔是个乐观而又活泼的女子，和她在一起的人都会觉得她能够带来喜悦的气氛。但是这并不代表她没有悲伤的往事。夜深之时有人会看到她独自弹奏曲子，寄予思念她从小失散的妹妹，琳恩。",
 			"specialty" :
 			{
-				"description" : "作战开场即施放欢欣鼓舞魔法。",
+				"description" : "作战开场即施放欢欣鼓舞魔法。隐藏技能:死骑攻防杀伤+15速度+2,同时可以把骑士升级为死骑",
 				"tooltip" : "Bonus: spell",
 				"name" : "欢欣鼓舞"
 			}
@@ -59,13 +59,23 @@
 		],
 		"specialty": 
 		{
+			"base" : {
+				"addInfo" : "creature.dreadKnight",
+				"type" : "SPECIAL_UPGRADE"
+			},
 			"bonuses":
 			{
+				"champion2dreadKnight": { "subtype": "creature.champion" },
+				"cavalier2dreadKnight": { "subtype": "creature.cavalier" },
+				//"ffpaladin2dreadKnight": { "subtype": "creature.ffpaladin" },
+				//"cthPaladin2dreadKnight": { "subtype": "creature.cthPaladin" },
+				//"cthTemplar2dreadKnight": { "subtype": "creature.cthTemplar" },
+
 				"choir":
 				{
 					"type": "OPENING_BATTLE_SPELL",
 					"subtype": "spell.mirth",
-					"val" : 3,
+					"val" : 10,
 					"valueType" : "BASE_NUMBER"
 				},
 				"bind" : 
@@ -74,7 +84,63 @@
 				"type" : "SPELL",
 				"val" : 1,
 				"valueType" : "BASE_NUMBER"
+				},
+					"damage" : 
+					{
+						"type" : "CREATURE_DAMAGE",
+						"subtype" : "creatureDamageBoth",
+						"val" : 15,
+						"limiters" : 
+						[
+							{
+								"type" : "CREATURE_TYPE_LIMITER",
+								"creature" : "blackKnight",
+								"includeUpgrades" : true
+							}
+						]
+					},
+				"attack" : 
+				{
+					"type" : "PRIMARY_SKILL",
+					"subtype" : "primarySkill.attack",
+					"val" : 15,
+					"limiters" : 
+					[
+						{
+							"type" : "CREATURE_TYPE_LIMITER",
+							"creature" : "blackKnight",
+							"includeUpgrades" : true
+						}
+					]
+				},
+				"defence" : 
+				{
+					"type" : "PRIMARY_SKILL",
+					"subtype" : "primarySkill.defence",
+					"val" : 15,
+					"limiters" : 
+					[
+						{
+							"type" : "CREATURE_TYPE_LIMITER",
+							"creature" : "blackKnight",
+							"includeUpgrades" : true
+						}
+					]
+				},
+				"speed" : 
+				{
+					"type" : "STACKS_SPEED",
+					"val" : 2,
+					"limiters" : 
+					[
+						{
+							"type" : "CREATURE_TYPE_LIMITER",
+							"creature" : "blackKnight",
+							"includeUpgrades" : true
+						}
+					]
 				}
+		}
 			}
 		}
 	}
```

</details>

- `f9592afc`，[Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json](<Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json>)：旧版JSONC无法完整解析（`Extra data: line 147 column 1 (char 2658)`）。不影响当前快照；该提交详情需用原始git diff进一步追溯。

<details>
<summary>f9592afc 无法结构化的历史原始差异（保留原文）</summary>

```diff
commit f9592afc3bfa5e75a088a7f58333aedec6f7ecc1
Author:     CanadaWorker2025 <vincent2016.liu@gmail.com>
AuthorDate: Sat Jun 13 01:15:32 2026 -0700
Commit:     CanadaWorker2025 <vincent2016.liu@gmail.com>
CommitDate: Sat Jun 13 01:15:32 2026 -0700

    upDATE

diff --git a/Heroes of Might and Magic III Expansion 1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json b/Heroes of Might and Magic III Expansion 1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json
index 5a43deb8..5f1d6912 100644
--- a/Heroes of Might and Magic III Expansion 1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json	
+++ b/Heroes of Might and Magic III Expansion 1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json	
@@ -67,9 +67,9 @@
 			{
 				"champion2dreadKnight": { "subtype": "creature.champion" },
 				"cavalier2dreadKnight": { "subtype": "creature.cavalier" },
-				//"ffpaladin2dreadKnight": { "subtype": "creature.ffpaladin" },
-				//"cthPaladin2dreadKnight": { "subtype": "creature.cthPaladin" },
-				//"cthTemplar2dreadKnight": { "subtype": "creature.cthTemplar" },
+				"ffpaladin2dreadKnight": { "subtype": "tidesofwar:ffpaladin" },
+				"cthPaladin2dreadKnight": { "subtype": "cathedral:cthPaladin" },
+				"cthTemplar2dreadKnight": { "subtype": "cathedral:cthTemplar" },
 
 				"choir":
 				{
```

</details>

- `f9592afc`，[Hero3Expasion1.83/config/spells/timed.json](<Hero3Expasion1.83/config/spells/timed.json>)：旧版JSONC无法完整解析（`Extra data: line 353 column 3 (char 5740)`）。不影响当前快照；该提交详情需用原始git diff进一步追溯。

<details>
<summary>f9592afc 无法结构化的历史原始差异（保留原文）</summary>

```diff
commit f9592afc3bfa5e75a088a7f58333aedec6f7ecc1
Author:     CanadaWorker2025 <vincent2016.liu@gmail.com>
AuthorDate: Sat Jun 13 01:15:32 2026 -0700
Commit:     CanadaWorker2025 <vincent2016.liu@gmail.com>
CommitDate: Sat Jun 13 01:15:32 2026 -0700

    upDATE

diff --git a/Heroes of Might and Magic III Expansion 1.83/config/spells/timed.json b/Heroes of Might and Magic III Expansion 1.83/config/spells/timed.json
index 03750769..275aabc2 100644
--- a/Heroes of Might and Magic III Expansion 1.83/config/spells/timed.json	
+++ b/Heroes of Might and Magic III Expansion 1.83/config/spells/timed.json	
@@ -306,6 +306,7 @@
 				}
 			},
 			"expert":{
+				"range" : "x",
 				"battleEffects":{
 					"spellImmunity":{
 						"bonus":{
@@ -332,15 +333,17 @@
 			"cast": "BACKLASH"
 		},
 		"levels" : {
-			"base":{
-				"range" : "0",
-				"targetModifier":{"smart":true},
-				"effects" : {
-					"magicMirror" : {
-						"type" : "MAGIC_MIRROR",
-						"valueType" : "INDEPENDENT_MAX",
-						"duration" : "N_TURNS"
-					}
+				"base":{
+					"range" : "0",
+					"targetModifier":{"smart":true},
+					"effects" : {
+						"magicMirror" : {
+							"type" : "MAGIC_MIRROR",
+							"valueType" : "INDEPENDENT_MAX",
+							"duration" : "N_TURNS"
+						}
+					},
+					"expert":{"range" : "X"}
 				}
 			}
 		},
@@ -1099,7 +1102,7 @@
 				}
 			},
 			"expert":{
-				"range" : "x",
+				"range" : "X",
 				"effects" : {
 					"slayer" : {
 						"val" : 3
```

</details>

- `69504b0e`，[Hero3Expasion1.83/config/spells/timed.json](<Hero3Expasion1.83/config/spells/timed.json>)：旧版JSONC无法完整解析（`Extra data: line 353 column 3 (char 5740)`）。不影响当前快照；该提交详情需用原始git diff进一步追溯。

<details>
<summary>69504b0e 无法结构化的历史原始差异（保留原文）</summary>

```diff
commit 69504b0e8d37cf104dd0cf44b85e5d35ce26aeb8
Author:     CanadaWorker2025 <vincent2016.liu@gmail.com>
AuthorDate: Sat Jun 13 11:08:02 2026 -0700
Commit:     CanadaWorker2025 <vincent2016.liu@gmail.com>
CommitDate: Sat Jun 13 11:08:02 2026 -0700

    rollback timed.json

diff --git a/Heroes of Might and Magic III Expansion 1.83/config/spells/timed.json b/Heroes of Might and Magic III Expansion 1.83/config/spells/timed.json
index 275aabc2..30e6b324 100644
--- a/Heroes of Might and Magic III Expansion 1.83/config/spells/timed.json	
+++ b/Heroes of Might and Magic III Expansion 1.83/config/spells/timed.json	
@@ -306,7 +306,6 @@
 				}
 			},
 			"expert":{
-				"range" : "x",
 				"battleEffects":{
 					"spellImmunity":{
 						"bonus":{
@@ -333,17 +332,15 @@
 			"cast": "BACKLASH"
 		},
 		"levels" : {
-				"base":{
-					"range" : "0",
-					"targetModifier":{"smart":true},
-					"effects" : {
-						"magicMirror" : {
-							"type" : "MAGIC_MIRROR",
-							"valueType" : "INDEPENDENT_MAX",
-							"duration" : "N_TURNS"
-						}
-					},
-					"expert":{"range" : "X"}
+			"base":{
+				"range" : "0",
+				"targetModifier":{"smart":true},
+				"effects" : {
+					"magicMirror" : {
+						"type" : "MAGIC_MIRROR",
+						"valueType" : "INDEPENDENT_MAX",
+						"duration" : "N_TURNS"
+					}
 				}
 			}
 		},
@@ -1468,4 +1465,4 @@
 		}
 
 	}
-}
+}
\ No newline at end of file
```

</details>

- `8448fdc5`，[Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json](<Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json>)：旧版JSONC无法完整解析（`Extra data: line 147 column 1 (char 2833)`）。不影响当前快照；该提交详情需用原始git diff进一步追溯。

<details>
<summary>8448fdc5 无法结构化的历史原始差异（保留原文）</summary>

```diff
commit 8448fdc5098a3a1862d259ff49af5b783466ed98
Author:     CanadaWorker2025 <vincent2016.liu@gmail.com>
AuthorDate: Sun Jun 14 12:13:32 2026 -0700
Commit:     CanadaWorker2025 <vincent2016.liu@gmail.com>
CommitDate: Sun Jun 14 12:13:32 2026 -0700

    update log

diff --git a/Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json b/Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json
index 222c9d11..25cf5604 100644
--- a/Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json
+++ b/Hero3Expasion1.83/Mods/preserve/content/config/preserve/heroes/P_renata.json
@@ -3,7 +3,6 @@
 	{
 		"class" : "bard",
 		"female" : true,
-
 		"texts" :
 		{
 			"name" : "蕾娜塔",
@@ -15,19 +14,16 @@
 				"name" : "欢欣鼓舞"
 			}
 		},
-
-    "images": {
-      "specialtySmall": "preserve/heroes/renata/UN32T195.bmp",
-      "specialtyLarge": "preserve/heroes/renata/UN44T195.bmp",
-      "large": "preserve/heroes/renata/HPL007BR.bmp",
-      "small": "preserve/heroes/renata/HPS007BR.bmp"
-    },
-    
-    "spellbook" :
-		[
-			"mirth"
-		],
-
+		"images": {
+		"specialtySmall": "preserve/heroes/renata/UN32T195.bmp",
+		"specialtyLarge": "preserve/heroes/renata/UN44T195.bmp",
+		"large": "preserve/heroes/renata/HPL007BR.bmp",
+		"small": "preserve/heroes/renata/HPS007BR.bmp"
+		},
+		"spellbook" :
+			[
+				"mirth"
+			],
 		"army" :
 		[
 			{
@@ -144,6 +140,6 @@
 		}
 	}
 }
-}
+
```

</details>


### 资料来源与维护方式

- 原始流水：[LSFupdatelog.md](<LSFupdatelog.md>)，保留不覆盖；本文件为按对象整理的最终状态手册。
- 主游戏Git历史及当前文件；独立引擎源码、已有Actions部署记录与patches补丁。构建编号／部署验收属于历史记录，本次没有重新执行构建或哈希复验。
- 英雄描述覆盖入口：[Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/specialtyDescriptions.json](<Hero3Expasion1.83/Mods/extra creatures specialty/content/config/heroes/specialtyDescriptions.json>)；每个英雄本地text不一定是最终显示文字。
- 后续修改应同时核对：配置值、有效注册链、最终说明、图标资源和新旧存档；按对象更新本手册最终状态，并在历史区追加提交来源，不把旧值当新值叠写。
