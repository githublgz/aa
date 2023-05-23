---
title: Typescript基础总结
cover: false
top: false
toc: false
mathjax: false
tags:
  - typescript
categories:
  - 前端
abbrlink: 23375
date: 2021-08-03 21:18:43
author:
img:
coverImg:
password:
summary:
keywords:
---

## 一、typescript 安装

```
npm i typescript -g
```

> 全局安装完成后，我们新建一个`hello.ts`的`ts`文件

```
// hello.ts内容
let a = "poet"
```

> 接下来我们在命令行输入`tsc hello.ts`来编译这个`ts`文件，然后会在同级目录生成一个编译好了的`hello.js`文件

```
// hello.js内容
var = "poet"
```

> 那么我们每次都要输`tsc hello.ts`命令来编译，这样很麻烦，能否让它自动编译？答案是可以的，使用`vscode`来开发，需要配置一下`vscode`就可以。

> 首先我们在命令行执行`tsc --init`来生成配置文件，然后我们在目录下看到生成了一个`tsconfig.json`文件

![img](https://poetries1.gitee.io/img-repo/2019/10/545.png)

> 这个`json`文件里有很多选项

- `target`是选择编译到什么语法
- `module`则是模块类型
- `outDir`则是输出目录，可以指定这个参数到指定目录

> 更多细节 https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/tsconfig.json.html

> 接下来我们需要开启监控了，在`vscode`任务栏中

![img](https://poetries1.gitee.io/img-repo/2019/10/546.png)

**Typescript在线编辑器**

> 建议使用在线编辑器练习 http://www.typescriptlang.org/play/index.html

## 二、数据类型

> `js`是弱类型语言，强弱类语言有什么区别呢？`typescript`最大的优点就是类型检查，可以帮你检查你定义的类型和赋值的类型。

### 2.1 布尔类型boolean

```
// 在js中，定义isFlag为true，为布尔类型boolean
let isFlag = true;
// 但是我们也可以重新给它赋值为字符串
isFlag = "hello swr";

// 在ts中，定义isFlag为true，为布尔类型boolean
// 在变量名后加冒号和类型，如  :boolean
let isFlag:boolean = true
// 重新赋值到字符串类型会报错
isFlag = "hello swr" 

// 在java中，一般是这样定义，要写变量名也要写类型名
// int a = 10; 
// string name = "poetries"
```

### 2.2 数字类型number

```
let age:number = 28;
age = 29;
```

### 2.3 字符串类型string

```
let name:string = "poetries"
name = "iamswr"
```

> 以上`boolean`、`number`、`string`类型有个共性，就是可以通过`typeof`来获取到是什么类型，是基本数据类型

那么复杂的数据类型是怎么处理的呢？

### 2.4 数组 Array

```
// 数组
// 这是一个字符串数组，只能往里面放字符串，写别的类型会报错
let persion:string[] = ['poetries', 'jing']
// 另一个写法 
let persions:Array<string> = ['poetries', 'jing']

// 如果数组里放对象呢
let persionObject:Array<object> = [{name:'poetries',age:22}]
let persionObjects:object[] = [{name:'poetries',age:22}]

// 在数组中放string、number、boolean、object
let arr:Array<number|object|string|boolean> = [22, 'test', true, {name:'poetries'}]

// 数组中放什么都可以
let arrAny:Array<any> = ['test',12,false]
```

### 2.5 元组类型tuple

- 什么是元组类型？其实元组是数组的一种。
- 有点类似解构赋值，但是又不完全是解构赋值，比如元组类型必须一一对应上
- 元组类型是一个不可变的数组，长度、类型是不可变的

```
// 元组类型tuple
// 什么是元组类型？其实元组是数组的一种
let per :[string,number,object] = ['poetries',22,{love: 'coding'}]
```

### 2.6 枚举类型enum

> 什么是枚举？枚举有点类似一一列举，一个一个数出来。一般用于值是某几个固定的值

```
// 枚举类型enum

enum sex {
    BOY='男孩',
    GIRL='女孩'
}
console.log(sex)
// 转化为es5语法
// 我们顺便看看实现的原理

var sex;
(function (sex) {
// 首先这里是一个自执行函数
// 并且把sex定义为对象，传参进给自执行函数
// 然后给sex对象添加属性并且赋值
    sex["BOY"] = "\u7537\u5B69";
    sex["GIRL"] = "\u5973\u5B69";
})(sex || (sex = {}));
console.log(sex);
```

> 比如我们实际项目中，特别是商城类，订单会存在很多状态流转，那么非常适合用枚举

```
enum orderStatus {
    WAIT_FOR_PAY = "待支付",
    UNDELIVERED = "完成支付，待发货",
    DELIVERED = "已发货",
    COMPLETED = "已确认收货"
}
```

> 到这里，我们会有一个疑虑，为什么我们不这样写呢？

```
let orderStatus2 = {
    WAIT_FOR_PAY : "待支付",
    ...
}
```

> 如果我们直接写对象的键值对方式，是可以在外部修改这个值的，而我们通过`enum`则不能修改定义好的值了

### 2.7 任意类型 any

> `any`有好处也有坏处，特别是前端，很多时候写类型的时候，几乎分不清楚类型，任意去写，写起来很爽，但是对于后续的重构、迭代等是非常不友好的，会暴露出很多问题，某种程度来说，`any`类型就是放弃了类型检查了

比如我们有这样一个场景，就是需要获取某一个dom节点

```
let btn = document.getElementById('btn');
btn.style.color = "blue";
```

> 此时我们发现在`ts`中会报错

![img](https://poetries1.gitee.io/img-repo/2019/10/547.png)

- 因为我们取这个`dom`节点，有可能取到，也有可能没取到，当没取到的时候，相当于是`null`，是没有`style`这个属性的。
- 那么我们可以给它添加一个类型为`any`

```
// 添加一个any类型，此时就不会报错了，但是也相当于放弃了类型检查了
let btn:any = document.getElementById('btn');
btn.style.color = "blue";
// 可以赋值任何类型的值
// 跟以前我们var let声明的一模一样的
let person:any = "poetries"
person = 22
```

### 2.8 null undefined类型

```
// (string | number | null | undefined) 相当于这几种类型
// 是 string 或 number 或 null 或 undefined

let str:(string | number | null | undefined)

str = 'poetries'
str = 28
str = null 
str = undefined
```

### 2.9 void类型

> `void`表示没有任何类型，一般是定义函数没有返回值

```
// void 不能再函数里写return
// 怎么理解叫没有返回值呢？此时我们给函数return一个值
function say(name:string):void{
    console.log('hello:', name)
    // return "ok" 会报错
    return undefined;
    return //不会报错
}
say('poetries')

// 返回一个字符串类型
function say1(name:string):string {
    return 'ok'
}
```

### 2.10 never类型

> 这个用得很少，一般是用于抛出异常

```
function error(message:string):never {
    throw new Error(message)
}
error('errorMsg')
```

### 2.11 我们要搞明白any、never、void

- `any`是任意的值
- `void`是不能有任何值
- `never`永远不会有返回值

> `any`比较好理解，就是任何值都可以

```
let str:any = "hello poetries"
str = 28
str = true
```

> `void`不能有任何值(返回值)

```
function say():void {
  
}
```

> `never`则不好理解，什么叫永远不会有返回值？

```
// 除了上面举例的抛出异常以外，我们看一下这个例子
// 这个loop函数，一旦开始执行，就永远不会结束
// 可以看出在while中，是死循环，永远都不会有返回值，包括undefined

function loop():never {
    while(true){
        console.log("陷入死循环啦")
    }
}

loop()

// 包括比如JSON.parse也是使用这种 never | any
function parse(str:string):(never | any){
    return JSON.parse(str)
}
// 首先在正常情况下，我们传一个JSON格式的字符串，是可以正常得到一个JSON对象的
let json = parse('{"name":"poetries"}')
// 但是有时候，传进去的不一定是JSON格式的字符串，那么就会抛出异常
// 此时就需要never了
let json = parse("iamswr")
```

> 也就是说，当一个函数执行的时候，被抛出异常打断了，导致没有返回值或者该函数是一个死循环，永远没有返回值，这样叫做永远不会有返回值。

实际开发中，是`never`和联合类型来一起用，比如

```
function say():(never | string) {
  return "ok"
}
```

## 三、函数

### 3.1 函数定义

```
function sayHello(name:string):void {
    
}
```

### 3.2 函数参数处理

```
// 函数是这样定义的
// 形参和实参一一对应，完全一样
function sayHello(name:string,age:number):void {
    console.log('hello', name, age)
}
sayHello('poetries',22)

// 形参和实参要完全一样，如想不一样，则需要配置可选参数，可选参数放在后面
// 可选参数，用 ？ 处理，只能放在后面
function sayHelloToYou(name:string,age?:number):void {
    console.log('hello', name, age)
}
sayHelloToYou('poetries')

// 那么如何设置默认参数呢？

function ajax(url:string,method:string = 'GET') {
    console.log(url, method)
}

// 那么如何设置剩余参数呢？可以利用扩展运算符

function sum(...args:Array<number>):number {
    return eval(args.join("+"))
}
let total:number = sum(1,2,3,4,5)
console.log(total)
```

### 3.3 函数重载

```
// 那么如何实现函数重载呢？函数重载是java中非常有名的，在java中函数的重载，是指两个或者两个以上的同名函数，参数的个数和类型不一样

// 比如我们现在有两个同名函数
// function eating(name:string) {
    
// }
// function eating(name:string,age:number) {
    
// }
// 那么我想达到一个效果
// 当我传参数name时，执行name:string这个函数
// 当我传参数name和age时，执行name:string,age:number这个函数
// 此时该怎么办？

// 接下来看一下typescript中的函数重载

// 首先声明两个函数名一样的函数
function eating(name: string):void;
function eating(name: number):void;

function eating(name:any): void {
    console.log(name)
}

eating("hello poetries")
eating(22)

// 在typescript中主要体现是同一个同名函数提供多个函数类型定义，函数实际上就只有一个，就是拥有函数体那个，如果想根据传入值类型的不一样执行不同逻辑，则需要在这个函数里面进行一个类型判断。

// 那么这个函数重载有什么作用呢？其实在ts中，函数重载只是用来限制参数的个数和类型，用来检查类型的，而且重载不能拆开几个函数，这一点和java的处理是不一样的，需要注意。
```

## 四、类

### 4.1 定义一个类

> 如何定义一个类？

```
// ts 写法
// 跟es6非常像 没有太大区别
class Persion {
    // 这里声明的变量 是实例上的属性
    name: string;
    age:number;

    constructor(name: string, age: number){
        // this.name和this.age 必须先在前面声明好类型
        // name: string
        // age: number
        this.name = name;
        this.age = age;
    }
    // 原型方法
    say():string {
        return 'hello poetries'
    }
}

let p = new Persion('poetries', 22)
// 那么转为es5呢？

var Persion = /** @class */ (function () {
    function Persion(name, age) {
        // this.name和this.age 必须先在前面声明好类型
        // name: string
        // age: number
        this.name = name;
        this.age = age;
    }
    // 原型方法
    Persion.prototype.say = function () {
        return 'hello poetries';
    };
    return Persion;
}());
var p = new Persion('poetries', 22);
```

### 4.2 类的继承

```
// 和es6也是差不多
class Parent {
    name: string;
    age: number;
    constructor(name:string, age: number){
        this.name = name;
        this.age = age;
    }
    say():string{
        return 'hello poetries'
    }
}

class Child extends Parent {
    childName: string;
    constructor(name: string,age:number,childName:string) {
        super(name,age)
        this.childName = childName
    }
    childSay():string {
        return this.childName
    }
}
let child = new Child('poetries', 22, '静观流叶')
console.log(child)
```

### 4.3 类的修饰符

- `public`公开的，可以供自己、子类以及其它类访问
- `protected`受保护的，可以供自己、子类访问，但是其他就访问不了
- `private`私有的，只有自己访问，而子类、其他都访问不了

```
class Parents {
    public name:string;
    protected age:number;
    private money:number;

   // 简写
   // constructor(public name:string,protected age:number,private money:number)

   constructor(name: string, age:number,money:number) {
       this.name = name;
       this.age = age;
       this.money = money;
   }
   getName():string {
       return this.name
   }
   getAge():number{
       return this.age
   }
   getMoney():number{
       return this.money
   }
}
let pare = new Parents('poetries', 22, 3000)
console.log(pare.name)
// console.log(pare.age)  报错
// console.log(pare.money) 报错
```

### 4.4 静态属性、静态方法

跟`es6`差不多

```
class Person2 {
    // 类的静态属性
    static name1 = 'poetries'

    // 类的静态方法
    static say() {
        console.log('hello poetries')
    }
}
let per2 = new Person2()
Person2.say() // hello poetries
// per2.say() 报错
```

### 4.5 抽象类

- 抽象类和方法，有点类似抽取共性出来，但是又不是具体化，比如说，世界上的动物都需要吃东西，那么会把吃东西这个行为，抽象出来
- 如果子类继承的是一个抽象类，子类必须实现父类里的抽象方法，不然的话不能实例化，会报错

```
/ 关键字 abstract抽象
// 定义抽象类

abstract class Animal {
    // 实际上是使用了public修饰符
    // 如果添加private修饰符会报错
    abstract eat():void;
}

// 需要注意的是这个Animal是不能实例化的
// let animal = new Animal() // 报错

// // 抽象类的抽象方法，意思就是，需要在继承这个抽象类的子类中
// 实现这个抽象方法，不然会报错
// 报错，因为在子类中没有实现eat抽象方法
// class Person4 extends Animal{
//     test(){
//         console.log("吃米饭")
//     }
// }

// Dog类继承Animal类后并且实现了抽象方法eat，所以不会报错
class Dog extends Animal{
    eat(){
        console.log("吃骨头")
    }
}
```

## 五、接口

> 这里的接口，主要是一种规范，规范某些类必须遵守规范，和抽象类有点类似，但是不局限于类，还有属性、函数等

### 5.1 接口规范对象

```
//假设我们需要获取用户信息
// 我们通过这样的方式 规范必须传name和age的值
function getUserInfo(user:{name:string,age:number}) {
    console.log(user.name,user.age)
}
getUserInfo({name: 'poetries', age: 22})

// 这样看挺完美的， 那么问题就出现了，如果我另外还有一个方法，也是需要这个规范呢？

function getUserInfo1(user:{name:string,age:number}){
    console.log(`${user.name} ${user.age}`)
}
function getInfo(user:{name:string,age:number}){
    console.log(`${user.name} ${user.age}`)
}
getUserInfo1({name:"poetries",age:22})
getInfo({name:"poetries",age:22})

// 可以看出，函数getUserInfo和getInfo都遵循同一个规范，那么我们有办法对这个规范复用吗？

// 首先把需要复用的规范，写到接口 关键字interface
interface infoInterface {
    name: string,
    age: number;
}
// 然后把这个接口 替换到我们需要复用的地方
function getUserInfo2(user:infoInterface) {
    console.log(user.name,user.age)
}
function getInfo2(user:infoInterface) {
    console.log(user.name,user.age)
}

getUserInfo2({name:"poetries",age:22})
getInfo2({name:"poetries",age:22})

// 那么有些参数可传可不传，该怎么处理呢？

interface infoInterface2{
    name: string;
    age: number;
    city?:string;
}
function getUserInfo3(user:infoInterface2){
    console.log(`${user.name} ${user.age} ${user.city}`)
}
function getInfo3(user:infoInterface){
    console.log(`${user.name} ${user.age}`)
}
getUserInfo3({name:"poetries",age:22,city:"深圳"})
getInfo3({name:"iamswr",age:22})
```

### 5.2 接口规范函数

```
// 对一个函数的参数和返回值进行规范
interface mytotal {
    // 左侧是函数的参数，右侧是函数的返回类型
    (a:number,b:number):number;
}

let totalSum:mytotal = function(a:number,b:number):number {
    return a + b
}

console.log(totalSum(10, 20))
```

### 5.3 接口规范数组

```
interface userInterface {
    // index为数组索引 类型是number
    // 右边是数组里为字符串的数组成员
    [index: number]: string;
}
let arrTest: userInterface = ['poetries', '静观流叶']

console.log(arrTest)
```

### 5.4 接口规范类

> 这个比较重要，因为写`react`的时候会经常使用到类

```
// 首先实现一个接口
interface Animal2 {
    // 这个类必须有name
    name:string;

    // 这个类必须有eat方法
    eat(any:string):void;
}

// 关键字implements实现
// 因为接口是抽象的，需要通过子类是实现它

class Person6 implements Animal2 {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    eat(any:string):void {
        console.log(`吃`+any)
    }
}

// 如果想遵循多个接口

interface Animal3 {
    name: string;
    eat(any: string):void;
}
// 新增一个接口
interface Animal4 {
    sleep():void;
}
// 可以在implements后面通过逗号添加和java一样
class Person7 implements Animal3,Animal4 {
    name: string;
    constructor(name:string){
        this.name = name;
    }
    eat(any:string) {
        console.log(`吃`+any)
    }
    sleep() {
        console.log('睡觉')
    }
}
```

### 5.5 接口继承接口

```
interface Animal5{
    name:string;
    eat(any:string):void;
}
// 像类一样 通过extends继承
interface Animal6 extends Animal5 {
    sleep():void;
}
// 因为Animal6类继承了Animal5
// 所以这里遵循Animal6就相当于把Animal5也继承了

class Person8 implements Animal2 {
    name: string;
    constructor(name:string) {
        this.name = name;
    }
    eat(any:string):void{
        console.log(`吃${any}`)
    }
    sleep(){
        console.log('睡觉')
    }
}
```

## 六、泛型

### 6.1 函数的泛型

> 泛型可以支持不特定的数据类型，什么叫不特定呢？比如我们有一个方法，里面接收参数，但是参数类型我们是不知道，但是这个类型在方法里面很多地方会用到，参数和返回值要保持一致性

```
// 假设我们有一个需求，我们不知道函数接收什么类型的参数，也不知道返回值的类型
// 而我们又需要传进去的参数类型和返回值的类型保持一致，那么我们就需要用到泛型

// <T>的意思是泛型，即generic type
// 可以看出value的类型也为T，返回值的类型也为T
function deal<T>(value:T):T{
    return value
}
// 下面的<string>、<number>实际上用的时候再传给上面的<T>
console.log(deal<string>("poetries"))
console.log(deal<number>(22))
```

> 实际上，泛型用得还是比较少，主要是看类的泛型是如何使用的

### 6.2 类的泛型

```
class MyMath<T> {
    // 定义一个私有属性

    private arr: T[] = []

    // 规定传参类型
    add(value: T) {
        this.arr.push(value)
    }
}
// 这里规定了类型为number
// 相当于把T替换为number

let mymath = new MyMath<number>()
mymath.add(1)
mymath.add(2)
mymath.add(3)
```

**有了接口为什么还需要抽象类？**

> 接口里面只能放定义，抽象类里面可以放普通类、普通类的方法、定义抽象的东西。