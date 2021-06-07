# 六月 todoList 

> 每个礼拜一去盘点巩固上周的知识

✅已复盘，并理解   ❌没有理解



## 复习基础 [ECMAScript 6 入门](https://es6.ruanyifeng.com/)



### 总结 6.1

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

[reduce详解](https://www.jianshu.com/p/e375ba1cfc47)



### 对象的扩展

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

有四个操作会忽略`enumerable`为`false`的属性（如果为false则不会遍历该条属性）

- `for...in`循环：只遍历对象自身的和继承的可枚举的属性。
- `Object.keys()`：返回对象自身的所有可枚举的属性的键名。
- `JSON.stringify()`：只串行化对象自身的可枚举的属性。
- `Object.assign()`： 忽略`enumerable`为`false`的属性，只拷贝对象自身的可枚举的属性。

引入“可枚举”（`enumerable`）这个概念的最初目的，就是让某些属性可以规避掉`for...in`操作，不然所有内部属性和方法都会被遍历到。比如，对象原型的`toString`方法，以及数组的`length`属性，就通过“可枚举性”，从而避免被`for...in`遍历到。

总的来说，操作中引入继承的属性会让问题复杂化，大多数时候，我们只关心对象自身的属性。所以，尽量不要用`for...in`循环，而用`Object.keys()`代替。



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

Object.create()方法创建一个新对象，使用现有的对象来提供新创建的对象的__proto__

Object.getPrototypeOf(obj) 

- 返回指定对象的原型（内部`[[Prototype]]`属性的值）。
- obj：要返回其原型的对象。
- 返回值：给定对象的原型。如果没有继承属性，则返回 null 。

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
[...'hellow'] // => ["h", "e", "l", "l", "o"]

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



