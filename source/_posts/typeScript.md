---
title: TypeScript
date: 2019-01-21 17:21:43
categories: 
  - TypeScript
tags: 
  - TypeScript
---
# [TypeScript](https://www.tslang.cn/docs/handbook/basic-types.html)
- JavaScript 的超集
- 微软开发的开源编程语言
- 遵循最新的 ES6、ES5规范
- 提供了类型校验

## 好处
- 为日后维护带来方便
- 增加代码可读性
- 提高安全性

## 安装
```bash
> npm install -g typescript
```

## 编译
```bash
> tsc <file>
```

## 配置
### 生成 tsconfig.json
```bash
> tsc --init 
```

## 使用
### 数据类型
- TypeScript 中为了使编写的代码更规范，更利于维护，增加了类型校验

#### 布尔值 `boolean`
```typescript
let isDone: boolean = false;
```

#### 数值 `number`
- 和JavaScript一样，TypeScript里的所有数字都是浮点数。 这些浮点数的类型是 number。 除了支持十进制和十六进制字面量，TypeScript还支持ECMAScript 2015中引入的二进制和八进制字面量。

```typescript
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;
```

#### 字符串 `string`
```typescript
let name: string = "bob";
name = "smith";
```
##### 可以使用`模板字符串`

#### 数组类型 `list`
##### 「类型 + 方括号」来表示数组 
```typescript
// 数组中的所有元素都是同一类型
let list: number[] = [1, 2, 3];
```

##### 使用数组`Array Generic`泛型表示
```typescript
// 使用数组 泛型
let list: Array<number> = [1, 2, 3];
```

##### 用接口表示数组
```typescript
interface NumberArray {
    [index: number]: number; // 只要 index 的类型是 number ，那么 值得类型 必须是 number
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```
##### 类数组
```typescript
// 事实上常见的类数组都有自己的接口定义，如 IArguments, NodeList, HTMLCollection 等
function sum() {
    let args: IArguments = arguments;
}
```

##### `any` 在数组中的应用
```typescript
let list: any[] = [{a:'b'}, 25, 'a'];
```

#### 枚举 `enum`
- enum类型是对JavaScript标准数据类型的一个补充。 像C#等其它语言一样，使用枚举类型可以为一组数值赋予友好的名字。
- 用于取值被限定在一定范围内的场景，比如一周只能有七天
- 最好不要使用手动赋值

##### 定义
```typescript
enum 枚举名 {
  标识符[=整形常数]
}
```
```typescript
enum Color {Red, Green, Blue}
let c: Color = Color.Green;
```
##### 编号
- 默认情况下，从0开始为元素编号。
- 你也可以手动的指定成员的数值。 例如，我们将上面的例子改成从 1开始编号：
  - 手动赋值后，未手动赋值的枚举项会接着上一个枚举项递增
  - 如果未手动赋值的枚举项与手动赋值的重复了，TypeScript 不会察觉到这一点
  - 当手动赋值的枚举项不是`数字`时，需要使用类型断言来让`tsc`无视类型检查
  - 手动赋值的枚举项也可以是`小数`或`负数`，此时未手动赋值的项递增步仍为`1`
```typescript
enum Color {Red = 1, Green, Blue=<any>"A"}
let c: Color = Color.Green;
```
- 也可以全部都采取手动赋值
```typescript
enum Color {Red = 1, Green = 2, Blue = 4}
let c: Color = Color.Green;
```

- 可以由枚举的值得到他的名字
```typescript
enum Color {Red = 1, Green, Blue}
let colorName: string = Color[2];

console.log(colorName);  // 显示'Green'因为上面代码里它的值是2
```
##### 枚举项
枚举项分为`常数项（constant member）`和`计算所得项（computed member）`
- 常数项
  - 不具有初始化函数并且之前的枚举成员是常数。在这种情况下，当前枚举成员的值为上一个枚举成员的值加 1。但第一个枚举元素是个例外。如果它没有初始化方法，那么它的初始值为 0。
  - 枚举成员使用常数枚举表达式初始化。常数枚举表达式是 TypeScript 表达式的子集，它可以在编译阶段求值。当一个表达式满足下面条件之一时，它就是一个常数枚举表达式：
    - 数字字面量
    - 引用之前定义的常数枚举成员（可以是在不同的枚举类型中定义的）如果这个成员是在同一个枚举类型中定义的，可以使用非限定名来引用
    - 带括号的常数枚举表达式
    - +, -, ~ 一元运算符应用于常数枚举表达式
    - +, -, *, /, %, <<, >>, >>>, &, |, ^ 二元运算符，常数枚举表达式做为其一个操作对象。若常数枚举表达式求值后为 NaN 或 Infinity，则会在编译阶段报错
