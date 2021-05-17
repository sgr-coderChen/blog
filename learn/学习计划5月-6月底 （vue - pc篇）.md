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
    
    总结 5.14 已复盘✅
    1.	https://github.com/dwqs/blog/issues/51
    	Vue.prototype.$test = { a: 'A' }
    
        console.log('$test', this.$test) // { a: "A" }
        this.$test = 'a'
    
        this.$test.a = 'a'
        
    
    	//writable = false 只读是默认的
        Object.defineProperty(Vue.prototype, '$fn', { value: { a: 'A' }, writable: false }) 
    
        console.log('$fn', this.$fn) // { a: "A" }
        this.$fn = 'a'
    	console.log('$fn修改了', this.$fn) //TypeError: Cannot assign to read only property 
        this.$fn.a = 'a' // 修改对象的属性是可以修改的 不受writable影响
        
    2. 	watch监听对象 https://www.cnblogs.com/yesu/p/9546458.html
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

- 尝试自己实现pc组件封装
- 用vue-admin-template实现几个业务demo





> 需要学习扩展的清单关键字：
>
> react 相关： react.js小书回温，antd-design  smart_npc_rn项目
>
> vue相关：  
>
> Webpack
>
> 数据Mock：mock-server ( vue-element-admin )