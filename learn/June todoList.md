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