- 计算所得项
  - 如果紧接在计算所得项后面的是未手动赋值的项，那么它就会因为无法获得初始值而报错：
```typescript
enum Color {Red, Green, Blue = "blue".length} // '"blue".length 就是一个计算所得项。
``` 
##### 常数枚举 `const enum`
常数枚举是使用 `const enum` 定义的枚举类型
- 常数枚举与普通枚举的区别是，它会在编译阶段被删除，并且不能包含计算成员。
```typescript
const enum Directions {
    Up,
    Down,
    Left,
    Right
}

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```
编译结果
```javascript
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```

##### 外部枚举 `declare enum`
- 外部枚举用来描述已经存在的枚举类型的形状。
- `declare` 定义的类型只会用于编译时的检查，编译结果中会被删除。
```typescript
declare enum Directions {
    Up,
    Down,
    Left,
    Right
}

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```
编译结果
```javascript
var directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```


#### 任意类型 `any`
- 为不清楚类型的变量指定一个类型
```typescript
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```

#### 空值 void
- 表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 void：

```typescript
// 方法没有返回值
function warnUser(): void {
    console.log("This is my warning message");
}
// 方法有返回值
function Num(): number {
    return 123
}
```

#### 联合类型 `Union Types`
- 表示取值可为多种类型中的一种
- 当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，只能访问此联合类型的所有类型里共有的属性或方法
- 联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型
```typescript
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```

#### Null 和 Undefined
- 默认情况下 `null` 和 `undefined` 是所有类型的子类型。
- 指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自。
```typescript
let num:number | null | undefined;
```

#### 元组 `Tuple`
- 元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。 比如，你可以定义一对值分别为 string和number类型的元组。
- 元祖合并了不同类型的对象
- 数组合并了相同类型的对象

```typescript
// Declare a tuple type
let x: [string, number];
// Initialize it
x = ['hello', 10]; // OK
// Initialize it incorrectly
x = [10, 'hello']; // Error
```
##### 访问
- 当访问一个已知索引的元素，会得到正确的类型：
```typescript
console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'
```
- 当访问一个越界的元素，会使用联合类型替代：
```typescript
x[3] = 'world'; // OK, 字符串可以赋值给(string | number)类型
console.log(x[5].toString()); // OK, 'string' 和 'number' 都有 toString
x[6] = true; // Error, 布尔不是(string | number)类型
```

#### Never
- never类型表示的是那些永不存在的值的类型
- 声明`never`的变量只能赋予`never`类型的值

#### Object
- object表示非原始类型
  - 除number，string，boolean，symbol，null或undefined之外的类型
  
  
#### 类型推论
- TypeScirpt 在没有明确的指定类型的时候会推测出一个类型
- 如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成 `any` 类型而完全不被类型检查
```typescript
let myFavoriteNumber = 'seven';
myFavoriteNumber = 7; // // index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

#### 类型断言
- 手动指定一个值得`类型`
- 类型断言有两种形式
```typescript
// 尖括号 语法
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;

// as 语法 => jsx 中 必须用这个
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
```

### 函数
> 函数是 JavaScript 中一等功民
#### 声明
- 一个函数有`输入`和`输出`，要在 TypeScript 中对其进行约束，需要把`输入`和`输出`都考虑在内
- 在 TypeScript 的类型定义中，=> 用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型。
##### 具名函数
```typescript
function f():number {
  return 123
}
```
##### 匿名函数
```typescript
let f = function():number {
  return 123
}
// 这段代码只对登号右侧的匿名函数进行了类型定义，等号左边的 f 是通过赋值操作进行类型推论而推断出来的，如果要对 f 添加类型，则：
let f :(x:number,y:number) => number = function(x:number,y:number):number {
  return x+y
}
```
##### 用 接口 定义函数
```typescript
interface F {
  (x:number,y:number):number;
}

let f:F;
f=function(x:number,y:number):number {
  return x+y
}

