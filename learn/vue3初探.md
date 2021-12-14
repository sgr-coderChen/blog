# Vue3初探

> vue3优化了对组件逻辑的重用性
>
> 1. 弥补mixin的缺陷，使同一部分的业务逻辑整合，类似react hook
>
> 2. proxy实现响应式，对比vue2 引用类型的数据则需要用$set



## 1.Ref

### 简单使用

```vue
<template>
  <div class="home">
    {{ count }}
    {{ double }}
    <button @click="increase">👍🏻 + 1</button>
  </div>
</template>


<script lang="ts">
import { ref, computed } from 'vue'

export default {
  name: 'Home',
  setup() {
    const count = ref(0) // 类比react的useState  vue3不需要setState，直接修改count.value即可
    const double = computed(() => count.value * 2)
    const increase = () => {
      count.value ++ 
    }

    return { // 同react hook 返回对应的变量和方法
      count,
      increase,
      double
    }
  }
}
</script>
```



### 类比react hook

```jsx
import React, { useState } from 'react';

// 1.hook写法 函数式组件

const Demo1 = () => {
    /**
     * count: 相当于this.state里面的状态
     * setCount: this.setState => setCount 修改状态
     * useState(10) 设置默认值为10
     */
    const [count, setCount] = useState(10)
    const [page] = useState(0)
 
    return (
        <div>
            hellow hook: { count } - { page }
            <button onClick={() => setCount(count + 1)}>add</button>
        </div>
        
    )
}
export default Demo1


// 2.class类写法

class Demo1 extends Component {  
     render() {
         return (
             <div>
                 hello!
             </div>
         );
     }
}

export default Demo1;
```







## 2.reactive与toRefs

> 对比ref，reactive可以将某个对象统一管理起来，但是引用类型的数据需要借助toRefs来实现响应式，原始类型数据不受影响两种都可以（个人推荐用这个代替ref）

```vue
<template>
    <div>
        {{ count }}
        {{ double }}
        <button @click="increase">👍🏻 + 1</button>
    </div>
</template>

<script>
import { reactive, toRefs, computed } from 'vue'

export default {
    setup() {
        const data = reactive({
            count: 0,
            increase: () => { data.count ++ },
            double: computed(() => data.count * 2)
        })
        return { // 引用类型需要借助toRefs才能得到响应式
            ...toRefs(data) // 这里直接...展开 在template中可以直接写count (而非data.count)
        }
    }
};
</script>

<style scoped lang="less">

</style>

```

### 使用toRefs的原因

```js
const obj = {
    x: 0,
    y: 0
}
function a() {
    obj.x = 1,
    obj.y = 2
}
// a()
const { x } = obj // 解构出来的还是老的值 在vue3的中reactive 丧失了响应式
obj.x = 3
console.log('x', obj.x) // 3
console.log('x2', x) // 0
```



### ref与reactive使用场景推荐

- 如果是基本类型推荐使用ref 写起来比较简洁
- 如果是引用类型（对象，数组等）必须使用reactive，来保证其响应式







## 3.响应式升级

### 伪代码解析原理

```js
// 1. vue2的响应式 只能对现有的count 实现响应（即在vue实例中已经定义的数据才能监听变化）
Object.defineProperty(data, 'count', {
    get() {},
    set() {}
})
// 2. vue3采用proxy实现响应式，这样就不需要在去使用$set
new Proxy(data, {
    get(key) {},
    set(key, value) {}
})
```

