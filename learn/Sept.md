## Vue 渲染函数，JSX的使用

[参考文章](https://juejin.cn/post/6844903903201329166)

### 1.使用template实现表格

```vue
 <template>
    <div>
        <layout-box>
            <el-table :data="tableData" style="width: 100%">
                <template v-for="(item, index) in showColumns" >
                    <el-table-column 
                        v-if="['name'].includes(item.prop)"
                        :key="index"
                        v-bind="item"
                    >
                        <template slot-scope="scope">
                            <span>{{ formate(scope.row.name) }}</span>
                        </template>
                    </el-table-column>
                    <el-table-column
                        v-else 
                        :key="index"
                        v-bind="item"
                    >
                    </el-table-column>
                </template>
            </el-table>
        </layout-box>
    </div>
</template>
<script>
export default {
    data() {
        return {
            columns: [
                { 
                    prop: 'date',
                    label: '日期',
                    width: '180',
                },
                { 
                    prop: 'name',
                    label: '姓名',
                    width: '180',
                },
                { 
                    prop: 'address',
                    label: '地址',
                },
            ],
            hideColumns: ['name', 'address'],
            tableData: [
                {
                    date: "2016-05-02",
                    name: "王小虎",
                    address: "上海市普陀区金沙江路 1518 弄",
                },
                {
                    date: "2016-05-04",
                    name: "王小虎",
                    address: "上海市普陀区金沙江路 1517 弄",
                },
            ],
        };
    },
    computed: {
        //	过滤hideColumns的选项
        showColumns() {
            return this.columns.filter(column => !this.hideColumns.find(prop => prop === column.prop))
        }
    },
    methods: {
        //	示例：特殊处理单独的列数据 
        formate(val) {
            return val + '156161566'
        }
    }
};
</script>
```

### 2.使用JSX实现

```jsx
<script>
export default {
    data() {
        return {
            list: [
                'wefwe', 'wefwef'
            ],
            columns: [
                { 
                    prop: 'date',
                    label: '日期',
                    width: '180',
                },
                { 
                    prop: 'name',
                    label: '姓名',
                    width: '180',
                },
                { 
                    prop: 'address',
                    label: '地址',
                },
            ],
            hideColumns: ['name', 'address'],
            tableData: [
                {
                    date: "2016-05-02",
                    name: "王小虎",
                    address: "上海市普陀区金沙江路 1518 弄",
                },
                {
                    date: "2016-05-04",
                    name: "王小虎",
                    address: "上海市普陀区金沙江路 1517 弄",
                },
            ],
        };
    },
    computed: {
        showColumns() {
            return this.columns.filter(column => !this.hideColumns.find(prop => prop === column.prop))
        }
    },
    methods: {
        formate(val) {
            return val + '156161566'
        }
    },
    render() {
        //	数据对象 参考 https://cn.vuejs.org/v2/guide/render-function.html#%E6%B7%B1%E5%85%A5%E6%95%B0%E6%8D%AE%E5%AF%B9%E8%B1%A1
        const tableAttrs = {
            props: {
                data: this.tableData
            }
            on: {},
            style: {},
        }
        return (
            <el-table {...tableAttrs} style="width: 100%">
                {this.showColumns.map((item, index) => {
                    return (
                        <el-table-column 
                            key={index}
                            props={item}
                        >
                        </el-table-column>
                    )
                })}
            </el-table>
        )
    }
};
</script>
```

### 3.columns中使用scopedSlots并传入

```jsx
// data columns数据中
{ 
    prop: 'address',
    label: '地址',
    scopedSlots: {
         default: scope => {
                return (
                    <div>测试scopedSlots插槽</div>
                    
                    // 使用自定义组件来插入
                    // <HighlightText 
                    //     props={{
                    //         text: scope.row.address,
                    //         highlightText: '普陀'
                    //     }}
                    // />
                )
          },
     },
},
    
    
    
    
// 渲染
render() {
        const tableAttrs = {
            props: {
                data: this.tableData
            }
        }
        return (
            <el-table {...tableAttrs} style="width: 100%">
                {this.showColumns.map((item, index) => {
                    //	这里的columnProps使用了解构 内容为 除了scopedSlots属性 其他属性的集合对象
                    //  const { a, ...y } = { a: 1, b: 2, c: 3 } 输出: a = 1, y ={b: 2, c: 3}
                    const { scopedSlots, ...columnProps } = item
                    return (
                        <el-table-column 
                            key={index}
                            props={columnProps}
                            scopedSlots={scopedSlots}// <= 传入自定义内容 还是看官方文档的深入数据对象
                        >

                        </el-table-column>
                    )
                })}
            </el-table>
        )
        
    }
```





## Vue $attrs和$listeners使用

[参考文章](https://blog.csdn.net/happy81997/article/details/107999711)

### 子组件

```vue
<template>
    <el-dialog :visible.sync="dialogVisible1" v-bind="$attrs" v-on="$listeners">
        <span>这是一段信息</span>
        <span slot="footer" class="dialog-footer">
            <el-button @click="dialogVisible1 = false">取 消</el-button>
            <el-button type="primary" @click="dialogVisible1 = false">
                确 定
            </el-button>
        </span>
    </el-dialog>
</template>

<script>
export default {
    inheritAttrs: false,
    props: {
        dialogVisible: {
            type: Boolean,
            default: false
        },
    },
    data() {
        return {
            dialogVisible1: false
        };
    },
    watch: {
        dialogVisible() {
            this.dialogVisible1 = this.dialogVisible
        }
    },
    mounted() {
        console.log('$attrs', this.$attrs)
        console.log('$listeners', this.$listeners)
        this.dialogVisible1 = this.dialogVisible
    },
    methods: {
        show() {
            this.dialogVisible1 = true
        }
    },
};
</script>

<style scoped lang="scss">
</style>

```

### 调用

```vue
<DialogAttrsDemo 
  	:dialogVisible="dialogVisible" title="标题" :a="123" 
  	@aaa="test"
/>
```

```js
//	输出
console.log('$attrs', this.$attrs)  // $attrs {title: '标题', a: 123} 在$attrs里面只会有子组件props没有注册的属性
console.log('$listeners', this.$listeners) // $listeners {aaa: ƒ} 会输出所有的订阅事件
```

### inheritAttrs的使用

```html
// 如果设置inheritAttrs: true, (默认为true) 传入的属性会在渲染在子组件的根元素上 如下：
<div class="el-dialog__wrapper" a="123" title="标题"></div>

// 设置为false则不会渲染上去
```

### 总结

- 在$attrs里面只会有子组件props没有注册的属性，已经在子组件中注册的prop不会再$attrs中出现
- inheritAttrs写在子组件中， 如果不设置为false 会将传入的属性插入到子组件上层的 div标签属性中
- 最底层的子组件一定要使用props接收从父组件传递过来的数据











