---
title: Dart入门
date: 2024-04-15 23:10:13
category: [移动端]
tags: [Dart]
---

## 一、Dart 语言基础

### 1. 语言注意事项

- **文件后缀**：Dart 文件以 `.dart` 结尾。
- **入口方法**：程序入口为 `main` 方法。
- **分号规则**：绝大多数语句需以分号 `;` 结尾，代码块 `{}` 后通常不加分号。

```dart
void main(List<String> args) {
  print("Hello, World!"); // 输出使用 `print` 方法
}
```

### 2. 变量与常量声明

| 关键字  | 类型       | 特点                                       | 语法示例                       |
| ------- | ---------- | ------------------------------------------ | ------------------------------ |
| `var`   | 变量       | 第一次赋值后类型确定，不可再赋其他类型的值 | `var age = 20;`                |
| `const` | 编译时常量 | 编译前确定值，不可修改，表达式只能用常量   | `const pi = 3.1415926;`        |
| `final` | 运行时常量 | 运行时初始化，赋值后不可修改               | `final time = DateTime.now();` |

### 3. 常用数据类型

#### （1）String（字符串）

- **模板字符串**：使用 `$变量名` 或 `${表达式}` 嵌入变量/表达式，推荐 `${}`。

```dart
String text = '我要在${DateTime.now()}吃早饭';
```

#### （2）数字类型

| 类型     | 含义     | 赋值关系                                                 |
| -------- | -------- | -------------------------------------------------------- |
| `int`    | 整型     | 与 `double` 不可直接互赋，需 `toInt()`/`toDouble()` 转换 |
| `double` | 浮点型   | 可直接赋值给 `num`                                       |
| `num`    | 数字父类 | 可接收 `int`/`double`，赋值给 `double` 需 `toDouble()`   |

#### （3）bool（布尔类型）

- 表示真/假，取值为 `true`/`false`，常用于条件判断。

```dart
bool isFinishWork = false;
```

#### （4）List（列表）

- 存储多个值，支持增删改查与遍历。
- **常用操作**：
  - `add()`：添加一个元素到列表末尾。
  - `addAll()`：在末尾添加一个列表。
  - `remove()`：删除第一个匹配的元素。
  - `removeLast()`：删除最后一个元素。
  - `removeRange(start, end)`：删除指定范围的元素。
  - `forEach((item) {})`：遍历列表中的每个元素。
  - `every((item) { return 布尔值 })`：判断列表中是否所有元素都满足指定条件。
  - `where((item) { return 布尔值 })`：根据条件筛选元素。
  - `length`：获取列表元素数量。
  - `last`：获取最后一个元素。
  - `first`：获取第一个元素。
  - `isEmpty`：判断列表是否为空。

```dart
List students = ["张三", "李四", "王五"];

students.add("赵六");
students.forEach((item) {
  print(item);
});
```

#### （5）Map（字典）

- 存储键值对
- **定义**：`{key: value}`
- **取值/赋值**：`map[key]`
- **常用操作**：
  - `forEach((item) {})`：遍历字典中的每个键值对。
  - `addAll()`：在末尾添加一个字典。
  - `containsKey(key)`：判断是否包含指定键。
  - `remove(key)`：删除指定键值对。
  - `clear()`：清空字典。

```dart
Map<String, int> scoresMap = {"张三": 90, "李四": 85, "王五": 95};
print(scoresMap["张三"]);

scoresMap.forEach((key, value) {
  print("$key: $value");
});
scoresMap.addAll({"赵六": 88});
scoresMap.containsKey("张三");
scoresMap.remove("李四");
scoresMap.clear();
print(scoresMap);
```

#### （6）动态类型

| 关键字    | 类型推断                       | 编译检查 | 特点                           |
| --------- | ------------------------------ | -------- | ------------------------------ |
| `var`     | 编译时推断（编辑器会提示错误） | 有       | 类型确定后不可修改             |
| `dynamic` | 无编译检查，运行时确定         | 无       | 可自由修改类型，运行时可能报错 |

