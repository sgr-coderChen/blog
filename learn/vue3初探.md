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

[转载-ref与reactive](https://juejin.cn/post/7029559671648518151)

1. ref和reactive都可以做响应式

2. ref:一般用在定义基本类型和引用类型，如果是引用类型底层会借助reactive形成proxy代理对象,可以直接复制整个对象，如table的数据请求回来，需要将数据整体赋值个响应对象这时如果使用的是reactive就无法进行响应。

3. reactive：一般用在引用类型，如{}等,不能一次性修改整个对象，如我们后端请求table的数据数据，如果想一次性赋值的整个数组的话，就行不通，此时建议使用ref来定义数组。

- 如果是基本类型推荐使用ref 写起来比较简洁
- 如果是引用类型（对象，数组等）推荐使用reactive，来保证其响应式
- 如果**ref**使用的是**引用类型**，底层ref会借助reactive的proxy 定义响应式
- **reactive** 无法定义基本类型的proxy,且设置值无法响应数据（需要**reactive({ key: value })**这样用，不能直接赋值基本类型）

### 除了对象都用ref来定义

```js
let switchKG = ref(false)
console.log(switchKG.value)

let arr = ref([])
arr.value = [1, 2, 3, 4, 5]
console.log(arr.value)

let Obj = reactive({
  arr: []
})
reactive.arr = [1, 2, 3, 4, 5]
```

### 都用reactive来定义，然后用toRefs进行导出到页面使用

```vue
<template>
  <div>{{arr}}</div>
  <div>{{obj}}</div>
  <div>{{swithKW}}</div>
</template>
<script setup>
import {reactive, toRefs} from "vue";

let state = reactive({
  swithKW:false,
  arr: [],
  obj: {}
})
console.log(state.arr)
console.log(state.obj)
//导出到页面使用
const {swithKW, arr, obj } = toRefs(state)
</script>
```





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

- react中的函数组件要求必须是纯函数也就是没有副作用的函数，就是因为这个函数会被很多次调用，如果有副作用比如修改了**全局变量**（如：修改了document.title），那么将无法控制该变量的准确性

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



## 7.teleport(传送)

> teleport可以指定组件的渲染节点，例如子组件中套有弹框组件 通过teleport包裹可以挂载到最外层的dom结构中

示例代码：modal弹框应用

1.新建顶层挂载节点

```html
<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
        
    <div id="modal"></div>  <!-- 这里要加一个挂载的id 对应 teleport的to路径 -->
        
        
  </body>
</html>

```



2.子组件 src/compontents/Modal.vue

```vue
<template>
    <teleport to='#modal'>
        <div v-if="isOpen" class="modal-container">
            <slot>
                <h1>我是弹框</h1>
            </slot>
            <button @click="handleClick">关闭</button>
        </div>
    </teleport>
</template>

<script lang="ts">
import { defineComponent } from 'vue'  // 通过defineComponent包裹 实现ts提示支持

export default defineComponent({
    props: {
        isOpen: {
            type: Boolean,
            default: false
        }
    },
    emits: {// 类似props的校验 https://v3.cn.vuejs.org/guide/migration/emits-option.html#%E6%A6%82%E8%BF%B0
        'close': null
    },
    // 通过setup整合组件的状态和方法，vue2中则需要写data，methods分块书写
    setup(props, context) { // context相当于this（api有 slot，emit，attrs，expose）
        const handleClick = () => {
            context.emit('close')
        }
        return {
            handleClick
        }
    }
})

</script>

<style scoped lang="less">

.modal-container{
    position: fixed;
    width: 150px;
    height: 150px;
    border: 2px solid #000;
    background-color: #fff;
    left: 50%;
    top: 50%;
    margin-left: -75px;
    margin-top: -75px;
}

</style>

```

3.父组件中使用

```vue
<template>
    <div>
        <button @click="openModal">打开弹框</button>
        <Modal :isOpen="isOpen" @close="closeModal" />
    </div>
</template>

<script lang="ts">
import Modal from '../components/Modal.vue'
import { ref } from 'vue'

export default {
    components: { Modal },
    setup() {
        const isOpen = ref(false)

        const closeModal = () => {
            isOpen.value = false
        }

        const openModal = () => {
            isOpen.value = true
        }

        return {
            isOpen,
            closeModal,
            openModal
        }
    }
};
</script>
```



## 8.suspense

> 异步展示组件，类似vue-promise库只关心数据状态

使用suspense组件必须在setup返回一个Promise，推荐使用async，会自动向外包裹Promise对象

```vue
<template>
    <div>
        <img :src="result && result.message" alt="">
        <!-- {{ result }} -->
    </div>
</template>

<script>
import { defineComponent } from 'vue'
import axios from 'axios'

export default defineComponent({
    // setup() {
    //     return new Promise((resolve) => {
    //         setTimeout(() => {
    //             resolve({
    //                 result: 11
    //             })
    //         }, 3000);
    //     })
    // }
    async setup() { // async最后会默认包裹返回promise对象
        const dog = await axios.get('https://dog.ceo/api/breeds/image/random')
        return {
            result: dog.data
        }
    }
})

</script>
```

父组件使用

- 如果有多名插槽 ，#default需要显式的写出来
- suspense会等待#default里面所有的异步请求返回后才显示

```vue
<template>
    <div>
        <suspense>
            <template #default>
                <AsyncDog />
                <AsyncDog2 />
            </template>
            <template #fallback>
                <div>loading...</div>
            </template>
        </suspense>
    </div>
</template>
```



## 9.封装Dropdown UI组件

> 组件模块化+ts+hook+双向绑定

- 引入boostrap样式库

```html
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css" integrity="sha384-HSMxcRTRxnN+Bdg0JdbxYKrThecOKuH5zCYotlSAcp1+c8xmyTe9GYg1l9a69psu" crossorigin="anonymous">
```

- 新建Dropdown.vue

```vue
<template>
    <div class="dropdown" ref="dropdownRef">
        <button
            @click="toggleOpen()"
            class="btn btn-default dropdown-toggle"
            type="button"
            id="dropdownMenu1"
            data-toggle="dropdown"
        >
            Dropdown
            <span class="caret"></span>
        </button>
        <ul :style="{ display: isOpen ? 'block' : 'none' }" class="dropdown-menu">
            <slot></slot>
        </ul>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from 'vue'
import useClickOutside from '../hooks/useClickOutside'

export default defineComponent({
    name: 'Dropdown',
    props: {
        value: {
            type: Boolean
        }
    },
    emits: ['input'], // 定义与校验事件，同props
    setup(props, context) {
        // console.log('props' ,props)
        const isOpen = ref(props.value)
        const dropdownRef = ref<null | HTMLElement>(null)
        const isClickOutside = useClickOutside(dropdownRef)

        watch(isClickOutside, (newVal) => {
            if (isOpen.value && newVal) {
                isOpen.value = false
            }
        })

        const toggleOpen = () => {
            isOpen.value = !isOpen.value
            context.emit('input', isOpen.value) // 在props定义value 并触发input事件 实现v-model
        }

        return {
            isOpen,
            dropdownRef,
            toggleOpen
        }
    }
})
</script>

```

- 新建DropdownItem.vue

```vue
<template>
    <li 
        class="dropdown-option"
        :class="{ 'is-disabled': disabled }"
    >
        <slot></slot>
    </li>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
    name: 'DropdownItem',
    props: {
        disabled: {
            type: Boolean,
            default: false
        }
    }
})
</script>


<style lang="less">
.dropdown-option.is-disabled *{
    background-color: transparent;
    pointer-events: none;
    color: #6c757d;
}
</style>
```

- 自定义hook，抽离dropdown组件的逻辑为 useClickOutside.ts文件

```ts
//  点击本身及子元素不关闭dropdown，否则关闭。 contains https://developer.mozilla.org/zh-CN/docs/Web/API/Node/contains
import { ref, Ref, onMounted, onUnmounted } from 'vue'

type ElementRef = Ref<null | HTMLElement>

const useClickOutside = (elementRef: ElementRef) => {

    const isClickOutside = ref(false)

    const handleClick = (e: MouseEvent) => {
        // console.log('e', e.target)
        if (elementRef.value) {
            if (elementRef.value.contains(e.target as HTMLElement)) {// 如果包含或者为本身 则点击了内部
                isClickOutside.value = false
            } else {// 点击在元素之外 => 关闭dropdown
                isClickOutside.value = true
            }
        }
    }

    onMounted(() => {
        document.addEventListener('click', handleClick)
        // console.log('elementRef', elementRef)
    })

    onUnmounted(() => {
        document.removeEventListener('click', handleClick)
    })

    return isClickOutside
}

export default useClickOutside
```

- 使用Dropdown组件

```vue
<template>
    <div class="dropdown-demo">
        <dropdown v-model="isOpen">
            <dropdown-item>
                <a href="#">用户详情</a>
            </dropdown-item>
            <dropdown-item>
                <a href="#">查询余额</a>
            </dropdown-item>
            <dropdown-item :disabled="true">
                <a href="#">退出登录</a>
            </dropdown-item>
        </dropdown>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import Dropdown from '../components/Dropdown.vue'
import DropdownItem from '../components/DropdownItem.vue'

export default defineComponent({
    components: { Dropdown, DropdownItem },
    setup() {
        const isOpen = ref(false)

        return {
            isOpen,
        }
    }
})
</script>

```



## 10.使用react hook实现Dropdown

- Dropdown.tsx 父组件

```tsx
import { useState, useRef, useEffect } from 'react'
import useClickOutside from '../../hooks/useClickOutside'

const Dropdown = (props: any) => {

    let [isOpen, setOpen] = useState(false)
    const dropdownRef = useRef<HTMLDivElement | null>(null);

    let isClickOutside = useClickOutside(dropdownRef)


    const toggleOpen = (e: any) => {
        setOpen(isOpen = !isOpen)
    }

    useEffect(() => {
        // console.log('isOpen改变了', isOpen)
        props.onChange?.(isOpen)
    }, [isOpen])

    useEffect(() => {
        // console.log('isClickOutside改变了', isClickOutside)
        if (isOpen && isClickOutside) {
            setOpen(isOpen = false)
        }
    }, [isClickOutside])

    return (
        <div
            className="dropdown" 
            ref={dropdownRef} 
            style={{width: '101px'}}
        >
            <button
                className="btn btn-default dropdown-toggle"
                type="button"
                id="dropdownMenu1"
                data-toggle="dropdown"
                onClick={(e) => toggleOpen(e)}
            >
                Dropdown
                <span className="caret"></span>
            </button>
            <ul style={{ display: isOpen ? 'block' : 'none' }} className="dropdown-menu">
                { props.children }
            </ul>
        </div>
    )
}

export default Dropdown
```

- DropdownItem.tsx子组件

```tsx
import React from 'react'
// import './index.less'
import styles from './index.less'

const DropdownItem = (props: any) => {
    return (
        <li 
            className={`dropdown-option ${props.disabled ? styles['is-disabled']  : ''}`}
        >
            { props.children }
        </li>
    )
}

export default DropdownItem
```

- useClickOutside.ts

```ts
import { useState, useEffect } from 'react'

const useClickOutside = (elementRef: any) => {

    let [isClickOutside, setClickOutside] = useState(false);

    const handleClick = (e: MouseEvent) => {
        if (elementRef) {
            if ((elementRef.current).contains(e.target)) {
                setClickOutside(isClickOutside = false)
            } else {
                setClickOutside(isClickOutside = true)
            }
        }
    
    }

    useEffect(() => {
        document.addEventListener('click', handleClick)
        return () => {
            // cleanup
            document.removeEventListener('click', handleClick)
        }
    }, [])
    
    return isClickOutside

}

export default useClickOutside
```

- useModelBind.ts （模拟vue的v-model双向绑定）

```ts
import { useState } from 'react'

const useModelBind = (initialValue?: any) => {
    let [value, setValue] = useState(initialValue);

    const onChange = (_value: any) => {
        setValue(value = _value)
    }

    return {
        value,
        onChange,
    }
}

export default useModelBind
```

- 使用Dropdown组件

```tsx
import { useEffect } from 'react'
import Dropdown from '../components/Dropdown/index'
import DropdownItem from '../components/Dropdown/DropdownItem'
import useModelBind from '../hooks/useModelBind'

const DropdownDemo = () => {
    // 不抽离hook（双向绑定）
    // let [isOpen, setIsOpen] = useState(false)
    // const handleOnChange = (_isOpen: boolean) => {
    //     console.log('子传过来的', _isOpen)
    //     setIsOpen(isOpen = _isOpen)
    //     console.log('父数据新', isOpen)
    // }
    
 	// 优化：抽离hook（双向绑定）
    const dropdownState = useModelBind(false)
    
    useEffect(() => {// 增加[dropdownState.value] 相当于vue的 watch
        console.log('变化了', dropdownState.value)
    }, [dropdownState.value])

    return (
        <div className="dropdown-demo">
            <Dropdown { ...dropdownState }>
                <DropdownItem>
                    <a href="javascript:void(0);">用户详情</a>
                </DropdownItem>
                <DropdownItem>
                    <a href="javascript:void(0);">查询余额</a>
                </DropdownItem>
                <DropdownItem disabled={true}>
                    <a href="javascript:void(0);">退出登录</a>
                </DropdownItem>
            </Dropdown>
        </div>
    )
}

export default DropdownDemo
```

