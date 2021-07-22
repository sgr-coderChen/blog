# 八月 todoList 

> 每个礼拜一去盘点巩固上周的知识

✅已复盘，并理解   ❌没有理解



# [专题系列目录](https://github.com/mqyqingfeng/Blog)



## [JavaScript专题之跟着underscore学防抖](https://github.com/mqyqingfeng/Blog/issues/22)（debounce）

> 防抖是虽然事件持续触发，但只有等事件停止触发后 n 秒才执行函数

防抖的原理就是：你尽管触发事件，但是我一定在事件触发 n 秒后才执行，如果你在一个事件触发的 n 秒内又触发了这个事件，那我就以新的事件的时间为准，n 秒后才执行，总之，就是要等你触发完事件 n 秒内不再触发事件

**白话总结：持续抖动桌子（一直会清除定时器），只有停了才触发事件（防抖就是防止持续抖动）**


```js
// 第三版
function debounce(func, wait) {
    var timeout;

    return function () {
        // 如果使用我们的 debounce 函数，this 就会指向 Window 对象
        var context = this;
        // 传入event 对象
        var args = arguments;

        clearTimeout(timeout)
        timeout = setTimeout(function(){
            func.apply(context, args)
        }, wait);
    }
}

function getUserAction() {
    container.innerHTML = count++;
};

container.onmousemove = debounce(getUserAction, 1000);
```

![2bslCT.png](https://github.com/mqyqingfeng/Blog/raw/master/Images/debounce/debounce-1.gif)



[使用示例](https://github.com/mqyqingfeng/Blog/issues/22#issuecomment-340372386)



## [JavaScript专题之跟着 underscore 学节流](https://github.com/mqyqingfeng/Blog/issues/26)（throttle）

> 节流是持续触发的时候，每 n 秒执行一次函数

节流的原理很简单：

如果你持续触发事件，每隔一段时间，只执行一次事件。

根据首次是否执行以及结束后是否执行，效果有所不同，实现的方式也有所不同。
我们用 leading 代表首次是否执行，trailing 代表结束后是否再执行一次。

关于节流的实现，有两种主流的实现方式，一种是使用时间戳，一种是设置定时器。

**白话总结：水库的水一直流向阀门（必须持续触发事件），但泄洪阀每隔一段时间才打开，则为节流**

### 使用时间戳

让我们来看第一种方法：使用时间戳，当触发事件的时候，我们取出当前的时间戳，然后减去之前的时间戳(最一开始值设为 0 )，如果大于设置的时间周期，就执行函数，然后更新时间戳为当前的时间戳，如果小于，就不执行。

```js
// 第一版
function throttle(func, wait) {
    var context, args;
    var previous = 0;// 初始值为0 表示第一次立即执行一次

    return function() {
        var now = +new Date(); // 获取当前时间戳 等同于getTime，valueOf
        context = this;
        args = arguments;
        if (now - previous > wait) {
            func.apply(context, args);
            previous = now;
        }
    }
}

container.onmousemove = throttle(getUserAction, 1000);
```

![2bslCT.png](https://github.com/mqyqingfeng/Blog/raw/master/Images/throttle/throttle1.gif)

当鼠标移入的时候，事件立刻执行，每过 1s 会执行一次，如果在 4.2s 停止触发，以后不会再执行事件。



### 使用时间戳

当触发事件的时候，我们设置一个定时器，再触发事件的时候，如果定时器存在，就不执行，直到定时器执行，然后执行函数，清空定时器，这样就可以设置下个定时器。

```js
// 第二版
function throttle(func, wait) {
    var timeout;
    var previous = 0;

    return function() {
        context = this;
        args = arguments;
        if (!timeout) {
            timeout = setTimeout(function(){
                timeout = null;
                func.apply(context, args)
            }, wait)
        }

    }
}
```

所以比较两个方法：

1. 第一种事件会立刻执行，第二种事件会在 n 秒后第一次执行
2. 第一种事件停止触发后没有办法再执行事件（因为最后一次未到达时间戳的if条件阈值，所有不触发），第二种事件停止触发后依然会再执行一次事件（最后一次触发定时器并清除了，又进了一次if条件）



### 综合两者的优势

```js
// 第三版
function throttle(func, wait) {
    var timeout, context, args, result;
    var previous = 0;

    var later = function() {
        previous = +new Date();
        timeout = null;
        func.apply(context, args)
    };

    var throttled = function() {
        var now = +new Date();
        //下次触发 func 剩余的时间
        var remaining = wait - (now - previous);
        context = this;
        args = arguments;
         // 如果没有剩余的时间了或者你改了系统时间
        if (remaining <= 0 || remaining > wait) {
            if (timeout) {
                clearTimeout(timeout);
                timeout = null;
            }
            previous = now;
            func.apply(context, args);
        } else if (!timeout) {
            timeout = setTimeout(later, remaining);
        }
    };
    return throttled;
}
```





## [JavaScript专题之类型判断](https://github.com/mqyqingfeng/Blog/issues/28#)



### typeof

```js
function a() {}
console.log(typeof a); // function
console.log(typeof('yayu')) // string


// typeof 无法区分object内部类型
var date = new Date();
var error = new Error();
console.log(typeof date); // object
console.log(typeof error); // object

```

### Object.prototype.toString

调用 Object.prototype.toString 会返回一个由 "[object " 和 class 和 "]" 组成的字符串，而 class 是要判断的对象的内部属性。

```js
// 以下是11种：
var number = 1;          // [object Number]
var string = '123';      // [object String]
var boolean = true;      // [object Boolean]
var und = undefined;     // [object Undefined]
var nul = null;          // [object Null]
var obj = {a: 1}         // [object Object]
var array = [1, 2, 3];   // [object Array]
var date = new Date();   // [object Date]
var error = new Error(); // [object Error]
var reg = /a/g;          // [object RegExp]
var func = function a(){}; // [object Function]
console.log(Object.prototype.toString.call(Math)); // [object Math]
console.log(Object.prototype.toString.call(JSON)); // [object JSON]
function a() {
    console.log(Object.prototype.toString.call(arguments)); // [object Arguments]
}
a();


function checkType() {
    for (var i = 0; i < arguments.length; i++) {
        console.log(Object.prototype.toString.call(arguments[i]))
    }
}

checkType(number, string, boolean, und, nul, obj, array, date, error, reg, func)


```



### type API（jquery源码）仅供参考 只能检测常用的数据类型

type 函数能检测各种类型的值 

```js
// 第二版
var class2type = {};

// 生成class2type映射
"Boolean Number String Function Array Date RegExp Object Error".split(" ").map(function(item, index) {
    class2type["[object " + item + "]"] = item.toLowerCase();
})

function type(obj) {
    // 一箭双雕
    if (obj == null) {
        return obj + "";
    }
    return typeof obj === "object" || typeof obj === "function" ?
        class2type[Object.prototype.toString.call(obj)] || "object" :
        typeof obj;
}

// 调用
type({})
"object"
type(function a(){})
"function"
```





## JavaScript专题之函数柯里化



