# 7月 todoList 

> 每个礼拜一去盘点巩固上周的知识

✅已复盘，并理解   ❌没有理解



## 复习基础 [ECMAScript 6 入门](https://es6.ruanyifeng.com/)



# [Generator 函数](https://es6.ruanyifeng.com/#docs/generator)✅



```javascript
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();
```

```javascript
hw.next()
// { value: 'hello', done: false }

hw.next()
// { value: 'world', done: false }

hw.next()
// { value: 'ending', done: true }

hw.next()
// { value: undefined, done: true }

// 总结一下，调用 Generator 函数，返回一个遍历器对象，代表 Generator 函数的内部指针。以后，每次调用遍历器对象的next方法，就会返回一个有着value和done两个属性的对象。value属性表示当前的内部状态的值，是yield表达式后面那个表达式的值；done属性是一个布尔值，表示是否遍历结束。
```

- `function`关键字与函数名之间有一个星号
- 函数体内部使用`yield`表达式，定义不同的内部状态
- 调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是遍历器对象（Iterator Object）。
- 必须调用遍历器对象的`next`方法，使得指针移向下一个状态
- 第一次调用next时等同于启动执行 Generator 函数的内部代码



Generator 函数可以不用`yield`表达式，这时就变成了一个单纯的暂缓执行函数。

```javascript
function* f() {
  console.log('执行了！')
}

var generator = f();

setTimeout(function () {
  generator.next()
}, 2000);
```



```javascript
var arr = [1, [[2, 3], 4], [5, 6]];

var flat = function* (a) {
  var length = a.length;
  for (var i = 0; i < length; i++) {// 这里不能用foreach等循环函数因为里面使用了yield ,yield表达式只能用在 Generator 函数里面
    var item = a[i];
    if (typeof item !== 'number') {// 如果是数组则拉平
      yield* flat(item);
    } else {
      yield item;
    }
  }
};

for (var f of flat(arr)) {// 因为Generator函数需要调用next()方法，而for...of循环时刚好会自动去调用next()方法
  console.log(f);
}
// 1, 2, 3, 4, 5, 6  
```



### 与iterator的关系

上一章说过，任意一个对象的`Symbol.iterator`方法，等于该对象的遍历器生成函数，调用该函数会返回该对象的一个遍历器对象。

由于 Generator 函数就是遍历器生成函数，因此可以把 Generator 赋值给对象的`Symbol.iterator`属性，从而使得该对象具有 Iterator 接口。

```javascript
var myIterable = {};
myIterable[Symbol.iterator] = function* () {
  yield 1;
  yield 2;
  yield 3;
};

[...myIterable] // [1, 2, 3]
```

上面代码中，Generator 函数赋值给`Symbol.iterator`属性，从而使得`myIterable`对象具有了 Iterator 接口，可以被`...`运算符遍历了。

### next方法的参数

`yield`表达式本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会被当作上一个`yield`表达式的返回值。

```javascript
function* f() {
  for(var i = 0; true; i++) {
    var reset = yield i;
    if(reset) { i = -1; }
  }
}

var g = f();

g.next() // { value: 0, done: false }
g.next() // { value: 1, done: false }
g.next(true) // { value: 0, done: false }
```

```javascript
function* foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);
   // 第二次执行的时候等同于 var z = yield (2 * (yield (x + 1)) / 3)
   // yield (x + 1) == 12 为next(12)传进来的12，所以返回8
  return (x + y + z); //第三次执行 上一次的yield表达式为13
}

var a = foo(5);
a.next() // Object{value:6, done:false}
a.next() // Object{value:NaN, done:false}
a.next() // Object{value:NaN, done:true}

var b = foo(5);
b.next() // { value:6, done:false }
b.next(12) // { value:8, done:false }
b.next(13) // { value:42, done:true }


```