```

#### 参数
- 函数调用时，输入多余或少于要求的参数，是不被允许的
##### 定义参数类型
```typescript
function f(x:number,y:number):number {
  return 123
}
```
##### 可选参数` ?`
- 可选参数必须写在最后
```typescript
function f(x:number,y?:number):number {
  return 123
}
```
##### 默认参数
```typescript
function f(x:number=20,y?:number):number {
  return 123
}
```
##### 剩余参数
```typescript
function f(a:number,...rest:number[]):number {
  return 213
}
```
#### 函数重载
- 重载允许一个`函数`接收`不同数量`或`类型`的参数时，做出不同的处理
```typescript
function f(name:string):string
function f(age:number):number
function f(str:any) {
  if (typeof str === 'string'){
    return `str${str}`
  } else {
    return `str${str}`
  }
}
```

### 类
- 传统方法中，JavaScript 通过构造函数实现类的概念，通过原型链实现继承
- ES6中 `class` 实现
#### 类的概念
- 类（Class）：定义一个事物的抽象特点，包涵他的属性和方法
- 对象（Object）：类的实例。通过`new`实现
- 面向对象（OOP）的三大特性：封装、继承、多态
- 封装（Encapsulation）：将数据的操作细节隐藏起来，只暴露对外的接口。外界调用端不需要（也不可能）知道细节，就能通过对外提供的接口来访问该对象，同时也保证了外界无法任意更改对象内部的数据
- 继承（Inheritance）：子类继承父类，子类除了拥有父类的所有特性外，还有一些更具体的特性
- 多态（Polymorphism）：由继承而产生了相关的不同的类，对同一个方法可以有不同的响应。比如：`Cat`和`Dog`都继承自`Animal`，但是分别实现了自己的`eat`方法。此时针对某一实例，我们无需了解它是`Cat`还是`Dog`，就可以直接调用`eat`方法，程序会自动判断出应如何执行`eat`
- 存取器（getter&setter）：用以改变属性的读取和赋值行为
- 修饰符（Modified）：修饰符是一些关键字，用于限定成员或类型的性质，比如`public`表示公有属性或方法
- 抽象类（Abstract Class）：抽象类是供其他类继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须在子类中实现
- 接口（Interfaces）：不同类之间公有的属性或方法，可以抽象成一个接口。接口可以被类实现（implements）。一个类只能继承自另一个类，但是可以实现多个接口

#### 定义类
- class：定义类
- constructor：定义构造函数
```typescript
class Person {
  name:string; // 属性 省略了 public 关键词
  constructor(name:string){ // 构造函数 实例化类时触发的方法
    this.name = name
  } 
  run():void{
   console.log(this.name); 
  }  
}
```
#### 继承
- extend：实现继承
- super：调用父类的构造函数和方法
```typescript
class Web extends Person{
  constructor(name:string){
    super(name)
  }
}
```
#### 存取器
使用 getter 和 setter 可以改变属性的赋值和读取行为：
```typescript
class Animal {
  constructor(name:string){
    this.name=name;
  }
   get name(){
    return 'a'
  }
   set name(value){
    console.log('setter'+value);
  }
}
```


#### 修饰符
属性不加修饰符默认是公有属性
##### 公有修饰符 `public`
- `public` 修饰的属性或方法都是公有的，可以在任何地方被访问到

##### 保护类型 `protected`
- `protected` 修饰的属性或方法是受保护的，它和`private`类似，区别是他在子类中也是被允许访问的
- 在当前类里、子类里可以访问

##### 私有类型 `private`
- `private`修饰的属性或方法是私有的，不能在声明他的类的外部访问
- 只有在当前类里可以访问

##### 只读属性 `readonly`


##### 静态类型 `static`
- 静态方法：使用`static`修饰的方法称为静态方法，他们不需要实例化，而是直接通过类来调用
- 静态属性：使用`static`修饰的属性称为静态属性，他们不需要实例化，而是直接通过类来调用
```typescript
class Animal {
  constructor(name:string){
    this.name=name
  }

  static isAnimal(a){
    return a instanceof Animal;
  }
}

let a = new Animal('a');
Animal.isAnimal(a); //true
a.(aisAnimal); // TypeError: a.isAnimal is not a function
```


#### 多态
父类定义一个方法不去实现，让继承他的子类去实现，每一个类有不同的表现
```typescript
class Animal {
  name:string;
  constructor(name:string){
    this.name=name
  }
 eat():void{
    console.log(this.name); 
 }  
}
class Dog extends Animal {
  constructor(name:string){
    super(name)
  }
  eat(name:string):void{
    console.log(`${name}吃`);
  }
}
```

#### 抽象类
提供其他类继承的基类，不能直接被实例化
##### 定义
`abstract`定义抽象类和抽象方法
- 抽象类中的抽象方法不包含具体实现并且必须在派生类中实现
```typescript
abstract class Animal {
    public name;
    public constructor(name) {
        this.name = name;
    }
    public abstract sayHi();
}

let a = new Animal('Jack'); // index.ts(9,11): error TS2511: Cannot create an instance of the abstract class 'Animal'.

class Cat extends Animal {
    sayHi(){
       console.log(`${this.name} say hi.`);
    }
    public eat() {
        console.log(`${this.name} is eating.`);
    }
}

