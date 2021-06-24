# 六月 todoList 

> 每个礼拜一去盘点巩固上周的知识

✅已复盘，并理解   ❌没有理解



## 复习基础 [ECMAScript 6 入门](https://es6.ruanyifeng.com/)





# 数组扩展✅✅✅



**Array.from** 

`Array.from`方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map）。

```javascript
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};

// ES5的写法
var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']

// ES6的写法
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']


// 任何有`length`属性的对象，都可以通过`Array.from`方法转为数组，而此时扩展运算符就无法转换。
Array.from({ length: 3 });
// [ undefined, undefined, undefined ]
```

对于还没有部署该方法的浏览器，可以用`Array.prototype.slice`方法替代。

```javascript
const toArray = (() =>
  Array.from ? Array.from : obj => [].slice.call(obj)
)();
```

`Array.from`还可以接受第二个参数，作用类似于数组的`map`方法，用来对每个元素进行处理，将处理后的值放入返回的数组。

```javascript
Array.from(arrayLike, x => x * x);
// 等同于
Array.from(arrayLike).map(x => x * x);

Array.from([1, 2, 3], (x) => x * x)
// [1, 4, 9]
```

下面的例子将数组中布尔值为`false`的成员转为`0`。

```javascript
Array.from([1, , 2, , 3], (n) => n || 0)
// [1, 0, 2, 0, 3]
```





**copyWithin**

```javascript
Array.prototype.copyWithin(target, start = 0, end = this.length)
```

- target（必需）：从该位置开始替换数据。如果为负值，表示倒数。
- start（可选）：从该位置开始读取数据，默认为 0。如果为负值，表示从末尾开始计算。
- end（可选）：到该位置前停止读取数据，默认等于数组长度。如果为负值，表示从末尾开始计算。(开区间 不取到end位)

```javascript
var p=[1,2,3,4,5];
p.copyWithin(0,3); //[4,5,3,4,5]
console.log(p);//[4,5,3,4,5]
[1,2,3,4,5].copyWithin(1,3);
//[1,4,5,4,5]
[1,2,,4,5].copyWithin(0,1);
//[2, 2: 4, 3: 5, 4: 5]

// 第三个参数end 如果传了那么就是开区间（不取），不传就默认等于数组长度
[1, 2, 3, 4, 5].copyWithin(0, 3, 4) 
// [4, 2, 3, 4, 5]
// 复制的位置为第一个（索引为0），需要拷贝的数据是从索引3到索引4（4不取）

[1, 2, 3, 4, 5,6].copyWithin(0, 3, 4)
// [4, 2, 3, 4, 5, 6]
[1, 2, 3, 4, 5,6].copyWithin(0, 3, 5)
// [4, 5, 3, 4, 5, 6]


// 将3号位复制到0号位
[].copyWithin.call({length: 5, 3: 1}, 0, 3)
// {0: 1, 3: 1, length: 5}

// 解析: 先将类数组转成数组Array.from，然后开始覆盖的位置（索引0），复制数组索引3到数组结尾
Array.from({length: 5, 3: 1})
// [undefined, undefined, undefined, 1, undefined]
// => [1, undefined, undefined, 1, undefined]
//结果为 =>  {0: 1, 3: 1, length: 5}
```



**find与findIndex** 

数组实例的`find`方法，用于找出第一个符合条件的数组成员。它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为`true`的成员，然后返回该成员。如果没有符合条件的成员，则返回`undefined`。

```javascript
[1, 5, 10, 15].find(function(value, index, arr) {
  return value > 9;
}) // 10
```

数组实例的`findIndex`方法的用法与`find`方法非常类似，返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回`-1`。

```javascript
[1, 5, 10, 15].findIndex(function(value, index, arr) {
  return value > 9;
}) // 2
```

find与findIndex都可以接受第二个参数，用来绑定回调函数的`this`对象。

```javascript
function f(v){
  return v > this.age;
}
let person = {name: 'John', age: 20};
[10, 12, 26, 15].find(f, person);    // 26
```

flat与flatMap 数组扁平化处理

```javascript
[1, 2, [3, 4]].flat()
// [1, 2, 3, 4]

// flat()默认只会“拉平”一层，如果想要“拉平”多层的嵌套数组，可以将flat()方法的参数写成一个整数，表示想要拉平的层数，默认为1。
[1, 2, [3, [4, 5]]].flat(2)
// [1, 2, 3, 4, 5]

[1, [2, [3]]].flat(Infinity)
// [1, 2, 3] Infinity无限层级嵌套扁平化



// 1.flatMap()方法对原数组的每个成员执行一个函数（相当于执行Array.prototype.map()），然后对返回值组成的数组执行flat()方法。该方法返回一个新数组，不改变原数组。
// 2.flatMap()只能展开一层数组。

// 相当于 [[2, 4], [3, 6], [4, 8]].flat()
[2, 3, 4].flatMap((x) => [x, x * 2])
// [2, 4, 3, 6, 4, 8]


```

Array.every()

