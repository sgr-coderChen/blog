# React 学习笔记

**[参考书籍-react小书](https://hyf.js.org/react-naive-book/lesson28)**

## 受控组件

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



## 事件绑定

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



## setState方法

- 是异步更新的（如果设计成同步会造成性能损失，使用异步可以通过异步队列来优化渲染性能）
- 调用的本质为更新state ,重新render 渲染UI
- 三种写法

```jsx
test = (title, e) => {
    // this.setState({ text: this.state.text + 1 }) //（第1种）
    // console.log('text', this.state.text) // 0
    // this.setState({ text: this.state.text + 1 }) // 0 + 1 第二次输出还是0 因为this.state.text一直为0
    // console.log('text', this.state.text) // 0

    // 推荐语法 （第2种） 这种执行 text 会变成 2 因为这种写法里面的state为上次调用的结果
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
	// 回调函数获取最新数据（第3种，个人推荐这个）
    this.setState({ text: this.state.text + 1 }, () => {
        console.log('回调函数里也是最新的', this.state.text) // 1
    })
}
```



## Render-Props模式

> 原理:  （为了复用逻辑，与高阶组件初衷一致） 其实就是父组件来提供render方法  子组件通过render方法回调执行传递state（回调函数子传父）（但是子组件必须要有返回的内容） === > 这里与vue的slot 作用域插槽相类似

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
                    { // 使用chilren实现  更加优雅  与vue的slot 作用域插槽相类似
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



## [高阶组件（HOC）](https://hyf.js.org/react-naive-book/lesson28)

- 高阶组件就是一个函数，传给它一个组件，它返回一个新的组件。新的组件使用传入的组件作为子组件。

- 高阶组件的作用是用于代码复用，可以把组件之间可复用的代码、逻辑抽离到高阶组件当中。新的组件和传入的组件通过 `props` 传递信息。
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



## react-router-dom路由

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


// withRouter用于那些没有直接被路由管理的组件来使用 withRouter为高阶组件 包裹后传递route相关api给组件
import { withRouter } from 'react-router-dom'
class YourComponents extends Component {
    componentDidMount() {
        console.log(this.props.history) // 包裹了高阶组件所以能访问到 router
    }

    render() {
        return (
            <div>
                Profile
            </div>
        )
    }
}

export default withRouter(YourComponents)

```





## 使用node express实现一个简单的mock数据

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



## create-react-app 解决跨域问题

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



## redux 与 react-redux

- redux为js的状态管理 createStore
- react-redux为了在react更好的使用connet provider等等

### 基本使用与搭建

**1.安装redux 与 react-redux   (yarn add redux react-redux)**

```jsx
//	src/index.js
import { createStore } from 'redux'
import { Provider } from 'react-redux' // Provider包裹根组件 向下传递store 这样所有组件都能拿到store
import counter from './reducers' // 引入counter reducers

const store = createStore(counter)

ReactDOM.render(
  <React.StrictMode>
    <Provider store={store}> 
      <App />
    </Provider>
  </React.StrictMode>,
  document.getElementById('root')
);

```

**2.新建reducers文件夹**

```jsx
//	reducers/counter.js
import * as actions from '../constants' // 导入公共action type变量

const counter = (state = 0, action) => {
    // console.log('action', action)
    switch (action.type) {
        case actions.adds:
            return state + action.payload
        case actions.deletes:
            return state - action.payload
        default:
            return state
    }
}

export default counter
```

**3.新建actions文件夹**

```jsx
// actions/index.js
import * as actions from '../constants' // 导入公共action type变量

export const adds = (payload) => ({
    type: actions.adds,
    payload
})

export const deletes = (payload) => ({
    type: actions.deletes,
    payload
})

```

**4.新建constants文件夹 用于存放公共action type变量**

```jsx
// constants/index.js
export const adds = "add"
export const deletes = "delete"
```

**5.使用connect高阶组件获取公共参数（推荐使用bindActionCreators）**

```jsx
// src/App.js
import React, { Component } from 'react'
import { connect } from 'react-redux'
// import { adds, deletes } from './actions/couter' // 方式一的导出
import * as couterActions from './actions/couter' // 统一导出action函数 采用对象方式来访问（推荐）
import { bindActionCreators } from 'redux'; // 用于合并actions函数合并成一个（推荐）

class App extends Component {
  render() {
    // console.log(this.props)
    return (
      <div className="App">
        { this.props.counter }
            
        {/* <button onClick={() => this.props.add(10)}>+1</button>
        <button onClick={() => this.props.delete(5)}>-1</button> */}
            
        <button onClick={() => this.props.couterActions.adds(10)}>+1</button>
        <button onClick={() => this.props.couterActions.deletes(5)}>-1</button>
      </div>
    )
  }
}

const mapStateToProps = (state) => ({
  // 这里store只有一个reducer 所以这里的state就是counter，如果合并了多个reducer需要变成state.counter
  counter: state 
})

// 方式一
// const mapDispatchToProps = (dispatch) => ({
//   add: (num) => dispatch(adds(num)),
//   delete: (num) => dispatch(deletes(num)),
// })

// 方式二（推荐）所有的action只需要写一次 通过this.props.couterActions.adds(10)这种进行调用
const mapDispatchToProps = (dispatch) => ({
  couterActions: bindActionCreators(couterActions, dispatch)
})

// connect 以后可以将redux的公共变量通过props传递给App组件
export default connect(mapStateToProps, mapDispatchToProps)(App)

```

**6.通过combineReducers将多个reducer进行合并，新建reducers/index.js**

```js
// reducers/index.js 公共根rootReducer
import { combineReducers } from 'redux' // 将多个reducer进行合并
import counter from './counter'
import user from './user'

const rootReducer = combineReducers({
    counter,
    user
})

export default rootReducer
```

```js
//	src/index.js  引用合并后的rootReducer
import rootReducer from './reducers'

const store = createStore(rootReducer)
```

```js
//	connect以后 使用有些变化  因为合并之后会在外层多一个对象  调用方式如下
const mapStateToProps = (state) => ({
  // 如果合并了多个reducer需要变成state.counter
  counter: state.counter
})
```



### redux的ducks模式

[参考链接](https://www.jianshu.com/p/4f98f1c5e6a9)

[redux架构可以参考这个](https://github.com/iHaroro/react-entry-template)

> 有些人喜欢把 action 单独切出去一个目录 `actions`，让 action 和 reducer 分开。个人观点觉得这种做法可能有点过度优化了，其实多数情况下特定的 action 只会影响特定的 reducer，直接放到一起可以更加清晰地知道这个 action 其实只是会影响到什么样的 reducer。而分开会给我们维护和理解代码带来额外不必要的负担

```js
// 例子

// Actions
const LOAD   = 'my-app/widgets/LOAD';
const CREATE = 'my-app/widgets/CREATE';
const UPDATE = 'my-app/widgets/UPDATE';
const REMOVE = 'my-app/widgets/REMOVE';

// Reducer
export default function reducer(state = {}, action = {}) {
  switch (action.type) {
    // do reducer stuff
    default: return state;
  }
}

// Action Creators
export function loadWidgets() {
  return { type: LOAD };
}

export function createWidget(widget) {
  return { type: CREATE, widget };
}

export function updateWidget(widget) {
  return { type: UPDATE, widget };
}

export function removeWidget(widget) {
  return { type: REMOVE, widget };
}
```



### 中间件的应用（日志中间件，异步中间件）

**1.安装 yarn add redux-logger redux-thunk**

```jsx
// 安装 redux-logger redux-thunk
import logger from 'redux-logger' // 中间件日志打印 触发action的时候控制台会打印 state的变化值
import thunk from 'redux-thunk' // 异步中间件（解决异步action，相当于vuex里的action）
import { createStore, applyMiddleware } from 'redux' // applyMiddleware中间件插件

const store = createStore(rootReducer, {}, applyMiddleware(logger, thunk))
```

**2.使用**

```js
// actions/user.js
import * as actions from '../constants'

// 同步action
// export const addUsers = (payload) => ({
//     type: actions.addUsers,
//     payload
// })

// 异步action (需要redux-thunk中间件来实现 内部返回一个dispatch)
export const addUsers = (payload) => {
    return (dispatch) => {
        setTimeout(() => { // 这里模拟异步 可以换成fetch等网络请求
            dispatch({ // 这里一定需要dispatch来触发reducer
                type: actions.addUsers,
                payload
            })
        }, 1000);
    }
}


```

**3.优化：异步action redux-thunk的第二个参数getState使用**

> 因为第二个参数getState，所以可以将reducer的return state的数据处理逻辑放到action中，最后通过dispatch传递，Switch..case中 返回action.payload即可

```js
//	使用场景： 在异步action处理好数据（payload）传递给reducer
// 示例1 
export function selectToggle(item, isSelectPersonMultiple) {
    return (dispatch, getState) => { // <= 第二个参数可以通过getState()方法 获取state ！！！！
        
        let commonList = getState().address_book_select.commonList;
        const index = commonList.findIndex(el => el.id == item.id);

        if (index > -1) {
            commonList.splice(index, 1)
        } else {
            if (isSelectPersonMultiple === false && commonList.length > 0) {return Toast.fail('只能选择一人',1)}
            if (isSelectPersonMultiple === true && commonList.length > 9) {return Toast.fail('最多只能选10人',1)}
            commonList.push(item)
        }
        dispatch({
            type: ADDRESS_BOOK_SELECT_PERSON,
            payload: { commonList: commonList }
        })
    }
}

// 示例2
export function clearSelected(callback) {
    return async (dispatch) => { // 因为redux-thunk是异步的 这里用了 async...await
        await dispatch({
            type: ADDRESS_BOOK_SELECT_PERSON_CLEAR,
            payload: { commonList: [] }
        })
        callback && callback()
    }
}

// 示例3
export function getNotesList({},callback){
    return (dispatch, getState)=> {
        _fetch(getState().user.host + `/v1/app/oneself/notes/list/`, {
            method: 'get',
            headers: {
                'AUTHORIZATION': getState().user.token,
                'unit': getState().user.unit
            }
        })
            .then(res => {
                dispatch({
                    type: NOTES_LIST, 
                    payload: {
                        ...getState().notes,
                        notes_list:res.data
                    }
                });
                callback(false);
            })
            .catch(err=>{
                callback(true)
            })
    }
}
```

**4.更改reducer swich case写法**

```js
import { NOTES_LIST } from "../actions";

export default function normal(state = {}, action) {
    switch (action.type) {
        case NOTES_LIST:
            return action.payload; // 这里直接返回处理好的数据即可，因为之前state的处理逻辑已经在acion中处理好了
        default:
            return state
    }
}

```



## [Dva.js](https://dvajs.com/guide/)



## [Umi.js](https://umijs.org/zh-CN/docs)



## CSS样式问题

模块与非模块导入

```tsx
// CSS Modules
import styles from './foo.css';

// 非 CSS Modules
import './foo.css';
```



组件代码

```tsx
import React from 'react'
// import './index.less'  // 样式会全局污染
import styles from './index.less' // 会在样式上面增加哈希 相当于vue.scoped 
//（生成出来的是这样的className: dropdown-option is-disabled___dWMn9）

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

样式代码 less

```less
.is-disabled {
    background-color: pink;
    pointer-events: none;
    color: red;
}
```







