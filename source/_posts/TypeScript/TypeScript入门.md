---
title: TypeScript入门.md
date: 2023-10-19 21:18:03
categories: [TypeScript]
tags: [TypeScript]
---

## TypeScript 是什么

`TypeScript` 是 `JavaScript` 的类型的超集，支持`ES6`语法，支持面向对象编程的概念，如类、接口、继承、泛型等。

`TypeScript` 是一种**静态类型**检查的语言，提供了类型注解，在代码编译阶段（代码运行前）就可以检查出数据类型的错误。

同时扩展了`JavaScript` 的语法，所以任何现有的`JavaScript` 程序可以不加改变的在 `TypeScript` 下工作。

## 为什么使用 TypeScript

1. JS中的数据类型不清晰
2. 逻辑漏洞
3. 访问不存在的属性
4. 代码拼写错误

## 起步

### 安装

```bash
npm install -g typescript
```

### 查看版本

tsc = typescript compiler

```bash
tsc -v
```

### 初始化

```bash
tsc --init
```

### 使用

**运行ts文件**

```bash
tsc [文件名]  // 转换单个文件
tsc --watch  // 监视目录中所有ts文件
tsc--noEmitOnError --watch  // 编译错误时不生成js文件，推荐可以在tsconfig.json配置
```

**在vs code中自动运行ts文件**

- 在tsconfig.json中取消 "outDir": "./", 的注释
- Terminal -》Run Task -》typescript -》tsc\:watch - tsconfig.json

**tsconfig.json中的选项**

| 选项      | 类型    | 默认值                                | 描述                                                                                                                                                                                                                |
| :-------- | :------ | :------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| module    | string  | target === "ES6" ? "ES6" : "commonjs" | 1.指定生成哪个模块系统代码： "None"， "CommonJS"， "AMD"， "System"， "UMD"， "ES6"或 "ES2015"。<br />2.只有 "AMD"和 "System"能和 --outFile一起使用。<br />3."ES6"和 "ES2015"可使用在目标输出为 "ES5"或更低的情况下 |
| target    | string  | ES3                                   | 1.指定ECMAScript目标版本<br />2."ES3"（默认）， "ES5"， "ES6"<br />3."ES2015"， "ES2016"， "ES2017"或 "ESNext"                                                                                                      |
| sourceMap | boolean | false                                 | 生成相应的 .map文件                                                                                                                                                                                                 |
| outDir    | string  |                                       | 重定向输出目录                                                                                                                                                                                                      |
| allowJs   | boolean | false                                 | 允许编译javascript文件                                                                                                                                                                                              |
| charset   | string  | utf8                                  | 输入文件的字符集                                                                                                                                                                                                    |

## 类型推论

TypeScript里，在有些没有明确指出类型的地方，类型推论会帮助提供类型。

推断发生在初始化变量和成员，设置默认参数值和决定函数返回值时。

## 数据类型

> typescript中定义变量必须定义数据类型，并且赋值的数据类型必须和定义的数据类型一致

### boolean

最基本的数据类型就是简单的true/false值。

```typescript
let bool: boolean = true;
console.log(bool); // true
```

### number

和JavaScript一样，TypeScript里的所有数字都是浮点数。 这些浮点数的类型是 `number`。 除了支持十进制和十六进制字面量，TypeScript还支持ECMAScript 2015中引入的二进制和八进制字面量。

```typescript
let num1: number = 7;
let num2: number = 7.5;
let num3: number = 0xffff;
let num4: number = 0b1010;
let num5: number = 0o744;
console.log(num1); // 7
console.log(num2); // 7.5
console.log(num3); // 65535
console.log(num4); // 10
console.log(num5); // 484
```

### string

```javascript
let str:string = '123';
console.log(str);   // 123
```

### object

关于`object`与 `Object` 实际开发中用的相对较少，为范围太大了.

**object (小写)**

object（小写）的含义是：所有非原始类型，可存储：对象、函数、数组等，由于限制的范围比较宽泛，在实际开发中使用的相对较少。

```typescript
let a: object; //a能存储的类型是【非原始类型】
a = {};
a = { name: "tom" };
a = [1, 3, 5, 7, 9];
a = function () {};
a = new String("123");
class Person {}
a = new Person();

// 以下代码，是将【原始类型】赋给a，有警告
a = 1; // 警告：不能将类型“number"分配给类型“object”
a = true; // 警告：不能将类型“boolean"分配给类型“object”
a = "你好"; // 警告：不能将类型“string"分配给类型“object”
a = null; // 警告：不能将类型“nul1"分配给类型"object"
a = undefined; // 警告：不能将类型“undefined"分配给类型“object”
```

