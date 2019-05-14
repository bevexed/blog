---
title: Generator
date: 2019-05-13 16:17:55
categories:
- JavaScript
tags:
- 异步
---
## 基本概念
- `Generator函数`是 ES6 提供的一种异步编程解决方案，语法行为与传统函数不同
- 语法上，可以将它理解成一个状态机，封装了多个内部状态
- 执行上，他是一个遍历器对象生成函数。
  - 执行 Generator函数 会返回一个遍历器对象，
  - 返回的遍历器对象可以依次遍历Generator函数内部的每一个状态
- 形式上，Generator函数是一个普通函数，但是有两个特征：
  1. function命令与函数名之间有一个星号
  2. 函数体内部使用 `yield` 语句来定义不同的内部状态
  ```javascript
    // 定义 一个 Generator 函数
    function* hellow() {
      yield 'he'; // 状态语句
      yield 'll';
      yield 'ow';
      return 'ending'; // 结束语句
    }
  
    let hw = hellow();
  ```
- Generator函数的调用方法和普通函数一样。但是，
  1. 调用后该函数不会执行
  2. 返回的不是函数运行的结果，而是一个指向内部状态的指针对象，也就是遍历器对象（Iterator Object）
  3. 必须调用对象的`next`方法，使指针移向下一个状态
  4. 每次调用`next`方法，会返回一个包函`value`和`done`两个属性的对象。
    - value：内部状态值
    - done：布尔值 ，表示是否遍历结束

## yield 表达式
`yield语句`就是暂停标志
### next方法的运行逻辑
1. 遇到`yield`就暂停执行后边的操作，并将紧跟其后的表达式的值作为返回对象的value属性值
2. 下一次调用 `next方法` 时会继续向下执行，知道遇到下一条`yield语句`
3. 如果没有遇到新的`yield语句`就一直运行到函数结束，直到`return语句`为止，并将`return语句`后面表达式的值作为返回对象的value属性值
4. 如果没有`return语句`则返回undefined

### yield语句与return语句
- 两者都返回紧跟在语句后面的表达式的值
- yield语句具有记忆功能，return语句不具有此功能
- 一个函数只能执行一次return语句，但是可以执行多条yield语句

### 暂缓执行函数
当`Generator函数`不包含`yield语句`时，就变成了一个暂缓执行函数
```javascript
function* f() {
  console.log(1);
}
let g = f(); // 此时不执行
setTimeout(function() {
  g.next()  // 只有调用 next 以后才执行
},3000)
```
### 注意事项
- `yield语句`只能用在`Generator函数`中，用在其他地方会报错
- 不可以用在 `forEach`等方法的回调函数内，可以使用for循环代替
- yield表达式如果在另一个表达式中，必须放在圆括号内
```javascript
function* f() {
  console.log(1+(yield 2));
}
```
- yield表达式作为函数参数或放在赋值表达式的右边可以不加括号

## 与Generator接口的关系
1. 任意一个对象的`Symbol.iterator`方法等于该对像的遍历器对象生成函数，调用该函数会返回该对象的一个遍历器对象
2. 由于`Generator函数`就是遍历器生成函数，所以可以把`Generator`赋值给对象的`Symbol.iterator`属性，从而使得该对象具有`Iterator接口`
```javascript
let myIterable = [];
myIterable[Symbol.iterator] = function*() {
  yield 1;
  yield 2;
}
console.log([...myIterable]) // [1, 2]
```
3. `Generator函数`执行后，返回一个遍历器对象。该对象本身也具有`Symbol.iterator`属性，执行后返回自身
```javascript
function* f() {
  console.log(1);
}
let g = f();
console.log(g[Symbol.iterator] === g); // true
```

## next方法的参数
- `yield语句`本身没有返回值，或者说总是返回`undefined`
- next 方法可以带有一个参数，该参数会被当做上一条 `yield语句`的返回值
- 由于 next 方法的参数表示上一条`yield语句`的返回值，所以第一次使用 next 方法时，传递参数是无效的
- 如果希望第一次调用 next 方法时能过输入值，可以在 `Generator函数` 外再包一层

## for...of循环
- for...of 循环可以自动遍历 `Generator函数` 生成的 Iterator 对象，且此时不需要调用 next 方法。
- 一旦 next 方法的返回对象的 done 属性为 true，for...of循环就会终止，且不包含返回的对象
```javascript
function* f() {
  yield 1;
  yield 2;
  return 3;
}
for (let v of foo()){
  console.log(v); // 1 2 不包含 return语句返回的 3
} 
```