上面代码中，第二次运行`next`方法的时候不带参数，导致 y 的值等于`2 * undefined`（即`NaN`），除以 3 以后还是`NaN`，因此返回对象的`value`属性也等于`NaN`。第三次运行`Next`方法的时候不带参数，所以`z`等于`undefined`，返回对象的`value`属性等于`5 + NaN + undefined`，即`NaN`。

如果向`next`方法提供参数，返回结果就完全不一样了。上面代码第一次调用`b`的`next`方法时，返回`x+1`的值`6`；第二次调用`next`方法，将上一次`yield`表达式的值设为`12`，因此`y`等于`24`，返回`y / 3`的值`8`；第三次调用`next`方法，将上一次`yield`表达式的值设为`13`，因此`z`等于`13`，这时`x`等于`5`，`y`等于`24`，所以`return`语句的值等于`42`。

注意，由于`next`方法的参数表示上一个`yield`表达式的返回值，所以在第一次使用`next`方法时，传递参数是无效的。V8 引擎直接忽略第一次使用`next`方法时的参数，只有从第二次使用`next`方法开始，参数才是有效的。从语义上讲，第一个`next`方法用来启动遍历器对象，所以不用带有参数。



```javascript
function* foo() {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
  yield 5;
  return 6;
}

for (let v of foo()) {// foo()调用生成遍历器 然后for...of自动调用next
  console.log(v);
}
// 1 2 3 4 5
```

`for...of`循环可以自动遍历 Generator 函数运行时生成的`Iterator`对象，且此时不再需要调用`next`方法。

上面代码使用`for...of`循环，依次显示 5 个`yield`表达式的值。这里需要注意，一旦`next`方法的返回对象的`done`属性为`true`，`for...of`循环就会中止，且不包含该返回对象，所以上面代码的`return`语句返回的`6`，不包括在`for...of`循环之中。



```js
// 每次调用next的执行位置
function* foo() {
    console.log('a')
  	yield 1;
	console.log('b')
  	yield 2;
	console.log('c')
  	yield 3;
 	yield 4;
  	yield 5;
  	return 6;
}
var cc = foo()
cc.next()
// a {value: 1, done: false}

cc.next() // 从上一次的yield 1 后面开始执行
// b {value: 2, done: false} 

cc.next()
// c {value: 3, done: false}
```



**Generator 函数内部部署了`try...catch`代码块，那么遍历器的`throw`方法抛出的错误，不影响下一次遍历**

`throw`方法被捕获以后，会附带执行下一条`yield`表达式。也就是说，会附带执行一次`next`方法。

```javascript
var gen = function* gen(){
  try {
    yield console.log('a');
  } catch (e) {
    // ...
  }
  yield console.log('b');
  yield console.log('c');
}

var g = gen();
g.next() // a
g.throw() // b    g.throw方法被捕获以后，自动执行了一次next方法
g.next() // c
```



**`yield*`表达式用来在一个 Generator 函数里面执行另一个 Generator 函数。**

**`yield`*后面可以跟原生遍历器如数组字符串等等**

```javascript
function* inner() {
  yield 'hello!';
  yield 'a!';
}

function* outer1() {
  yield 'open';
  yield inner();
  yield 'close';
}

var gen = outer1()
gen.next().value // "open"
gen.next().value // 返回一个遍历器对象
gen.next().value // "close"


// `yield*`表达式优化后

function* outer2() {
  yield 'open'
  yield* inner() 
  yield 'close'
}

var gen = outer2()
gen.next().value // "open"
gen.next().value // "hello!"
gen.next().value // "a"
gen.next().value // "close"



```



`yield*`后面的 Generator 函数（没有`return`语句时），等同于在 Generator 函数内部，部署一个`for...of`循环。

```javascript
function* concat(iter1, iter2) {
  yield* iter1;
  yield* iter2;
}

// 等同于

function* concat(iter1, iter2) {
  for (var value of iter1) {
    yield value;
  }
  for (var value of iter2) {
    yield value;
  }
}
```