**Object (大写)**

- 官方描述：所有可以调用object方法的类型。
- 简单记忆：除了 `undefined` 和 `null` 的任何值
- 由于限制的范围实在太大了！所以实际开发中使用率极低，

```typescript
let a: Object;
a = {};
a = { name: "tom" };
a = [1, 3, 5, 7, 9];
a = function () {};
a = new String("123");
class Person {}
a = new Person();
a = 1;
a = true;
a = "你好";

a = null; // 警告
a = undefined; // 警告
```

**声明对象类型**

- 索引签名：允许定义对象可以具有**任意数量的属性**，这些属性的**健和类型是可变的**，常用于：描述类型不确定的属性（具有动态属性的对象）

```typescript
let person: {
  name: string;
  age: number;
  [key: string]: any; // 索引签名
};

person = { name: "tom", age: 18, gender: "男", city: 12 };
```

### 函数

```typescript
let count: (a: number, b: number) => number;
count = function (x, y) {
  return x + y;
};
```

备注

- TypeScript 中的 `=>` 在陷数类型声明时表示函数类型，描述其参数类型和返回类型
- JavaScnipt 中的 `=>` 是一种定义函数的9语法，是具体的数实现
- 函数类型声明还可以使用：接口、自定义类型等方式。

### array

- 元素类型后面接上 `[]`
- 数组泛型

```typescript
let arr1: string[]
let arr2: Array<string>
arr = ['a', 'b', 'c']
arr2 = ['hello', 'world’]
```

### tuple

元组（Tuple）是一种特殊的数组类型，可以存储圆定数量的元素，并且每个元素的类型是已知的且可以不同，元组用于精确描述一组值的类型，？表示可选元素

元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。定义每个元素的数据类型，赋值的时候需要一一对应

tuple 不是关键字

```typescript
// 第一个元素必须是 string 类型，第二个元素必余是number 关型、
let arrl:[string,number]
// 第一个元素必须是 number 类型，第二个元素是可透的，如果存在，必须是 boolean 类型，
let arr2:[number,boolean?]
// 第一个元素必须是 nuber 类型，后面的元素可以是任意数量的 string 类型
let arr3:[nuber,....string[]]

// 可以赋值
arr1 = ['hello', 123]
arr2 = [108, false]
arr2 = [200]
arr3 = [1ee, ‘hello', ’jworld′]
arr3[108]

//不可以赋值，arr1声明时是两个元素，赋值的是三个
arr1 = ['he1lo', 123, false]
```

### enum

枚举可以定义一组命名常数。能增强代码的可读性，也让代码更好维护。

默认情况下，值从0开始，也可以手动赋值 没有赋值的变量的值会在前面赋值的变量的值上加1

- 数字枚举
  - 数字枚举一种最常见的枚举类型，其成员的值会**自动递增**，且数字枚举还具备反向映射的特点，可以通过值来获取对应的枚举成员名称
- 字符串枚举
  - 枚举成员的值是字符串
- 异构枚举

```typescript
enum enum1 {a,b,c}
enum enum2 {a=1,b,c=2}
enum enum3 {a=0,b=7,c}
console.log(enum1);   // a=0,b=1,c=2
console.log(enum2);   // a=1,b=2,c=2
console.log(enum3);   // a=0,b=7,c=8
console.log(enum3.a);   // 0

enum Direction {
  up,
  Down,
  Left,
  Right,
}
function walk(n:Direction){
  if (n == Direction.up) {
    console.log("向【上】走");
  } else if  (n == Direction.Down) {
    console.log("向【下】走");
  }
}
walk(Direction.Up)
walk(Direction.Down)

enum birection {
  up = "up",
  Down = "down”,
  Left = "left",
  Right ="right"
}
let dir: Direction = Direction.Up;
console.log(dir);  // 输出："up"
```

### any

定义any 可以传任何类型的值

```typescript
let a: any;
a = "123";
let list3: any[] = [1, true, "str"];
console.log(a); // 123
console.log(list3); // [1, true, "str"]
```

### unknown