## Generator.prototype.throw()
- `Generator函数`返回的遍历器对象都有一个 throw 方法，可以在函数体外抛出错误，然后再 Generator函数 内捕获；反过来，`Generator函数`体内抛出的错误也可以被函数体外的 catch 捕获
- 如果 `Generator函数` 内部没有部署 try...catch 代码块，那么 throw 方法抛出的错误将被外部的 try...catch 代码块捕获
- 如果 `Generator函数` 内部部署了 try...catch 代码块，那么遍历器的 throw 方法抛出的错误不影响下一次遍历，否则遍历会终止
- 一旦`Genertator函数`执行过程中抛出错误，就不会往下执行了，如果此后调用 next 方法，将返回 {value: undefined, done: true} 这个对象
- throw 方法执行后会附带执行下一条 yield 表达式，即附带执行一次 next 方法
- throw 命令与 Generator.prototype.throw()方法互不影响
```javascript
let g = function*() {
  try {
    yield ;
  }catch (e) {
    console.log('内部捕获',e);
  }
}

let i = g();
i.next();
try {
  i.throw('a');
  i.throw('b');
}catch (e) {
  console.log('外部捕获',e);
}

// 内部捕获 a
// 外部捕获 b
```

## Generator.prototype.return()
- 该方法返回给定的值,并终结`Generator函数`的遍历
- 如果不提供参数，则返回值的 value 属性为 undefined
```javascript
function* f() {
  yield 1;
  yield 2;
}

let g = f();
g.next(); // {value: 1, done }
g.return('foo'); // {value: 'foo', done: true}
g.next() // {value: undefined, deone: true}
```

- 如果`Generator函数`内部有 try...finally 代码块，那么 return 方法会推迟到 finally 代码块执行完成在执行

```javascript
function* f() {
  yield 1;
  try {
    yield 2;
    yield 3;
  } catch (e) {
    yield 4;
    yield 5;
  }
  yield 6;
}

let g = f();
g.next(); // { value: 1, done: false }
g.next(); // { value: 2, done: false}
g.return(); // { value: 4, done: false}
g.next(); // { value: 5, done: false}
g.next(); // { value: 7, done: true}
```

## yield* 表达式
- 如果在一个`Generator函数`内调用另一个`Generator函数`，默认情况下是没有效果的
```javascript
function* a() {
  yield 'a';
}

function* b() {
  a();
  yield 'b';
}

for (let v of b()){
  console.log(v); // 'b'
} 
// a 和 b 都是 Generator函数 在b中调用a不会生效
```

- `yield*`，用来在一个`Generator函数`里执行另一个`Generator函数`
```javascript
function* a() {
  yield 'a';
}

function* b() {
  yield* a();
  yield 'b';
}

for (let v of b()){
  console.log(v); // 'a' 'b'
} 
```
- 如果 yield 命令后面跟的是一个遍历器对象，那么需要在 yield 命令后面加上星号，表明返回的是一个遍历器对象
```javascript
function* inner() {
  yield 'hellow'
}

function* outer1() {
  yield 'open';
  yield inner();
  yield 'colse'
}

let gen = outer1()
gen.next().value; // 'open'
gen.next().value; // 返回一个遍历器对象
gen.next().value; // 'colse'

function* outer2() {
  yield 'open';
  yield* inner();
  yield 'close';
}
let gen2 = outer2();
gen2.next().value; // 'open'
gen2.next().value; // 'hellow'
gen2.next().value; // 'close'
```

- yield* 后面的`Generator语句`没有 return 语句时等同于在`Generator函数`内部部署了一个 `for...of` 循环
- yield* 后面的`Generator语句`有 return 语句时，需要用 `let value = yield* iterator` 的形式获取 return语句的值
- 如果被代理的`Generator语句`有 return 语句，那么可以向代理他的`Generator函数`返回数据
- 任何数据只要有`Iterator`接口，就可以被`yield*`遍历
```javascript
let a = (function*() {
  yield 'hellow';
  yield* 'he'
  yield* [1,2,3] 
}())

a.next().value; // 'hellow'
a.next().value; // 'h'
a.next().value; // 'e'
a.next().value; // '1'
a.next().value; // '2'
a.next().value; // '3'
```
## 应用
- 异步操作的同步化表达
- 控制流管理
- 部署 `Iterator` 接口
- 作为数据结构
