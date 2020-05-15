

# <p align="center">js函数—隐形参数this</p>



## 前言

　　this是函数中的隐形参数，它绑定的值取决于函数的调用位置。

## this的定义

　　《你不知道的js》中是这样说的：是函数体内的隐式参数，this就是记录函数调用上下文的一个属性。可以在函数体中使用this引用函数的调用上下文。this的绑定关注的是函数的调用位置。

　　调用位置：是函数在调用时的位置，区别于函数声明时的位置。

## this的绑定规则

　　this绑定方式取决于函数的调用位置，根据不同的场景，有四中绑定方式（默认、隐式、显示、构造调用中的绑定）。多种情况同时存在时，依靠的是这四中规则的优先级别采用相应的绑定。

　　### 默认绑定

　　　　当函数不使用任何修饰时直接调用。无论在哪里调用，它指向的都是全局对象（浏览器环境下指向的是window）。注：严格模式下this会绑定到undefined。

```
function run(){
    console.log(this); //window
    console.log("running");
    function speak(){
        console.log("speak");
        console.log(this);  // window
    }
    return speak;
}
run()();
```
　　
  ### 隐形绑定

　　　　当函数拥有上下文对象的时候，隐式绑定规则会把函数调用中的this绑定到这个上下文对象。

```
// 对象的属性不管以任何形式拥有函数的引用时，通过对象属性调用这个函数时，这个函数当前调用上下文就是这个对象。
function foo(){
    console.log(this.a);
}
var obj = {
    a:1,
    foo:foo
}
obj.foo();  // 1
// 直接上下文：只有最后一层调用影响this的绑定
function foo(){
　　console.log(this.a);
}
var obj1 = {
　　a:1,
　　foo:foo
}
var obj2 = {
　　a:2,
　　obj1:obj1
}
obj2.obj1.foo(); // 1
```
　　
  ### 显示绑定

　　　　直接指定this的绑定对象就叫做显示绑定。js中所有函数都从Function.prototype上继承了call和apply这两个方法。他们都是用来显示指定函数执行时其中的this的。

　　　　call和apply的异同：

　　　　　　目的相同：都用来指定函数执行时其中的this。

　　　　　　参数个数和类型不同：apply接收两个参数，第一个是本次执行要绑定在函数this上的值，第二个是一个数组。call接收的是一个参数列表，参数需要一个一个的传，他会把第一个参数绑定到本次执行函数的this上去。

　　　　　　参数的处理：apply会把第二个参数（Array类型）展开后依次传到本次执行的函数中去。call是取第一绑定到this，再把后面的参数依次传入本次执行函数。

```
注：argument是函数体中的另一个隐式参数。可以在函数体内拿到函数运行时，接收到的实参。它是一个伪数组，其中的元素和实参一 一对应
// 以下输出结果出自chrome
function test(){
    console.log(test.arguments);
}
// apply 数组[1,2,3]被展开分成三个参数 依次传入 test。
test.apply({},[1,2,3]); // Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
// call 数组[1,2,3] 作为一个参数传进了test中
test.call({},[1,2,3]); // Arguments [Array(3), callee: ƒ, Symbol(Symbol.iterator): ƒ]
```
　　
  ### 常用操作
```
// 使用call调用数组map方法。 
var arr = [1,2,3,4];
Array.prototype.map.call(arr,function(item){
    console.log(item); // 分别输出 1 2 3 4
})
```

### new绑定
　　使用new 操作符调用函数叫做构造调用。函数做构造调用时，其中this的绑定。函数做构造调用时其中有四个默认操作。

　　1. 创建一个新的对象。

　　2. 这个对象会被执行原型链接（添加到构造函数的原型链的末尾）。

　　3. 把当前正在做构造调用的函数中的this绑定到新创建的对象。

　　4. 如果函数没有显式的返回其他对象，那么默认返回这个新建的对象。

```
// 对foo进行构造调用创建一个新的对象
function foo(a){
    this.a = a;
}
var bar = new foo(2);
console.log(bar.a);
```

## js中的构造函数

　　　　1. 只是使用new操作符调用的普通函数。

　　　　2. 不属于某个类，也不会实例化某个类。

　　　　3. 所有函数使用new 进行调用时都被称作构造调用。

　　　　4. js中不存在面向类语言意义上的构造函数，只有基于new操作符号的构造调用。
