这是为您整理的今日 Unity 游戏开发深度学习日志。这份日志涵盖了从**底层物理机制**到**C# 内存管理**，再到**软件架构设计**的全方位知识体系复盘。

---

# 📔 Unity 游戏开发进阶学习日志

**日期**：2025 年 12 月 11 日
**核心主题**：物理系统机制、C# 内存与 OOP 架构、解耦设计模式、3D 过渡准备

---

## 一、 Unity 物理与 Layer 机制深度解析

**核心问题**：为什么陷阱不需要 LayerMask，而攻击判定需要？

### 1. 两种检测逻辑的区别

- **被动碰撞 (Passive Collision)**：
  - **机制**：`OnCollisionEnter` / `OnTriggerEnter`。
  - **逻辑**：基于物理引擎的“默认规则”。如果不干预，Default 层物体之间默认都会互相碰撞。
  - **应用**：踩陷阱、跳平台。
- **主动查询 (Active Query)**：
  - **机制**：`Physics2D.OverlapCircle`。
  - **逻辑**：代码主动发起询问。必须使用 **LayerMask** 进行“过滤”，否则会检测到自身、地板等无关物体（甚至导致自己打自己）。
  - **应用**：挥刀攻击。

### 2. 图层碰撞矩阵 (Layer Collision Matrix)

- **位置**：Project Settings -> Physics 2D。
- **作用**：决定物理世界的“法律法规”。
- **技巧**：若想实现“主角能穿过敌人身体（不卡位）”但“又能攻击敌人”，应在矩阵中取消 Player 与 Enemy 的碰撞勾选。

---

## 二、 C# 核心语言特性与内存管理 (C++ 对照版)

**核心问题**：`Vector3` 到底是什么？为什么 `Class` 和 `Struct` 不同？

### 1. Struct (结构体) vs Class (类)

- **Struct (`Vector3`, `Quaternion`)**：
  - **类型**：**值类型 (Value Type)**，存放在 **栈 (Stack)**。
  - **赋值行为**：**完全克隆**。`Vector3 b = a;` 修改 b 不会影响 a。
  - **性能**：无 GC（垃圾回收），速度快。
- **Class (`Transform`, `GameObject`)**：
  - **类型**：**引用类型 (Reference Type)**，存放在 **堆 (Heap)**。
  - **赋值行为**：**指针拷贝**。`Person b = a;` 修改 b 会影响 a。

### 2. 属性 (Property) 与 `transform.position` 陷阱

- **陷阱**：`transform.position.x = 10;` 会报错。
- **原理**：`position` 是属性（Property），其 `get` 方法返回的是内部数据的**Struct 复印件**。修改复印件的 `.x` 对原件无效，编译器拦截。
- **正解**：必须整体赋值。`transform.position = new Vector3(10, 0, 0);`（调用 `set` 方法覆盖原数据）。

### 3. 泛型与继承

- **泛型 (`<T>`)**：`GetComponent<Rigidbody2D>()`，通用模板，避免类型转换。
- **继承 (`:`)**：`Player : MonoBehaviour`，继承父类的 `transform` 访问权和生命周期函数。

---

## 三、 软件架构与解耦设计 (Decoupling)

**核心问题**：随着项目变大，如何避免代码变成“面条”？

### 1. 什么是解耦？

- **紧耦合**：A 脚本直接引用 B 脚本 (`public Enemy enemy;`)。修改 A 可能搞坏 B，多人协作冲突多。
- **解耦**：A 脚本只依赖“标准”或“事件”，不关心 B 具体是谁。

### 2. 接口 (Interface) —— 策略模式的基础

- **痛点**：主角攻击需要分别判断 `if (Slime)`、`if (Box)`。
- **解法**：定义 `interface IDamageable { void TakeDamage(); }`。
- **优势**：主角只管调用接口，不管对面是人是鬼。**解决了 C++ 菱形继承的数据冗余问题（C# 禁止多重类继承，但允许多重接口实现）。**

### 3. 事件 (Events) —— 观察者模式的基础

- **痛点**：主角死的时候要分别调用 UI、音效、敌人停止脚本。
- **解法**：主角广播 `public event Action OnDeath;`，各系统自己监听。
- **优势**：主角不需要认识 UI 和 音效管理器。

### 4. 求职 Demo 亮点建议

- **拒绝过度设计**：不要盲目引入 Zenject 等重型框架。
- **展示内功**：熟练使用 **C# 原生 Interface 和 Events** 进行解耦，体现 SOLID 原则和设计模式理解。

---

## 四、 游戏设计理论 (Game Design)

**核心问题**：一个完整的 Demo 应该包含什么？

- **核心循环**：战斗 -> 收集 -> 变强/通关。
- **3C**：角色(Character)、控制(Control)、摄像机(Camera) 的手感打磨。
- **Flow (流程)**：主菜单 -> 游戏 -> 暂停 -> 结算/失败界面。
- **Juice (多汁感)**：屏幕震动、粒子特效、**音效 (Audio)**（目前项目缺失）。

---

## 五、 下一阶段：进军 3D 世界

**状态**：2D 基础已毕业，准备进入 3D。

### 1. 2D 转 3D 技能映射

- Sprite Renderer ➡️ **Mesh Renderer**
- Sorting Layer ➡️ **Depth (Z 轴深度)**
- Physics 2D ➡️ **Physics (3D)**
- Vector2 ➡️ **Vector3 (左手坐标系)**