如果`yield*`后面跟着一个数组，由于数组原生支持遍历器，因此就会遍历数组成员。

```javascript
// 数组
function* gen(){
  yield* ["a", "b", "c"];
}

gen().next() // { value:"a", done:false }

// 字符串
let read = (function* () {
  yield 'hello';
  yield* 'hello';
})();

read.next().value // "hello"
read.next().value // "h"
```



### Generator实现异步操作同步化表达

```javascript
function* loadUI() {
  showLoadingScreen();
  yield loadUIDataAsynchronously();
  hideLoadingScreen();
}
var loader = loadUI();
// 加载UI
loader.next() // 第一次执行到第一个yield表达式 即执行第一第二行代码

// 卸载UI
loader.next()
```

第一次调用`loadUI`函数时，该函数不会执行，仅返回一个遍历器。下一次对该遍历器调用`next`方法，则会显示`Loading`界面（`showLoadingScreen`），并且异步加载数据（`loadUIDataAsynchronously`）。等到数据加载完成，再一次使用`next`方法，则会隐藏`Loading`界面

```javascript
// Ajax 是典型的异步操作，通过 Generator 函数部署 Ajax 操作，可以用同步的方式表达。
function* main() {
  var result = yield request("http://some.url");
  var resp = JSON.parse(result);
    console.log(resp.value);
}

function request(url) {
  makeAjaxCall(url, function(response){
    it.next(response);
  });
}

var it = main();
it.next();
```



### Generator封装一个异步fetch请求

```javascript
var fetch = require('node-fetch');

function* gen(){
  var url = 'https://api.github.com/users/github';
  var result = yield fetch(url);
  console.log(result.bio);
}

// 执行
var g = gen();
var result = g.next();

result.value.then(function(data){
  return data.json();
}).then(function(data){
  g.next(data);
});
```

上面代码中，首先执行 Generator 函数，获取遍历器对象，然后使用`next`方法（第二行），执行异步任务的第一阶段。由于`Fetch`模块返回的是一个 Promise 对象，因此要用`then`方法调用下一个`next`方法。

可以看到，虽然 Generator 函数将异步操作表示得很简洁，但是流程管理却不方便（即何时执行第一阶段、何时执行第二阶段）。



```js
// 自己写的例子
function* gen(){
    var result = yield Promise.resolve(7777)
    console.log('第二阶段', result);
}

// 执行
var g = gen();
var result = g.next();
console.log(result,'第一阶段')

result.value.then(function(data){// 拿到第一阶段的promise
    g.next(data); // 在then中执行第二次next 返回第二阶段的 请求结果
});
```



### Thunk 函数

