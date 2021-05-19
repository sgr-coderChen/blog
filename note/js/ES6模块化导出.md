

### example.js

```javascript
//  导出数据
export var color = "red";
export let name = "Nicholas";
export const magicNumber = 7;
// 导出函数
export function sum(num1, num2) {
    return num1 + num2;
}
// 导出类
export class Rectangle {
    constructor(length, width) {
        this.length = length;
        this.width = width;
    }
}
// 此函数为模块私有
function subtract(num1, num2) {
    return num1 - num2;
}
// 定义一个函数……
function multiply(num1, num2) {
    return num1 * num2;
}
// ……稍后将其导出
export {
    multiply
};
```
### index.js

**1.导出多个绑定**

```javascript
import { sum, multiply } from './example'


console.log('sum', sum(1,2))
console.log('multiply', multiply(2,2))
```



**2.完全导入一个模块**
youName 为自定义命名导出文件对象

即允许你将整个模块当作单一对象进行导入，该模块的所有导出都会作为对象的属性存在

```javascript
import * as youName from './example'

console.log('objs', youName)
console.log('sum', youName.sum(1,2))
console.log('multiply', youName.multiply(2,2))

```


**3.模块内部更改导出名称**

```javascript
function sum(num1, num2) {
	return num1 + num2
}

export { sum as add }
```

此处的 sum() 函数被作为 add() 导出，前者是本地名称（ local name ），后者则是导出名称（ exported name ）。
这意味着当另一个模块要导入此函数时，它必须改用 add 这个名称

```javascript
import { add } from "./example.js
```

**4.外部文件导入模块重命名**

```javascript
import { add as sum } from "./example.js";

console.log(typeof add); // "undefined"

console.log(sum(1, 2)); //3
```
此代码导入了 add() 函数，并使用了导入名称（ import name ）将其重命名为 sum()
（本地名称）。这意味着在此模块中并不存在名为 add 的标识符。

**5.导入默认值**

```javascript
function sum(num1, num2) {
	return num1 + num2;
}
export default sum
```

```javascript
// 导入默认值
import sum from "./example.js";

console.log(sum(1, 2)); //3
```

这个导入语句从 example.js 模块导入了其默认值。注意此处并未使用花括号，与之前在非默认的导入中看到的不同。

**6.导入默认值,非默认**

```javascript
export let color = "red";

export default function(num1, num2) {
	return num1 + num2
}
```

```javascript
import sum, { color } from "./example.js";
console.log(sum(1, 2)); // 3
console.log(color); // "red

```

逗号将默认的本地名称与非默认的名称分隔开，后者仍旧被花括号所包裹。要记住在 import语句中默认名称必须位于非默认名称之


**7.无绑定导入**
有些模块也许没有进行任何导出，相反只是修改全局作用域的对象。尽管这种模块的顶级变量、函数或类最终并不会自动被加入全局作用域，但这并不意味着该模块无法访问全局作用域。诸如 Array 与 Object 之类的内置对象的共享定义在模块内部是可访问的，并且对于这些对象的修改会反映到其他模块中。

即 直接import一个文件   （ import "./example.js"; ）
```javascript
// 没有导出与导入的模块  example.js
Array.prototype.pushAll = function(items) {
    // items 必须是一个数组
    if (!Array.isArray(items)) {
        throw new TypeError("Argument must be an array.");
    }
    // 使用内置的 push() 与扩展运算符
    return this.push(...items);
};
window.common = {
    multiply,
    subtract
}

```

```javascript
//  index.js
import "./example.js";
let colors = ["red", "green", "blue"];
let items = [];
items.pushAll(colors)

```



深入理解es6<sup>[1]</sup>

 - [1] 参考 《深入理解es6》 用模块封装代码