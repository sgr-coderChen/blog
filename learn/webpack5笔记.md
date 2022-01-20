# webpack5笔记



## 相关学习资料

- [视频](https://www.bilibili.com/video/BV1YU4y1g745?p=38&spm_id_from=pageDriver)


## 基本配置

```js
const path = require('path')
//  HtmlWebpackPlugin打包后自动插入js到html中
const HtmlWebpackPlugin = require('html-webpack-plugin')
//  抽离css为单独文件
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
//  压缩css代码
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin')

module.exports = {
    entry: './src/index.js',
    output: {
        filename: '[name].bundle.js',
        path: path.resolve(__dirname, './dist'),
        clean: true, // 清理dist文件夹
        assetModuleFilename: 'image/[contenthash][ext]',//定义图片打包位置和命名
    },
    mode: 'development',
    devtool: 'inline-source-map', // 代码出错对应到源文件（不然会指向打包后的js代码中）
    plugins: [
        new HtmlWebpackPlugin({
            template: './index.html', // 基于index.html生成新的html inject自动注入打包资源
            filename: 'app.html',
            inject: 'body'
        }),
        new MiniCssExtractPlugin({ // 打包成单独的css
            filename: 'styles/[contenthash].css'
        })
    ],
    devServer: { // 启动本地服务（热更新）
        static: './dist'
    },
    module: {
        rules: [
            {
                test: /\.png$/,
                type: 'asset/resource',
                generator: {// 同assetModuleFilename 但 generator优先级高
                    filename: 'image/[contenthash][ext]'
                }
            },
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, 'css-loader']
            },
            {
                test: /\.(woff|woff2|eot|ttf|otf)$/,
                type: 'asset/resource'
            },
            {
                test: /\.js$/,
                exclude: /node_modules/, // 排除node_modules
                use: {
                    loader: 'babel-loader',
                    options: {
                        // 设置 babel-loader的预设
                        presets: ['@babel/preset-env'],
                        plugins: [
                            [
                                '@babel/plugin-transform-runtime'
                            ]
                        ]
                    }
                }
            }
        ]
    },
    // 优化配置项
    optimization: {
        minimizer: [
            new CssMinimizerPlugin()
        ],
        splitChunks: {
            // 需要修改输出文件名 filename: '[name].bundle.js',
            chunks: 'all'
        }
    }
}
```




## babel高级语法转换

**[实时转义效果查看  点击这里](https://www.babeljs.cn/repl#?browsers=defaults%2C%20not%20ie%2011%2C%20not%20ie_mob%2011&build=&builtIns=false&corejs=3.6&spec=false&loose=false&code_lz=JYFwpgtg_AdAhkA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=env%2Creact%2Cstage-2&prettier=false&targets=&version=7.16.9&externalPlugins=&assumptions=%7B%7D)**

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

- Vue-cli 4转义结果-可在浏览器控制台sources中查看搜索对应的代码 

**（vue脚手架已默认配置预设，无需再安装对应的babel插件集）**

```js
	//  编译前
		testA(item) {
            let a = item?.a?.b ?? '无';
            console.log('testA', a);
        },
        testBabel() {
            this.testA({
                a: {
                    b: 123,
                },
            });
        },
	//  编译后
        testA: function (e) {
            var n,
                t,
                c =
                    null !==
                        (n =
                            null === e ||
                            void 0 === e ||
                            null === (t = e.a) ||
                            void 0 === t
                                ? void 0
                                : t.b) && void 0 !== n
                        ? n
                        : '无';
            console.log('testA', c);
        },
        testBabel: function () {
            this.testA({
                a: {
                    b: 123,
                },
            });
        },
```





## css模块化

1.开启模块

```js
module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: [
                    {
                        loader: 'babel-loader',
                        options: {
                            presets: ['@babel/preset-env'],
                        }
                    },
                ]
            },
            {
                test: /\.css$/,
                use: [
                    'style-loader',
                    {
                        loader: 'css-loader',
                        options: {
                            modules: true, // <= 开启css模块化
                        }
                    },
                    'postcss-loader'
                ]
            }
        ]
    }
```

插入head标签中的style, 会生成额外的哈希值

```css
.jiiwoQM4RBXA8T7kGVrB{
    ...
}
```

2.css文件变为模块导入

```js
import styles from './app.css'
console.log(styles)
// => {box: 'jiiwoQM4RBXA8T7kGVrB'}  有一个css类名和哈希的映射关系
```

> vue react内部就使用了这个css模块化

3.也可以部分开启 CSS 模块模式，比如全局样式可以冠以 .global 前缀，如：

1. *.global.css 普通模式

2. *.css css module模式

这里统一用 global 关键词进行识别。正则匹配 .global.css不加哈希，其他都加上哈希 ，达到vue scoped的效果