未知类型

- 可以将任意值赋值给 `unknown` 类型。
- 保持类型检查完整，便用前需要通过类型断言或类型守卫。

```typescript
let a: unknown;
a = 99;
a = false;
a = "hello";
console.log(a); // hello

let x: string;
x = a; // 报错

// 解决方法
// 第一种
if (typeof a === "string") {
  x = a;
}
// 第二种（断言）
x = a as string;
x = <string>a;
```

### void

表示没有任何类型。 一般用在函数上，如果用在变量是，只能赋值 `undefined`

- `void` 通常用于函数返回值声明，含义：【函数不返回任何值，调用者也不应依赖其返回值进行任同操作】

```typescript
let v:void = undefined;
console.log(v);   // undefined

//无警告
function logMessage1(msg:string):void
console.log(msg)
}
//无警告
function logMessage2(msg:string):void{
  console.log(msg)
  return;
}
//无警告
function logMessage3(msg:string):void{
  console.1og(msg)
  return undefined
}

function f(): void {
  console.log("没有返回值的函数");
}
let result = f(); // 没有返回值的函数
if (result) {  // 警告，不应依赖其返回值进行任同操作

}
```

理解 `void` 与 `undefined`

- `void` 是一个广泛的概念，用来表达而 `undefined` 则是这种的具体实现之一。
- 因此可以说 `undefined` 是`void` 能接受的 “空" 状态的一种具体形式。
- 换句话说：`void` 包含 `undefined`，但 `void` 表达的语义超越了单纯的 undefined ，它是一种意图上的约定，而不仅仅是特定值的限制。

总结：若函数返回类型为 `void` ，那么:

1. 从语法上讲：函数是可以返回 `undefined` 的，至于显示返回，还是隐式返回，这无所谓！

2. 从语义上讲：函数调用者不应关心函数返回的值， 也不应依赖返回值进行任何操作！即使返回了 `undefined` 值。

### null 和 undefined

```typescript
// null
let n: null = null;
console.log(n); // null
// undefined
let u: undefined = undefined;
console.log(u); // undefined
```

### never

不能有值，undefined、null、‘’、0 都不行

- 几乎不用 never 去直接限制变量，因为没有意义
- never 一般是 TypeScript 主动推新出来的
- never 也可用于限制函数的返回值，例如抛错、递归函数

```typescript
/*
    不存在的值的类型
    是任何类型的子类型
    可以赋值给任何类型
 */
```

## 高级类型

### 联合类型

联合类型的语法规则和逻辑 “或” 的符号一致，表示其类型为连接的多个类型中的任意一个，本质上是一个交的关系

使用这种类型可以接收可能是字符串或者undefined的值

```typescript
let l:string | undefined;   // 可以联合其他的数据类型
console.log(l);   // undefined

type Status = number | string
type Gender = ‘男' | '女
```

### 交叉类型

通过 `&` 将多个类型合并为一个类型，包含了所需的所有类型的特性，本质上是一种并的操作

```typescript
// 面积
type Area = {
  height: number; // 高
  width:number; 	// 宽
}
// 地址
type Address = {
  num:number  //楼号
  cell:number  //单元号
  room:string //房间号
}
type House = Area & Address
const house:House ={
  height: 100, //高
  width: 100,		//宽
  num: 3，			//楼号
  cell: 4，		//单元号
  room: '702'		//房间号
}
// 适用于对象合并场景，将两个对象合并成一个对象并返回
function extend<T, U>(first: T, second: U) : T & U {
    let result: <T & U> = {}
    for (let key in first) {
        result[key] = first[key]
    }
    for (let key in second) {
        if(!result.hasOwnProperty(key)) {
            result[key] = second[key]
        }
    }
    return result
}
```

### 类型别名

类型别名会给一个类型起个新名字，类型别名有时和接口很像，但是可以作用于原始值、联合类型、元组以及其它任何你需要手写的类型

```typescript
type some = boolean | string;

// 类型别名可以是泛型
type Container<T> = { value: T };
```

类型别名和接口使用十分相似，都可以描述一个对象或者函数

区别在于，`interface`只能用于定义对象类型，而 `type` 的声明方式除了对象之外还可以定义交叉、联合、原始类型等，类型声明的方式适用范围显然更加广泛

### 类型索引

`keyof` 类似于 `Object.keys` ，用于获取一个接口中 Key 的联合类型。