[js中的柯里化、偏函数、Thunk](https://blog.csdn.net/m0_37036014/article/details/102697130)

Thunk方法主要使用在有cb(回调函数)参数的方法中，是指将参数拆分为cb部分和非cb部分：

```javascript
// ES5版本
var Thunk = function(fn){
  return function (){
    var args = Array.prototype.slice.call(arguments);
    return function (callback){
      args.push(callback);
      return fn.apply(this, args);
    }
  };
};

// ES6版本
const Thunk = function(fn) {
  return function (...args) {
    return function (callback) {
      return fn.call(this, ...args, callback);
    }
  };
};
```

```javascript
function f(a, cb) {
  cb(a);
}
const ft = Thunk(f);

ft(1)(console.log) // 1
```



### Promise对象自动执行

```javascript
var fs = require('fs');

var readFile = function (fileName){
  return new Promise(function (resolve, reject){
    fs.readFile(fileName, function(error, data){
      if (error) return reject(error);
      resolve(data);
    });
  });
};

var gen = function* (){
  var f1 = yield readFile('/etc/fstab');
  var f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```



```javascript
// 这里是调用过程 通过调用next来输出 gen中的f1.toString() f2.toString()
var g = gen();

g.next().value.then(function(data){
  g.next(data).value.then(function(data){
    g.next(data);
  });
});

// 优化为自动执行器
function run(gen){
  var g = gen();

  function next(data){
    var result = g.next(data);
    if (result.done) return result.value;
    result.value.then(function(data){
      next(data);
    });
  }

  next();
}

run(gen);
// 上面代码中，只要 Generator 函数还没执行到最后一步，next函数就调用自身，以此实现自动执行。
```

> Promise 和 Thunk 本质都是对回调函数的包装，只是 API 更友好了。

### Generator协程的理解

一个线程（或函数）执行到一半，可以暂停执行，将执行权交给另一个线程（或函数），等到稍后收回执行权的时候，再恢复执行。这种可以并行执行、交换执行权的线程（或函数），就称为协程。

通过yield表达式交换控制权，next方法返回控制权





# [asycn函数](https://es6.ruanyifeng.com/#docs/async)✅

```js
const fs = require('fs');

const readFile = function (fileName) {
  return new Promise(function (resolve, reject) {
    fs.readFile(fileName, function(error, data) {
      if (error) return reject(error);
      resolve(data);
    });
  });
};

const gen = function* () {
  const f1 = yield readFile('/etc/fstab');
  const f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
// 等同于
const asyncReadFile = async function () {
  const f1 = await readFile('/etc/fstab');
  const f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

**总结**

-  Generator 函数的语法糖 ，星号（*）就是async关键字  yield就是await
- 自带自动执行器（co模块）不需要分段执行next，全自动
- async函数在await之前的代码都是同步执行的，可以理解为await之前的代码属于new Promise时传入的代码（即同步代码），await表达式下面的所有代码都是在Promise.then中的回调（异步代码）
- await 命令后面，可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时会自动转成立即 resolved 的 Promise 对象）。
- async 函数的返回值是 Promise 对象



### 返回一个 Promise 对象

- `async`函数返回一个 Promise 对象。
- `async`函数内部`return`语句返回的值，会成为`then`方法回调函数的参数。
- 任何一个`await`语句后面的 Promise 对象变为`reject`状态，那么整个`async`函数都会中断执行

```javascript
async function f() {
  return 'hello world';
}

f().then(v => console.log(v))
// "hello world"

// 如果有reject 不会执行后面的代码 会中断
async function f() {
  await Promise.reject('出错了');
  await Promise.resolve('hello world'); // 不会执行
}

// try...catch解决上面的reject中断问题
async function f() {
  try {
    await Promise.reject('出错了');
  } catch(e) {
  }
  return await Promise.resolve('hello world');
}

f()
.then(v => console.log(v))
// hello world
```

`async`函数内部抛出错误，会导致返回的 Promise 对象变为`reject`状态。抛出的错误对象会被`catch`方法回调函数接收到。

```javascript
async function f() {
  throw new Error('出错了');
}

f().then(v => console.log('resolve', v))
    .catch(e => console.log('reject', e))
//reject Error: 出错了
```



### Promise的状态改变

>  只有`async`函数内部的异步操作执行完，才会执行`then`方法指定的回调函数

```javascript
async function getTitle(url) {
  let response = await fetch(url);
  let html = await response.text();
  return html.match(/<title>([\s\S]+)<\/title>/i)[1];
}
getTitle('https://tc39.github.io/ecma262/').then(console.log)
// "ECMAScript 2017 Language Specification"
```

上面代码中，函数`getTitle`内部有三个操作：抓取网页、取出文本、匹配页面标题。只有这三个操作全部完成，才会执行`then`方法里面的`console.log`。

```javascript
async function f() {
  // 等同于
  // return 123;
  return await 123;
}

f().then(v => console.log(v))
// 123
```



每隔一秒循环输出i

```javascript
function sleep(interval) {
  return new Promise(resolve => {
    setTimeout(resolve, interval);
  })
}

// 用法
async function one2FiveInAsync() {
  for(let i = 1; i <= 5; i++) {
    console.log(i);
    await sleep(1000);
  }
}

one2FiveInAsync();
```



下面的例子使用`try...catch`结构，实现多次重复尝试。

```javascript
const superagent = require('superagent');
const NUM_RETRIES = 3;

async function test() {
  let i;
  for (i = 0; i < NUM_RETRIES; ++i) {
    try {
      await superagent.get('http://google.com/this-throws-an-error');
      break;
    } catch(err) {}
  }
  console.log(i); // 3
}

test();
```

上面代码中，如果`await`操作成功，就会使用`break`语句退出循环；如果失败，会被`catch`语句捕捉，然后进入下一轮循环。

**执行的先后继发顺序**

```javascript
// 必须完成foo才会调用bar （继发关系）
let foo = await getFoo();
let bar = await getBar();

// 非继发 同时触发
// 写法一   Promise.all：要么全部成功 ，要么有一个失败
let [foo, bar] = await Promise.all([getFoo(), getBar()]);//Promise.all返回值为数组

// 写法二
let fooPromise = getFoo();
let barPromise = getBar();
let foo = await fooPromise;
let bar = await barPromise;
```



**async 函数的实现原理，就是将 Generator 函数和自动执行器，包装在一个函数里。**

```javascript
async function fn(args) {
  // ...
}

// 等同于

function fn(args) {
  return spawn(function* () {
    // ...
  });
}
// 所有的async函数都可以写成上面的第二种形式，其中的spawn函数就是自动执行器。
```



思考题 ： 原题链接 https://juejin.cn/post/6844903988584775693

```js
console.log('script start')

async function async1() {
    await async2()
    console.log('async1 end')
}
async function async2() {
	console.log('async2 end')
}
async1()

setTimeout(function() {
	console.log('setTimeout')
}, 0)

new Promise(resolve => {
    console.log('Promise')
    resolve()
}).then(function() {
	console.log('promise1')
}).then(function() {
	console.log('promise2')
})

console.log('script end')

// script start => async2 end => Promise => script end => async1 end => promise1 => promise2 => setTimeout



```

   如果await 后面直接跟的为一个变量，比如：await 1；这种情况的话相当于直接把await后面的代码注册为一个微任务，可以简单理解为promise.then(await下面的代码)。然后跳出async1函数，执行其他代码，当遇到promise函数的时候，会注册promise.then()函数到微任务队列，注意此时微任务队列里面已经存在await后面的微任务。所以这种情况会先执行await后面的代码（async1 end），再执行async1函数后面注册的微任务代码(promise1,promise2)。

参考文章 ：[async 异步函数 不完全使用攻略](https://juejin.cn/post/6844903729171283975)

### async await总结

- 先搞懂事件循环 [微任务、宏任务与Event-Loop](https://juejin.cn/post/6844903657264136200)
- 理解Generator的协程概念 
- async await与Generator





# class语法

- class类的写法其实就是构造函数的一种语法糖

```javascript
function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};

