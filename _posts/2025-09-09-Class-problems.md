---
title: "Class problems"
date: 2025-09-09 10:00:00 +0800
categories: [CS basics, Python] # [Top_category, sub_category]
tags: [python, log]      # TAG names should always be lowercase
# author: gaoshan
---


# 为什么我在类方法里没使用cls，而是用来self也行得通？

### **为什么 `cls` 和 `self` 混用会“行得通”？**

在 Python 中，`self` 和 `cls` 只是约定俗成的参数名，**它们本身没有特殊的含义**。重要的是它们在调用时被 Python 解释器隐式传入的**实际值**。

  * 当你在一个**对象方法**里使用第一个参数 `self` 时，Python 解释器在调用时传入的是**对象实例本身**。
  * 当你在一个**类方法**里使用第一个参数 `cls` 时，Python 解释器在调用时传入的是**类本身**。

如果你在类方法里把参数名写成 `self`，就像这样：

```python
class MyClass:
    @classmethod
    def my_class_method(self, arg1):
        print(f"参数 'self' 的实际值是: {self}")
        print(f"传入的参数 arg1 是: {arg1}")

MyClass.my_class_method("hello")
```

输出会是：

```
参数 'self' 的实际值是: <class '__main__.MyClass'>
传入的参数 arg1 是: hello
```

你看，尽管你把参数名写成了 `self`，但 Python 解释器在调用时，还是把**类** `MyClass` 传给了这个参数。这说明 `self` 此时代表的不是对象实例，而是类本身。

### **为什么强烈不建议这样做？**

尽管从技术上讲这种写法“行得通”，但这会给其他开发者（包括未来的你自己）造成极大的**误导和困惑**。

1.  **违反编程惯例：** 绝大多数 Python 开发者都遵循 `self` 用于对象方法、`cls` 用于类方法的约定。不遵循这个惯例会使你的代码难以阅读和理解。
2.  **混淆概念：** `self` 顾名思义是“自身”，通常指代实例对象。如果它在一个类方法中突然代表类本身，这会模糊实例和类的界限，导致你更容易犯错。
3.  **调试困难：** 当你或同事在调试代码时，看到 `self.some_attribute`，会下意识地以为 `some_attribute` 是一个实例属性。但如果是在类方法中，它实际上可能是一个类属性，这会浪费大量时间去排查看似不存在的问题。

所以，虽然 Python 语言本身允许你这样做，但请务必遵循**最佳实践**：在类方法中始终使用 `cls` 作为第一个参数，因为它能清晰地表明这个方法是在操作类本身，而不是某个特定的对象。
# 
方法methods

属性Attributes

### 一个更具体的例子来解释 `cls` 和 `self` 在类方法中的区别。

### 例子：汽车制造厂

假设你正在编写一个管理汽车的程序。

首先，我们定义一个 `Car` 类，它有一个**类属性** `num_wheels`（轮子数量，所有汽车都一样），以及一个**实例属性** `color`（每辆汽车的颜色可能不同）。

```python
class Car:
    # 这是一个类属性，所有 Car 类的对象共享这个值
    num_wheels = 4 

    def __init__(self, color):
        # 这是一个实例属性，每个对象的 color 属性都可能不同
        self.color = color

    def get_color(self):
        # 这是一个对象方法
        # self 指的是具体的对象，比如 my_car
        return self.color

    @classmethod
    def get_num_wheels(cls):
        # 这是一个类方法
        # cls 指的是类本身，也就是 Car 这个类
        return cls.num_wheels
```

现在，我们来创建两个对象并进行测试：

```python
my_car = Car("blue")
your_car = Car("red")

# 调用对象方法
print(my_car.get_color())  # 输出: blue
print(your_car.get_color()) # 输出: red
```

`get_color()` 方法需要知道是哪辆车在调用它，所以它需要 `self` 来访问具体的 `color` 属性。

现在，我们调用类方法：

```python
# 通过类名调用
print(Car.get_num_wheels())  # 输出: 4

# 甚至可以通过对象调用，但 Python 仍然传入的是类本身
print(my_car.get_num_wheels()) # 输出: 4
```

`get_num_wheels()` 方法只需要知道**类** `Car` 的轮子数量是多少，而不需要知道具体是哪辆车，所以它使用 `cls`。

-----

### 混淆 `cls` 和 `self` 的“危险”

现在，我们用错误的参数名来重写 `get_num_wheels` 方法，看看会发生什么。

```python
class Car:
    num_wheels = 4

    # ... (省略 __init__ 和 get_color 方法)

    @classmethod
    def get_num_wheels(self): # 错误地使用了 self
        # 这里的 self 其实是类本身，但名字会让人困惑
        return self.num_wheels
```

这段代码**可以运行**，因为 Python 依然将 `Car` 类传给了 `self` 参数。但请想象一下你作为其他程序员第一次看到这段代码：

1.  你看到 `def get_num_wheels(self):`，你的大脑会立刻认为这是一个**对象方法**。
2.  你可能会去找这个方法在哪里使用了像 `self.color` 这样的**实例属性**。
3.  但你找不到任何实例属性，因为这个方法实际上是在操作**类属性**。

这种不匹配会浪费你大量时间去理解代码，并可能导致你误以为 `get_num_wheels` 方法依赖于某个不存在的实例状态。

**总结：**

**`self`**：表示**对象实例**。当方法需要访问或修改对象特有的数据时使用。

**`cls`**：表示**类本身**。当方法需要访问或修改类共享的数据（类属性），或者需要创建类的新实例时使用。

虽然 Python 允许你在类方法中使用 `self` 作为参数名，但这完全是为了方便，而不是鼓励你这样做。请务必遵循\*\*`cls` 用于类方法，`self` 用于对象方法\*\*的惯例，这会让你的代码更清晰、更易于维护。

### **补充一个区分：方法和属性是什么？**

在面向对象编程中，**方法 (Method)** 和 **属性 (Attribute)** 是构成一个类的两个核心要素，它们共同定义了类的“是什么”和“能做什么”。

  * **属性 (Attribute)：**

      * **是什么？** 属性是类或对象的**数据**。它代表了对象的状态或特征。
      * **比喻：** 如果一个类是“汽车”，那么它的属性就是它的颜色、品牌、马力、车牌号等。
      * **在代码中：** 属性通常是变量。比如 `my_car.color = "red"`，这里的 `color` 就是一个属性。

  * **方法 (Method)：**

      * **是什么？** 方法是类或对象的**行为**或**功能**。它代表了对象能执行的操作。
      * **比喻：** 如果一个类是“汽车”，那么它的方法就是“启动引擎”、“加速”、“刹车”等。
      * **在代码中：** 方法是定义在类中的函数。比如 `my_car.start_engine()`，这里的 `start_engine` 就是一个方法。

简单来说：**属性是名词，方法是动词。**