```typescript
interface Button {
  type: string;
  text: string;
}

type ButtonKeys = keyof Button;
// 等效于
type ButtonKeys = "type" | "text";
```

### 类型约束

通过关键字 `extend` 进行约束，不同于在 `class` 后使用 `extends` 的继承作用，泛型内使用的主要作用是对泛型加以约束

```typescript
type BaseType = string | number | boolean;

// 这里表示 copy 的参数
// 只能是字符串、数字、布尔这几种基础类型
function copy<T extends BaseType>(arg: T): T {
  return arg;
}
```

### 映射类型

通过 `in` 关键字做类型的映射，遍历已有接口的 `key` 或者是遍历联合类型

```typescript
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

interface Obj {
  a: string;
  b: string;
}

type ReadOnlyObj = Readonly<Obj>;
```

### 条件类型

条件类型的语法规则和三元表达式一致，经常用于一些类型不确定的情况。

```typescript
T extends U ? X : Y
```

### 特殊情况

使用类型声明限制函数返回值为void时，TypeScript井不会严格要求函数返回空

- 是为了确保如下代码成立，我们灰道 Array·prototype.push 的返回一个数字，而Array.prototype.forEach方法期望其回调的返回类型是void

```typescript
type LogFunc = () => void;
const f1: LogFunc = function () {
  return 66;
};
const f2: LogFunc = () => 666;
const f3: LogFunc = function () {};
```

## 函数

### 命名函数

```typescript
function add1(x: number, y: number): number {
  return x + y;
}
console.log(add1(1, 2)); // 3
```

### 匿名函数

```typescript
let add2 = function (x: number, y: number): number {
  return x + y;
};
console.log(add1(1, 2)); // 3
```

### 可选参数

变量后面加上？，表示是可选参数，可以不传参 可选参数必须跟在必须参数后面

```typescript
function buildName1(firstName: string, lastName?: string) {
  if (lastName) return firstName + " " + lastName;
  else return firstName;
}
let name1 = buildName1("Bob");
let name2 = buildName1("Bob", "Adams");
console.log(name1); // Bob
console.log(name2); // Bob Adams
```

### 默认参数

变量先赋值，传参改变值

```typescript
function buildName2(firstName: string, lastName = "Smith") {
  return firstName + " " + lastName;
}
let name3 = buildName2("Bob");
let name4 = buildName2("Bob", undefined);
let name5 = buildName2("Bob", "Adams");
console.log(name3); // Bob Smith
console.log(name4); // Bob Smith
console.log(name5); // Bob Adams
```

### 剩余参数

接收不知个数的参数

```typescript
function getList(a: number, ...b: number[]) {
  return a + " " + b.join(" ");
}
let list = getList(0, 1, 2, 3);
console.log(list); // 0 1 2 3
```

## 类

```typescript
class Animal {
  name: string; // 属性
  constructor(name: string) {
    // 构造函数
    this.name = name;
  }
  run() {
    // 方法
    console.log(this.name + "去吃饭了");
  }
}

// 继承
class Dog extends Animal {
  // name:string;
  constructor(name: string) {
    super(name);
  }
  run() {
    // 重写
    console.log(this.name + "去跑步了");
  }
}

let an = new Animal("小红");
an.run(); // 小红去吃饭了
let d = new Dog("小明");
d.run(); // 小明去跑步了
```

### 属性简写

```typescript
class Phone {
  public name: string = "name";
  constructor(name: string) {
    this.name = name;
  }
}
// 简写
class Phone {
  constructor(public name: string) {}
}
```

### 属性修饰符

1.  public 公有： 默认修饰符，**所有成员**都可以访问
2.  protected 受保护： 只有**本类或者子类**可以访问
3.  private 私有： 只有本**类内**可以访问
4.  readonly 只读：只读属性必须在声明时或构造函数里被初始化，属性无法修改

