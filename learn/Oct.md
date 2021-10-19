

# [TypeScript 入门](https://www.tslang.cn/docs/handbook/basic-types.html)

> 目标：理解 TypeScript 基础的`interface`和`type`编写和泛型的普通使用（可以理解为类型系统里的函数传参），目前不做深入学习

- TypeScript兼容低版本js（es3）
- 编译后为JS，并且除去类型注释

# 基本类型



### 数组

```typescript
// 第一种，可以在元素类型后面接上 []，表示由此类型元素组成的一个数组：
let list: number[] = [1, 2, 3];

// 第二种方式是使用数组泛型，Array<元素类型>：
let list: Array<number> = [1, 2, 3];

```

```typescript
const arr1: Array<number> = [1,2,3]
const arr2: number[] = [1, 2, 3]


function sum (...args: number[]) {
    return args.reduce((pre, cur) => pre + cur, 0) 
}

sum(1,2)
// sum(1,2, 'string')  // error
```



### 类型断言

有时候你会遇到这样的情况，你会比TypeScript更了解某个值的详细信息。 通常这会发生在你清楚地知道一个实体具有比它现有类型更确切的类型。

通过*类型断言*这种方式可以告诉编译器，“相信我，我知道自己在干什么”。 类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。 TypeScript会假设你，程序员，已经进行了必须的检查。

```typescript
let someValue: any = "this is a string";

let strLength: number = (<string>someValue).length;



// 另一个为as语法：
let someValue: any = "this is a string";

let strLength: number = (someValue as string).length;
```

```typescript
const nums = [100, 1100, 251]

// res可能是number 或者 undefind 可能会找不到
const res = nums.find(item => item > 0) 

const a  = res * res 

const result1 = a as number // 告诉ts  a必定是number（即类型断言）
// 或者
const result2 = <number>a // jsx 无法使用
```





# 接口（interface）



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

```typescript
interface Post {
    title: string
    content: string
    subTitle?: string
    readonly footer: string
}

function demo(post: Post) {
    console.log(post.title)
    console.log(post.content)
}

const hello: Post = {
    title: 'wfwef',
    content: 'wfwef',
    footer: 'wfwef',
}

//  hello.footer = 'wefwef'  只读无法修改error


interface Cache {
    [prop: string]: string
}
const cache: Cache = {
    a: 'sfsfs'
}

```



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

最简单判断该用`readonly`还是`const`的方法是看要把它做为变量使用还是做为一个属性。 做为变量使用的话用 `const`，若做为属性则使用`readonly`。

**个人理解：相当于加强版的const，const定义的是对象的话 里面的属性无法限制其改动，只能限制外层的修改，而只读属性定义对象后 内部属性也是无法修改的**



### 字符串索引签名

```typescript
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
// 除了color，width  额外开放一个随机的字段传入
```



### 函数类型

```typescript
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  let result = source.search(subString);
  return result > -1;
}

// 函数的参数名不需要与接口里定义的名字相匹配
let mySearch: SearchFunc;
mySearch = function(src: string, sub: string): boolean {
  let result = src.search(sub);
  return result > -1;
}

// 或者
let mySearch: SearchFunc;
mySearch = function(src, sub) {
    let result = src.search(sub);
    return result > -1;
}
```



### 类类型

```typescript
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```



```typescript
interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}
interface ClockInterface {
    tick();
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
    return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("beep beep");
    }
}
class AnalogClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("tick tock");
    }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```

因为`createClock`的第一个参数是`ClockConstructor`类型，在`createClock(AnalogClock, 7, 32)`里，会检查`AnalogClock`是否符合构造函数签名。



### 继承接口

```typescript
interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
```

一个接口可以继承多个接口，创建出多个接口的合成接口

```typescript
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```



### 混合类型

一个对象可以同时具有上面提到的多种类型，一个对象可以同时做为函数和对象使用，并带有额外的属性

```typescript
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter; // 返回的counter对象 满足Counter接口
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```





## 类

