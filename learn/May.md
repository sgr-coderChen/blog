# 5月 todoList 

> 每个礼拜一去盘点巩固上周的知识

✅已复盘，并理解   ❌没有理解

### 总结 5.10  已复盘✅✅✅✅✅

- post-css Autoprefixer 自动补全css浏览器前缀 
    https://blog.csdn.net/weixin_44388523/article/details/106957559
    安装教程 => https://blog.csdn.net/qq_38935512/article/details/111866711
- el-scrollbar的使用
    https://www.cnblogs.com/myfirstboke/p/10218138.html
    https://segmentfault.com/a/1190000015068613

- ```javascript
    左侧菜单栏重定向 
    this.$router.replace({
    	path: '/redirect' + fullPath
    }) 
    
    ```

    

### 总结 5.14 已复盘✅✅✅✅✅✅

- 最佳全局挂载方式（Object.defineProperty）

    https://github.com/dwqs/blog/issues/51

    ```javascript
    // 方式一
    Vue.prototype.$test = { a: 'A' }
    
    console.log('$test', this.$test) // { a: "A" }
    this.$test = 'a'  // a
    this.$test.a = 'a' // { a: "a" }
    
    // 方式二（推荐）
    // writable = false 只读是默认的
    Object.defineProperty(Vue.prototype, '$fn', { value: { a: 'A' }, writable: false }) 
    
    console.log('$fn', this.$fn) // { a: "A" }
    this.$fn = 'a' //使用第二种定义的 不能直接修改整个属性（writable属性的关系， 所有这样挂载更优雅）
    console.log('$fn修改了', this.$fn) //TypeError: Cannot assign to read only property 
    this.$fn.a = 'a' // 修改对象的属性是可以修改的 不受writable影响 =>  { a: "a" }
    ```

- watch监听对象 

    ​	https://www.cnblogs.com/yesu/p/9546458.html
    ​	https://www.cnblogs.com/lijjj/p/12978469.html
    ​	a:  Vue 不能检测到对象属性的添加或删除，或者数组索引直接设置一个项。由于 Vue 会在初始化实例时对属性执行 getter/setter 转化过程，所以属性必须在 data 对象上存在才能让 Vue 转换它，这样才能让它是响应的。

    ​    b:  默认情况下 handler 只监听obj这个属性它的引用的变化，我们只有给obj赋值的时候它才会监听到，比如我们在 mounted事件钩子函数中对obj进行重新赋值

- this.$nextTick 

    ```javascript
    //改变数据
    vm.message = 'changed'
    //想要立即使用更新后的DOM。这样不行，因为设置message后DOM还没有更新
    console.log(vm.$el.textContent) // 并不会得到'changed'
    //这样可以，nextTick里面的代码会在DOM更新后执行
    Vue.nextTick(function(){
        console.log(vm.$el.textContent) //可以得到'changed'
    })
    ```

    

### 总结 5.18✅✅

- 页面分布引导插件 Driver.js （vue-element-admin Guide组件）
    https://kamranahmed.info/driver.js/#single-element-no-popover



## JavaScript系列



### 总结 5.19✅✅✅✅✅✅

