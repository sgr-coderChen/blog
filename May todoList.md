# 5月 todoList 

> 每个礼拜一去盘点巩固上周的知识

✅已复盘，并理解   ❌没有理解

### 总结 5.10  已复盘✅✅

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

    

### 总结 5.14 已复盘✅✅

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
    ​	a:  Vue 不能检测到对象属性的添加或删除。由于 Vue 会在初始化实例时对属性执行 getter/setter 转化过程，所以属性必须在 data 对象上存在才能让 Vue 转换它，这样才能让它是响应的。

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

    

### 总结 5.18✅

- 页面分布引导插件 Driver.js （vue-element-admin Guide组件）
    https://kamranahmed.info/driver.js/#single-element-no-popover



## JavaScript系列



### 总结 5.19✅

- [JavaScript深入之从原型到原型链](https://github.com/mqyqingfeng/Blog/issues/2)	
    原型链，构造函数和对象 (主要去理解原型链那一张图)
    
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

    ```javascript
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
    ```

- Vue.observable小型状态管理 https://segmentfault.com/a/1190000019292569

- Vue.mixin 除了生命周期钩子函数不会合并(先触发混入对象，看下面例子)，其他所有配置已组件对象本身为主

    ```javascript
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

    

### 总结 5.20✅

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
    this一般有几种调用场景
    var obj = {a: 1, b: function(){console.log(this);}}
    1、作为对象调用时，指向该对象 obj.b(); // 指向obj
    2、作为函数调用, var b = obj.b; b(); // 指向全局window
    3、作为构造函数调用 var b = new Fun(); // this指向当前实例对象
    4、作为call与apply调用 obj.b.apply(object, []); // this指向当前的object
    ```




### 总结5.24

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
    ```

    



> 需要学习扩展的清单关键字：
>
> react 相关： react.js小书回温，antd-design  smart_npc_rn项目
>
> vue相关：  
>
> Webpack
>
> 数据Mock：mock-server ( vue-element-admin )
