# 2025-12-06 C# 学习日志 (Day 2)

## 一、知识点补充：

1、**C#的范围运算符 `..`的用法：**

看下面示例代码

```Csharp
var names = new List<string> {"Alice ", "Mike" , "Bob", "John"} ;

//输出为Hello, MIKE
//      Hello, BOB
//相当于 1 <= pos < 3

foreach(var name in names [1..3])
{
    Console.WriteLine($"Hello, {name.ToUpper()}") ;
}

```

2、**注意 array 和 list 的差别：**

array 的大小是固定的，而 list 的大小是可变的。
但是对于一个数组 array 我们可以使用`array = [..array , 新元素]` 通过将原来的数组复制之后添加新的元素组成新的数组。

3、**LINQ 与 IEnumberable**

**LINQ (Language Integrated Query)：**

- 概念：LINQ (Language Integrated Query) 是 C# 处理数据的通用查询技术。

- 写法：

(1)查询语法 (Query Syntax)

```Csharp
var query = from score in scores
            where score > 80
            select score;
```

(2). 方法语法 (Method Syntax)

```Csharp
// 逻辑清晰：源数据 -> 筛选 -> 变换
var query = scores.Where(s => s > 80).Select(s => s);
```

这段代码同样揭示了 LINQ 的功能：筛选，变换。

**IEnumerable：**

- 定义：一个“只读、单向”的迭代器接口。

示例代码

```Csharp
// 多态性：List 和 Array 都可以赋值给 IEnumerable
int[] arr = { 1, 2, 3 };
IEnumerable<int> data = arr;

// 只能做一件事：遍历
foreach (var i in data) { ... }
```

### 二、面向对象编程（Object-oriented Programming）

示例代码

```Csharp
using System ;

namespace MyNameSpace
{
    public class Person(string Firstname , string Lastname , DateOnly Birthday)
    {
        public string FirstName { get ;} = Firstname ;
        public string LastName {get ;} = Lastname ;
        public DateOnly birthday = Birthday ;
        public List<Pet> pets {get ; set ;}  =  new() ;

        public override string ToString()
        {
            return $"{FirstName} {LastName}" ;
        }

    }

    public abstract class Pet(string Firstname)
    {
        public string FirstName {get ; } = Firstname ;

        public abstract string MakeNoise() ;

        public override string ToString()
        {
            return $"{FirstName} and I am a {GetType().Name}" ;
        }
    }
    public class Cat(string Firstname) : Pet(Firstname)
    {
        // public string FirstName {get ; } = Firstname ;
        public override string MakeNoise() =>"miao ~~ miao ~~" ;
    }

    public class Dog(string Firstname) : Pet(Firstname)
    {
        // public string FirstName {get ; } = Firstname ;
        public override string MakeNoise() => "bark ~ bark ~ bark " ;

    }

    public class Myapp
    {
        public static void Main()
        {
            var p1 = new Person("Li","ly",new DateOnly(2005,12,10)) ;
            var p2 = new Person("John","Bob",new DateOnly(2004,12,10)) ;

            List<Person> people = [p1 , p2] ;

            p1.pets.Add(new Cat("steven")) ;
            p1.pets.Add(new Dog("John")) ;

            p2.pets.Add(new Cat("Mike")) ;
            p2.pets.Add(new Dog("Bob")) ;


            foreach(var player in people)
            {
                Console.WriteLine($"{player}") ;

                foreach (var pet in player.pets)
                {
                    Console.WriteLine($"   {pet}") ;
                }
            }

        }
    }
}



```

1、**语法进化：从传统构造函数到主构造函数：**

_传统写法 (Old School)_
需要显式定义私有字段、写一个繁琐的构造函数进行赋值。

```C#
public class Person
{
    private string _name; // 私有字段
    public Person(string name) // 显式构造函数
    {
        _name = name;
    }
}
```

*主构造函数(Primary Constructor, C# 12+)*直接在类名后定义参数，极度简洁。

```C#
// 参数 name 在整个类内部可见，但默认是私有的
public class Person(string name)
{
}
```

2、核心疑问：为什么要写`{get ;}`？

**Q：** 为什么不直接用主构造函数的参数，非要写 `public string Name { get; } = name;`?

**A：** 这是为了实现 _“封装 (Encapsulation)”_

（1）**可见性 (Visibility)：**

- 主构造函数的参数（如 `name`）本质上是
  **私有 (private)**
  的局部变量。
- 在类内部可以用，但在类外部（如 `Main` 函数）无法通过 `player.name` 访问。
- 解决：必须通过 `public` 属性把它“暴露”出去。

（2）**安全性 (Immutability)：**

- 如果不写 `{ get; }` 而直接用 `public string Name = name`;（字段），外部可以随意修改名字。
- 写了 `{ get; }`（只读属性），数据在初始化后就不可被修改，保护了数据的安全性。

（3）**可维护性：**
属性允许未来在 `get` 中添加逻辑（如自动变大写），而不需要修改外部调用代码。

3、**继承与抽象类 (Inheritance & Abstract Class)**

（1）**抽象类** ：

- **概念：** 抽象类不能被直接实例化（你不能 `new Pet()`），它存在的唯一目的就是被继承。它是所有宠物的“模板”。
- **作用：** 你定义了所有宠物共有的属性（`FirstName`），并强制要求所有子类必须具备某些行为（`MakeNoise`），但基类自己不负责实现这个行为。

（2）**主析构函数的继承链**

- `public class Cat(string Firstname) : Pet(Firstname)`

- 这是 C# 12 的新语法。子类 Cat 的主构造函数接收参数 `Firstname`，并立即把它传递给基类 `Pet` 的构造函数。这相当于以前的 : `base(Firstname)`。

4、**多态性 (Polymorphism)：**
多态让你的 List<Pet> 变得极其灵活。

（1）**抽象方法**

- (`abstract` & `override`)
  在 `Pet` 中：`public abstract string MakeNoise()`;（我不确定具体怎么叫，但我要求你们必须会叫）。
- 在 `Cat/Dog` 中：`public override string MakeNoise() => "..."`（我来具体实现怎么叫）。虽然都是 `Pet`，但不同的子类表现出了不同的形态。

（2）**ToString()的重写**

- 重写了 `Person` 和 `Pet` 的 `ToString()` 方法。

- 当你调用`Console.WriteLine($"{player}") `时，C# 会自动调用写好的 ToString 方法，而不是打印一串看不懂的内存地址或类名。

- `GetType().Name` 是一个反射的小技巧，它能动态获取当前实例的类名（"`Cat`" 或 "`Dog`"），这让你不用在每个子类里单独写 `"I am a Cat"`。

5、**语法糖与新特性 (Syntactic Sugar)**

（1） **自动属性初始化：**`public List<Pet> pets {get ; set ;} = new();` —— 直接在声明时实例化列表，防止空引用。

（2） **集合表达式：**
`List<Person> people = [p1, p2];` —— C# 12 最简洁的列表创建方式。

（3）`Expression-bodied members：=> "miao ~~" `—— 箭头函数写法，让简单的重写变得非常干
