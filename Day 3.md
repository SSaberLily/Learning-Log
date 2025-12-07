# 2025-12-07 C# 学习日志 (Day 3) - Text-RPG

> **今日目标**：通过开发“TEXT_RPG”控制台游戏，实战演练 C# 面向对象 (OOP) 的三大支柱：封装、继承、多态。

## 一、项目架构设计 (Architecture)

项目采用了经典的 面向对象 (OOP) 设计，主要分为三层：

### **数据层 (Model)：** 定义“是什么”（生物、属性）。

**数据层：** 生物继承体系 (`Creature.cs`)
这是你整个游戏的基石，利用继承和多态实现了代码复用。

`Creature` (抽象基类)

核心数据：`Hp` (生命), `Damage` (伤害), `Name` (名字), `Profession` (职业)

共享行为：`Normal_Attack` (通用的扣血逻辑)

抽象行为：`Special_Attack` (强制子类必须实现)

**⬇️ 派生**

`Hero` (抽象类)：代表玩家

新增功能：`GetExp` (升级逻辑), `LevelUpBonus` (升级加属性)

特殊逻辑：`Run()` ➡️ 直接将 `Hp` 设为 0 (触发战败)

**⬇️ 具体职业**

`Warrior`：高血量，技能是几率暴击（旋风斩）。

`Mage`：高攻击，技能也是几率暴击（大魔法）。

`Monster` (抽象类)：代表敌人

新增数据：`Exp_CanBeGet` (掉落经验)

**⬇️ 具体怪物**

`Slime`：低攻低血，技能是冲撞。

`Dragon`：BOSS 级数值，技能是烈焰吐息。

### **逻辑层 (Controller)：** 定义“怎么做”（战斗流程、回合管理）。

逻辑层：游戏流程控制 (GameLogic 类)
这是游戏的“大脑”，负责串联各个环节。

**流程步骤：**

1、 **初始化 (`line` 方法)**

调用 `GetHero()` ➡️ 创建玩家 `(Warrior/Mage)` ➡️ ⚡️ 关键逻辑：调用 `GetSpecialAbility()` 初始化数值。

调用 `GetMonster()` ➡️ 创建敌人 `(Slime/Dragon)` ➡️ ⚡️ 关键逻辑：调用 `GetSpecialAbility()` 初始化数值。

2、**战斗循环 (`StartBattle` 方法)**

条件：`while (player.Hp > 0 && monster.Hp > 0)`

玩家回合 (`PlayerTurn`)：

输入 1: 普通攻击。

输入 2: 特殊攻击。

输入 3: 逃跑 ➡️ 执行 `Run()`，`Hp` 变为 0。

判定：如果玩家 `Hp` 归零（因逃跑或被打死），跳出循环。

敌人回合 (`MonsterTurn`)：

如果怪物还活着，90% 几率普攻，10% 几率特攻。

**结算 (`GetTheEnd` 方法)**

检查 `hero.Hp`：

Case 0: 打印 "You Lose!" (包含被打死和逃跑两种情况)。

Default: 打印 "You Win!" ➡️ 调用 `hero.GetExp(monster)` 进行升级结算。

### **入口层 (Entry)：** 程序的启动点。

**入口层 (`Program.cs`)**

`Main` 类

`Main()` 方法 (注意大小写修复)：

实例化 `GameLogic`。

调用 `logic.line()` 启动游戏。

---

## 二、遇到的问题与解决方案 (Bug Report)

| 问题描述                                      | 错法                                                  | 解决方案                                                                                                                 |
| :-------------------------------------------- | :---------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------- |
| 混用了 `abstract` 和 `virtual`                | `public virtual abstract void Attack();`              | 二选一。`abstract` = 强迫子类必须写.`virtual` = 子类可以改（override），也可以不改。                                     |
| 父类“看不见”子类方法                          | 变量声明为 `Hero`，却想调用只有`Warrior` 才有的方法。 | 父类必须有“占位符”。在 Hero 里加一个 abstract 方法，编译器才允许通过父类变量调用它。运行时会自动找到子类的实现（多态）。 |
| 类体中直接写逻辑                              | 在类`Warrior`之中直接写`Hp += 1000`                   | 逻辑必须放在 方法、构造函数 或 属性访问器 里。                                                                           |
| 继承传参时再次声明类型                        | ` : BaseClass(int hp, string name)`                   | 传给父类的是值（变量名），不是定义。`: BaseClass(hp, name)`                                                              |
| 不要在循环或频繁调用的方法里 `new Random()。` | 随机数会重复（因为时间种子没变）。                    | 声明为 `static readonly Random _rnd = new Random();` 全局共用一个。                                                      |
|                                               |                                                       |                                                                                                                          |