```typescript
class Person {
  constructor(
  	public name:string,
    protected sex:string,
    private age:number,
    readonly idCard:string
  ) {}
  protected say() {
    console.log(‘’this.sex);
    console.log(this.age);
	}
  run() {
    console.log(this.name);
    console.log(this.sex);
    console.log(this.age);
  }
}
class Student extends Person {
  constructor() {
    super();
  }
  run() {
    console.log(this.name);
    console.log(this.sex);  // 子类里面，受保护属性可以访问
    // console.log(this.age);   // error
  }
}

let p = new Person('小明', '男', 18, '100');
console.log(p.name);  	 // 小明
// console.log(p.sex);   // error，受保护属性
// console.log(p.age);   // error，私有属性
p.run();
let s = new Student('小红', '女', 18, '1000');
console.log(s.name);     // 小红
// console.log(s.sex);   // error，受保护属性
// console.log(s.age);   // error，私有属性
s.run();
```

### 抽象类

概述：抽象类是**无法被实例化**的类，专门用来定义类的**结构和行为**，类中可以抽象方法，也可以写具体实现，抽象类主要用来为其派生类提供一个**基础结构**，要求具派生类**必须实现**其中的抽象方法。

简记：抽象类不能实例化，其意义是可以被继承，抽象类里可以有曾通方法、也可以有抽象方法，抽象方法必须在派生类中实现。

1.  抽象类做为其它派生类的基类使用
2.  一般不会直接被实例化
3.  抽象方法必须在派生类中实现

```typescript
abstract class Department {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  printName(): void {
    console.log("Department name: " + this.name);
  }
  abstract printMeeting(x: string): void; // 必须在派生类中实现
}

class technology extends Department {
  constructor(name: string) {
    super(name); // 在派生类的构造函数中必须调用 super()
  }
  printMeeting(x: string): void {
    console.log("Meets at 10am.");
  }
  generateReports(): void {
    console.log("Generating accounting reports...");
  }
}

let department: Department; // 允许创建一个对抽象类型的引用
// department = new Department();   // 错误: 不能创建一个抽象类的实例
department = new technology(); // 允许对一个抽象子类进行实例化和赋值
department.printName();
department.printMeeting();
// department.generateReports();   // 错误: 方法在声明的抽象类中不存在
```

## 接口

接口所描述的是一个对象相关的属性和方法，但并不提供具体创建此对象实例的方法。

interface是一种定义结构的方式，主要作用是为：类、对象、函数等规定一种契约，这样可以确保代码的一致性和类型安全，但要注意interface只能定义格式，**不能包含任何实现**

### 属性接口

传入的对象参数会包含很多属性，但是编译器只会检查那些必需的属性是否存在，并且其类型是否匹配

```typescript
interface User {
  name: string;
  sex: string;
  age: number;
}

function getUser(user: User, classes: string) {
  console.log(user.name);
  // console.log(user.major);   // error
  console.log(classes);
}
```

直接在里面添加对象，属性要与接口定义的属性一致

```typescript
getUser(
  {
    name: "小花",
    sex: "女",
    age: 16,
  },
  "1班",
);
```

如果先定义对象，再传入函数，对象里面的属性可以比接口定义 多出的属性可以输出，但是会报错，建议属性与接口的设置一致

```typescript
let obj = {
  name: "小花",
  sex: "女",
  age: 16,
  major: "计算机",
};
getUser(obj, "2班");

// 可选属性
interface style {
  font: string;
  color?: string;
  size?: string;
}
function getStyle(config: style): {
  font: string;
  color: string;
  size: string;
} {
  let newFont = { font: "", color: "#000000", size: "16px" };
  newFont.font = config.font;
  if (config.color) {
    newFont.color = config.color;
  }
  if (config.size) {
    newFont.size = config.size;
  }
  return newFont;
}

let s = getStyle({
  font: "微软雅黑",
  // color: '#fff',
  // size: '14px',
});
console.log(s);
```

### 函数接口

使用接口表示函数类型 函数的参数名不需要与接口里定义的名字相匹配

```typescript
interface Search {
  (source: string, subString: string): boolean;
}

// let mySearch: Search;
let mySearch: Search;
mySearch = function (src: string, sub: string) {
  let result = src.search(sub);
  return result > -1;
};

let ms = mySearch("123456789", "123");
console.log(ms); // true
```

### 可索引接口

```typescript
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
console.log(myStr);
```

### 类接口

接口描述了类的公共部分 一个接口可以继承多个接口

```typescript
interface Animals {
  eat(): void;
}
interface Person extends Animals {
  work(): void;
}
class runs implements Person {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  eat() {
    console.log(this.name + "在吃饭");
  }
  work() {
    console.log(this.name + "在写程序");
  }
}
let r = new runs("小明");
r.eat(); // 小明在吃饭
r.work(); // 小明在写程序
```