every() 方法用于检测数组所有元素是否都符合指定条件（通过函数提供）。

every() 方法使用指定函数检测数组中的所有元素：

- 如果数组中检测到有一个元素不满足，则整个表达式返回 *false* ，且剩余的元素不会再进行检测。
- 如果所有元素都满足条件，则返回 true。

**注意：** every() 不会对空数组进行检测。

**注意：** every() 不会改变原始数组。

```javascript
var ages = [32, 33, 16, 40];
ages.every(x => x>= 18) // false
```

Array.some()

some() 方法用于检测数组中的元素是否满足指定条件（函数提供）。

some() 方法会依次执行数组的每个元素：

- 如果有一个元素满足条件，则表达式返回*true* , 剩余的元素不会再执行检测。
- 如果没有满足条件的元素，则返回false。

**注意：** some() 不会对空数组进行检测。

**注意：** some() 不会改变原始数组。

```javascript
var ages = [3, 10, 18, 20];
ages.some(x => x>= 18) // true
```



# 对象的扩展✅✅✅

1. 对象简写

```javascript
let birth = '2000/01/01';

const Person = {

  name: '张三',

  //等同于birth: birth
  birth,

  // 等同于hello: function ()...
  hello() { console.log('我的名字是', this.name); }

};


// 注意，简写的对象方法不能用作构造函数，会报错。
const obj = {
  f() {
    this.foo = 'bar';
  }
};

new obj.f() // 报错
// 上面代码中，f是一个简写的对象方法，所以obj.f不能当作构造函数使用。
```