```dart
var numVar = 10
dynamic numDynamic = 10;
numVar.startsWith("1"); // 编译时报错，int 类型没有 `startsWith` 方法
numDynamic.startsWith("1"); // 运行时报错，但是编译通过，不会提示
```

### 4. 空安全机制

- **核心**：编译时静态检查，提前暴露空指针异常。
- **常用操作符**：

| 操作符   | 符号 | 作用                           | 示例                                   |
| -------- | ---- | ------------------------------ | -------------------------------------- |
| 可空类型 | `?`  | 声明变量允许为 `null`          | `String? name;`                        |
| 安全访问 | `?.` | 对象为 `null` 时返回 `null`    | `user?.name`，user为`null`时返回`null` |
| 非空断言 | `!.` | 开发者保证变量非空（否则崩溃） | `name!.length`                         |
| 空合并   | `??` | 左侧为 `null` 时返回右侧默认值 | `name ?? "Guest"`                      |

### 5. 运算符

#### （1）算术运算符

`+`、`-`、`*`、`/`（除）、`~/`（整除）、`%`（取余）。

#### （2）赋值运算符

`=`、`+=`、`-=`、`*=`、`/=`。

#### （3）比较运算符

`==`、`!=`、`>`、`>=`、`<`、`<=`，结果为 `bool` 类型。

#### （4）逻辑运算符

`&&`（与）、`||`（或）、`!`（非），仅支持 `bool` 类型的变量。

---

## 二、流程控制

### 1. if 分支语句

- **单分支**：`if (条件) { 逻辑 }`
- **双分支**：`if (条件) { 逻辑1 } else { 逻辑2 }`
- **多分支**：`if (条件1) { 逻辑1 } else if (条件2) { 逻辑2 } else { 逻辑3 }`

### 2. 三元运算符

- 简化双分支：`条件 ? 结果1 : 结果2`

```dart
print(score > 60 ? "及格" : "不及格");
```

### 3. switch/case 语句

- 适用于多等值判断，每个 `case` 后需加 `break`，支持 `default` 默认分支。

```dart
switch (变量) {
  case 值1: 逻辑1; break;
  case 值2: 逻辑2; break;
  default: 默认逻辑;
}
```

### 4. 循环语句

#### （1）while 循环

```dart
while (条件) {
  逻辑;
}
```

#### （2）for 循环

```dart
for (int i = 0; i < 10; i++) {
  逻辑;
}
```

#### （3）循环控制

- `break`：跳出整个循环。
- `continue`：跳过当前迭代，进入下一次循环。

---

## 三、函数

### 1. 函数定义

- 结构：`返回类型 函数名(参数) { 函数体 }`
- 返回值：
  - 有返回值：`具体类型 函数名称() { 函数体 }`
  - 无返回值：`void 函数名称() { 函数体 }`
  - 自动推断：省略返回类型时，Dart 推断为 `dynamic`。

```dart
// 有返回值函数
int add(int a, int b) {
  return a + b;
}
// 无返回值函数
void test() {
  print("测试");
}
// 省略返回类型
getHello() {
  return "Hello, World!";
}
```

### 2. 参数类型

#### （1）必传参数（位置参数）

- 无默认值，必须在调用时传递。
- 顺序不能改变。

```dart
int add(int a, int b) { return a + b; }
```

#### （2）可选位置参数

- 用 `[]` 包裹，必须在必传参数后，支持默认值，按顺序传递。
- 适用场景：参数数量少，且顺序固定。
- 默认值：可以设置默认值，不设置时默认值为 `null`。

```dart
String combine(String a, [String? b = "b"]) { ... }
```

#### （3）可选命名参数

- 用 `{}` 包裹，必须在必传参数后，支持默认值，按 `参数名:值` 传递（无需顺序）。
- 适用场景：参数数量多，且含义明确。
- 默认值：可以设置默认值，不设置时默认值为 `null`。

```dart
void showPerson(String username, {int? age = 10}) { ... }
```

### 3. 特殊函数