## 泛型

使用泛型来创建可重用的组件，一个组件可以支持多种类型的数据，传入和返回的数据类型需要一致

泛型允许我们在定义函数、类或接口时，使用类型参数来表示未指定的类型，这些参数在具体使用时，才被指定具体的类型。

泛型能让同一段代码适用于多种类型，同时仍然保持类型的安全性。

### 泛型函数

- 泛型可以有多个 `<T, U>`

```typescript
function getData<T, U>(a: T, b: U): T | U {
  return a % 2 ? a : b;
}
getData<number, string>(123, "123");
```

### 泛型接口

```typescript
interface PersonInterface<T> {
  name: string,
  age: number,
  info: T
}

// 传入number类型
let p: PersonInterface<number> = {
  name: 'tom',
  age: 18,
  info: 18
}

// 也可以传入交叉类型
type JobInfo = {
  title: string;
  company: string;
}
let p: PersonInterface<JobInfo> = {
  name: 'tom',
  age: 18,
  info: {
    title：'高级开发工程师',
    company：'发发发科技有限公司'
  }
}
```

### 泛型类

```typescript
class Person<T> {
  constructor(
  	public name: string,
   	public age: number,
   	public info: T,
  ) {}
  say() {
    console.log(this.info);
  }
}

type JobInfo = {
  title: string;
  company: string;
}
let p = new Person<JobInfo>(
  name: 'tom',
  age: 18,
  info: {
    title：'高级开发工程师',
    company：'发发发科技有限公司'
  }
);
```

## 模块

1.  解决命名相同的问题
2.  命名空间里面的成员默认是私有的

```typescript
namespace A {
  interface Animal {
    name: string; // 属性
    eat(): void;
  }
  export class Cat implements Animal {
    name: string;
    constructor(name: string) {
      this.name = name;
    }
    eat() {
      console.log(`${this.name}在吃猫粮`);
    }
  }
}

namespace B {
  interface Animal {
    name: string; // 属性
    eat(): void;
  }
  export class Cat implements Animal {
    name: string;
    constructor(name: string) {
      this.name = name;
    }
    eat() {
      console.log(`${this.name}在吃鱼`);
    }
  }
}

let c1 = new A.Cat("小明");
let c2 = new B.Cat("小花");
c1.eat(); // 小明在吃猫粮
c2.eat(); // 小花在吃鱼
```

## 类型声明文件

类型声明文件是 `TypeScript` 中的一种特殊文件，通常以 `.d.ts` 作为扩展名。它的主要作用是**为现有的 `JavaScript` 代码提供类型信息**，使得`TypeScript` 能够在使用这些`JavaScript`库或模块时进行**类型检查和提示**。

## 装饰类

装饰器本质是一种特殊的**函数**，可以对类、方法、访问符、属性、参数进行扩展，是一种在不改变原类和使用继承的情况下，动态地**扩展对象功能**。

装饰器自2015年在ECMAScript-6中被提出到现在，已将近10年。截止目前，装饰器依然是**实验性特性**，需要开发者手动调整配置，来开启装饰器支持。

- 分类：类装饰器、属性装饰器、方法装饰器、访问器装饰器、参数装饰器
  - 普通装饰器：无法传参
  - 装饰器工厂：可以传参

当多个装饰器应用于一个声明上，将由上至下依次对装饰器表达式求值，求值的结果会被当作函数，由下至上依次调用。

备注：虽然Typescript5.0中可以直接使用类装饰器，但为了确保其他装饰器可用，现阶段使用时，仍建议使用 **experirentalDecorators** 配置来开启装饰器支持，而且不排除在来的版本中，官方会进一步调整装饰器的相关语法！参考：《TypeScript 5.0发版公告》

### 类装饰器

类装饰器是一个应用在**类声明**上的函数，可以为类添加额外的功能，或添加额外的逻辑

- 类装饰器在类声明之前被声明（紧靠着类声明）
- 参数：target 是被装饰的类

#### 普通装饰器

```typescript
function logClass(target: any) {
  // target是当前类
  target.prototype.url = "动态扩展的属性";
  target.prototype.run = function () {
    console.log("进入run方法");
  };
}

@logClass
class HttpClient {
  constructor() {}
  getData() {}
}
let http: any = new HttpClient();
console.log(http.url); // 动态扩展的属性
http.run(); // 进入run方法
```