2. [属性的可枚举性和遍历](https://es6.ruanyifeng.com/?search=rest&x=0&y=0#docs/object#%E5%B1%9E%E6%80%A7%E7%9A%84%E5%8F%AF%E6%9E%9A%E4%B8%BE%E6%80%A7%E5%92%8C%E9%81%8D%E5%8E%86)

    对象的每个属性都有一个描述对象（Descriptor），用来控制该属性的行为。`Object.getOwnPropertyDescriptor`方法可以获取该属性的描述对象。

```javascript
let obj = { foo: 123 };
Object.getOwnPropertyDescriptor(obj, 'foo')
//  {
//    value: 123,
//    writable: true,
//    enumerable: true,
//    configurable: true
//  }
```

描述对象的`enumerable`属性，称为“可枚举性”，如果该属性为`false`，就表示某些操作会忽略当前属性。

有四个操作会跳过`enumerable`为`false`的属性（如果为false则不会遍历该条属性）

- `for...in`循环：只遍历对象自身的和继承的可枚举的属性。
- `Object.keys()`：返回对象自身的所有可枚举的属性的键名。
- `JSON.stringify()`：只串行化对象自身的可枚举的属性。
- `Object.assign()`： 忽略`enumerable`为`false`的属性，只拷贝对象自身的可枚举的属性。

引入“可枚举”（`enumerable`）这个概念的最初目的，就是让某些属性可以规避掉`for...in`操作，不然所有内部属性和方法都会被遍历到。比如，对象原型的`toString`方法，以及数组的`length`属性，就通过“可枚举性”，从而避免被`for...in`遍历到。

总的来说，操作中引入继承的属性会让问题复杂化，大多数时候，我们只关心对象自身的属性。所以，尽量不要用`for...in`循环，而用`Object.keys()`代替。

### in关键字扩展

单独使用时，in操作符会在通过对象能够访问给定属性时返回true（对象实例或者原型中存在即可）

object.hasOwnProperty只在属性存在于实例中时才返回 true，因此只要 in 操作符返回 true 而 hasOwnProperty()返回 false，就可以确定属性是原型中的属性

**所以 for...in循环能遍历继承原型的属性（in关键字的作用）**

[in关键字参考链接](https://www.bbsmax.com/A/l1dyrY2Aze/)



### 属性的遍历

ES6 常用的三种

**（1）for...in**

`for...in`循环遍历对象自身的和继承的可枚举属性（不含 Symbol 属性）。

**（2）Object.keys(obj)**

`Object.keys`返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含 Symbol 属性）的键名。

**（3）Object.getOwnPropertyNames(obj)**

`Object.getOwnPropertyNames`返回一个数组，包含对象自身的所有属性（不含 Symbol 属性，但是包括不可枚举属性）的键名。



### super 关键字

`this`关键字总是指向函数所在的当前对象，ES6 又新增了另一个类似的关键字`super`，指向当前对象的原型对象。

```javascript
const proto = {
  foo: 'hello'
};

const obj = {
  foo: 'world',
  find() {
    return super.foo;
  }
};

Object.setPrototypeOf(obj, proto);
obj.find() // "hello"
// 上面代码中，对象obj.find()方法之中，通过super.foo引用了原型对象proto的foo属性。
```



Object.setPrototypeOf() 方法设置一个指定的对象的原型 ( 即, 内部[[Prototype]]属性）到另一个对象或  null。

```javascript
const proto = {
  x: 'hello',
  foo() {
    console.log(this.x);
  },
};

const obj = {
  x: 'world',
  foo() {
    super.foo();
  }
}

Object.setPrototypeOf(obj, proto);

obj.foo() // "world"

// 上面代码中，super.foo指向原型对象proto的foo方法，但是绑定的this却还是当前对象obj，因此输出的就是world。


```



Object.setPrototypeOf扩展

```javascript
// !!!注意这里不要和this的指向搞混  call，apply都是改变this指向 Object.setPrototypeOf是改变原型指向

var obj1 = {a:"asfssdf",b:121111,c:[1,2,3]};
var obj2 = {d:["sdf",111,"ok"],e:999};
Object.setPrototypeOf(obj1,obj2);//将obj1原型设置为obj2
for(item in obj1){console.log(item)}//a b c d e  （for in会遍历继承属性）
```

![结果图](https://upload-images.jianshu.io/upload_images/9628812-84f2f25cbe34c0bc.png?imageMogr2/auto-orient/strip|imageView2/2/w/700/format/webp)





Object.getPrototypeOf(obj) 

- 返回指定对象的原型（内部`[[Prototype]]`属性的值）。
- obj：要返回其原型的对象。
- 返回值：给定对象的原型。如果没有继承属性，则返回 null 。

```javascript
var proto = {};
var obj = Object.create(proto);// Object.create创建的是对象的__proto__
Object.getPrototypeOf(obj) === proto; // true
 
var reg = /a/;
Object.getPrototypeOf(reg) === RegExp.prototype; // true
Object.getPrototypeOf(new Date()) === Date.prototype // true
```

Object.create()方法创建一个新对象，使用现有的对象来提供新创建的对象的__proto__

(即设置对象的原型)



[关于创建对象的三种写法 ---- 字面量,new构造器和Object.create()](https://segmentfault.com/a/1190000019914240)

```javascript
const a = Object.create({ x: 1, y: 2 });
const b = {x: 1, y: 2}
const c = new Object({x: 1, y: 2})
console.log(a)
console.log(b)
console.log(c)
// 放到控制台执行一下
// 对象字面量（b）和new Object创建是一样的（c）， Object.create创建的会放在对象的_proto_中

```

**扩展运算符**

```javascript
{...'hello'} // => {0: "h", 1: "e", 2: "l", 3: "l", 4: "o"}
[...'hello'] // => ["h", "e", "l", "l", "o"]

// 对象的扩展运算符等同于使用Object.assign()方法。
let aClone = { ...a };
// 等同于
let aClone = Object.assign({}, a);

// 与数组的扩展运算符一样，对象的扩展运算符后面可以跟表达式。
const obj = {
  ...(x > 1 ? {a: 1} : {}),
  b: 2,
};
```

**注意：** `Object.assign()`无法正确拷贝对象的`get`属性和`set`属性

```javascript
var a = {
   set foo(value) {
    console.log(value);
  }
}
console.log({...a}) //{foo: undefined}

```



[“链判断运算符”（optional chaining operator）?.](https://es6.ruanyifeng.com/?search=rest&x=0&y=0#docs/object#%E9%93%BE%E5%88%A4%E6%96%AD%E8%BF%90%E7%AE%97%E7%AC%A6)

```javascript
// 错误的写法
const  firstName = message.body.user.firstName;

// 正确的写法
const firstName = (message
  && message.body
  && message.body.user
  && message.body.user.firstName) || 'default';

// 链判断运算符
const firstName = message?.body?.user?.firstName || 'default';
```



```javascript
// 对于那些可能没有实现的方法，这个运算符尤其有用。
if (myForm.checkValidity?.() === false) {
  // 表单校验失败
  return;
}
// 上面代码中，老式浏览器的表单可能没有checkValidity这个方法，这时?.运算符就会返回undefined，判断语句就变成了undefined === false，所以就会跳过下面的代码。
```



### [`__proto__`属性](https://es6.ruanyifeng.com/?search=rest&x=0&y=0#docs/object-methods)

`__proto__`属性（前后各两个下划线），用来读取或设置当前对象的原型对象（prototype）。目前，所有浏览器（包括 IE11）都部署了这个属性。

```javascript
// es5 的写法
const obj = {
  method: function() { ... }
};
obj.__proto__ = someOtherObj;

// es6 的写法
var obj = Object.create(someOtherObj);
obj.method = function() { ... };
```



从兼容性的角度，都不要使用`__proto__`这个属性，而是使用下面的`Object.setPrototypeOf()`（写操作）、`Object.getPrototypeOf()`（读操作）、`Object.create()`（生成操作）代替。



`Object.keys`配套的`Object.values`和`Object.entries`，作为遍历一个对象的补充手段，供`for...of`循环使用。

```javascript
let {keys, values, entries} = Object;
let obj = { a: 1, b: 2, c: 3 };

for (let key of keys(obj)) {
  console.log(key); // 'a', 'b', 'c'
}

for (let value of values(obj)) {
  console.log(value); // 1, 2, 3
}

for (let [key, value] of entries(obj)) {
  console.log([key, value]); // ['a', 1], ['b', 2], ['c', 3]
}
```



`Object.entries()`方法返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值对数组。

```javascript
const obj = { foo: 'bar', baz: 42 };
Object.entries(obj)
// [ ["foo", "bar"], ["baz", 42] ]


// `Object.entries`方法的另一个用处是，将对象转为真正的`Map`结构。
const obj = { foo: 'bar', baz: 42 };
const map = new Map(Object.entries(obj));
map // Map { foo: "bar", baz: 42 } 

```

`Object.fromEntries()`方法是`Object.entries()`的逆操作，用于将一个键值对数组转为对象

```javascript
// 例一
const entries = new Map([
  ['foo', 'bar'],
  ['baz', 42]
]);

Object.fromEntries(entries)
// { foo: "bar", baz: 42 }

// 例二
const map = new Map().set('foo', true).set('bar', false);
Object.fromEntries(map)
// { foo: true, bar: false }
```

该方法的一个用处是配合`URLSearchParams`对象，将查询字符串转为对象。

```javascript
Object.fromEntries(new URLSearchParams('foo=bar&baz=qux'))
// { foo: "bar", baz: "qux" }
```



# Symbol✅✅✅

ES5 的对象属性名都是字符串，这容易造成属性名的冲突。比如，你使用了一个他人提供的对象，但又想为这个对象添加新的方法（mixin 模式），新方法的名字就有可能与现有方法产生冲突。如果有一种机制，保证每个属性的名字都是独一无二的就好了，这样就从根本上防止属性名的冲突。

```javascript
let mySymbol = Symbol();

// 第一种写法
let a = {};
a[mySymbol] = 'Hello!';

// 第二种写法
let a = {
  [mySymbol]: 'Hello!'
};

// 第三种写法
let a = {};
Object.defineProperty(a, mySymbol, { value: 'Hello!' });

// 以上写法都得到同样结果
a[mySymbol] // "Hello!"
```

```javascript
// 注意，Symbol 值作为对象属性名时，不能用点运算符。
const mySymbol = Symbol();
const a = {};

a.mySymbol = 'Hello!';
a[mySymbol] // undefined
a['mySymbol'] // "Hello!"
```



# Set 和 Map 数据结构✅✅✅



### set

ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。

```javascript
// 例一
const set = new Set([1, 2, 3, 4, 4]);
[...set]
// [1, 2, 3, 4]

// 例二
const items = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
items.size // 5

// 去除数组的重复成员
[...new Set(array)]

[...new Set('ababbc')].join('')
// "abc"
```

在 Set 内部，两个`NaN`是相等的。但两个对象总是不相等的。

```javascript
let set = new Set();
let a = NaN;
let b = NaN;
set.add(a);
set.add(b);
set // Set {NaN} 两个`NaN`是相等的

// 上面代码向 Set 实例添加了两次NaN，但是只会加入一个。这表明，在 Set 内部，两个NaN是相等的。

let set = new Set();

set.add({});
set.size // 1

set.add({});
set.size // 2 两个对象总是不相等
```

- 用法

```javascript
s.add(1).add(2).add(2);
// 注意2被加入了两次

s.size // 2

s.has(1) // true
s.has(2) // true
s.has(3) // false

s.delete(2);
s.has(2) // false
```

- 数组去重

```javascript
function dedupe(array) {
  return Array.from(new Set(array));
}

dedupe([1, 1, 2, 3]) // [1, 2, 3]

// or
let arr = [3, 5, 2, 2, 5, 5];
let unique = [...new Set(arr)];
// [3, 5, 2]
```

- 并集与交集

```javascript
let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);

// 并集
let union = new Set([...a, ...b]);
// Set {1, 2, 3, 4}

// 交集
let intersect = new Set([...a].filter(x => b.has(x)));
// set {2, 3}

// （a 相对于 b 的）差集
let difference = new Set([...a].filter(x => !b.has(x)));
// Set {1}
```

WeakSet和set的区别：WeakSet只能放对象且不能循环遍历成员

### map

各种类型的值（包括对象）都可以当作键。也就是说，Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应，

```javascript
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);

map.size // 2
map.has('name') // true
map.get('name') // "张三"
map.has('title') // true
map.get('title') // "Author"
```

```javascript
const set = new Set([
  ['foo', 1],
  ['bar', 2]
]);
const m1 = new Map(set);
m1.get('foo') // 1

const m2 = new Map([['baz', 3]]);
const m3 = new Map(m2);
m3.get('baz') // 3
```



```javascript
const map = new Map();

map.set(['a'], 555);
map.get(['a']) // undefined

map.set(function(){}, 555);
map.get(function(){}) // undefined

map.set(true, 555);
map.get(true) // 555

map.set('a', 555);
map.get('a') // 555

// 基本类型好像可以正常获取  但是引用类型的键名内存地址不一样
```

上面代码的`set`和`get`方法，表面是针对同一个键，但实际上这是两个不同的数组实例，内存地址是不一样的，因此`get`方法无法读取该键，返回`undefined`。

```javascript
const map = new Map();

const k1 = ['a'];
const k2 = ['a'];

map
.set(k1, 111)
.set(k2, 222);

map.get(k1) // 111
map.get(k2) // 222
```

上面代码中，变量`k1`和`k2`的值是一样的，但是它们在 Map 结构中被视为两个键。

Map 的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键

Map 结构转为数组结构，比较快速的方法是使用扩展运算符（`...`）

```javascript
const map = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);

[...map.keys()]
// [1, 2, 3]  先 运算map.keys() 在 运算扩展运算符

[...map.values()]
// ['one', 'two', 'three']

[...map.entries()]
// [[1,'one'], [2, 'two'], [3, 'three']]

[...map]
// [[1,'one'], [2, 'two'], [3, 'three']]


const map0 = new Map()
  .set(1, 'a')
  .set(2, 'b')
  .set(3, 'c');

const map1 = new Map(
  [...map0].filter(([k, v]) => k < 3)
);
// 产生 Map 结构 {1 => 'a', 2 => 'b'}

const map2 = new Map(
  [...map0].map(([k, v]) => [k * 2, '_' + v])
    );
// 产生 Map 结构 {2 => '_a', 4 => '_b', 6 => '_c'}
```



# Proxy

Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

Proxy 对象的所有用法，都是上面这种形式，不同的只是`handler`参数的写法。其中，`new Proxy()`表示生成一个`Proxy`实例，`target`参数表示所要拦截的目标对象，`handler`参数也是一个对象，用来定制拦截行为。

```javascript
var proxy = new Proxy({}, {
  get: function(target, propKey) {
    return 35;
  }
});

proxy.time // 35
proxy.name // 35
proxy.title // 35
// get方法的两个参数分别是目标对象和所要访问的属性 由于拦截函数总是返回35，所以访问任何属性都得到35
```

下面是 Proxy 支持的拦截操作一览，一共 13 种。

- **get(target, propKey, receiver)**：拦截对象属性的读取，比如`proxy.foo`和`proxy['foo']`。
- **set(target, propKey, value, receiver)**：拦截对象属性的设置，比如`proxy.foo = v`或`proxy['foo'] = v`，返回一个布尔值。
- **has(target, propKey)**：拦截`propKey in proxy`的操作，返回一个布尔值。
- **deleteProperty(target, propKey)**：拦截`delete proxy[propKey]`的操作，返回一个布尔值。
- **ownKeys(target)**：拦截`Object.getOwnPropertyNames(proxy)`、`Object.getOwnPropertySymbols(proxy)`、`Object.keys(proxy)`、`for...in`循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而`Object.keys()`的返回结果仅包括目标对象自身的可遍历属性。
- **getOwnPropertyDescriptor(target, propKey)**：拦截`Object.getOwnPropertyDescriptor(proxy, propKey)`，返回属性的描述对象。
- **defineProperty(target, propKey, propDesc)**：拦截`Object.defineProperty(proxy, propKey, propDesc）`、`Object.defineProperties(proxy, propDescs)`，返回一个布尔值。
- **preventExtensions(target)**：拦截`Object.preventExtensions(proxy)`，返回一个布尔值。
- **getPrototypeOf(target)**：拦截`Object.getPrototypeOf(proxy)`，返回一个对象。
- **isExtensible(target)**：拦截`Object.isExtensible(proxy)`，返回一个布尔值。
- **setPrototypeOf(target, proto)**：拦截`Object.setPrototypeOf(proxy, proto)`，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
- **apply(target, object, args)**：拦截 Proxy 实例作为函数调用的操作，比如`proxy(...args)`、`proxy.call(object, ...args)`、`proxy.apply(...)`。
- **construct(target, args)**：拦截 Proxy 实例作为构造函数调用的操作，比如`new proxy(...args)`。

```javascript
var handler = {
  get: function(target, name) {
    if (name === 'prototype') {
      return Object.prototype;
    }
    return 'Hello, ' + name;
  },
  // 拦截 Proxy 实例作为函数调用的操作
  apply: function(target, thisBinding, args) {
    return args[0];
  },

  // 拦截 Proxy 实例作为构造函数调用的操作
  construct: function(target, args) {
    return {value: args[1]};
  }
};

var fproxy = new Proxy(function(x, y) {
  return x + y;
}, handler);

fproxy(1, 2) // 1
new fproxy(1, 2) // {value: 2}
fproxy.prototype === Object.prototype // true
fproxy.foo === "Hello, foo" // true
```

`get`方法用于拦截某个属性的读取操作，可以接受三个参数，依次为目标对象、属性名和 proxy 实例本身（严格地说，是操作行为所针对的对象），其中最后一个参数可选。

**get方法可以继承**

```javascript
let proto = new Proxy({}, {
  get(target, propertyKey, receiver) {
    console.log('GET ' + propertyKey);
    return target[propertyKey];
  }
});

let obj = Object.create(proto);
obj.foo // "GET foo"
```



`set`方法用来拦截某个属性的赋值操作，可以接受四个参数，依次为目标对象、属性名、属性值和 Proxy 实例本身，其中最后一个参数可选。

假定`Person`对象有一个`age`属性，该属性应该是一个不大于 200 的整数，那么可以使用`Proxy`保证`age`的属性值符合要求。

```javascript
let validator = {
  set: function(obj, prop, value) {
    if (prop === 'age') {
      if (!Number.isInteger(value)) {
        throw new TypeError('The age is not an integer');
      }
      if (value > 200) {
        throw new RangeError('The age seems invalid');
      }
    }

    // 对于满足条件的 age 属性以及其他属性，直接保存
    obj[prop] = value;
    return true;
  }
};

let person = new Proxy({}, validator);

person.age = 100;

person.age // 100
person.age = 'young' // 报错
person.age = 300 // 报错
```



# [Reflect](https://es6.ruanyifeng.com/?search=rest&x=0&y=0#docs/reflect)

- 将`Object`对象的一些明显属于语言内部的方法（比如`Object.defineProperty`），放到`Reflect`对象上。现阶段，某些方法同时在`Object`和`Reflect`对象上部署

- 修改某些`Object`方法的返回结果，让其变得更合理。比如，`Object.defineProperty(obj, name, desc)`在无法定义属性时，会抛出一个错误，而`Reflect.defineProperty(obj, name, desc)`则会返回`false`

- `Reflect`对象的方法与`Proxy`对象的方法一一对应，只要是`Proxy`对象的方法，就能在`Reflect`对象上找到对应的方法。这就让`Proxy`对象可以方便地调用对应的`Reflect`方法，完成默认行为，作为修改行为的基础

- 让`Object`操作都变成函数行为

    ```javascript
    // 老写法
    'assign' in Object // true
    
    // 新写法
    Reflect.has(Object, 'assign') // true
    ```

[JS的Reflect学习和应用](https://zhuanlan.zhihu.com/p/92700557)

Reflect用于简化Object的原始方法

```js
var myObject = Object.create(null) // 此时myObject并没有继承Object这个原型的任何方法,因此有：

myObject.hasOwnProperty === undefined // 此时myObject是没有hasOwnProperty这个方法，那么我们要如何使用呢？如下：

Object.prototype.hasOwnProperty.call(myObject, 'foo') // 是不是很恐怖，写这么一大串的代码！！！！

// Reflect写法
var myObject = Object.create(null)
Reflect.has(myObject, 'foo')
```



### 使用proxy实现观察者模式

观察者模式（Observer mode）指的是函数自动观察数据对象，一旦对象有变化，函数就会自动执行。

```javascript
const person = observable({
  name: '张三',
  age: 20
});

function print() {
  console.log(`${person.name}, ${person.age}`)
}

observe(print);
person.name = '李四';
// 输出
// 李四, 20
```

上面代码中，数据对象`person`是观察目标，函数`print`是观察者。一旦数据对象发生变化，`print`就会自动执行。

下面，使用 Proxy 写一个观察者模式的最简单实现，即实现`observable`和`observe`这两个函数。思路是`observable`函数返回一个原始对象的 Proxy 代理，拦截赋值操作，触发充当观察者的各个函数。

```javascript
const queuedObservers = new Set();

const observe = fn => queuedObservers.add(fn);
const observable = obj => new Proxy(obj, {set});

function set(target, key, value, receiver) {
  // 设置成功后返回true
  const result = Reflect.set(target, key, value, receiver);
  // 一旦有赋值操作 执行所有观察者函数
  queuedObservers.forEach(observer => observer());
  return result;
}
```

上面代码中，先定义了一个`Set`集合，所有观察者函数都放进这个集合。然后，`observable`函数返回原始对象的代理，拦截赋值操作。拦截函数`set`之中，会自动执行所有观察者。



# Promise



```javascript
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});

promise.then(function(value) {
  // success
}, function(error) {
  // failure
});
```



```javascript
function timeout(ms) {
  return new Promise((resolve, reject) => {
    setTimeout(resolve, ms, 'done');
  });
}

timeout(100).then((value) => {
  console.log(value);
});
```

上面代码中，`timeout`方法返回一个`Promise`实例，表示一段时间以后才会发生的结果。过了指定的时间（`ms`参数）以后，`Promise`实例的状态变为`resolved`，就会触发`then`方法绑定的回调函数。

下面是一个用`Promise`对象实现的 Ajax 操作的例子。

```javascript
const getJSON = function(url) {
  const promise = new Promise(function(resolve, reject){
    const handler = function() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
    const client = new XMLHttpRequest();
    client.open("GET", url);
    client.onreadystatechange = handler;
    client.responseType = "json";
    client.setRequestHeader("Accept", "application/json");
    client.send();

  });

  return promise;
};

getJSON("/posts.json").then(function(json) {
  console.log('Contents: ' + json);
}, function(error) {
  console.error('出错了', error);
});
```

Promise 对象后面要跟`catch()`方法，这样可以处理 Promise 内部发生的错误。`catch()`方法返回的还是一个 Promise 对象，因此后面还可以接着调用`then()`方法。



### Promise.all()

```javascript
// 生成一个Promise对象的数组
const promises = [2, 3, 5, 7, 11, 13].map(function (id) {
  return getJSON('/post/' + id + ".json");
});

Promise.all(promises).then(function (posts) {
  // ...
}).catch(function(reason){
  // ...
});
```

上面代码中，`promises`是包含 6 个 Promise 实例的数组，只有这 6 个实例的状态都变成`fulfilled`，或者其中有一个变为`rejected`，才会调用`Promise.all`方法后面的回调函数。

### Promise.race()

```javascript
const p = Promise.race([p1, p2, p3]);
```

上面代码中，只要`p1`、`p2`、`p3`之中有一个实例率先改变状态，`p`的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给`p`的回调函数。

```javascript
const p = Promise.race([
  fetch('/resource-that-may-take-a-while'),
  new Promise(function (resolve, reject) {
    setTimeout(() => reject(new Error('request timeout')), 5000)
  })
]);

p
.then(console.log)
.catch(console.error);
// 注意： fetch也是基于Promise的 所以可以放在里面
```

上面代码中，如果 5 秒之内`fetch`方法无法返回结果，变量`p`的状态就会变为`rejected`，从而触发`catch`方法指定的回调函数。



### Promise.allSettled()

`Promise.allSettled()`方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例。只有等到所有这些参数实例都返回结果，不管是`fulfilled`还是`rejected`，包装实例才会结束

```javascript
const promises = [
  fetch('/api-1'),
  fetch('/api-2'),
  fetch('/api-3'),
];

await Promise.allSettled(promises);
removeLoadingIndicator();
```

上面代码对服务器发出三个请求，等到三个请求都结束，不管请求成功还是失败，加载的滚动图标就会消失。

```javascript
const resolved = Promise.resolve(42);
const rejected = Promise.reject(-1);

const allSettledPromise = Promise.allSettled([resolved, rejected]);

allSettledPromise.then(function (results) {
  console.log(results);
});
// [
//    { status: 'fulfilled', value: 42 },
//    { status: 'rejected', reason: -1 }
// ]
```



### Promise.allSettled()与Promise.all()的区别

- all方法必须全部成功或者有一个失败
- allSettled方法不关心成功与否 ，只关心是否全部请求完毕

```javascript
const urls = [ /* ... */ ];
const requests = urls.map(x => fetch(x));

try {
  await Promise.all(requests);
  console.log('所有请求都成功。');
} catch {
  console.log('至少一个请求失败，其他请求可能还没结束。');
}
```

上面代码中，`Promise.all()`无法确定所有请求都结束。想要达到这个目的，写起来很麻烦，有了`Promise.allSettled()`，这就很容易了。



# [Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)



![](http://www.ruanyifeng.com/blogimg/asset/2014/bg2014100802.png)



上图中，主线程运行的时候，产生堆（heap）和栈（stack），栈中的代码调用各种外部API，它们在"任务队列"中加入各种事件（click，load，done）。只要栈中的代码执行完毕，主线程就会去读取"任务队列"，依次执行那些事件所对应的回调函数。



```javascript
var req = new XMLHttpRequest();
req.open('GET', url);    
req.onload = function (){};    
req.onerror = function (){};    
req.send();

// 等同于
var req = new XMLHttpRequest();
req.open('GET', url);
req.send();
req.onload = function (){};    
req.onerror = function (){};   

// 回调函数的部分（onload和onerror）是异步的
```



setTimeout()只是将事件插入了"任务队列"，必须等到当前代码（执行栈）执行完，主线程才会去执行它指定的回调函数。要是当前代码耗时很长，有可能要等很久，所以并没有办法保证，回调函数一定会在setTimeout()指定的时间执

# [微任务、宏任务与Event-Loop](https://juejin.cn/post/6844903657264136200)

```js
setTimeout(_ => console.log(4)) //宏任务

new Promise(resolve => {
  resolve()
  console.log(1) //这里是同步 
}).then(_ => {
  console.log(3) // 微任务
})

console.log(2)
// 1 2 3 4
```

`setTimeout`就是作为宏任务来存在的，而`Promise.then`则是具有代表性的微任务，上述代码的执行顺序就是按照序号来输出的。

**所有会进入的异步都是指的事件回调中的那部分代码**
 也就是说`new Promise`在实例化的过程中所执行的代码都是同步进行的，而`then`中注册的回调才是异步执行的。
 在同步代码执行完成后才回去检查是否有异步任务完成，并执行对应的回调，而微任务又会在宏任务之前执行。
 所以就得到了上述的输出结论`1、2、3、4`。

```js
// +部分表示同步执行的代码 -表示异步
+setTimeout(_ => {
-  console.log(4)
+})

+new Promise(resolve => {
+  resolve()
+  console.log(1)
+}).then(_ => {
-  console.log(3)
+})

+console.log(2)

```

- 完成一个宏任务+里边的N多微任务为一次事件循环。



# Iterator

主要是数组（`Array`）和对象（`Object`），ES6 又添加了`Map`和`Set`。这样就有了四种数据集合，用户还可以组合使用它们，定义自己的数据结构，比如数组的成员是`Map`，`Map`的成员是对象。这样就需要一种统一的接口机制，来处理所有不同的数据结构

```javascript
var it = makeIterator(['a', 'b']);

it.next() // { value: "a", done: false }
it.next() // { value: "b", done: false }
it.next() // { value: undefined, done: true }

function makeIterator(array) {
  var nextIndex = 0;
  return {
    next: function() {
      return nextIndex < array.length ?
        {value: array[nextIndex++], done: false} :
        {value: undefined, done: true};
    }
  };
}
```

- 只要某个数据结构部署了 Iterator 接口，就可以对它使用扩展运算符，将其转为数组
- for...of 无法直接遍历对象 ，需要在对象提供Iterator 接口（一般用object.keys()转成数组）

```javascript
iterator.next() // 指针对象的next循环方法
iterator.return()// return()方法的使用场合是，如果for...of循环提前退出（通常是因为出错，或者有break语句），就会调用return()方法。如果一个对象在完成遍历前，需要清理或释放资源，就可以部署return()方法。


```

### 总结 ：Iterator首先创建一个指针对象 然后调用指针的next方法去遍历返回数据成员





### `for...of`循环

一个数据结构只要部署了`Symbol.iterator`属性，就被视为具有 iterator 接口，就可以用`for...of`循环遍历它的成员。也就是说，`for...of`循环内部调用的是数据结构的`Symbol.iterator`方法。

`for...of`循环可以使用的范围包括数组、Set 和 Map 结构、某些类似数组的对象（比如`arguments`对象、DOM NodeList 对象）、后文的 Generator 对象，以及字符串。



```javascript
const arr = ['red', 'green', 'blue'];

for(let v of arr) {
  console.log(v); // red green blue
}

const obj = {};
obj[Symbol.iterator] = arr[Symbol.iterator].bind(arr);

for(let v of obj) {
  console.log(v); // red green blue
}
```

上面代码中，空对象`obj`部署了数组`arr`的`Symbol.iterator`属性，结果`obj`的`for...of`循环，产生了与`arr`完全一样的结果。

JavaScript 原有的`for...in`循环，只能获得对象的键名，不能直接获取键值。ES6 提供`for...of`循环，允许遍历获得键值。

```javascript
var arr = ['a', 'b', 'c', 'd'];

for (let a in arr) {
  console.log(a); // 0 1 2 3
}

for (let a of arr) {// 键值
  console.log(a); // a b c d
}

for (let a of arr.keys()) {// 索引
  console.log(a); // 0 1 2 3 
}
```



### For...of与其他遍历语法比较

```javascript
for (var index = 0; index < myArray.length; index++) {
  console.log(myArray[index]);
}
```

这种写法比较麻烦，因此数组提供内置的`forEach`方法。

```javascript
myArray.forEach(function (value) {
  console.log(value);
});
```

这种写法的问题在于，无法中途跳出`forEach`循环，`break`命令或`return`命令都不能奏效。

```javascript
for (var index in myArray) {
  console.log(myArray[index]);
}
```

`for...in`循环有几个缺点。

- 数组的键名是数字，但是`for...in`循环是以字符串作为键名“0”、“1”、“2”等等。
- `for...in`循环不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括原型链上的键。
- 某些情况下，`for...in`循环会以任意顺序遍历键名。



`for...of`循环相比上面几种做法，有一些显著的优点。

```javascript
// 使用 break 语句，跳出for...of循环的例子
for (var n of fibonacci) {
  if (n > 1000)
    break;
  console.log(n);
}
```

- 有着同`for...in`一样的简洁语法，但是没有`for...in`那些缺点。
- 不同于`forEach`方法，它可以与`break`、`continue`和`return`配合使用。
- 提供了遍历所有数据结构的统一操作接口。



# Generator 函数



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
```

- `function`关键字与函数名之间有一个星号
- 函数体内部使用`yield`表达式，定义不同的内部状态
- 调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是遍历器对象（Iterator Object）。
- 必须调用遍历器对象的`next`方法，使得指针移向下一个状态



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
  for (var i = 0; i < length; i++) {// 这里不能用foreach等循环函数因为里面使用了yield
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
function* dataConsumer() {
  console.log('Started');
  console.log(1,yield);
  console.log(2,yield);
  return 'result';
}

let genObj = dataConsumer();
genObj.next();// yield是暂停标志 第一次next碰到第二行的yield就停止了，yield后面的表达式下次next才执行，所以只输出Started
// Started
genObj.next('a')
// 1. a
genObj.next('b')
// 2. b
```



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



































todo：链式调用原理