#### （1）匿名函数

- 无函数名，可赋值给变量进行调用，或作为参数传递。
- 使用 `Function` 类型定义。

```dart
Function test = () {
  print("测试");
};
```

#### （2）箭头函数

- 函数体只有一行代码时使用，可以省略 `{}` 和 `return`。

```dart
int add(int a, int b) => a + b;
```

---

## 四、面向对象编程

### 1. 类的基础

- 类(class)是**面向对象**编程的核心，类包含**属性**和**方法**来定义对象的行为和状态。
- 定义：`class 类名 { 属性; 方法(); }`
- 实例化：`类名 变量 = 类名();`
- 访问成员：`变量.属性` / `变量.方法()`

### 2. 构造函数

#### （1）默认构造函数

```dart
class Person {
  String? name;
  Person({this.name}); // 语法糖简写
}
```

#### （2）命名构造函数

```dart
class Person {
  String? name;
  Person.createPerson({this.name});
}
// 调用：Person p = Person.createPerson(name: "张三");
```

### 3. 公有与私有属性

- 公有属性：默认可见，无特殊前缀。
- 私有属性：以下划线 `_` 开头，仅类内部可访问。

```dart
class Person {
  String? _name; // 私有属性
}
```

### 4. 继承

- 关键字 `extends`，Dart 是单继承。
- 子类需通过 `super` 调用父类构造函数。
- 方法重写：使用 `@override` 注解重写父类方法。

```dart
class Child extends Parent {
  Child({String? name}) : super(name: name);
  @override
  void study() { ... }
}
```

### 5. 多态

- 实现方式：
  1. 继承 + 方法重写：子类重写父类方法，同一操作产生不同效果。
  2. 抽象类 + 接口：`abstract` 定义抽象类，`implements` 实现抽象方法。

```dart
abstract class PayBase { void pay(); }
class WxPay implements PayBase { @override void pay() { ... } }
```

### 6. Mixin（混入）

- 不使用继承，向类中添加功能，关键字 `mixin` 定义、`with` 混入。
- 支持多混入，同名方法遵循“后来居上”原则，先混入的类方法会被后混入的类方法覆盖。

```dart
mixin Base { void song() { ... } }
class Student with Base { ... }
```

### 7. 泛型

- 使用 `<T>` 定义类型参数，提升类型安全与代码复用。
- 分类：泛型集合（`List<String>`）、泛型方法、泛型类。

```dart
class Student<T> {
  T? name;
}
```

---

## 五、异步编程

### 1. 事件循环机制

- Dart 是单线程语言，采用「同步代码 → 微任务队列 → 事件队列」循环处理异步任务。
- 微任务队列：`Future.microtask()`（高优先级）。
- 事件队列：`Future`、`Future.delayed()`、I/O 操作等（普通优先级）。

### 2. Future

- `Future` 代表异步操作的结果，状态：`Uncompleted` → `Completed with value/error`。
- 基本用法：

```dart
Future(() { return "结果"; })
  .then((value) { print(value); }) // 成功回调
  .catchError((error) { print(error); }); // 失败回调
```

- 链式调用：多个 `then` 按顺序执行，返回值传递给下一个 `then`。

### 3. async/await

- `Future.then()` 的语法糖，让异步代码像同步代码一样易读。
- `async` 标记函数为异步，`await` 等待 `Future` 执行完成。
- 异常捕获：使用 `try/catch`。

```dart
void test() async {
  try {
    await Future(() { ... });
    print("成功逻辑");
  } catch (e) {
    print("失败逻辑");
  }
}
```

## 六、总结

| 模块         | 核心要点                                   |
| ------------ | ------------------------------------------ |
| **基础语法** | 变量/常量、数据类型、空安全、运算符        |
| **流程控制** | if/else、switch、for/while、break/continue |
| **函数**     | 参数类型、匿名函数、箭头函数               |
| **面向对象** | 类、构造函数、继承、多态、Mixin、泛型      |
| **异步编程** | 事件循环、Future、async/await              |