// 等同于
class Point {
  constructor(x, y) {// 对应构造方法
    this.x = x;
    this.y = y;
  }

  toString() {// 对应原型对象的方法
    return '(' + this.x + ', ' + this.y + ')';
  }
}

// 使用
var p = new Point(1, 2);
```

- 类的所有方法都定义在类的`prototype`属性上面包括（constructor）

```javascript
class Point {
  constructor() {
    // ...
  }

  toString() {
    // ...
  }
}

// 等同于

Point.prototype = {// 都是定义在Point.prototype上面
  constructor() {},
  toString() {},
};
```

```javascript
class B {}
const b = new B();

b.constructor === B.prototype.constructor // true
```

- 类的内部所有定义的方法，都是不可枚举的，但是ES5的写法是可以的

```javascript
Object.keys(Point.prototype)
// []
```

```javascript
//	es5能枚举出内部所有定义的方法
var Point = function (x, y) {
  // ...
};

Point.prototype.toString = function () {
  // ...
};

Object.keys(Point.prototype)
// ["toString"]
Object.getOwnPropertyNames(Point.prototype)
// ["constructor","toString"]
```

### constructor方法

`constructor()`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。一个类必须有`constructor()`方法，如果没有显式定义，一个空的`constructor()`方法会被默认添加

```javascript
class Point {
}

