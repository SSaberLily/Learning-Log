这是一份为您整理的完整 Markdown 格式的学习日志，涵盖了今天（2025-12-10）所有解决的技术问题、代码实现和理论知识。您可以直接复制保存到您的笔记软件中。

---

# 📅 2025-12-10 Unity 2D 游戏开发学习日志

## 1\. 新增功能实现 (Features)

### 🏃‍♂️ 1.1 移动浮空平台 (Moving Platform)

- **功能目标**：创建一个在 A 点和 B 点之间自动往返的平台，且角色站上去后能跟随移动 。
- **核心逻辑**：
  - 使用 `Vector2.MoveTowards` 处理匀速位移 。
  - 使用 `Vector2.Distance` 判断是否到达目标点 。
  - 使用取余数运算 `CurrentPoint = (CurrentPoint + 1) % waypoints.Length` 实现路径点的循环切换 。
- **“粘性”机制 (Sticky Logic)**：
  - **问题**：如果不处理，平台移动时角色会因惯性留在原地（滑落）。
  - **解决**：
    - `OnCollisionEnter2D`：当检测到 "Player" 时，设置 `SetParent(transform)`，让角色成为平台的子物体 。
    - `OnCollisionExit2D`：当离开时，设置 `SetParent(null)`，解除绑定 。

### 🔊 1.2 音效系统 (Audio System)

- **SFX (短音效)**：
  - **跳跃音效**：在 `Player_Movement` 脚本中引用 `AudioSource`，在跳跃时调用 `.Play()` 。需取消勾选 "Play On Awake" 。
  - **拾取音效**：在 `Item_collection` 中使用 `PlayOneShot(clip)`。
    - _优势_：允许声音叠加（连续吃樱桃时声音不会被打断）。
- **BGM (背景音乐)**：
  - 创建独立空物体 "Background Music"。
  - 设置：✅ **Play On Awake** (自动播放)，✅ **Loop** (循环播放) 。

### 🚪 1.3 场景交互与跳转

- **晃动的指示牌 (Start Sign)**：
  - **逻辑**：角色接触时晃动，离开时立刻静止。
  - **实现**：使用 `OnTriggerEnter/Exit` 配合 Animator 的 `Bool` 参数（`isShaking`），而不是 Trigger 参数。
  - _原理_：Bool 像开关，适合持续状态；Trigger 像扳机，适合一次性动作（如死亡）。
- **关卡跳转 (Next Level)**：
  - **逻辑**：触碰终点旗帜自动跳转下一关。
  - **代码**：
    ```csharp
    using UnityEngine.SceneManagement; // 必须引入命名空间
    // 加载索引+1的场景
    SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex + 1);
    ```
  - **注意**：必须将新场景添加到 **Build Settings** 列表中，否则代码无效。

---

## 🐛 2. 遇到的 Bug 与修复 (Troubleshooting)

### 2.1 平台逻辑错误：“动一下就不动了”

- **原因**：将移动代码 `MoveTowards` 写在了 `if (Distance < 0.1f)` 的判断块内部。这意味着只有到达终点的那一帧才会尝试移动，导致逻辑卡死 。
- **修复**：将移动逻辑移到 `if` 块之外，确保每一帧都执行 。

### 2.2 层级错误：“胡萝卜挂驴”

- **原因**：将路标点 (Point A/B) 放到了 Platform 的子层级下。平台移动时带着目标点一起跑，永远追不上目标 。
- **修复**：将 Point A/B 拖出父子层级，使其固定在世界坐标中 。

### 2.3 物理 Bug：贴墙滑落无法“补票”

- **现象**：从墙上滑到地面时，跳跃次数没有刷新（无法再次跳跃）。
- **原因**：使用了 `Composite Collider`，导致墙壁和地面被视为同一个物体。从墙滑到地，没有触发 `OnCollisionEnter`（没有离开过物体）。
- **修复**：
  - **方案 A（推荐）**：拆分 Tilemap。将墙壁 (`Wall`) 和地面 (`Ground`) 画在不同的 Tilemap 层上。
  - **方案 B（代码）**：使用 `contact.normal.y > 0.7f` 检测碰撞点法线，区分是踩在头顶还是贴在侧面 。

### 2.4 设置错误：巨大的绿色碰撞框

- **现象**：Tilemap 的碰撞体变成了一个巨大的矩形框，没有贴合瓦片形状 。
- **原因**：错误地添加了 `Box Collider 2D` 组件 。
- **修复**：
  1.  移除 `Box Collider 2D`。
  2.  添加 **`Tilemap Collider 2D`**。
  3.  (优化) 添加 **`Composite Collider 2D`** 并勾选 "Used By Composite"，同时将 Rigidbody 改为 Static 。

---

## 🧠 3. 核心概念对比

### Trigger (触发器) vs Bool (布尔值)

| 特性         | Trigger                    | Bool                      |
| :----------- | :------------------------- | :------------------------ |
| **比喻**     | 门铃 / 扳机                | 电灯开关                  |
| **行为**     | 触发一次后自动复位         | 保持状态，需手动开启/关闭 |
| **适用场景** | 攻击、受伤、死亡、跳跃触发 | 跑步中、晃动中、地面检测  |

### Play() vs PlayOneShot()

| 方法              | 行为                             | 适用场景                         |
| :---------------- | :------------------------------- | :------------------------------- |
| **Play()**        | 打断当前正在播放的声音，重新开始 | 独占性声音（如背景音乐）         |
| **PlayOneShot()** | 允许声音叠加，互不干扰           | 高频触发的声音（如吃金币、开枪） |

---

## 📝 4. 关键代码备忘

**平台移动 (StickyPlatform.cs):**

```csharp
void Update()
{
    // 1. 切换目标点
    if(Vector2.Distance(waypoints[currentWaypointIndex].transform.position, transform.position) < 0.1f)
    {
        currentWaypointIndex++;
        currentWaypointIndex %= waypoints.Length; // 循环索引
    }
    // 2. 移动 (放在if外面!)
    transform.position = Vector2.MoveTowards(transform.position, waypoints[currentWaypointIndex].transform.position, speed * Time.deltaTime);
}
```

**场景跳转 (FinishPoint.cs):**

```csharp
private void OnTriggerEnter2D(Collider2D collision)
{
    if (collision.gameObject.name == "Player")
    {
        // 跳转到 Build Settings 列表中的下一关
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex + 1);
    }
}
```
