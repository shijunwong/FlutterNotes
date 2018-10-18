# Dart 入门

# 一 创建一个简单的类

```dart
// 创建一个Bicycle 类.
class Bicycle {
  int cadence;
  int speed;
  int gear;
}

// Dart 主方法.
void main() {
}
```

- `main()` 方法是 Dart 的主方法，如果你需要访问命令行传递过来的参数，可以使用 `main(List<String> args)` 方法。
- `main()` 方法存在于最外层的作用域，在 Dart 中你可以在类之外编写代码，变量、方法、存取方法都可以独立于类之外维持生命周期。
- 无论是 `main()` 还是 Bicycle 类都声明为 public 的，默认情况下都是 public 的，在 Dart 中没有诸如 public、private、protected 这样的关键词


```dart
class Bicycle {
  // 构造方法
  Bicycle(this.cadence, this.speed, this.gear);
  int cadence;
  int speed;
  int gear;
}
```

- 这个构造函数没有方法体，这个在 Dart 中是合法的。
- 如果你在没有方法体的构造函数后忘记书写分号(;) ，DartPad 将会显示一个错误，告诉你"A function body must be provided."
- 在构造函数的参数中使用 this 可以直接对实例变量进行赋值，不用再编写多余的代码。
- 上述代码同下列的代码是相同功效的。

```dart
Bicycle(int cadence, int speed, int gear) {
  this.cadence = cadence;
  this.speed = speed;
  this.gear = gear;
}
```

## 实例化对象

```dart
void main() {
  var bike0 = new Bicycle(2, 0, 1);
  var bike1 = Bicycle(2, 0, 1);
  print(bike);
}
```
- 在 Dart 2 中，new 关键字变成了可选的。
- 如果你确信某个变量的值不会再发生改变，你可以使用 final 来代替 var
- Dart中会进行自动类型推导.

```dart
// 重写toString方法.
@override
String toString() => 'Bicycle: $speed mph';
```

- 修饰符 @override 会告诉分析器你当前是在复写某个成员方法，如果该复写不成功，分析器就会报错。
- Dart 可以使用单引号或者双引号进行字符串的声明。
- 可以在字符串内使用 ${expression} 的方式来实现字符串模板的效果，如果该表达式仅仅是一个标识符，还可以去掉花括号 $variableName。
- 针对只有一行的方法可以使用 => 来简化方法的书写。

## 将变量设置为只读变量

你可以在变量名前增加下划线 _ 来标记为它是私有的，也就是说可以仅仅通过改变变量名来实现将 speed 标记为只读的。

**将speed标记为私有的, 只读变量**

```dart
// 移除构造方方法中的speed
Bicycle(this.cadence, this.gear);
// 将speed修改为以_开头的.并初始化
int _speed = 0;
// 增加get方法
int get speed => _speed;
```
- 未初始化的变量（即使是数字类型的变量）的值都为 null
- 所有名字以下划线开头的变量，Dart 的编译器都会将其强制标记为私有的
- 默认情况下，Dart 会为所有公开的变量提供存取方法，除非你需要提供仅仅可读、可写，或者在某些情况下需要在 getter 方法中进行计算或是在 setter 方法中进行某些值得更新，否则都不需要再重新定义存取方法。
- 这些实例变量可以直接通过 bike.gear 或者 bike.cadence 访问到。
- 你先直接通过 bike.cadence 来进行实例变量的访问，然后再去通过定义存取方法来进行重构。提供重构之后的 API 和开始直接访问的版本并不会有很大的差别。

**最终代码**

```dart
class Bicycle {
  int cadence;
  // 私有变量
  int _speed = 0;
  int gear;

  Bicycle(this.cadence, this.gear);

  // 提供私有变量的访问方法.
  void applyBrake(int decrement) {
    _speed -= decrement;
  }

  void speedUp(int increment) {
    _speed += increment;
  }
  // 重写toString
  @override
  String toString() => 'Bicycle: $_speed mph';
}

void main() {
  var bike = Bicycle(2, 1);
  print(bike);
}
```



# 可选参数

在Dart中支持可选参数, 也就是为参数提供默认值

```dart
Rectangle({this.origin = const Point(0, 0), this.width = 0, this.height = 0});
```