let cat = new Cat('Tom');

// index.ts(9,7): error TS2515: Non-abstract class 'Cat' does not implement inherited abstract member 'sayHi' from class 'Animal'.
```
- 抽象方法只能放在抽象类里
- 抽象类的派生类必须必须实现抽象类里的抽象方法
```typescript
abstract class Animal {
    abstract makeSound(): void;
    move(): void {
        console.log('roaming the earch...');
    }
}
```

### 接口 `Interfaces`
TypeScript 中，使用 接口 来定义对象的类型

#### 作用
- 在面向对象的编程中，接口是一种规范的定义，他定义了行为和动作的规范，在程序设计里，接口起到了限制和规范的作用。
- 接口定义了某一批类所需遵守的规范，接口不关心这些类的内部状态数据，也不关心这些类里方法的实现细节，他只规定这批类里必须提供默写方法，提供这些方法的类就可以满足实际需求。

#### 分类
##### 普通接口
###### 定义
```typescript
interface FullName {
  firstName:string,
  secondName:string,
}
```
###### 使用
- 赋值的时候，变量的形状和接口的形状必须保持一致`不可以多，不可以少`
```typescript
interface FullName {
  firstName:string,
  secondName:string,
}

let name:FullName= {
   firstName:'a',
   secondName:'bc',
}
```

##### 函数类接口(混合类型)
- 使用接口定义一个函数需要符合的类型
```typescript
interface Config {
  (value:string):string;
}
let setData:Config=function(value:string):string {
  return ''
}
```
- 当函数有自己的属性和方法
```typescript
interface Counter {
  (start:number):string;
  interval:number;
  reset():void;
}

function getCounter():Counter {
  let counter = <Counter>function(start:number) {}
  counter.interval=123;
  counter.reset=function() {}
  return counter
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```

##### 可索引接口
对数值、对象的约束
````typescript
interface UserArr{
  [index:number]:number
}
let arr:UserArr=[123,13]
````
```typescript
interface UserObj {
  [index:string]:string
}
let user:UserObj={w:'qw'}
```

##### 类类型接口 
- 实现`implements`是面向对象的一个重要概念。
- 一般来讲，一个类只能继承自另一个类，有时候不同类之间可以有一些共有的特性，这时候可以把特性提取成接口`interfaces`，用`implements`关键字来实现，这个特性大大提高了面向对象的灵活性
- 举例来说，门是一个类，防盗门是门的子类。如果防盗门有一个报警器的功能，我们可以简单的给防盗门添加一个报警方法。这时候如果有另一个类，车，也有报警器的功能，就可以考虑把报警器提取出来，作为一个接口，防盗门和车都去实现它：
```typescript
interface Alarm {
    alert();
}

class Door {
}

class SecurityDoor extends Door implements Alarm {
    alert() {
        console.log('SecurityDoor alert');
    }
}

class Car implements Alarm {
    alert() {
        console.log('Car alert');
    }
}
```
- 一个类也可以实现多个接口
```typescript
interface Alarm {
    alert();
}

interface Light {
    lightOn();
    lightOff();
}

class Car implements Alarm, Light {
    alert() {
        console.log('Car alert');
    }
    lightOn() {
        console.log('Car light on');
    }
    lightOff() {
        console.log('Car light off');
    }
}
```
上例中，Car 实现了 Alarm 和 Light 接口，既能报警，也能开关车灯。

##### 接口扩展
```typescript
interface A {
  a():void;
}
interface B extends A{
  b():void;
}
class C implements B{
  a():void {}
  b():void {}
}
```

#### 可选属性
- 可选属性的含义是 该属性 可以不存在，但是 `仍然不允许添加未定义的属性`
- 当不需要完全匹配一个形状时，可以用可选属性
```typescript
interface Person {
  name:string,
  age?:number
}
let ton:Person = {
  name:'Tom'
}

// 添加 未定义属性 报错
let jone:Person = {
   name: 'Tom',
    age: 25,
    gender: 'male'
}
```

#### 任意属性
- 希望一个`接口`允许有任意的属性
```typescript
interface Person {
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    name: 'Tom',
    gender: 'male'
};
```

- 一旦定义`任意属性`，那么`确定属性` 和`可选属性`的类型都必须是他的类型的子集
```typescript
interface Person {
    name: string;
    age?: number;
    [propName: string]: string;
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};

// index.ts(3,5): error TS2411: Property 'age' of type 'number' is not assignable to string index type 'string'.
// index.ts(7,5): error TS2322: Type '{ [x: string]: string | number; name: string; age: number; gender: string; }' is not assignable to type 'Person'.
//   Index signatures are incompatible.
//     Type 'string | number' is not assignable to type 'string'.
//       Type 'number' is not assignable to type 'string'.

//上例中，任意属性的值允许是 string，但是可选属性 age 的值却是 number，number 不是 string 的子属性，所以报错了。

//另外，在报错信息中可以看出，此时 { name: 'Tom', age: 25, gender: 'male' } 的类型被推断成了 { [x: string]: string | number; name: string; age: number; gender: string; }，这是联合类型和接口的结合。
```

#### 只读属性 `readonly`
当希望对象中的一些字段只能在`创建`的时候被赋值，可以用`只读`属性
```typescript
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    id: 89757,
    name: 'Tom',
    gender: 'male'
};

