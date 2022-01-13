# webpack5笔记



## 相关学习资料

- [视频](https://www.bilibili.com/video/BV1YU4y1g745?p=38&spm_id_from=pageDriver)

## babel高级语法转换

### 1.示例：链判断符（ a.callback?.() ） 编译前与编译后的代码分析

- [链判断符-es6](https://es6.ruanyifeng.com/#docs/operator#%E9%93%BE%E5%88%A4%E6%96%AD%E8%BF%90%E7%AE%97%E7%AC%A6)
- babel7的preset-env预设中已经支持了链判断符（已查阅证实）

- 安装babel全家桶

```json
"@babel/core": "^7.16.7",
"@babel/plugin-transform-runtime": "^7.16.8",
"@babel/preset-env": "^7.16.8",
"@babel/runtime": "^7.16.7",
"babel-loader": "^8.2.3",
```

- webpack.config.js

```js
// 相关代码 这里编译打包输出路径为dist/bundle.js
module: {
    rules: [
        {
            test: /\.js$/,
            exclude: /node_modules/, // 排除node_modules
            use: {
                loader: 'babel-loader',
                options: {
                    // 设置 babel-loader的预设
                    presets: ['@babel/preset-env'],
                    plugins: [
                        // yarn add @babel/runtime @babel/plugin-transform-runtime -D
                        // 安装上面两个依赖转义 async await等语法
                        [
                            '@babel/plugin-transform-runtime' 
                        ]
                    ]
                }
            }
        }
    ]
},
```

- 需要编译的js

```js
function testA(item) {
    // 如果存在callback函数才执行
    item.callback?.()
}

function testB() {
    testA({ callback: function(){console.log(123)} })
}
```

- 不使用babel转义

```js
// dist/bundle.js (打包编译后的文件)
function testA(item) {
    item.callback?.()  // 直接输出 未转义 低版本浏览器会出现报错
}

function testB() {
    testA({ callback: function(){console.log(123)} })
}
```

- 使用babel转义

```js
// 这里的链判断运算符被babel转义了，与编译前不同 
function testA(item) {
  var _item$callback;

  (_item$callback = item.callback) === null || _item$callback === void 0 ? void 0 : _item$callback.call(item);
}

function testB() {
  testA({
    callback: function callback() {
      console.log(123);
    }
  });
}
```

