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
- 「类型 + 方括号」来表示数组 
```typescript
// 数组中的所有元素都是同一类型
let list: number[] = [1, 2, 3];
```

- 使用数组泛型表示
```typescript
// 使用数组 泛型
let list: Array<number> = [1, 2, 3];
```

- 用接口表示数组
```typescript
interface NumberArray {
    [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```
- 类数组
```typescript
// 事实上常见的类数组都有自己的接口定义，如 IArguments, NodeList, HTMLCollection 等
function sum() {
    let args: IArguments = arguments;
}
```

#### 元组 `Tuple`
- 元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。 比如，你可以定义一对值分别为 string和number类型的元组。

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

#### 枚举 `enum`
- enum类型是对JavaScript标准数据类型的一个补充。 像C#等其它语言一样，使用枚举类型可以为一组数值赋予友好的名字。

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
```typescript
enum Color {Red = 1, Green, Blue}
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

#### 联合类型
- 表示取值可为多种类型中的一种
```typescript
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```

#### Null 和 Undefined
- 默认情况下null和undefined是所有类型的子类型。
- 指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自。
```typescript
let num:number | null | undefined;
```

#### Never
- never类型表示的是那些永不存在的值的类型
- 声明`never`的变量只能赋予`never`类型的值

#### Object
- object表示非原始类型
  - 除number，string，boolean，symbol，null或undefined之外的类型

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
```

#### 参数
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
function f(a:number,...result:number[]):number {
  return 213
}
```
##### 函数重载

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
#### 定义类
- class
- constructor
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
- extend
- super
```typescript
class Web extends Person{
  constructor(name:string){
    super(name)
  }
}
```
#### 修饰符
属性不加修饰符默认是公有属性
##### public
公有修饰符
- 在当前类里面、子类、类外面都可以访问

##### protected
保护类型
- 在当前类里、子类里可以访问

##### private
私有类型
- 只有在当前类里可以访问

##### readonly
只读属性

##### static
静态类型

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

### 接口
#### 作用
在面向对象的编程中，接口是一种规范的定义，他定义了行为和动作的规范，在程序设计里，接口起到了限制和规范的作用。
接口定义了某一批类所需遵守的规范，接口不关心这些类的内部状态数据，也不关心这些类里方法的实现细节，他只规定这批类里必须提供默写方法，提供这些方法的类就可以满足实际需求。

#### 定义
##### 普通接口
```typescript
interface FullName {
  firstName:string,
  secondName:string,
}
function printName(name:FullName) {
  console.log(name);
}
```
##### 函数类接口
```typescript
interface Config {
  (value:string):string;
}
let setData:Config=function(value:string):string {
  return ''
}
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

##### 类类型接口 `implements`
```typescript
interface Animal {
  name:string;
  eat(str:string):void;
}
class Dog implements Animal{
  name:string;
  constructor(name:string){
    this.name = name
  }
  eat(str:string):void {
    console.log(str)
  }
}
```
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

## 参考链接
- [TypeScript 入门教程](https://ts.xcatliu.com/basics/type-of-function.html)
- 
