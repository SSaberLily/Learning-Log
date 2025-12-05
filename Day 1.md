# 2025-12-05 C# 学习日志

## 今天学的知识点

1. **.NET 与 C# 的关系**：C# 是车，.NET 是高速公路。。NET 为 C#提供了许多方便的工具以及平台。
2. **项目创建并启动**：学会了用 `dotnet new console` 创建项目并使用`code .`将其在 Vscode 中打开。
3. **输入处理**：
   - 遇到了 `CS8600` 警告 (空引用)。
   - 知道了 `Console.ReadLine()` 可能返回 null。
   - 解决方案：使用 `string.IsNullOrWhiteSpace()`。

```csharp
// 这里的代码修复了空引用的问题
Console.WriteLine("请输入名字：");
string? input = Console.ReadLine();
if (string.IsNullOrWhiteSpace(input))
{
    input = "Default Player";
}
```

4.**C#程序的代码格式：** C#的代码通常有以下两种格式（以输出“Hello，world”为例）：

**(1)顶级语句：**
.NET SDK 版本在 6 或更高版本的时候使用命令行`dotnet new console`默认生成的`Program.cs`就会是顶级语言格式。在一个项目中只有一个文件使用顶级语言。在生成时使用`dotnet new console --use-program-main`可以强制生成旧版格式。

**(2)旧版格式：**
特点是结构严谨，面向对象（它清晰地展示了代码是属于哪个层级、哪个类的）。更加适用于实际的开发。

5.**CLI & GUI:**

CLI (Command-Line Interface)：命令行界面

GUI (Graphical User Interface)：图形用户界面

6.**String：**

（1）**内插和拼串：**

**内插**：允许直接在字符串字面量中嵌入表达式。

```Csharp
//待插入的字符串变量
string MyFriendName = "Lilyzi";
//插入后输出为"My friend's name is Lilyzi"
Console.WriteLine($"My friend's name is {MyFriendName}") ;
```

**拼串：** 用加号 ‘+’将不同的字符串连接在一起

```Csharp
string MyFriendName = "Lilyzi";
//使用‘+’号将两部分连接
Console.WriteLine("My friend's name is " + MyFriendName) ;

```

（2）**Trim：** 去除字符串两端的空格

```Csharp
string greeting = "      Hello World!       ";
Console.WriteLine($"[{greeting}]");
//仅去除前端空格
string trimmedGreeting = greeting.TrimStart();
Console.WriteLine($"[{trimmedGreeting}]");
//仅去除后端空格
trimmedGreeting = greeting.TrimEnd();
Console.WriteLine($"[{trimmedGreeting}]");
//去除前后两端空格
trimmedGreeting = greeting.Trim();
Console.WriteLine($"[{trimmedGreeting}]");
```

（3）**替换：**

**搜索替换：** 将目的字符串替换为新的字符串

```Csharp
string sayHello = "Hello World!";
//输出为"Hello World!"
Console.WriteLine(sayHello);

sayHello = sayHello.Replace("Hello", "Greetings");

//输出为"Greetings World!"
Console.WriteLine(sayHello);
```

**全部大小写：** 将字符串转换为全部大写格式或全部小写格式

```Csharp
//大写
Console.WriteLine(sayHello.ToUpper());
//小写
Console.WriteLine(sayHello.ToLower());
```

（4）**搜索字符串：** 它会告诉你一个字符串是否包含某个子字符串，返回布尔值。

```Csharp
string songLyrics = "You say goodbye, and I say hello";
Console.WriteLine(songLyrics.Contains("goodbye"));
Console.WriteLine(songLyrics.Contains("greetings"));
```

7.**浮点数：** C#的浮点数运算和 cpp 的标准都是`IEEE 754 `，但是 C#禁止任何可能丢失精度的隐式转换，只有数字部分的话类型默认是`double`。此外还要注意`decimal`与`double`的区别。前者范围更小，但是精度更高。

8.**元组和类型：** 元组是具有固定长度的有序数值序列。可以使用`with`表达式来创建一个新元组，它是原始元组的修改副本

```Csharp
var pt = (X : 12 , Y : 12) ;
pt.X += 12 ;
//输出为 "(24,12)"
Console.WriteLine(pt) ;
var pt2 = pt with {Y = 10} ;
//输出为 "(24,10)"
Console.WriteLine(pt2);
```

除此之外元组还可以声明多个用括号括起来的成员。

```Csharp
var namedData = (Name: "Morning observation", Temp: 17, Wind: 4);

var person = (FirstName: "", LastName: "");

var order = (Product: "guitar picks", style: "triangle", quantity: 500, UnitPrice: 0.10m);
```

9.**分支与循环：**

**分支：**

（1）**判断条件：** C#中 if 的判断条件必须是 `True`或者`False`,不能把整数当布尔值使用。

（2）**switch 的使用：** C#中 switch 的判断条件可以是字符串类型。

```Csharp
string name = Console.ReadLine() ?? "Lily" ;

switch (name)
{
    case "Lilyzi" :
        Console.WriteLine("666666666") ;
        break;
    case "Lily" :
        Console.WriteLine("3333333333") ;
        break;
}
```

**循环：** ~~与 c++类似？~~(待完善)

10.**列表：**

（1）**增删：** 通过`Add()`和 `Remove()`方法来进行增删元素(如果数组中有多个相同元素只会删除第一个符合要求的元素)。

（2）**查找：** 通过`IndexOf()`方法来查找目标元素 (如果数组中有多个相同元素只会返回第一个符合要求的元素的索引)。
