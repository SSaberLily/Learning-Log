# Unity 游戏开发学习日志 - Day 4

**日期**：2025-12-08
**主题**：2D 横版卷轴游戏基础 (Pixel Adventure)
**重点**：环境配置、角色移动、动画状态机、物理碰撞与触发器

---

## 🛠️ 一、环境与配置排雷

今天起步阶段遇到了一些因为环境配置导致的“拦路虎”，但掌握了 IDE 与 Unity 交互的核心逻辑。

### 1. VS Code 代码提示失效 (全是白字)

- **现象**：写代码没有智能提示，全是白色文本。
- **原因**：项目路径中包含特殊字符（如 `#`）导致 Unity 无法正确生成 `.csproj` 文件。
- **解决**：
  1.  修改文件夹名为纯英文（无空格、无特殊符号）。
  2.  删除项目根目录下的 `.csproj` 和 `.sln` 文件。
  3.  Unity 菜单：`Edit` -> `Preferences` -> `External Tools` -> 点击 `Regenerate project files`。

### 2. 输入系统冲突 (Input System Conflict)

- **现象**：报错 `InvalidOperationException... switched active Input handling`。
- **原因**：代码使用了旧版 `Input.GetKey`，但项目默认启用了新版 Input System Package。
- **解决**：`Project Settings` -> `Player` -> `Active Input Handling` 改为 **Both**，并重启 Unity。

### 3. Play Mode 保存机制

- **重要教训**：**必须退出播放模式 (Play Mode) 才能保存场景！** 运行时修改的任何参数（Inspector 面板数值）在停止游戏后都会重置。

---

## 🎮 二、核心角色控制 (C# 脚本)

从零开始实现了角色的移动和跳跃，并适配了 **Unity 6** 的新特性。

### 1. 基础移动逻辑

- 使用 `Rigidbody2D` 进行物理控制。
- **Unity 6 变化**：旧版的 `velocity` 被弃用，必须使用 **`linearVelocity`**。
- **输入检测**：
  - 左右移动：`Input.GetAxisRaw("Horizontal")` (返回 -1, 0, 1)。
  - 跳跃：`Input.GetButtonDown("Jump")` (检测按下的瞬间，防止连跳)。

### 2. 角色状态机 (State Machine)

- **进化**：从最初简单的 `bool` 变量，进化为更专业的 **`Enum` (枚举)** + **`int` (整数)** 状态管理。
- **状态定义**：
  - `0`: Idle (静止)
  - `1`: Running (跑)
  - `2`: Jumping (跳)
  - `3`: Falling (落)
- **优先级逻辑**：空中状态 (Y 轴速度 > 0.1) **覆盖** 地面状态 (X 轴输入)。

---

## 🎬 三、动画系统 (Animator)

学会了如何让静态的像素小人“活”过来，并解决“拖泥带水”的手感问题。

### 1. 动画制作细节

- **采样率 (Samples)**：像素游戏动作快，通常设为 **12-15** 帧（需开启 `Show Sample Rate`）。
- **锁定窗口**：使用 Animation 窗口右上角的 **“小锁头”**，防止点选素材时丢失主角焦点。

### 2. Animator 参数配置

- 使用 `int state` 参数替代多个 bool 值。
- **像素游戏铁律**：
  - 取消勾选 `Has Exit Time`。
  - 将 `Transition Duration` (过渡时间) 设为 **0**，实现瞬间切换。

### 3. “忘保存”陷阱

- **惨痛教训**：在修改完各项设置参数之后忘记保存
- **解决**：下次记得保存

---

## 💥 四、物理系统与交互

解决了 2D 物理游戏常见的“穿模”和“旋转”问题。

### 1. 物理 Bug 修复

- **撞墙旋转 (不倒翁)**：在 `Rigidbody 2D` -> `Constraints` 中勾选 **Freeze Rotation Z**。
- **无法贴墙 (悬空)**：在 `Box Collider 2D` 中点击 **Edit Collider**，手动将绿色碰撞框缩小至贴合角色身体。
- **穿过新平台**：新拖入的图片没有物理属性，需添加 `Box Collider 2D` 或 `Tilemap Collider 2D`。

### 2. 物品拾取 (樱桃)

- **机制**：**Trigger (触发器)**。
- **设置**：
  - 樱桃的 Collider 必须勾选 **Is Trigger**。
  - Tag 设置为 `Cherry`。
- **代码**：使用 `OnTriggerEnter2D` 检测，判断 Tag 后 `Destroy` 销毁物体。
- **易错点**：方法名拼写错误 (`Origger` ❌ -> `OnTrigger` ✅)。

### 3. 死亡与重开 (陷阱)

- **机制**：**Collision (碰撞)**。
- **逻辑**：
  1.  碰到 Tag 为 `Trap` 的物体。
  2.  播放死亡动画。
  3.  触发 **Animation Event** (在动画最后一帧添加事件)。
  4.  调用 `RestartLive()` 重载场景。

---

## 📝 明日计划

- [ ] **UI 系统**：把吃到的樱桃数量显示在屏幕左上角。
- [ ] **完善流程**：彻底跑通“死亡动画 -> 重启游戏”的完整闭环。
- [ ] **关卡设计**：学习使用 Tilemap (瓦片地图) 快速绘制复杂关卡。
- [ ] **图片与音乐**：学习处理锯齿与图层，添加 bgm。
