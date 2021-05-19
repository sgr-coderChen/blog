

# 5月-6月底 （vue - pc篇）

## 5月 todoList ✅

> 每个礼拜一去盘点巩固上周的知识

- 通过vue-element-admin熟悉pc的架构及常用业务

    ```javascript
    总结 5.10 已复盘✅
    1.	post-css Autoprefixer 自动补全css浏览器前缀 
    	https://blog.csdn.net/weixin_44388523/article/details/106957559
    	安装教程 => https://blog.csdn.net/qq_38935512/article/details/111866711
    2.	el-scrollbar的使用
        https://www.cnblogs.com/myfirstboke/p/10218138.html
        https://segmentfault.com/a/1190000015068613
    3.	左侧菜单栏重定向 
        this.$router.replace({
          path: '/redirect' + fullPath
        })
    ```

    ```javascript
    总结 5.14 已复盘✅
    1.	最佳全局挂载方式（Object.defineProperty）
    	https://github.com/dwqs/blog/issues/51
    	Vue.prototype.$test = { a: 'A' }
    
        console.log('$test', this.$test) // { a: "A" }
        this.$test = 'a'  // a
        this.$test.a = 'a' // { a: "a" }
        
    
    	//writable = false 只读是默认的
        Object.defineProperty(Vue.prototype, '$fn', { value: { a: 'A' }, writable: false }) 
    
        console.log('$fn', this.$fn) // { a: "A" }
        this.$fn = 'a' 
    	console.log('$fn修改了', this.$fn) //TypeError: Cannot assign to read only property 
        this.$fn.a = 'a' // 修改对象的属性是可以修改的 不受writable影响 =>  { a: "a" }
        
    2. 	watch监听对象 
    	https://www.cnblogs.com/yesu/p/9546458.html
    	https://www.cnblogs.com/lijjj/p/12978469.html
    	a: Vue 不能检测到对象属性的添加或删除。由于 Vue 会在初始化实例时对属性执行 getter/setter 转化过程，		所以属性必须在 data 对象上存在才能让 Vue 转换它，这样才能让它是响应的。
        b: 默认情况下 handler 只监听obj这个属性它的引用的变化，我们只有给obj赋值的时候它才会监听到，比如我们在 	 mounted事件钩子函数中对obj进行重新赋值
        
    3.	this.$nextTick 
        //改变数据
        vm.message = 'changed'
        //想要立即使用更新后的DOM。这样不行，因为设置message后DOM还没有更新
        console.log(vm.$el.textContent) // 并不会得到'changed'
        //这样可以，nextTick里面的代码会在DOM更新后执行
        Vue.nextTick(function(){
            console.log(vm.$el.textContent) //可以得到'changed'
        })
    ```

    

    ```javascript
    总结 5.18
    1.	页面分布引导插件 Driver.js （vue-element-admin Guide组件）
    https://kamranahmed.info/driver.js/#single-element-no-popover
    
    ```

    ```
    
    ```

- 尝试自己实现pc组件封装

- 用vue-admin-template实现几个业务demo

## JavaScript深入系列

```javascript
总结 5.19

1.	JavaScript深入之从原型到原型链	
	原型链，构造函数和对象 (主要去理解原型链那一张图)
	https://github.com/mqyqingfeng/Blog/issues/2
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
	这里new出来的多了一个person前缀名
	不同点： https://blog.csdn.net/fanfanzk1314/article/details/79289850


2.	JavaScript深入之词法作用域和动态作用域
	要点： JavaScript 采用词法作用域(lexical scoping)，也就是静态作用域。
    函数的作用域在函数定义的时候就决定了，函数的作用域基于函数创建的位置。

3.	JavaScript深入之变量对象 (没懂)
	同一作用域下,函数提升比变量提升得更靠前
	首先会处理函数声明，其次会处理变量声明，如果变量名称跟已经声明的形式参数或函数相同，则变量声明不会干扰已经存在		的这类属性。
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
4. Vue.observable小型状态管理 https://segmentfault.com/a/1190000019292569
5. Vue.mixin 除了生命周期钩子函数不会合并，其他所有配置已组件对象本身为主
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



> 需要学习扩展的清单关键字：
>
> react 相关： react.js小书回温，antd-design  smart_npc_rn项目
>
> vue相关：  
>
> Webpack
>
> 数据Mock：mock-server ( vue-element-admin )