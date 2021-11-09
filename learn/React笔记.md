# React 学习笔记

**[参考书籍-react小书](https://hyf.js.org/react-naive-book/lesson28)**

### 受控组件

```jsx
import React, { Component } from 'react'

export default class CommonInput extends Component {
    

    //  react 受控组件 相当于 vue中的 v-model的底层原理
    handleChange = (e) => {
        const inputValue = e?.target?.value
        const { onChange } = this.props
        if (onChange && typeof onChange === 'function') {
            onChange(inputValue)
        }
    }

    render() {
        const { value } = this.props
        return (
            <div>
                <input 
                    ref={() => this.inputRef}
                    type="text" 
                    value={value}
                    onChange={this.handleChange}
                />
            </div>
        )
    }
}

```



### 事件绑定

```jsx
constructor(props) {
    super(props)
    // 事件绑定 方式4
    // this.test = this.test.bind(this) //这里全局统一绑定也可以单独在jsx中绑定
    this.state = {
        text: 0
    }
}

//	事件绑定 方式5 （推荐）使用箭头函数,  如果要传参 可以配合方式1 + 方式5
test = (title, e) => {
    
}

render() {
    let title = 5
    return (
        // 事件绑定 方式1 箭头函数的this指向外部环境render方法 而 render方法所在的this为组件实例
        <div onClick={(e) => this.test(e)}>
            123
        </div>

        // 事件绑定 方式2
        // <div onClick={this.test}>
        //     123
        // </div>

        // 事件绑定 方式3 单独bind绑定
        // <div onClick={this.test.bind(this)}>
        //     123
        // </div>
    )
}
```



### setState方法

- 是异步更新的（如果设计成同步会造成性能损失，使用异步可以通过异步队列来优化渲染性能）
- 调用的本质为更新state ,重新render 渲染UI
- 三种写法

```jsx
test = (title, e) => {
    // this.setState({ text: this.state.text + 1 })
    // console.log('text', this.state.text) // 0
    // this.setState({ text: this.state.text + 1 }) // 0 + 1 第二次还是1 因为this.state.text一直为0
    // console.log('text', this.state.text) // 0

    // 推荐语法 这种执行 text 会变成 2 因为这种写法里面的state为上次调用的结果
    this.setState((state) => { 
        return {
            text: state.text + 1 
        }
    })
    this.setState((state) => { 
        console.log('text 第二次调用', state.text) // 1
        return {
            text: state.text + 1 
        }
    })

}
```



### Render-Props模式

> 原理: 其实就是父组件来提供render方法  子组件通过render方法回调执行传递state（回调函数子传父）（但是子组件必须要有返回的内容）

**案例：在屏幕实时获取鼠标的坐标位置** 

```jsx
import React, { Component } from 'react'

// 子组件
class Mouse extends Component {
    state = {
        x: 0,
        y: 0
    }

    handleMousemove = (e) => {
        this.setState({
            x: e.clientX,
            y: e.clientY
        })
    }

    componentDidMount() {
        window.addEventListener('mousemove', this.handleMousemove)
    }

    componentWillUnmount() {
        window.removeEventListener('mousemove', this.handleMousemove)
    }

    render() {
        return this.props.render(this.state) // 使用render实现 
        // return this.props.children(this.state) // 使用children实现 
    }

}



// 父组件
/**
 * Render-Props模式 （提供state methods 或者声明周期）
    其实就是父组件来提供render方法  子组件通过render方法回调执行传递state（回调函数子传父）（但是子组件必须要有返回的内容）
**/
export default class RenderProps extends Component {
    render() {
        return (
            <div>
                <h1>render-props模式</h1>
                <Mouse 
                    render={ // 使用render实现 可以换成其他的任意函数（推荐chilren）
                        ({x, y}) => {
                            // 这里就可以使用其他组件 渲染不同的UI
                            return (
                                <p>鼠标的位置：{x}:{y}</p>
                            )
                            // renturn <ComponentA />
                            // renturn <ComponentB />
                        }
                    } 
                />
                <Mouse>
                    { // 使用chilren实现  更加优雅
                        ({x, y}) => {
                            // 这里就可以使用其他组件 渲染不同的UI
                            return (
                                <p>鼠标的位置：{x}:{y}</p>
                            )
                        }
                    }
                </Mouse>
            </div>
        )
    }
}

```



### [高阶组件（HOC）](https://hyf.js.org/react-naive-book/lesson28)

- 高阶组件就是一个函数，传给它一个组件，它返回一个新的组件*。新的组件使用传入的组件作为子组件。

- 高阶组件的作用是用于代码复用*，可以把组件之间可复用的代码、逻辑抽离到高阶组件当中。*新的组件和传入的组件通过 `props` 传递信息。
- 参考Vue2是通过mixin混入模式来实现的

```jsx
//  高阶组件 HOC写法  with_ 为命名规范
export function withMouse(WrappedComponent) {
    class Mouse extends Component {
        state = {
            x: 0,
            y: 0
        }
    
        handleMousemove = (e) => {
            this.setState({
                x: e.clientX,
                y: e.clientY
            })
        }
    
        componentDidMount() {
            window.addEventListener('mousemove', this.handleMousemove)
        }
    
        componentWillUnmount() {
            window.removeEventListener('mousemove', this.handleMousemove)
        }
    
        render() {
            return <WrappedComponent { ...this.state } { ...this.props } />
        }
    
    }
    return Mouse
    
}

//  Position假设为UI业务组件
const Position = ({x, y}) => (
    <p>鼠标的位置：{x}:{y}</p>
)

export const MousePosition = withMouse(Position) // 测试高阶组件
```



### react-router-dom路由

```jsx
// 组件式
<Link
  to={{
    pathname: "/courses",
    search: "?sort=name",
    hash: "#the-hash",
    state: { fromDashboard: true }
  }}
/>

// 函数式导航
this.props.history.push({
    pathname: '/BankGoodsDetail',
    state: { // 这里是隐形传递的 不会在浏览器url显示  这里刷新浏览器 不会丢失！
        itemId: 'scscd',
        type: 3,
    },
});
// 函数式导航 读取
const { itemId, type } = this.props.history.location.state;
```







### 使用node express实现一个简单的mock数据

1. yarn add express
2. 新建mock/index.js  router.js
3. Mock文件夹下命令行运行 node index.js
4. 浏览器输入 http://localhost:3100/api/list 验证接口
5. 配置跨域问题（下面有写），请求数据 ，即使在同一环境下  但是端口不同 还是会出现跨域的问题

```js
// index.js
const express = require("express")
const app = express()
const router = require('./router')

app.use('/', router)

app.listen(3100, function() {
    console.log('serve running 3100')
})
```

```js
// router.js
const express = require('express')
const router = express.Router()

router.get('/api/list', (req, res) => {
    res.send([
        {
            name: 'chenhui',
            age: 27
        },
        {
            name: 'zhangsan',
            age: 22
        }
    ])
})

module.exports = router
```



### create-react-app 解决跨域问题

[配置步骤-create-react-app官方文档](https://github.com/facebook/create-react-app/blob/main/docusaurus/docs/proxying-api-requests-in-development.md)

第一种 通过package.json 配置proxy

```js
// package.json 
// 
{
    "proxy": "http://localhost:3100" // 你的接口前缀 如 https://cnxx.mmstat.com
}

//	获取数据 proxy 默认带了上面的地址
fetch('/api/list')
    .then(res => res.json())
    .then(data => {
        console.log('data', data)
    }).catch(err => {
        console.log(err)
    })
```

第二种 通过http-proxy-middleware

```jsx
// 新建src/setupProxy.js 
const { createProxyMiddleware } = require('http-proxy-middleware');

module.exports = function(app) {
  app.use(
    '/api',
    createProxyMiddleware({
      target: 'http://localhost:5000', // 你服务端的地址
      changeOrigin: true,
    })
  );
};

// 使用fetch获取
fetch('/api/list')
    .then(res => res.json())
    .then(data => {
        console.log('data', data)
    }).catch(err => {
        console.log(err)
    })
```





