# vue组件发布到NPM



[参考文章](https://juejin.cn/post/6844903953180835847#heading-0)

[示例项目-vue-h5-drawing-board](https://github.com/sgr-coderChen/vue-h5-drawing-board)



## 注意点

### vue.config.js

```js
module.exports = {
    css: {
        extract: false,  // 将组件里的css打入到umd.js中,不需要额外在安装插件后引入css (注意：公共style文件夹的样式需要在入口文件单独引入 src/index.js)
    },
    productionSourceMap: false, // 生产环境 去除 map文件
    configureWebpack: (config) => {
        if (process.env.NODE_ENV === 'production') {
            config.externals = {
                'vue': 'Vue',
            }
        }
    }
}
```

### src/index.js

可以在src项目创建一个打包的入口文件

```js
import DrawingBoard from './packages/index.vue';
import './assets/style/index.css' // 公共style 需要在入口文件额外引入

const components = [ DrawingBoard ]

const install = (Vue) => {
    if (install.installed) return   // 判断是否安装注册过
    components.forEach(component => Vue.component(component.name, component)) // 遍历并注册组件
}

if (typeof window !== 'undefined' && window.Vue) {
    install(window.Vue) // window中有Vue时去install()
}

DrawingBoard.install = install // 挂载到DrawingBoard上 这样可以通过use注册 也可以通过引用注册

export default DrawingBoard

```

使用

```js
import Vue from 'vue'
import DrawingBoard from 'vue-drawing-board-ch'

Vue.use(DrawingBoard)

// or 组件内使用
import DrawingBoard from 'vue-drawing-board-ch'

export default {
  components: { DrawingBoard }
}

<DrawingBoard />
```



### 组件样式问题

- 如果npm模块中的组件不加scoped 则会全局生效（即别人安装了这个依赖，里面的组件样式会污染父项目）
- npm模块在打包的入口文件引入全局样式以后，这样就不需要安装npm的时候额外引入css了，已经在npm内部处理了（src/index.js 中引入css）
- 如需修改依赖包的样式（加了scoped），用样式穿透即可



### 打包优化（vue依赖问题未解决！！！）

- 配置productionSourceMap : false   生产环境 去除 map文件
- **打包是排除vue等依赖 ( 未实现，实测安装以后，npm内部还是安装了node_modules/vue的依赖)**

```js
module.exports = {
    css: {
        extract: false,  // 将组件里的css打入到umd.js中,不需要额外在安装插件后引入css (注意：公共style文件夹的样式需要在入口文件单独引入 src/index.js)
    },
    productionSourceMap: false, // 生产环境 去除 map文件
    configureWebpack: (config) => { // 这里的优化未实现
        if (process.env.NODE_ENV === 'production') {
            config.externals = {
                'vue': 'Vue',
            }
        }
    }
}
```

[![TRo46s.jpg](https://s4.ax1x.com/2021/12/30/TRo46s.jpg)](https://imgtu.com/i/TRo46s)



### 坑记录

- vue-h5-drawing-board这个项目当时因为阿里字体库损坏，后来重新下载了一份覆盖就解决了
- npm publish 先要切换到默认源https://registry.npmjs.org/
- npm包名重复会发布失败
- 每次发布需要修改package.json的版本号

