好的，这是为您整理并修正后的完整学习日志，直接采用了 Markdown 格式。您可以直接复制保存到您的笔记软件中。

# 2025-12-05 C# 学习日志 (修订版)

## 一、 环境与基础架构

### 1. .NET 与 C# 的关系

- **概念**：C# 是车，.NET 是高速公路。.NET 为 C# 提供了运行平台和丰富的类库工具。

### 2. 项目结构与启动

- **创建项目**：`dotnet new console`
- **打开项目**：`code .` (在 VS Code 中打开当前目录)
- **代码格式风格**：
  - **顶级语句 (Top-level statements)**：.NET 6+ 默认格式。特点是简洁，项目中只能有一个文件使用此格式。
  - **旧版格式**：显式包含 `namespace`、`class Program`、`Main` 方法。特点是结构严谨，面向对象层级清晰，更适用于大型实际开发。
  - **强制生成旧版**：使用命令 `dotnet new console --use-program-main`

### 3. 界面类型

- **CLI (Command-Line Interface)**：命令行界面。
- **GUI (Graphical User Interface)**：图形用户界面。

---

## 二、 基础语法与类型安全

### 1. 输入处理与空安全 (Null Safety)

- **问题**：`Console.ReadLine()` 可能返回 `null`，导致 `CS8600` 警告。
- **解决**：使用 `string.IsNullOrWhiteSpace()` 进行校验。

```csharp
Console.WriteLine("请输入名字：");
// string? 表示该变量允许为空
string? input = Console.ReadLine();

if (string.IsNullOrWhiteSpace(input))
{
    input = "Default Player";
}
```

### 2\. 浮点数与精度

- **标准**：遵循 IEEE 754 标准。
- **类型安全**：C\# 禁止隐式“缩窄转换”（如 `double` 转 `int` 必须强制转换），防止意外精度丢失。
- **类型区别**：
  - `double`：默认浮点类型，范围大，适合物理/数学计算。
  - `decimal`：范围较小但精度极高，**专用于金融/货币计算**（后缀需加 `m`）。

### 3\. 分支判断 (Bool Only)

- **If 条件**：判断条件必须是 `bool` (`True` / `False`)。
- **区别**：**不能**像 C++ 那样将整数（0 或 1）当作布尔值使用。

---

## 三、 字符串操作 (String)

### 1\. 拼接与格式化

- **拼串**：使用 `+` 连接。
- **内插 (Interpolation)**：使用 `$` 符号（推荐，更易读）。

<!-- end list -->

```csharp
string MyFriendName = "Lilyzi";
// 输出: My friend's name is Lilyzi
Console.WriteLine($"My friend's name is {MyFriendName}");
```

### 2\. 常用处理方法

- **去除空格 (Trim)**：
  - `Trim()`: 去除两端空格
  - `TrimStart()`: 去除头部空格
  - `TrimEnd()`: 去除尾部空格
- **替换 (Replace)**：
  - `str.Replace("旧值", "新值")`。注意：String 是不可变的，此方法返回一个新的字符串。
- **大小写转换**：
  - `ToUpper()`: 转大写
  - `ToLower()`: 转小写
- **搜索 (Contains)**：
  - `str.Contains("子串")`：返回布尔值，表示是否包含。

---

## 四、 复合数据结构

### 1\. 元组 (Tuples)

- **定义**：轻量级、有序的数值序列，可包含多个不同类型的成员。
- **非破坏性修改 (with)**：使用 `with` 表达式创建修改后的**新副本**，原元组不变。

<!-- end list -->

```csharp
var pt = (X: 12, Y: 12);
pt.X += 12; // 此时 pt 为 (24, 12)

// 使用 with 创建新元组 pt2，仅修改 Y 值
var pt2 = pt with { Y = 10 };

Console.WriteLine(pt);  // 输出: (24, 12)
Console.WriteLine(pt2); // 输出: (24, 10)
```

### 2\. 列表 (List\<T\>)

> **修正注**：原文提到的 Add/Remove 特性主要适用于 `List<T>`，C\# 的原始数组 (Array) 长度固定，不支持直接 Add/Remove。

- **概念**：长度可变的动态数组。需要引用 `System.Collections.Generic`。
- **增删**：
  - `Add(item)`: 添加元素。
  - `Remove(item)`: 删除元素（若有重复，只删除第一个匹配项）。
- **查找**：
  - `IndexOf(item)`: 返回第一个匹配项的索引，找不到返回 -1。

---

## 五、 流程控制

### 1\. Switch

- **特性**：C\# 的 switch **支持字符串**作为判断条件，非常方便。

<!-- end list -->

```csharp
string name = Console.ReadLine() ?? "Lily"; // ?? 是空合并运算符，若为 null 则赋值 "Lily"

switch (name)
{
    case "Lilyzi":
        Console.WriteLine("666666666");
        break;
    case "Lily":
        Console.WriteLine("3333333333");
        break;
}
```

### 2\. 循环 (Loops)

- **For / While / Do-While**：语法与 C++ 基本一致。
- **Foreach (重点)**：C\# 特有的强大循环，专门用于遍历集合（数组、List 等），语法简洁且不易越界。

<!-- end list -->

```csharp
string[] names = { "Anna", "Bob", "Clare" };
foreach (string name in names)
{
    Console.WriteLine(name);
}
```

```

### 修正与完善说明：
1.  **列表部分**：明确了这是 `List<T>` 而非普通数组（Array），因为普通数组长度不可变。
2.  **循环部分**：补全了 C# 中非常重要的 `foreach` 循环。
3.  **代码细节**：
    - 增加了 `string?` 注解，体现了你学到的空引用警告知识点。
    - 增加了空合并运算符 `??` 的示例，这在处理 `ReadLine` 默认值时非常常用。
    - 修正了元组代码块中注释的逻辑，使其更清晰。
```