- [JavaScript深入之从原型到原型链](https://github.com/mqyqingfeng/Blog/issues/2)	
    原型链，构造函数和对象 (主要去理解原型链那一张图，蓝色的就是原型链)
    
     __proto__
    
    其次是 __proto__ ，绝大部分浏览器都支持这个非标准的方法访问原型，然而它并不存在于 Person.prototype 中，实际上，它是来自于 Object.prototype ，与其说是一个属性，不如说是一个 getter/setter，当使用 obj.__proto__ 时，可以理解成返回了 Object.getPrototypeOf(obj)。
    
- [![2bslCT.png](https://z3.ax1x.com/2021/06/15/2bslCT.png)](https://imgtu.com/i/2bslCT)
  
    
  
    [js中的类和constructor](https://www.jianshu.com/p/5c501677b5b8)
```javascript
    function Person() {
    
    }
    var person1 = new Person()
    var obj = {}
    console.log('person', Person, typeof Person, Object.prototype.toString.call(Person))
    console.log('new', person1, typeof person1, Object.prototype.toString.call(person1))
    console.log('obj', obj, typeof obj, Object.prototype.toString.call(obj))
    // person: ƒ Person() {} function [object Function]
    // new: Person {}__proto__: Object object [object Object]
    // obj: {} object [object Object]
    // 这里new出来的多了一个person前缀名
    // 不同点： https://blog.csdn.net/fanfanzk1314/article/details/79289850
```



- [JavaScript深入之词法作用域和动态作用域](https://github.com/mqyqingfeng/Blog/issues/3)

    要点： JavaScript 采用词法作用域(lexical scoping)，也就是静态作用域。（注意：这里this是动态的 http://javascript.ruanyifeng.com/oop/this.html#toc3）

    函数的作用域在函数定义的时候就决定了，函数的作用域基于函数创建的位置。

- [JavaScript深入之变量对象](https://github.com/mqyqingfeng/Blog/issues/5) 
    同一作用域下,函数提升比变量提升得更靠前
    首先会处理函数声明，其次会处理变量声明，如果变量名称跟已经声明的形式参数或函数相同，则变量声明不会干扰已经存在的这类属性。

    [js函数声明和函数表达式的区别](https://www.cnblogs.com/gaoht/p/10401863.html)
    
    [js中对象的constructor属性及其作用](https://blog.csdn.net/summer199605/article/details/88700382)
    
```javascript
    // 例子一
    console.log(foo);
    var foo = 1;
    console.log(foo);
    function foo(){};
    
    执行顺序：
    foo() 			  //函数提升
	var foo			  //和函数重名了，被忽略
    console.log(foo);	  //打印函数
    foo = 1;		  //全局变量foo
    console.log(foo);	  //打印1，事实上函数foo已经不存在了，变成了1
    
    
    // 例子二
    函数提升，函数提升优先级高于变量提升
    
    console.log(bar);         // f bar() { console.log(123) }
    console.log(bar());       // 123
    var bar = 456;
    function bar() {
        console.log(123);     
    }
    console.log(bar);         // 456
    
    // -----------------解析后-----------------
    // 函数提升，函数提升优先级高于变量提升
    function bar() {
        console.log(123)
    };
    // 变量提升，变量提升不会覆盖（同名）函数提升，只有变量再次赋值时，才会被覆盖
    var bar;
    console.log(bar);	// f bar() { console.log(123) }
    console.log(bar());	// 123
    
    bar = 456;      	// 变量赋值，覆盖同名函数
    console.log(bar); 	// 456
    
    // 注意*：函数声明和函数表达式是有区别的 https://www.cnblogs.com/gaoht/p/10401863.html
```

- Vue.observable小型状态管理 https://segmentfault.com/a/1190000019292569

- Vue.mixin 除了生命周期钩子函数不会合并(先触发混入对象，看下面例子)，其他所有配置已组件对象本身为主

    ```javascript
    // 一个混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。
    var mixin = {
        created: function () {
            console.log('混入对象的钩子被调用')
        }
    }
    
    new Vue({
        mixins: [mixin],
        created: function () {
            console.log('组件钩子被调用')
        }
    })
    
    // => "混入对象的钩子被调用"
    // => "组件钩子被调用"
    ```

    - mixin中的data属性混入不同的组件时都是独立的
    - mixin.js中能拿到被混入组件的data，methods等等
    
       [vue中mixins的使用方法和注意点（详）](https://www.jianshu.com/p/bcff647d24ec)



### 总结 5.20✅✅✅✅✅✅

- JavaScript专题之跟着underscore学防抖

    ```javascript
    // JavaScript专题之跟着underscore学防抖 第一版
    function debounce(func, wait) {
        var timeout;
        return function () {
            clearTimeout(timeout)
            timeout = setTimeout(func, wait);
        }
    }
    
    -------------------------------------
    
    //  onmousemove接收一个函数体 每次移动鼠标 去触发这个函数 所以 debounce 要 return一个方法
    container.onmousemove = this.debounce(this.test, 1000);
    
    test() {
        // console.log("触发了");
        this.count += 1
    },
    // 闭包: http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html
    // https://blog.csdn.net/qq_34629352/article/details/105602859?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-1&spm=1001.2101.3001.4242
    // https://blog.csdn.net/u010585120/article/details/52047251
    // 闭包可以用在许多地方。它的最大用处有两个，一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。
        
    // 使用闭包的注意点
    // 1）由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。
    
    // 2）闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。
    debounce(func, wait) {
        var timeout; // 因为闭包 执行一次后timeout变量 仍然存在 所以第二次进入的时候删除的是老的那个定时器
        return function() {
            clearTimeout(timeout); //  闭包读取父函数的timeout 并删除
            timeout = setTimeout(func, wait); //再次触发函数 会以新的事件的时间为准 
        };
    },
    ```

    ```javascript
    // this一般有几种调用场景
        var obj = {a: 1, b: function(){console.log(this);}}
        1、作为对象调用时，指向该对象 obj.b(); // 指向obj
        2、作为函数调用, var b = obj.b; b(); // 指向全局window
        3、作为构造函数调用 var b = new Fun(); // this指向当前实例对象
        4、作为call与apply调用 obj.b.apply(object, []); // this指向当前的object
    ```

### 闭包总结

```js
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f;
}
checkscope()(); // "local scope"

var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    
    return function (){// 向上找到local scope
        return scope;
    };
}
checkscope()(); // "local scope" 和上面的防抖闭包一样的

var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    
    return f;
}
function f(){// 声明在和checkscope同级 根据作用域链找到global scope
    return scope;
}
checkscope()(); // "global scope"
```

- 核心就是: **函数的作用域在函数定义的时候就决定了** ，真正理解这句话的时候闭包什么的也就理解了。




### 总结5.24✅✅✅✅✅

- this的指向问题结合思考题  http://javascript.ruanyifeng.com/oop/this.html#toc3

    ```javascript
    //	 闭包的思考题 看上面地址  “避免多层this” 的问题
    var name = "The Window";
    
    var object = {
        name : "My Object",
    
        getNameFunc : function(){
            return function(){
                return this.name;
            };
    
        }
    
    };
    
    alert(object.getNameFunc()()); // The Window
    
    //	等同于
    
    var name = "The Window";
    var f = function(){
        return this.name;
    };
    var object = {
        name : "My Object",
        getNameFunc : function(){
            return f
        }
    
    };
    
    alert(object.getNameFunc()());// The Window
    
    
    var name = "The Window";
    
    　　var object = {
    　　　　name : "My Object",
    
    　　　　getNameFunc : function(){
    　　　　　　var that = this; // 多层嵌套需要固定this的指向
    　　　　　　return function(){
    　　　　　　　　return that.name;
    　　　　　　};
    　　　　}
    
    　　};
    
    　　alert(object.getNameFunc()());// My Object
    ```


### 总结5.25✅✅✅✅✅

- [JavaScript深入之参数按值传递](https://github.com/mqyqingfeng/Blog/issues/10)

    重点：栈与堆的概念 ，指针，基本类型，引用类型拷贝的区别

    https://github.com/mqyqingfeng/Blog/issues/10#issuecomment-305145483
    https://github.com/mqyqingfeng/Blog/issues/10#issuecomment-305645497

- [JavaScript深入之call和apply的模拟实现](https://github.com/mqyqingfeng/Blog/issues/11)

    原理：1.将函数设为对象的属性   2.执行该函数  3.删除该函数

    ```javascript
	// 第一版
    Function.prototype.call2 = function(context) {
        // 首先要获取调用call的函数，用this可以获取
        context.fn = this;
        context.fn();
        delete context.fn;
    }
    
    // 测试一下
    var foo = {
        value: 1
    };
    
    function bar() {
        console.log(this.value);
    }
    
    bar.call2(foo); // 1
    ```
    
    思考：context.fn = this;  为什么方法里面调用方法,的this 为方法本身呢？
    
    https://github.com/mqyqingfeng/Blog/issues/11#issuecomment-353509920
    
    
    
    ```javascript
    function Person() {
      this.name = 'ClarenceC';
    }
     
    Person.prototype.greet = function(){
      console.log('hello ' + this.name);
    }
    
    var person = new Person();
    console.log(person.name)
    
    person.greet()
    // 这个例子是想说明 person 作为构造函数 Person 的实例对象，person.greet 函数中的 this 是指向实例对象 person 的。
    
    // 总结
    Function.prototype.call2 = function(context) {
        context.fn = this; // 这里的this就是bar实例本身 因为他是在bar上调用的 this指向bar  
        context.fn();
        delete context.fn;
    }
    function bar(name, age) {
        console.log(name)
        console.log(age)
        console.log(this.value);
    }
    bar.call2(foo, 'kevin', 18);
    ```
    
    - call与apply的唯一的区别就是，它接收一个数组作为函数执行时的参数
    
    - call和apply不仅绑定函数执行时所在的对象，还会立即执行函数
    
        ```js
        var o = new Object();
        
        o.f = function () {
          console.log(this === o);
        }
        o.f.apply(o); // 一旦掉用 直接输出了 console.log(this === o); 因为apply源码中就是 赋值this 调用fn 删除fn，所以需要放到一个函数体内
        ```
    
        
    
    - bind与（call或者apply）的区别 bind方法用于将函数体内的this绑定到某个对象，然后返回一个新函数。
    
    使用场景如下 摘自[阮一峰javascript标准参考](http://javascript.ruanyifeng.com/oop/this.html#toc3)
    
    ```javascript
    // 使用场景一
    
    var o = new Object();
    o.f = function () {
      console.log(this === o);
    }
    
    // jQuery 的写法
    $('#button').on('click', o.f);
    // 上面代码中，点击按钮以后，控制台会显示false。原因是此时this不再指向o对象，而是指向按钮的 DOM 对象，因为f方法是在按钮对象的环境中被调用的。这种细微的差别，很容易在编程中忽视，导致难以察觉的错误。
    
    // 解决办法
    // 1.用call或者apply
    var o = new Object();
    
    o.f = function () {
      console.log(this === o);
    }
    
    var f = function (){
      o.f.apply(o);
      // 或者 o.f.call(o);  如果不绑定函数内部的this，谁调用 this就指向谁（button DOM），所以要进行绑定this
    };
    
    // jQuery 的写法
    $('#button').on('click', f);
    // 上面代码中，点击按钮以后，控制台将会显示true。由于apply方法（或者call方法）不仅绑定函数执行时所在的对象，还会立即执行函数，因此不得不把绑定语句写在一个函数体内
    
    // 2.bind解决
    var o = new Object();
    
    o.f = function () {
      console.log(this === o);
    }
    
    var f = o.f.bind(o)
    // jQuery 的写法
    $('#button').on('click', f); //这里不能直接写在里面click事件绑定bind方法生成的一个匿名函数。这样会导致无法取消绑定 需要var f = o.f.bind(o) 在放入
    
    
    ```
    
    ```javascript
    // 使用场景二
    var d = new Date();
    d.getTime() // 1481869925657
    
    
    
    var print = d.getTime;
    print() // Uncaught TypeError: this is not a Date object.
    // 上面代码中，我们将d.getTime方法赋给变量print，然后调用print就报错了。这是因为getTime方法内部的this，绑定Date对象的实例，赋给变量print以后，内部的this已经不指向Date对象的实例了。
    
    // print（）报错的解释
    function Date () {// 伪代码
        currentTime: '65165156156156'
        getTime: function() {
            return this.currentTime
        }
    }
    // 当赋值给print时 堆中的getTime方法指针已经指向了print 所以去执行getTime的时候，里面的this.currentTime为print.currentTime 所以需要给getTime方法重新绑定上Date实例
    
    
    // 1.用bind
    var d = new Date();
    d.getTime() // 1481869925657
    
    var print = d.getTime.bind(d);
    print() // 1481869925657
    
    
    // 2.用call或者apply
    var d = new Date();
    d.getTime() // 1481869925657
    
    var print = function(){
       return d.getTime.call(d) //这里要return 因为call是会立即执行的 而bind会自动返回一个绑定好的函数
    };
    print() // 1481869925657
    
    // 3.用es6的箭头函数固定this指向
    var d = new Date();
    
    var print = () => d.getTime();
    print() // 1481869925657
    ```
    
    ```javascript
    // 使用场景三
    var counter = {
      count: 0,
      inc: function () {
        this.count++;
      }
    };
    
    var func = counter.inc.bind(counter);
    // 或者 var func = () => counter.inc.call(counter)  用call、apply需要 return 返回
    // 或者用es6   var func = () => counter.inc();
    func();
    counter.count // 1
    ```
    
    



### 总结5.26✅✅✅✅

- [JavaScript深入之new的模拟实现](https://github.com/mqyqingfeng/Blog/issues/13)

    ```js
    // 第一版代码
    function objectFactory() {
    
        var obj = new Object(),
            
    	// shift() 方法用于把数组的第一个元素从其中删除，并返回第一个元素的值。即Constructor为需要创建实例的构造函数（var person = new Person() 中的Person）
        Constructor = [].shift.call(arguments);
    
        obj.__proto__ = Constructor.prototype;
    
        Constructor.apply(obj, arguments);
    
        return obj;
    
    };
    // 1.用new Object() 的方式新建了一个对象 obj
    // 2.取出第一个参数，就是我们要传入的构造函数。此外因为 shift 会修改原数组，所以 arguments 会被去除第一个参数
    // 3.将 obj 的原型指向构造函数，这样 obj 就可以访问到构造函数原型中的属性
    // 4.使用 apply，改变构造函数 this 的指向到新建的对象，这样 obj 就可以访问到构造函数中的属性
    // 5.返回 obj
    ```

    

- [原生JS实现NEW,带参数](https://www.jianshu.com/p/76aac2f6fabe)

    ```javascript
    function New(func) {
        // 创建一个空对象，继承构造函数的原型对象
        var res = Object.create(func.prototype);
        // 执行构造函数，传递上下文和参数
        var ret = func.apply(res, Array.prototype.slice.call(arguments, 1));
        if (typeof ret === 'object' && ret !== null) {
            return ret
        } else {
      // Array等类型返回自身 Array.apply(Object.create(Array.prototype)) => []
            return res
        }
    }
    //测试代码：
	function Person(name, age, job) {
	    this.name = name;
	    this.age = age;
	    this.job = job;
	    this.sayName = function () {
	        alert(this.name);
	    };
	}
	var p1 = New(Person, "Ysir", 24, "stu");
	console.log(p1)
	var a = New(Array)
	console.log(a)
	
	// Array.prototype.slice.apply(arguments)和[].shift.call(arguments)的使用方法
	// https://blog.csdn.net/qq_27626333/article/details/51831282
	// 原因：arguments是一个类数组对象，包含着传入函数中的所有参数，而且可以使用length属性来确定传递进来多少个参数。直接调用arguments.slice()将返回一个"Object doesn't support this property or method"错误，因为arguments不是一个真正的数组。而以上代码调用Array.prototype.slice.apply(arguments)的意义就在于它能将函数的参数对象转化为一个真正的数组。另一方面也可推知Arguments对象和Array对象的亲缘关系。如果你在编写JavaScript的时候，常常碰到需要将arguments对象转成Array来处理的情形，这个技巧可以帮上忙。Array其他的原型方法也可以应用在arguments上
	
	var args = Array.from(arguments).slice(1) 
	// 等同于
	var args = Array.prototype.slice.call(arguments, 1)
	console.log('args', args)
	
	// [].shift.call(arguments) 等同于 Array.prototype.shift.call(arguments)
	
	// 看 JavaScript深入之类数组对象与arguments  https://github.com/mqyqingfeng/Blog/issues/14
	
	```





## 复习基础 [ECMAScript 6 入门](https://es6.ruanyifeng.com/)



### 总结 5.27✅✅✅✅

1. Babel是一个广泛使用的 ES6 转码器

```javascript
{
  "presets": [],//presets字段设定转码规则
  "plugins": []
}
```

2. polyfill :Babel 默认只转换新的 JavaScript 句法（syntax），而不转换新的 API，比如Iterator、Generator、Set、Map、Proxy、Reflect、Symbol、Promise等全局对象，以及一些定义在全局对象上的方法（比如Object.assign）都不会转码。

    举例来说，ES6 在`Array`对象上新增了`Array.from`方法。Babel 就不会转码这个方法。如果想让这个方法运行，可以使用**core-js**和`regenerator-runtime`(后者提供generator函数的转码)，为当前环境提供一个垫片。

3. const 对于对象属性可修改 ，const let 不会去修改顶层对象的属性

    ```javascript
    var a = 1;
    // 如果在 Node 的 REPL 环境，可以写成 global.a
    // 或者采用通用方法，写成 this.a
    window.a // 1
    
    let b = 1;
    window.b // undefined
    ```

4. 解构设置函数参数的默认值

    ```javascript
    // 写法一
    function m1({x = 0, y = 0} = {}) {//设定整个默认值为一个对象
      return [x, y];
    }
    
    // 写法二
    function m2({x, y} = { x: 0, y: 0 }) {//设置对象中具体属性 x y的默认值
      return [x, y];
    }
    // 上面两种写法都对函数的参数设定了默认值，区别是写法一函数参数的默认值是空对象，但是设置了对象解构赋值的默认值；写法二函数参数的默认值是一个有具体属性的对象，但是没有设置对象解构赋值的默认值。
    ```
    ```javascript
    // 函数没有参数的情况
    m1() // [0, 0]
    m2() // [0, 0]
    
    // x 和 y 都有值的情况
    m1({x: 3, y: 8}) // [3, 8]
    m2({x: 3, y: 8}) // [3, 8]
    
    // x 有值，y 无值的情况
    m1({x: 3}) // [3, 0]
    m2({x: 3}) // [3, undefined]
    
    // x 和 y 都无值的情况
    m1({}) // [0, 0];
    m2({}) // [undefined, undefined]
    
    m1({z: 3}) // [0, 0]
    m2({z: 3}) // [undefined, undefined]
    ```
    ```javascript
    // 3.注意点：与解构赋值默认值结合使用
    function foo({x, y = 5}) {
      console.log(x, y);
    }
    
    foo({}) // undefined 5
    foo({x: 1}) // 1 5
    foo({x: 1, y: 2}) // 1 2
    foo() // TypeError: Cannot read property 'x' of undefined
    // 上面代码只使用了对象的解构赋值默认值，没有使用函数参数的默认值。只有当函数foo的参数是一个对象时，变量x和y才会通过解构赋值生成。如果函数foo调用时没提供参数，变量x和y就不会生成，从而报错。通过提供函数参数的默认值，就可以避免这种情况。
    
    function foo({x, y = 5} = {}) {
      console.log(x, y);
    }
    
    foo() // undefined 5
    // 上面代码指定，如果没有提供参数，函数foo的参数默认为一个空对象。
    ```
    ```javascript
    jQuery.ajax = function (url, {
      async = true,
      beforeSend = function () {},
      cache = true,
      complete = function () {},
      crossDomain = false,
      global = true,
      // ... more config
    } = {}) {
      // ... do stuff
    };
    //指定参数的默认值，就避免了在函数体内部再写var foo = config.foo || 'default foo';这样的语句。
    
    ```

    ```javascript
    function fetch(url, { body = '', method = 'GET', headers = {} }) {
      console.log(method);
    }
    fetch('http://example.com', {})
    // "GET"
    fetch('http://example.com')
    // 报错
    
    
    
    function fetch(url, { body = '', method = 'GET', headers = {} } = {}) {
       //多了个{}
      console.log(method);
    }
    fetch('http://example.com')
    // "GET"
    ```

    

5. 字符串扩展

    ```javascript
    'aabbcc'.replace(/b/g, '_')
    // 'aa__cc'
    
    'aabbcc'.replaceAll('b', '_')
    // 'aa__cc' replaceAll  ES6方法
    ```

 6. 箭头函数

    ```javascript
    // 如果箭头函数只有一行语句，且不需要返回值，可以采用下面的写法，就不用写大括号了。
    let fn = () => void doesNotReturn();
    ```

    ```javascript
    // 由于箭头函数使得this从“动态”变成“静态”，下面两个场合不应该使用箭头函数。
    const cat = {
      lives: 9,
      jumps: () => {
        this.lives--;
      }
    }
    // 上面代码中，cat.jumps()方法是一个箭头函数，这是错误的。调用cat.jumps()时，如果是普通函数，该方法内部的this指向cat；如果写成上面那样的箭头函数，使得this指向全局对象，因此不会得到预期结果。这是因为对象不构成单独的作用域，导致jumps箭头函数定义时的作用域就是全局作用域。
    ```

    

    

    ### [ES6语法之函数式编程实现 pipeline](https://blog.csdn.net/sunq1982/article/details/68939811)

    

    [**实现管道机制的核心 -  reduce解析**](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

    ```js
    [0, 1, 2, 3, 4].reduce(function(accumulator, currentValue, currentIndex, array){
      return accumulator + currentValue;
    });
    ```

    callback 被调用四次，每次调用的参数和返回值如下表：

    | **callback** | **accumulator** | **currentValue** | **currentIndex** | **array**       | return value |
    | ------------ | --------------- | ---------------- | ---------------- | --------------- | ------------ |
    | first        | 0               | 1                | 1                | [0, 1, 2, 3, 4] | 1            |
    | second       | 1               | 2                | 2                | [0, 1, 2, 3, 4] | 3            |
    | third        | 3               | 3                | 3                | [0, 1, 2, 3, 4] | 6            |
    | fourth       | 6               | 4                | 4                | [0, 1, 2, 3, 4] | 10           |

    第二次的**accumulator**为第一次 accumulator + currentValue

    ```js
    // 这里改成函数执行 把有返回值的函数当成一个变量在函数中传参调用
    funcs.reduce(function(pre,cur){
     return cur(pre);
    }, val);
    
    var pipeline = (val, ...funcs) => funcs.reduce((pre, cur) => cur(pre), val); 
    
    var double = n => n * 2;
    var pow = n => n * n;
    var pow3 = n => n * n * n;
    
    var result = pipeline(2, double, pow, pow3); // 4096
    ```

    

    ```javascript
    
    // 下面是一个部署管道机制（pipeline）的例子，即前一个函数的输出是后一个函数的输入。
    const pipeline = (...funcs) => val => funcs.reduce((a, b) => b(a), val);
    
    const plus1 = a => a + 1;
    const mult2 = a => a * 2;
    const addThenMult = pipeline(plus1, mult2);
    
    
    addThenMult(5)// 12
    // 总结：pipeline具体实现看上面的链接 （ES6语法之函数式编程实现 pipeline）
    
    // val到底传给了谁？  因为箭头函数可以传递参数
    function foo() {
      setTimeout(() => {
        console.log('args:', arguments);
      }, 100);
    }
    
    foo(2, 4, 6, 8)
    // args: [2, 4, 6, 8] 上面代码中，箭头函数内部的变量arguments，其实是函数foo的arguments变量。
    
    
    ```

7. rest参数和扩展运算符的区别

    扩展运算符（spread）是三个点（`...`）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列。

    rest参数

    ```javascript
    // ES6 引入 rest 参数（形式为...变量名），用于获取函数的多余参数，这样就不需要使用arguments对象了。rest 参数搭配的变量是一个数组，该变量将多余的参数放入数组中。
    function add(...values) {// [2, 5 ,3]
      let sum = 0;
    
      for (var val of values) {
        sum += val;
      }
    
      return sum;
    }
    
    add(2, 5, 3) // 10
    
    // arguments变量的写法
    function sortNumbers() {
      return Array.prototype.slice.call(arguments).sort();
    }
    
    // rest参数的写法
    const sortNumbers = (...numbers) => numbers.sort();
    
    
    // 函数的length属性，不包括 rest 参数。
    (function(a) {}).length  // 1
    (function(...a) {}).length  // 0
    (function(a, ...b) {}).length  // 1
    ```

    

    扩展运算符

    ```javascript
    function push(array, ...items) {
      array.push(...items);
    }
    
    function add(x, y) {
      return x + y;
    }
    
    const numbers = [4, 38];
    add(...numbers) // 42
    // 上面代码中，array.push(...items)和add(...numbers)这两行，都是函数的调用，它们都使用了扩展运算符。该运算符将一个数组，变为参数序列。
    
    // 扩展运算符后面还可以放置表达式。
    const arr = [
      ...(x > 0 ? ['a'] : []),
      'b',
    ];
    
    // 与解构赋值结合
    const [first, ...rest] = [1, 2, 3, 4, 5];
    first // 1
    rest  // [2, 3, 4, 5]
    
    const [first, ...rest] = [];
    first // undefined
    rest  // []
    
    const [first, ...rest] = ["foo"];
    first  // "foo"
    rest   // []
    ```

    














需要学习扩展的清单关键字：

> react 相关： react.js小书回温，antd-design  smart_npc_rn项目
>
> vue相关：  
>
> Webpack
>
> 数据Mock：mock-server ( vue-element-admin )
