

# [TypeScript 入门](https://www.tslang.cn/docs/handbook/basic-types.html)

> 目标：理解 TypeScript 基础的`interface`和`type`编写和泛型的普通使用（可以理解为类型系统里的函数传参），目前不做深入学习



## 基本类型



### 数组

```typescript
// 第一种，可以在元素类型后面接上 []，表示由此类型元素组成的一个数组：
let list: number[] = [1, 2, 3];

// 第二种方式是使用数组泛型，Array<元素类型>：
let list: Array<number> = [1, 2, 3];
```



### 类型断言❌

有时候你会遇到这样的情况，你会比TypeScript更了解某个值的详细信息。 通常这会发生在你清楚地知道一个实体具有比它现有类型更确切的类型。

通过*类型断言*这种方式可以告诉编译器，“相信我，我知道自己在干什么”。 类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。 TypeScript会假设你，程序员，已经进行了必须的检查。

```typescript
let someValue: any = "this is a string";

let strLength: number = (<string>someValue).length;



// 另一个为as语法：
let someValue: any = "this is a string";

let strLength: number = (someValue as string).length;
```





## 接口（interface）



### 接口初探

例子：使用接口来描述必须包含一个`label`属性且类型为`string`：

```typescript
interface LabelledValue {
  label: string;
}

function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

`LabelledValue`接口就好比一个名字，用来描述上面例子里的要求。 它代表了有一个 `label`属性且类型为`string`的对象。 需要注意的是，我们在这里并不能像在其它语言里一样，说传给 `printLabel`的对象实现了这个接口。我们只会去关注值的外形。 只要传入的对象满足上面提到的必要条件，那么它就是被允许的。

还有一点值得提的是，类型检查器不会去检查属性的顺序，只要相应的属性存在并且类型也是对的就可以。



### 可选属性

```typescript
interface SquareConfig { // 可选属性名字定义的后面加一个 ? 符号表示非必须
  color?: string; 
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  let newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare = createSquare({color: "black"});
```

可选属性的好处之一是可以对可能存在的属性进行预定义，好处之二是可以捕获引用了不存在的属性时的错误。 比如，我们故意将 `createSquare`里的`color`属性名拼错，就会得到一个错误提示



### 只读属性

只能在对象刚刚创建的时候修改其值

```typescript
interface Point {
    readonly x: number;
    readonly y: number;
}

```

你可以通过赋值一个对象字面量来构造一个`Point`。 赋值后， `x`和`y`再也不能被改变了。


```typescript
let p1: Point = { x: 10, y: 20 };
p1.x = 5; // error!
```