// 等同于
class Point {
  constructor() {}
}


// constructor()方法默认返回实例对象（即this），完全可以指定返回另外一个对象
class Foo {
  constructor() {
    return Object.create(null);
  }
}

new Foo() instanceof Foo
// false
```



### 取值函数（getter）和存值函数（setter）

```javascript
class MyClass {
  constructor() {
    // ...
  }
  get prop() {
    return 'getter';
  }
  set prop(value) {
    console.log('setter: '+value);
  }
}

let inst = new MyClass();

inst.prop = 123;
// setter: 123

inst.prop
// 'getter'
```

- 存值函数和取值函数是设置在属性的 Descriptor 对象上的。与es5一致

```javascript
class CustomHTMLElement {
  constructor(element) {
    this.element = element;
  }

  get html() {
    return this.element.innerHTML;
  }

  set html(value) {
    this.element.innerHTML = value;
  }
}

var descriptor = Object.getOwnPropertyDescriptor(
  CustomHTMLElement.prototype, "html"
);

"get" in descriptor  // true
"set" in descriptor  // true
```

- this的指向

```javascript
class Logger {
  printName(name = 'there') {
    this.print(`Hello ${name}`);
  }

  print(text) {
    console.log(text);
  }
}

const logger = new Logger();
const { printName } = logger;
printName(); // TypeError: Cannot read property 'print' of undefined
```

上面代码中，`printName`方法中的`this`，默认指向`Logger`类的实例。但是，如果将这个方法提取出来单独使用，`this`会指向该方法运行时所在的环境（由于 class 内部是严格模式，所以 this 实际指向的是`undefined`），从而导致找不到`print`方法而报错

```javascript
// 解决方法
class Logger {
  constructor() {
    this.printName = this.printName.bind(this);
  }

  // ...
}

// 或者
class Obj {
  constructor() {
    this.getThis = () => this;
  }
}

const myObj = new Obj();
myObj.getThis() === myObj // true
```



### 静态方法

类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上`static`关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

```javascript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

Foo.classMethod() // 'hello'

var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function
```

- 注意，如果静态方法包含`this`关键字，这个`this`指的是类，而不是实例。

```javascript
class Foo {
  static bar() {
    this.baz();
  }
  static baz() {
    console.log('hello');
  }
  baz() {
    console.log('world');
  }
}

Foo.bar() // hello
```

上面代码中，静态方法`bar`调用了`this.baz`，这里的`this`指的是`Foo`类，而不是`Foo`的实例，等同于调用`Foo.baz`。另外，从这个例子还可以看出，静态方法可以与非静态方法重名。

- 父类的静态方法，可以被子类继承。

```javascript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
}

Bar.classMethod() // 'hello'
```

- 静态方法也是可以从`super`对象上调用的。

```javascript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
  static classMethod() {
    return super.classMethod() + ', too';
    //  return Foo.classMethod() + ', too';
  }
}

Bar.classMethod() // "hello, too"
```

### 实例属性的新写法

```javascript
class IncreasingCounter {
  constructor() {
    this._count = 0;
  }
  get value() {
    console.log('Getting the current value!');
    return this._count;
  }
  increment() {
    this._count++;
  }
}

// 实例属性除了定义在constructor()方法里面的this上面，也可以定义在类的最顶层

class IncreasingCounter {
  _count = 0; // 等同于上面的constructor的this._count

