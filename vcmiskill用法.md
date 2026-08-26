# vcmiskill

vcmiskill pathfinding    获得 寻路（增加崎岖地形移动折扣）

vcmiskill archery        获得 弓箭（远程伤害加成）

vcmiskill logistics      获得 后勤（地面移动加成）

vcmiskill scouting       获得 侦查（视野半径）

vcmiskill diplomacy     获得 外交（野生部队加入机会/投降折扣）

vcmiskill navigation    获得 航海（海上移动加成）

vcmiskill leadership    获得 领导力（士气提升）

vcmiskill wisdom        获得 智慧（可学的最高法术等级上限）

vcmiskill mysticism     获得 神秘学（法力回复）

vcmiskill luck          获得 幸运（战斗幸运值）

vcmiskill ballistics    获得 弹道学（攻城/投石额外）

vcmiskill eagleEye      获得 鹰眼（战斗中学习法术概率/等级上限）

vcmiskill necromancy    获得 召魂术（提高死灵学效果 / 提升复活单位）

vcmiskill estates       获得 理财（城镇收益增强）

vcmiskill fireMagic     获得 火系魔法（学习/提高火系法术等级）

vcmiskill airMagic      获得 气系魔法（学习/提高气系法术等级）

vcmiskill waterMagic    获得 水系魔法（学习/提高水系法术等级）

vcmiskill earthMagic    获得 土系魔法（学习/提高土系法术等级）

vcmiskill scholar       获得 学术（增加遇到时可学法术数）

vcmiskill tactics       获得 战术（战前重新部署格数）

vcmiskill artillery     获得 炮术（攻城武器增益）

vcmiskill learning      获得 学习能力（英雄经验获得百分比提升）

vcmiskill offence       获得 近战进攻（近战伤害加成）

vcmiskill armorer       获得 防御术（通用伤害减免）

vcmiskill intelligence  获得 智力（提高每点知识带来的法力）

vcmiskill sorcery       获得 巫术/法术强化（法术伤害加成）

vcmiskill resistance    获得 抵抗（魔法抗性）

vcmiskill firstAid      获得 急救（特定治疗法术威力）

举例 vcmiskill firstAid 1  获得初级急救，1初级2中级3高级0删除

说明：
这些 `vcmiskill <skillID>` 命令可以通过 VCMI 的作弊控制台或聊天输入触发（前提是游戏允许作弊）。
技能 ID 来自 `config/skills.json`，中文名称为简短描述，具体数值和等级由 `config/skills.json` 定义。

新增扩展技能（Mod 中发现的可用 `vcmiskill` 技能 ID）：
vcmiskill intimidation  ：恐吓术（扩展技能，来自 `Mods\tidesOfWar`）
vcmiskill dv_necromancy  ：死灵术（扩展技能，来自 `Mods\Deathvalley`）
vcmiskill demonicBlood  ：恶魔血统 / 地狱的血统（扩展技能，来自 `Mods\tartarus`）
vcmiskill hexes  ：巫蛊术（扩展技能，来自 `Mods\tidesOfWar`）
vcmiskill magicFader ：法术抑制（扩展技能，来自 `Mods\tidesOfWar`）
vcmiskill runes ：符文学（扩展技能，来自 `Mods\hota\Mods\bulwark`）

注意：`core:xxx` 形式的条目通常是 mod 对基础技能的覆盖或重定义，不是新的 `vcmiskill` 命令关键字。实际可用的基础技能 ID 仍然是 `archery`、`luck`、`leadership`、`sorcery` 等。