```typescript
class Person {
    // name: string = 'init name' // 一般在constructor中初始化数据

    public name: string // 默认 public
    private age: number
    protected gender: boolean

    constructor(name: string, age: number) {
        this.name = name
        this.age = age
        this.gender = true
    }
    

    sayHi(msg: string): void {
        console.log(this.name)
    }
    
}

const tom = new Person('tom', 18)

// console.log(tom.age) // 属性“age”为私有属性，只能在类“Person”中访问。
// console.log(tom.gender) // 属性“gender”受保护，只能在类“Person”及其子类中访问。

class Student extends Person {
    constructor(name: string, age: number) {
        super(name, age);
        console.log(this.gender) // 可以访问 protected gender
    } 
    // private constructor(name: string, age: number) { // 如果构造函数被 private，则无法实例化，只能通过Static方法内部创建
    //     this.name = name
    //     this.age = age
    //     this.gender = true
    // }

    // static create(name: string, age: number) { // 静态方法创建实例
    //     return new Student(name, age)
    // }
}


// const jack = Student.create('jack', 21)



// 类与接口

interface Eat {
    eat(food: string): void
}

interface Run {
    run(distance: number): void
}

class PersonA implements Eat, Run {
    eat(food: string): void {
        console.log(`优雅的吃：${food}`)
    }

    run(distance: number): void {
        console.log(`行走：${distance}`)
    }
}

class Animal implements Eat, Run {
    eat(food: string): void {
        console.log(`大口吃：${food}`)
    }

    run(distance: number): void {
        console.log(`四肢跑：${distance}`)
    }
}
```



```typescript
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter = new Greeter("world");
```

```typescript
class Animal {
    name: string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 5) {
        console.log("Slithering...");
        super.move(distanceInMeters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45) {
        console.log("Galloping...");
        super.move(distanceInMeters);
    }
}

let sam = new Snake("Sammy the Python");
let tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
```

派生类包含了一个构造函数，它 *必须*调用 `super()`，它会执行基类的构造函数。 而且，在构造函数里访问 `this`的属性之前，我们 *一定*要调用 `super()`。 这个是TypeScript强制执行的一条重要规则。

这个例子演示了如何在子类里可以重写父类的方法。 `Snake`类和 `Horse`类都创建了 `move`方法，它们重写了从 `Animal`继承来的 `move`方法，使得 `move`方法根据不同的类而具有不同的功能。 注意，即使 `tom`被声明为 `Animal`类型，但因为它的值是 `Horse`，调用 `tom.move(34)`时，它会调用 `Horse`里重写的方法：

```typescript
Slithering...
Sammy the Python moved 5m.
Galloping...
Tommy the Palomino moved 34m.
```



### 公共，私有与受保护的修饰符

**public**

在TypeScript里，成员都默认为 `public`

```typescript
class Animal {
    public name: string;
    public constructor(theName: string) { this.name = theName; }
    public move(distanceInMeters: number) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}
```

**private**

当成员被标记成 `private`时，它就不能在声明它的类的外部访问

```typescript
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

new Animal("Cat").name; // 错误: 'name' 是私有的.
```

```typescript
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
    constructor() { super("Rhino"); }
}

class Employee {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

let animal = new Animal("Goat");
let rhino = new Rhino();
let employee = new Employee("Bob");

animal = rhino;
animal = employee; // 错误: Animal 与 Employee 不兼容.
```

这个例子中有 `Animal`和 `Rhino`两个类， `Rhino`是 `Animal`类的子类。 还有一个 `Employee`类，其类型看上去与 `Animal`是相同的。 我们创建了几个这些类的实例，并相互赋值来看看会发生什么。 因为 `Animal`和 `Rhino`共享了来自 `Animal`里的私有成员定义 `private name: string`，因此它们是兼容的。 然而 `Employee`却不是这样。当把 `Employee`赋值给 `Animal`的时候，得到一个错误，说它们的类型不兼容。 尽管 `Employee`里也有一个私有成员 `name`，但它明显不是 `Animal`里面定义的那个。

**protected属性**

```typescript
class Person {
    protected name: string; // 1.被保护了 只能通过自身函数访问 无法直接访问name
    constructor(name: string) { this.name = name; }
}

class Employee extends Person {
    private department: string;

    constructor(name: string, department: string) {
        super(name)
        this.department = department;
    }

    public getElevatorPitch() {
        return `Hello, my name is ${this.name} and I work in ${this.department}.`;
    }
}

let howard = new Employee("Howard", "Sales");
console.log(howard.getElevatorPitch());
console.log(howard.name); // 错误
```