  get value() {
    console.log('Getting the current value!');
    return this._count;
  }
  increment() {
    this._count++;
  }
}
```

### 静态属性

静态属性指的是 Class 本身的属性，即`Class.propName`，而不是定义在实例对象（`this`）上的属性。

```javascript
// 老写法
class Foo {
  // ...
}
Foo.prop = 1;

// 新写法
class Foo {
  static prop = 1;
}
```

### new.target

Class 内部调用`new.target`，返回当前 Class。

```javascript
class Rectangle {
  constructor(length, width) {
    console.log(new.target === Rectangle);
    this.length = length;
    this.width = width;
  }
}

var obj = new Rectangle(3, 4); // 输出 true
```



### 写出不能独立使用、必须继承后才能使用的类

```javascript
class Shape {
  constructor() {
    if (new.target === Shape) {
      throw new Error('本类不能实例化');
    }
  }
}

// 需要注意的是，子类继承父类时，new.target会返回子类。
class Rectangle extends Shape {
  constructor(length, width) {
    super();
    // ...
  }
}

var x = new Shape();  // 报错
var y = new Rectangle(3, 4);  // 正确
```

上面代码中，`Shape`类不能被实例化，只能用于继承。



# Class的继承



```javascript
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y) 先生成父类的 xy
    this.color = color;
  }

  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```

上面代码中，`constructor`方法和`toString`方法之中，都出现了`super`关键字，它在这里表示父类的构造函数，用来新建父类的`this`对象

子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错。这是因为子类自己的`this`对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用`super`方法，子类就得不到`this`对象。



**重点： ES5 的继承，实质是先创造子类的实例对象`this`，然后再将父类的方法添加到`this`上面（`Parent.apply(this)`）。ES6 的继承机制完全不同，实质是先将父类实例对象的属性和方法，加到`this`上面（所以必须先调用`super`方法），然后再用子类的构造函数修改`this`。**

[es5模拟new的实现](https://github.com/mqyqingfeng/Blog/issues/13)

```js
// es5模拟new的实现
function objectFactory() {

    var obj = new Object(),

    Constructor = [].shift.call(arguments);

    obj.__proto__ = Constructor.prototype;

    var ret = Constructor.apply(obj, arguments);

    return typeof ret === 'object' ? ret : obj;

};
// ES5 的继承，实质是先创造子类的实例对象`this`，然后再将父类的方法添加到`this`上面（`Parent.apply(this)`）
```



在子类的构造函数中，只有调用`super`之后，才可以使用`this`关键字，否则会报错。这是因为子类实例的构建，基于父类实例，只有`super`方法才能调用父类实例。

```javascript
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
}

class ColorPoint extends Point {
  constructor(x, y, color) {
    this.color = color; // ReferenceError
    super(x, y);
    this.color = color; // 正确
  }
}
// ES6 的继承机制完全不同，实质是先将父类实例对象的属性和方法，加到`this`上面（所以必须先调用`super`方法），然后再用子类的构造函数修改`this`。
```



```javascript
// 实例对象cp同时是ColorPoint和Point两个类的实例，这与 ES5 的行为完全一致。
let cp = new ColorPoint(25, 8, 'green');

cp instanceof ColorPoint // true
cp instanceof Point // true
Object.getPrototypeOf(ColorPoint) === Point
// true

// 父类的静态方法，也会被子类继承
class A {
  static hello() {
    console.log('hello world');
  }
}

class B extends A {
}

B.hello()  // hello world
```



### super关键字

`super`这个关键字，既可以当作函数使用，也可以当作对象使用

- 第一种情况，`super`作为函数调用时，代表父类的构造函数。ES6 要求，子类的构造函数必须执行一次`super`函数。

```javascript
class A {}

class B extends A {
  constructor() {
    super();// 代表调用父类的构造函数 实例化前必须先 生成父类this
  }
}
```























todo：链式调用原理