[ES6的proxy](https://es6.ruanyifeng.com/#docs/proxy)

### 示例

```vue
<template>
    <div>
        <li v-for="item in numbers" :key="item">
            <h3>{{ item }}</h3>
        </li>
        <div>
             测试对象响应式的添加: {{ person.name }}
        </div>
    </div>
</template>

<script lang="ts">
import { reactive, toRefs } from 'vue'
interface DataProps {
    numbers: number[];
    person: { name?: string }
}
export default {
    setup() {
        const data: DataProps = reactive({
            numbers: [1,2,3,4],
            person: {},
        })
        
        // proxy的响应式 无需再使用 $set
        data.numbers[2] = 666
        data.person.name = '马保国'

        return {
            ...toRefs(data)
        }
    }
};
</script>

<style scoped lang="less">
li{
    list-style: none;
}
</style>

```









## 4.生命周期

> 1. 用法与vue2差不多 命名多了on关键字
> 2. 增加了两个调试钩子函数

[生命周期-官方文档](https://v3.cn.vuejs.org/guide/composition-api-lifecycle-hooks.html)

```vue
<template>
    <div>
        生命周期测试
        {{ count }}
        <button @click="increase()">触发update</button>
    </div>
</template>

<script lang="ts">
import { 
    onMounted, 
    onUpdated, 
    onRenderTracked, 
    reactive, 
    toRefs, 
    onRenderTriggered
} from 'vue'

export default {
    setup() {
        const data = reactive({
            count: 0,
            increase: () => data.count ++
        })
        onMounted(() => {
            console.log('onMounted')
        })

        onUpdated(() => {
            console.log('onUpdated')
        })

        onRenderTriggered((event) => {
            console.log('onRenderTriggered', event)
        })

        onRenderTracked((event) => {
            console.log('onRenderTracked', event)
        })

        return {
            ...toRefs(data)
        }
    }
};
</script>

<style scoped lang="less">

</style>

```









## 5.watch的使用

**注意点**

- watch单个属性监听与多个属性监听的区别
- ref与reactive的混用

### 示例：点击按钮改变标题的值（副作用）

```vue
<template>
    <div>
        {{ count }}
        <button @click="increase">👍🏻 + 1</button>
        <div style="margin:30px">------------- 分割线 -------------</div>
        watch测试
        <div>
            titleValue: {{ titleValue }}
        </div>
        <button @click="updateTtile()">点击watch测试</button>
    </div>
</template>

<script lang="ts">
import { watch, reactive, toRefs, ref } from 'vue'

export default {
    setup() {
        // reactive  取值不需要.value 但是要加上当前的对象（data.count）
        const data = reactive({
            count: 0,
            increase: () => data.count ++
        })
        // ref 取值需要.value才行
        const titleValue = ref('')
        const updateTtile = () => {
            titleValue.value += 'hello'
        }

        // 监听一个
        // watch(titleValue, (newVal, oldVal) => {
        //     document.title = 'updated' + titleValue.value
        // })

        /** 监听多个
         * 1. 第一个参数改成数组格式
         * 2. 如果需要监听reactive中的属性 需要给一个回调函数 () => data.count
        */
        watch([titleValue, () => data.count], (newVal, oldVal) => {
            console.log('newVal', newVal)
            console.log('oldVal', oldVal)
            document.title = 'updated' + titleValue.value + data.count
        })
        
        return {// 与reactive混用 
            // reactive
            ...toRefs(data),
            // ref
            titleValue,
            updateTtile
        }
    }
};
</script>

<style scoped lang="less">

</style>

```



### 类比react的useEffect

```jsx
import React, { useState, useEffect } from 'react';

export default function Example() {
  const [count, setCount] = useState(0);

  
  useEffect(() => {
    // Update the document title using the browser API
    document.title = `You clicked ${count} times`;
  });
  

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```



### 副作用

- 一般的副作用就是说这个函数做了不属于它应该做的事，比如console.log，或者它修改了其他的外部变量之类的。

- react中的函数组件要求必须是纯函数也就是没有副作用的函数，就是因为这个函数会被很多次调用，如果有副作用比如修改了全局变量（如：修改了document.title），那么将无法控制该变量的准确性

- vue里面一般就是说，一些当状态改变时需要执行的行为，比如在每次msg变量改变时我需要重新打印它，那么这个打印行为就可以说是一个副作用(effect)





## 6.自定义hook

> 1. vue2中使用的是mixin解决组件逻辑复用，
>
> 2. react通过高阶组件或者是renderProps来解决，
>
> 3. vue的自定义hook与react 自定义hook非常相似



### 使用场景

```js
// src/hooks/useMousePosition.ts  注意命名：规范开头要用 use_
import { onMounted, onUnmounted, ref } from 'vue'

function useMousePosition(initX = 0, initY = 0) {
    const x = ref(initX)
    const y = ref(initY)

    const updateMouse = (e: MouseEvent) => {
        x.value = e.pageX
        y.value = e.pageY
    }

    onMounted(() => {
        document.addEventListener('click', updateMouse)
    })

    onUnmounted(() => {
        document.removeEventListener('click', updateMouse)  
    })

    return {
        x,
        y,
    }
}

export default useMousePosition
```

优化后的业务组件

```vue
<!-- vue自定义hook测试 -->
<template>
    <div>
        <h3 style="margin-bottom: 30px">compositionApi模块化测试</h3>
        <h4 style="margin-bottom: 30px">案例: 点击屏幕记录鼠标坐标位置</h4>
        当前鼠标坐标的位置：{{ x }}，{{ y }}
    </div>
</template>

<script lang="ts">
// import { onMounted, onUnmounted, ref } from 'vue'
import useMousePosition from '../hooks/useMousePosition'

export default {
    setup() {
        const { x, y } = useMousePosition()
        return { // 这里记得要返回出去
            x,
            y,
        }
    }
};
</script>

<style scoped lang="less">

</style>

```



### 类比react 自定义hook

```jsx
// 自定义 hook 抽离input属性和change事件
// 个人理解 这里的useInputValue自定义hook其实就是对应的 v-model语法糖 当然不局限与 v-model语法糖 还有其他自定义hook 
// 自定义hook库 https://github.com/rehooks/awesome-react-hooks

import React, { useState } from 'react'
// 自定义hook
const useInputValue = (initialValue) => { 
    const [value, setValue] = useState(initialValue);

    return {
        value,
        onChange: e => setValue(e.target.value),
    }

}

// 业务组件
const TodoForm = () => {
    const text = useInputValue('') // 使用hook
    const password = useInputValue('') // 使用hook

    return (
        <form>
            <input type="text" { ...text } />
            <input type="password" { ...password } />
        </form>
    )

}

export default TodoForm

```