注意，我们不能在 `Person`类外使用 `name`，但是我们仍然可以通过 `Employee`类的实例方法访问，因为 `Employee`是由 `Person`派生而来的。

**protected构造函数** 

```typescript
class Person {
    protected name: string;
    // 2.构造函数也可以被标记成 protected。 这意味着这个类不能在包含它的类外被实例化，但是能被继承
    protected constructor(theName: string) { this.name = theName; }
}

// Employee 能够继承 Person
class Employee extends Person {
    private department: string;

    constructor(name: string, department: string) {
        super(name);
        this.department = department;
    }

    public getElevatorPitch() {
        return `Hello, my name is ${this.name} and I work in ${this.department}.`;
    }
}

let howard = new Employee("Howard", "Sales");
let john = new Person("John"); // 错误: 'Person' 的构造函数是被保护的.
```

### readonly 

你可以使用 `readonly`关键字将属性设置为只读的。 只读属性必须在声明时或构造函数里被初始化。

```typescript
class Octopus {
    readonly name: string;
    readonly numberOfLegs: number = 8;
    constructor (theName: string) {
        this.name = theName;
    }
}
let dad = new Octopus("Man with the 8 strong legs");
dad.name = "Man with the 3-piece suit"; // 错误! name 是只读的.
```



### 把类当做接口使用

```typescript
class Point {
    x: number;
    y: number;
}

interface Point3d extends Point {
    z: number;
}

let point3d: Point3d = {x: 1, y: 2, z: 3};
```





## 函数



### 函数类型

```typescript
function add(x: number, y: number): number {
    return x + y;
}

let myAdd = function(x: number, y: number): number { return x + y; };
```

我们可以给每个参数添加类型之后**再为函数本身添加返回值类型**。 TypeScript能够根据返回语句自动推断出返回值类型，因此我们通常省略它。



```typescript
function buildName(firstName: string, lastName?: string) {
    if (lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}

let result1 = buildName("Bob");  // works correctly now
let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result3 = buildName("Bob", "Adams");  // ah, just right
```

**可选参数必须跟在必须参数后面**。 如果上例我们想让first name是可选的，那么就必须调整它们的位置，把first name放在后面。



### 剩余参数

```typescript
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}

let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
```



### this和箭头函数

JavaScript里，`this`的值在函数被调用的时候才会指定。 这是个既强大又灵活的特点，但是你需要花点时间弄清楚函数调用的上下文是什么。 但众所周知，这不是一件很简单的事，尤其是在返回一个函数或将函数当做参数传递的时候。