#### 关于返回值

类装饰器**有**返回值：若类装饰器返回一个新的类，那这个新类将替换掉被装饰的类

类装饰器**无**返回值：若类装饰器无返回值或返回undefined，那被装饰的类不会被替换。

#### 构造类型

```typescript
/*
  new 表示：该类型是可以用new操作符调用。
  ...args 表示：构造器可以接受【任意数量】的参数。
  any[] 表示：构造器可以接受【任意类型】的参数。
  {} 表示：返回类型是对象（非null、非undefined的对象）。
*/

// 定义Constructor类型，表示构造函数
type Constructor = new (...args:any[]) => {}

type Constructor {
  new (...args:any[]):{}
  wife:string  // 需要指定静态属性
}
```

#### 装饰器工厂

装饰器工厂是一个返回装饰器函数的函数，可以为装饰器添加参数，可以更灵活地控制装饰器的行为，

```typescript
// logClass 是装饰器工厂，返回一个装饰器
function logClass(params: any) {
  return function (target: any) {
    // params是传入的参数
    console.log(params); // http://www.baidu.com
    // target是被装饰的类
    console.log(target);
    target.prototype.url = params;
    target.prototype.run = function () {
      console.log("进入run方法");
    };
  };
}

@logClass("http://www.baidu.com")
class HttpClient {
  constructor() {}
  getData() {}
}
let http: any = new HttpClient();
console.log(http.url); // http://www.baidu.com
```

#### 重载

```typescript
function logClass(target: any) {
  console.log(target);
  return class extends target {
    url: any = "修改后的url";
    getData() {}
  };
}

@logClass
class HttpClient {
  url: string | undefined;
  constructor() {
    this.url = "原来的url";
  }
  getData() {
    console.log("原来的数据");
  }
}
let http: any = new HttpClient();
console.log(http.url); // 修改后的url
```

#### 装饰器组合

装饰器可以组合使用，执行顺序为：先【由上到下】的执行所有的装饰器工厂，依次获取到装饰器，然后再【由下到上】执行所有的装饰器。

### 方法装饰类

- 方法装饰器声明在一个方法的声明之前（紧靠着方法声明）
- 参数
  - target 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象
  - methodName 成员的名字
  - desc 成员的属性描述符

```typescript
function logMethods(params: any) {
  return function (target: any, methodName: any, desc: any) {
    console.log(target);
    console.log(methodName); // getData
    console.log(desc);
    // 修改装饰器的方法
    let oMethod = desc.value;
    desc.value = function (...args: any[]) {
      args = args.map((value) => {
        return String(value);
      });
      console.log(args); // ["123", "abc"]
      oMethod.apply(this, args);
    };
  };
}

class HttpClient2 {
  url: string | undefined;
  constructor() {}
  @logMethods("http://www.baidu.com")
  getData(...args: any[]) {
    console.log("getData方法"); // getData方法
  }
}
let http2: any = new HttpClient2();
http2.getData(123, "abc");
```

### 属性装饰类

- 属性装饰器声明在一个属性声明之前（紧靠着属性声明）
- 参数
  - target 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象
  - propertykey 是成员的名字

```typescript
function logProperty(params: any) {
  return function (target: any, propertykey: any) {
    console.log(target);
    console.log(propertykey); // url
    target[propertykey] = params;
  };
}

class HttpClient3 {
  @logProperty("http://www.baidu.com")
  url: string;
  constructor(url: string) {
    this.url = url;
  }
  getData() {
    console.log(this.url);
  }
}
let http3: any = new HttpClient3("111");
console.log(http3.url); // http://www.baidu.com
```

### 参数装饰类

- 参数装饰器声明在一个参数声明之前（紧靠着参数声明）
- 参数
  - target 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象
  - paramsName 成员的名字
  - paramsIndex 参数在函数参数列表中的索引

```typescript
function logParams(params: any) {
  return function (target: any, paramsName: any, paramsIndex: any) {
    console.log(target);
    console.log(paramsName); // getData
    console.log(paramsIndex); // 0
  };
}

class HttpClient4 {
  url: string | undefined;
  constructor() {}
  getData(@logParams("xxx") uuid: any) {
    console.log(uuid); // 123
  }
}
let http4: any = new HttpClient4();
http4.getData(123);
```