tom.id = 9527; // 报错
```




### 泛型
软件工程中，我们不仅要创建一致的定义良好的API，同时也要考虑可重用性。 组件不仅能够支持当前的数据类型，同时也能支持未来的数据类型，这在创建大型系统时为你提供了十分灵活的功能。
#### 定义
```typescript
function identity<T>(arg: T): T {
    return arg;
}
```

#### 泛型类
```typescript
class Min<T>{
  public list:T[]=[]
  add(value:T):void{
    this.list.push(value)
  }
  min():T{
    let min = this.list[0];
    this.list.forEach(item => {
      if (min > item){
        min = item;
      }
    })
    return min
  }
}
const m = new Min<number>()
```

#### 泛型接口
```typescript
interface Config<T> {
  <T>(value:T):T;
}
function f<T>(value:T):T {
  return value
}

let a = f<number>(1);
let b:Config<number> = f
b(2)
```

### 声明文件
- 当使用第三方库时，需要引用他的声明文件，才能获得对应的代码补全，接口提示功能
- 通常会吧 `声明语句` 放到一个单独的文件 （*.d.ts）中
- `声明文件` 必须以 `.d.ts` 结尾

#### 声明语句 `declare var`
- declare var 没有定义一个变量，只是定义了全局变量的类型
```typescript
declare var jQuert:(selector:string)=>any
jQuert('#foo')
```

#### 第三方声明文件
[搜索地址](http://microsoft.github.io/TypeSearch/)

#### 书写声明文件
在不同场景下，声明文件的内容和使用方式有所区别
- 全局变量：通过 `script` 标签引入的第三方库，注入全局变量
- npm包：通过 import foo from 'foo' 导入，符合 ES6模块规范
- UMD库：即可以通过 `script` 标签引入，也可以通过 ES6 模块规范
- 模块插件：通过 `import` 导入后，可以改变另一模块的结构
- 直接扩展全局变量：通过 `script` 引入后， 改变一个全局变量的结构。比如 ：为 Array.prototype 新增一个方法
- 通过导入扩展全局变量：通过 import 导入后，可以改变一个全局变量的结构


### 内置对象
JavaScript 中的很多[内置对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects),可以直接在 TypeScript 中当做定义好了的类型

#### ECMAScript 的内置对象
```typescript
let b: Boolean = new Boolean(1);
let e: Error = new Error('Error occurred');
let d: Date = new Date();
let r: RegExp = /[a-z]/;
```

#### DOM 和 BOM 的 内置对象
Document、HTMLElement、Event、NodeList 等
```typescript
let body: HTMLElement = document.body;
let allDiv: NodeList = document.querySelectorAll('div');
document.addEventListener('click', function(e: MouseEvent) {
  // Do something
});
```

#### TypeScript 核心库的定义文件
- TypeScript 核心库的定义文件中定义了所有浏览器环境需要用到的类型，并且是预置在 TypeScript 中的。
- 当你在使用一些常用的方法的时候，TypeScript 实际上已经帮你做了很多类型判断的工作了

#### 用 TypeScript 写 Node.js
Node.js 不是内置对象的一部分，如果想用 TypeScript 写 Node.js，则需要引入第三方声明文件：
```bash
> npm install @types/node --save-dev
```

### 类型别名 `type`
- 给一个类型取一个新名字
- 类型别名适用于 `联合类型`
```typescript
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n:NameOrResolver) {
  if (typeof n === 'string'){
    return n;
  } else {
    return n();
  }
}
```

### 字符串字面量类型
- 约束取值只能是`某几个`字符串中的一个
```typescript
type EventNames = 'click'| 'scroll'|'mouseOver'
function handleEvent(ele:Element,event:EventNames) {
  // do sth
}
```



## 参考文章
- [TypeScript 入门教程](https://ts.xcatliu.com/basics/type-of-function.html)
- [TypeScript Handbook（中文版）](https://zhongsp.gitbooks.io/typescript-handbook/content/)