```typescript
let deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        return function() {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

可以看到`createCardPicker`是个函数，并且它又返回了一个函数。 如果我们尝试运行这个程序，会发现它并没有弹出对话框而是报错了。 因为 `createCardPicker`返回的函数里的`this`被设置成了`window`而不是`deck`对象。 因为我们只是独立的调用了 `cardPicker()`。 顶级的非方法式调用会将 `this`视为`window`。 （注意：在严格模式下， `this`为`undefined`而不是`window`）。

为了解决这个问题，我们可以在函数被返回时就绑好正确的`this`。 这样的话，无论之后怎么使用它，都会引用绑定的‘deck’对象。 我们需要改变函数表达式来使用ECMAScript 6箭头语法。 箭头函数能保存函数创建时的 `this`值，而不是调用时的值：

```typescript
let deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        // NOTE: the line below is now an arrow function, allowing us to capture 'this' right here
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```



### this参数

```typescript
interface Card {
    suit: string;
    card: number;
}
interface Deck {
    suits: string[];
    cards: number[];
    createCardPicker(this: Deck): () => Card;
}
let deck: Deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    // NOTE: The function now explicitly specifies that its callee must be of type Deck
    createCardPicker: function(this: Deck) {
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```



## 枚举

```typescript

const enum PostStatus { // 使用常量类型的枚举  编译后不会影响js代码
    success, // 如果不写默认为0  且每写一个会自动加一
    failed, // 1
    unconnect // 2
}
enum PostStatus1 { // 这个会多生成js代码
    success,
    failed,
    unconnect
}
const post = { 
    state: PostStatus.success,
    title: 'aaaaa'
}
```



# [泛型](https://www.tslang.cn/docs/handbook/generics.html)

```typescript
function createNumberArray(length: number, value: number): number[] {
    const arr = Array<number>(length).fill(value)
    return arr
}

//  如果需要一个字符串数组 代码会冗余
function createStringArray(length: number, value: string): string[] {
    const arr = Array<string>(length).fill(value)
    return arr
}

const res = createNumberArray(3, 100) // res => [100, 100, 100]

// 泛型优化
function createArray<T>(length: number, value: T): T[] {
    const arr = Array<T>(length).fill(value)
    return arr
}

const res2 = createArray<string>(3, 'foo')
```



使用`泛型`来创建可重用的组件，一个组件可以支持多种类型的数据。 这样用户就可以以自己的数据类型来使用组件。

```typescript
// 不用泛型的话，这个函数可能是下面这样：
function identity(arg: number): number {
    return arg;
}
// 或者，我们使用any类型来定义函数：
function identity(arg: any): any {
    return arg;
}
```

使用`any`类型会导致这个函数可以接收任何类型的`arg`参数，这样就丢失了一些信息：传入的类型与返回的类型应该是相同的。如果我们传入一个数字，我们只知道任何类型的值都有可能被返回。

因此，我们需要一种方法使返回值的类型与传入参数的类型是相同的。 这里，我们使用了 *类型变量*，它是一种特殊的变量，只用于表示类型而不是值。

```typescript
function identity<T>(arg: T): T {
    return arg;
}
```

我们给identity添加了类型变量`T`。 `T`帮助我们捕获用户传入的类型（比如：`number`），之后我们就可以使用这个类型。 之后我们再次使用了 `T`当做返回值类型。现在我们可以知道参数类型与返回值类型是相同的了。 这允许我们跟踪函数里使用的类型的信息。

我们把这个版本的`identity`函数叫做泛型，因为它可以适用于多个类型。 不同于使用 `any`，它不会丢失信息，像第一个例子那像保持准确性，传入数值类型并返回数值类型。

```typescript
let output = identity<string>("myString");  // type of output will be 'string'

// 第二种方法更普遍。利用了类型推论 -- 即编译器会根据传入的参数自动地帮助我们确定T的类型
let output = identity("myString");  // type of output will be 'string'
```



**泛型变量T当做类型的一部分使用**

```typescript
function loggingIdentity<T>(arg: T[]): T[] {
    console.log(arg.length);  // Array has a .length, so no more error
    return arg;
}
//	或者
function loggingIdentity<T>(arg: Array<T>): Array<T> {
    console.log(arg.length);  // Array has a .length, so no more error
    return arg;
}
```

你可以这样理解`loggingIdentity`的类型：泛型函数`loggingIdentity`，接收类型参数`T`和参数`arg`，它是个元素类型是`T`的数组，并返回元素类型是`T`的数组。 如果我们传入数字数组，将返回一个数字数组，因为此时 `T`的的类型为`number`。 这可以让我们把泛型变量T当做类型的一部分使用，而不是整个类型，增加了灵活性。



### 泛型类型

泛型函数的类型与非泛型函数的类型没什么不同，只是有一个类型参数在最前面，像函数声明一样：

```typescript
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <T>(arg: T) => T = identity;
```

我们还可以使用带有调用签名的对象字面量来定义泛型函数：

```typescript
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: {<T>(arg: T): T} = identity;


// 或者
interface GenericIdentityFn {
    <T>(arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn = identity;
```

我们可能想把泛型参数当作整个接口的一个参数

```typescript
interface GenericIdentityFn<T> {
    (arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn<number> = identity;
```



### 泛型类

```typescript
class GenericNumber<T> { 
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();// 限制它只能使用number类型
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```



### 泛型约束

```typescript
function loggingIdentity<T>(arg: T): T { // 并不能证明每种类型都有length属性
    console.log(arg.length);  // Error: T doesn't have .length
    return arg;
}
```

我们定义一个接口来描述约束条件。 创建一个包含 `.length`属性的接口，使用这个接口和`extends`关键字来实现约束：

```typescript
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // Now we know it has a .length property, so no more error
    return arg;
}
```

```typescript
// 现在这个泛型函数被定义了约束，因此它不再是适用于任意类型：
loggingIdentity(3);  // Error, number doesn't have a .length property

// 我们需要传入符合约束类型的值，必须包含必须的属性：
loggingIdentity({length: 10, value: 3});
```



## 高级类型



### 交叉类型

交叉类型是将多个类型合并为一个类型。 这让我们可以把现有的多种类型叠加到一起成为一种类型，它包含了所需的所有类型的特性。 例如， `Person & Serializable & Loggable`同时是 `Person` *和* `Serializable` *和* `Loggable`。 就是说这个类型的对象同时拥有了这三种类型的成员。

我们大多是在混入（mixins）或其它不适合典型面向对象模型的地方看到交叉类型的使用。 （在JavaScript里发生这种情况的场合很多！） 下面是如何创建混入的一个简单例子：

```typescript
function extend<T, U>(first: T, second: U): T & U {
    let result = <T & U>{};
    for (let id in first) {
        (<any>result)[id] = (<any>first)[id];
    }
    for (let id in second) {
        if (!result.hasOwnProperty(id)) {
            (<any>result)[id] = (<any>second)[id];
        }
    }
    return result;
}

class Person {
    constructor(public name: string) { }
}
interface Loggable {
    log(): void;
}
class ConsoleLogger implements Loggable {
    log() {
        // ...
    }
}
var jim = extend(new Person("Jim"), new ConsoleLogger());
var n = jim.name;
jim.log();
```



### 联合类型

联合类型与交叉类型很有关联，但是使用上却完全不同。 偶尔你会遇到这种情况，一个代码库希望传入 `number`或 `string`类型的参数。 例如下面的函数：

```typescript
/**
 * Takes a string and adds "padding" to the left.
 * If 'padding' is a string, then 'padding' is appended to the left side.
 * If 'padding' is a number, then that number of spaces is added to the left side.
 */
function padLeft(value: string, padding: any) {
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
    if (typeof padding === "string") {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}

padLeft("Hello world", 4); // returns "    Hello world"
```

`padLeft`存在一个问题， `padding`参数的类型指定成了 `any`。 这就是说我们可以传入一个既不是 `number`也不是 `string`类型的参数，但是TypeScript却不报错。

```typescript
// 使用联合类型
/**
 * Takes a string and adds "padding" to the left.
 * If 'padding' is a string, then 'padding' is appended to the left side.
 * If 'padding' is a number, then that number of spaces is added to the left side.
 */
function padLeft(value: string, padding: string | number) {
    // ...
}

let indentedString = padLeft("Hello world", true); // errors during compilation
```

```typescript
interface Bird {
    fly();
    layEggs();
}

interface Fish {
    swim();
    layEggs();
}

function getSmallPet(): Fish | Bird {
    // ...
}

let pet = getSmallPet();
pet.layEggs(); // okay
pet.swim();    // errors  只能访问此联合类型的所有类型里共有的成员。
```



### 类型别名（Type）

```typescript
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    }
    else {
        return n();
    }
}
```

同接口一样，类型别名也可以是泛型 - 我们可以添加类型参数并且在别名声明的右侧传入：

```typescript
type Container<T> = { 
    value: T 
};
```

类型别名不能出现在声明右侧的任何地方。

```typescript
type Yikes = Array<Yikes>; // error
```



### 接口interface 与 类型别名type

接口创建了一个新的名字，可以在其它任何地方使用。 类型别名并不创建新名字—比如，错误信息就不会使用别名。 在下面的示例代码里，在编译器中将鼠标悬停在 `interfaced`上，显示它返回的是 `Interface`，但悬停在 `aliased`上时，显示的却是对象字面量类型。

```typescript
type Alias = { num: number }
interface Interface {
    num: number;
}
declare function aliased(arg: Alias): Alias;
declare function interfaced(arg: Interface): Interface;
```

另一个重要区别是类型别名不能被 `extends`和 `implements`（自己也不能 `extends`和 `implements`其它类型）。 因为 [软件中的对象应该对于扩展是开放的，但是对于修改是封闭的](https://en.wikipedia.org/wiki/Open/closed_principle)，你应该尽量去使用接口代替类型别名。

另一方面，如果你无法通过接口来描述一个类型并且需要使用联合类型或元组类型，这时通常会使用类型别名。



## declare

```typescript

import { cameCase } from 'lodash'

//  declare 类型声明用于兼容npm未用ts的库 ，从而增加类型注释

// 可以单独安装对应的 声明模块 如： @types/lodash

declare function cameCase(input: string): string

const res = cameCase('hellow')
```







# TS的使用场景



### 数组

```ts
const arr1: Array<number> = [1,2,3]
const arr2: number[] = [1, 2, 3]


function sum (...args: number[]) {// 使用了ts 就不需要去挨个判断是否为数字类型  ts会直接报错
    return args.reduce((pre, cur) => pre + cur, 0) 
}

sum(1,2)
```



### 类型断言

```ts
const nums = [100, 1100, 251]

// res可能是number 或者 undefind(没找到会返回undefind)
const res = nums.find(item => item > 0) 

const a  = res * res 

const result1 = a as number  // 因为自身已明确 nums都为数字 所以可以自己进行断言

const result2 = <number>a // jsx 无法使用
```



### 接口

```ts
interface Post {
    title: string
    content: string
    subTitle?: string
    readonly footer: string
}

function demo(post: Post) {
    console.log(post.title)
    console.log(post.content)
}

const hello: Post = {
    title: 'wfwef',
    content: 'wfwef',
    footer: 'wfwef',
}

//  hello.footer = 'wefwef'  只读无法修改error


interface Cache {
    [prop: string]: string
}
const cache: Cache = {
    a: 'sfsfs'
}





// --------------
interface List {
    id: number,
    name: string
}

interface Res {
    data: List[]
}

function init(res: Res) {
    res.data.forEach(el => {
        console.log(el.id, el.name)
    });
}

let res = { 
    data: [
        { id: 1, name: 'A', sex: 'male' },
        { id: 2, name: 'B' },
    ]
}

init(res)

init({ 
    data: [
        { id: 1, name: 'A', sex: 'male' },
        { id: 2, name: 'B' },
    ]
} as Res)

init(<Res>{ 
    data: [
        { id: 1, name: 'A', sex: 'male' },
        { id: 2, name: 'B' },
    ]
})
```



### 类

```ts
class Person {
    // name: string = 'init name' // 一般在constructor中初始化数据 （这里直接写在class顶层则为实例属于的简写）

    public name: string // 默认 public (public是ts中的修饰符) 在es6中还有一个static修饰符 表示为 静态属性指的是 Class 本身的属性，即Class.propName
    private age: number
    protected gender: boolean

    constructor(name: string, age: number) {
        this.name = name
        this.age = age
        this.gender = true
    }
    

    sayHi(msg: string): void {
        console.log(this.name)
    }
    
}

const tom = new Person('tom', 18)

// console.log(tom.age) // 属性“age”为私有属性，只能在类“Person”中访问。
// console.log(tom.gender) // 属性“gender”受保护，只能在类“Person”及其子类中访问。

class Student extends Person {
    constructor(name: string, age: number) {
        super(name, age);
        console.log(this.gender) // 可以访问 protected gender
    } 
    // private constructor(name: string, age: number) { // 如果构造函数被 private，则无法实例化，只能通过Static方法内部创建
    //     this.name = name
    //     this.age = age
    //     this.gender = true
    // }

    // static create(name: string, age: number) { // 静态方法创建实例
    //     return new Student(name, age)
    // }
}


// const jack = Student.create('jack', 21)





class A {
    constructor(private _count: number) {
        
    }
    get count() {
        return this._count
    }
    set count(count: number) {
        this._count = count
    }
}
const z = new A(18)
console.log(z.count)

abstract class Gril{ //抽象类表示 继承与他的所有子类必须要有这个skill方法
    abstract skill()
}

class G1 extends Gril {
    skill() {
        
    }
}

```

### 类与接口

```ts
interface Eat {
    eat(food: string): void
}

interface Run {
    run(distance: number): void
}

class PersonA implements Eat, Run {
    eat(food: string): void {
        console.log(`优雅的吃：${food}`)
    }

    run(distance: number): void {
        console.log(`行走：${distance}`)
    }
}

class Animal implements Eat, Run {
    eat(food: string): void {
        console.log(`大口吃：${food}`)
    }

    run(distance: number): void {
        console.log(`四肢跑：${distance}`)
    }
}
```



### 枚举

```ts

const enum PostStatus { // 使用常量类型的枚举  编译后不会影响js代码
    success = 2, // 如果不写默认为0  且每写一个会自动加一
    failed = 3, // 1
    unconnect = 4 // 2
}
enum PostStatus1 { // 这个会多生成js代码
    success,
    failed,
    unconnect
}
const post = { 
    state: PostStatus.success,
    title: 'aaaaa'
}

// 枚举成员类型

enum Char {
    //  常量枚举（编译时运算）
    a,
    b = Char.a, // 现有的引用
    c = 1 + 3, // 表达式计算
    //  计算类型 （编译后运行）
    d = Math.random(),
    e = '123'.length
}

const enum G {
    a = 1,
    b = 2,
}

let g1: G = G.a
console.log(g1)


// -----------  编译后的结果
var PostStatus1;
(function (PostStatus1) {
    PostStatus1[PostStatus1["success"] = 0] = "success";
    PostStatus1[PostStatus1["failed"] = 1] = "failed";
    PostStatus1[PostStatus1["unconnect"] = 2] = "unconnect";
})(PostStatus1 || (PostStatus1 = {}));
var post = {
    state: 2 /* success */,
    title: 'aaaaa'
};
// 枚举成员类型
var Char;
(function (Char) {
    //  常量枚举（编译时运算）
    Char[Char["a"] = 0] = "a";
    Char[Char["b"] = 0] = "b";
    Char[Char["c"] = 4] = "c";
    //  计算类型 （编译后运行）
    Char[Char["d"] = Math.random()] = "d";
    Char[Char["e"] = '123'.length] = "e";
})(Char || (Char = {}));
var g1 = 1 /* a */;
console.log(g1);
```



### 函数

```ts
function func1(a: number, b?: number, ...rest: number[]): string {
    return 'wfwfw'
}

func1(100, 200)

const fun2: (a: number, b: number) => string = function(a: number, b: number): string {
    return 'afewf'
}
```



### 泛型

```ts
// 泛型

export {}

function createNumberArray(length: number, value: number): number[] {
    const arr = Array<number>(length).fill(value)
    return arr
}

//  如果需要一个字符串数组 代码会冗余
function createStringArray(length: number, value: string): string[] {
    const arr = Array<string>(length).fill(value)
    return arr
}

const res = createNumberArray(3, 100) // res => [100, 100, 100]

// 泛型优化
function createArray<T>(length: number, value: T): T[] {
    const arr = Array<T>(length).fill(value)
    return arr
}

const res2 = createArray<string>(3, 'foo')



// 泛型例子1----------
function join<T>(first: T, second: T) {
    return `${first}${second}`
}

// join<string>('wef',1) // err
join<string>('wef','wef')

// 多种泛型
function join2<T, P>(first: T, second: P) {
    return `${first}${second}`
}

join2<string, number>('wef', 1)


// 泛型在数组中
function myArray<T>(arr: T[]) {
    return arr
}

myArray<string>(['wef', 'wf23'])


// 类中使用泛型
class SelectGirl<T> {
    constructor(public girls: T[]) {}
    getGirl(index: number): T {
        return this.girls[index]
    }
}

const selectGirl1 = new SelectGirl<string>(['小红', '大黄'])
console.log(selectGirl1.getGirl(1))


class SelectGirlDemo<T extends string | number> {// 泛型约束  只能从string numver里面选类型
    constructor(public girls: T[]) {}
    getGirl(index: number): T {
        return this.girls[index]
    }
}


// 类中使用泛型继承
interface Girl {
    name: string
}

class SelectGirl2<T extends Girl> {
    constructor(public girls: T[]) {}
    getGirl(index: number): string {
        return this.girls[index].name
    }
}

const selectGirl2 = new SelectGirl2<Girl>([{ name: '小红' }, { name: '大黄' }])
console.log(selectGirl2.getGirl(1))
```



### 类型保护

```ts
interface Waiter {
    work: boolean
    say: () => {}
}

interface Teacher {
    work: boolean
    teach: () => {}
}

function judgeWho(params: Waiter | Teacher) {
    if (params.work) {
        (params as Waiter).say()
    } else {
        (params as Teacher).teach()
    }
}
```

