### vue.config.js

```javascript
const path = require('path');
function resolve (dir) {//自己定义的函数
    return path.join(__dirname, dir)
}
module.exports = {
    lintOnSave: false,//关闭eslint 保存时检测
    configureWebpack: {
        resolve: {
            extensions: ['.js', '.vue', '.json'],
            alias: {
                '@': resolve('src'),//写法1 @代表src vue-cli中已经默认 无需配置 直接在项目使用如（@/components/HelloWorld.vue）代表src/components/HelloWorld.vue
                // '@': path.join(__dirname, 'src'),//写法2
                '@components': path.join(__dirname, 'src/components'),//写法3
                '@config': path.join(__dirname, 'config')//写法4
                '@img': path.join(__dirname, 'src/assets/img')// 写法5 配置图片的默认路径 <img src="@img/1.png" alt="">
            }
        },
    }
}
```

### 具体使用场景1About.vue

```vue
<template>
    <div class="">
    	123
        <SubmitBtn />
        <!-- <img :src="imgUrl" alt="" srcset=""> -->
        <img src="@config/ciricle.png" alt="">
    </div>
</template>

<script>
import SubmitBtn from '@/components/SubmitBtn'
// import SubmitBtn from '@components/SubmitBtn'

export default {
    data() {
        return {
            imgUrl: require('@/assets/logo.png')
        }
    },
    components: {
    	SubmitBtn
    },
    mounted() {

    },
    methods: {

    }
}
</script>
```
[参考链接](https://blog.csdn.net/xiazeqiang2018/article/details/81325996?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param)
[![项目结构路径.png](https://s1.ax1x.com/2020/11/11/BjNj1A.png)](https://imgchr.com/i/BjNj1A)



### 引入公共css


```less
<style scoped lang="less">
@import '~@/assets/style/less/demo.less';  // 这里要加 ~  
.peopleCongressMeeting {
    .content-table{
        padding: 15px;
        background-color: #fff;
        .pagination-box{
            width: 100%;
            display: inline-block;
            text-align: right;
            margin-top: 15px;
        }
    }
}
</style>

```