- this.origin, this.width 和 this.height 使用了 Dart 提供的简便方法来直接对类中的实例变量进行赋值。
- this.origin, this.width 和 this.height 嵌套在闭合的花括号中 ({}) ，用来表示它们是可选的命名参数。
- this.origin = const Point(0, 0) 这样的代码表明给实例变量 origin 提供了默认的值 Point(0,0)，默认值必须是在编译器就可以确定的常量。上述代码中的构造方法为三个实例变量都提供了默认参数。



# 工厂模式

Dart中有两种方式实现工厂方法

- 创建一个顶层的方法
- 创建一个工厂模式的构造方法

基础代码

```dart
import 'dart:math';
// 基础父类
abstract class Shape {
  num get area;
}
// 子类1
class Circle implements Shape {
  final num radius;
  Circle(this.radius);
  num get area => pi * pow(radius, 2);
}
// 子类2
class Square implements Shape {
  final num side;
  Square(this.side);
  num get area => pow(side, 2);
}

main() {
  final circle = Circle(2);
  final square = Square(2);
  print(circle.area);
  print(square.area);
}

```

- Dart 支持抽象类
- 你可以在一个文本中定义多个类
- dart.math 是一个 Dart 核心库，其余的还有诸如 dart:core，dart:async，dart:convert 和 dart:collection 这样的核心库
- 在 Dart 1.x 的版本中，核心库中的常量是大写的（比如 PI）；在 Dart 2 的版本中，都是小写的(pi)。
- 该代码包含了两个 getter 方法，分别用来计算圆的面积和四边形的面积

## 通过提供一个工厂方法.

```dart
Shape shapeFactory(String type) {
  if (type == 'circle') return Circle(2);
  if (type == 'square') return Square(2);
  // 抛出异常
  throw 'Can\'t create $type.';
}

// 使用
final circle = shapeFactory('circle');
final square = shapeFactory('square');
```

- 如果在调用函数时传递的参数不是 'circle'或者 'square'，那么将会抛出一个异常。
- Dart SDK 中已经定义了许多常见的异常，也提供了通过扩展 Exception 类来进行异常的自定义，或者你也可以像上述代码中一样直接抛出字符串用来描述所出现的问题。
- 当出现异常后，DartPad 会直接抛出 Uncaught，你可以将代码封装在 try-catch 语句中来捕获异常。你可以选择通过这个示例代码中来进行相关的练习。
- 如果一个字符串中包含单引号，那么如果该字符串使用单引号声明的则需要在字符串内部的单引号之前增加转移符('Can\'t create $type.')，如果字符串是使用双引号进行生命的("Can't create $type.")，则什么都不需要做。


## 创建一个工厂模式的构造方法(Dart方式)

```
abstract class Shape {
  // 工厂模式构造方法.
  factory Shape(String type) {
    if (type == 'circle') return Circle(2);
    if (type == 'square') return Square(2);
    throw 'Can\'t create $type.';
  }
  num get area;
}
```

# 接口实现


Dart 语言并没有提供 interface 关键字，但是每一个类都隐式地定义了一个接口。

```dart
class CircleMock implements Circle {
  num area;
  num radius;
}
```

# 函数式编程

在函数式编程中，你可以做到：

- 将函数当做参数进行传递
- 将函数直接赋值给变量
- 对函数进行解构，只传递给函数一部分参数来调用它，让它返回一个函数去处理剩下的参数（也被称为柯里化）
- 创建一个可以被党作为常量的匿名函数（也被称为 lambda 表达式，在 Java 的 JDK 8 release 中支持了 lambda 表达式）

Dart 支持所有的这些特性，在 Dart 中，每个函数都是一个对象，并且每个函数都有它的类型 Function，这意味着所有函数都可以支持赋值操作，以及都可以作为参数传递给其他的函数。你可以将实例化 Dart 类当做一个函数的调用行为，

```dart
// ${'a' * length} 这样的代码执行效果是将字符 'a' 重复 length 次
String scream(int length) => "A${'a' * length}h!";

main() {
  final values = [1, 2, 3, 5, 10, 50];
  // 命令式.
  for (var length in values) {
    print(scream(length));
  }

  // 函数式
  values.map(scream).forEach(print);
}
```

**更多:**
dart:collection 库中的 List 和 Iterable 支持 fold, where, join, skip 等函数式方法，另外 Dart 还支持 Map 和 Set 类型。
