## JavaScript -- new

@(Interview)

`new`操作符我们用的算是很多了，创建对象经常会用到它，今天把他的本质给大家好好讲一下

### 前言

`new`操作符其实是一种**语法糖**，它的实质其实是用来帮助我们完成一些操作，也就是说我们用了`new`就可以少写几行代码，具体用法和实现我们接下来来看。


### 一丶基本用法

> `new constructor([arguments])`

参数：
1. `constructor`：一个指定对象实例的类型的类或函数
2. `arguments`：一个用来被`constructor`调用的参数列表

简单的例子：
```
function Person() {
}

let a = new Person();
```


> **注意：`constructor`必须有一个名为`[[Construct]]`的内部方法，否则会抛出异常**

比如，下面这种就会报错
```
let Str = "zhao";
let a = new Str; // Str is not a constructor
```
在平时写代码的时候，基本都是用来创建对象的，所以一般不会有这种错误

---

### 二丶原理

`new`操作符调用的时候到底发生了什么，这得去问问这个内部方法`[[Construct]]`了，所有的实现都是这个方法的功劳

首先我们要明白，`[[Construct]]`是函数内部的方法，所以在函数内执行调用

`[[Construct]]`在调用了`new`操作符之后会自动在函数内部执行，它分为如下几步

1. 首先在函数内部创建一个**原生对象**，即`{}`或`new Object`
2. 然后将它的内部属性`[[Class]]`设置为`Object`
3. 得到该函数的`prototype`属性，然后将新建原生对象内部的`[[Prototype]]`属性指向函数的`Prototype`，**绑定原型**
4. 以该对象作为`this`对象，再将传入函数的参数传递过去，通过该构造函数的`[[Call]]`内部方法执行调用一遍该构造函数，也就是**绑定`this`**
5. 将这个对象返回

这就是`new`操作符所做的事情，它就是帮助我们完成了上述工作，所以很像是一种语法糖

如果没有这个操作符，我们也可以自己来实现下

---

### 三丶实现

我们先写一个构造函数

```
function Person(name, age) {
	this.name = name;
	this.age = age;
}
``` 

现在给它写一个`construct`方法
```
Person.construct = function () {
	// 声明一个对象，并将自动内置属性Class属性设置为Object
	let object = {};
	// 保存构造函数
	let Construct = Person;
	// 将构造函数的原型赋给新对象的`[[Prototype]]`属性
	object.__proto__ = Construct.prototype;
	// 绑定`this`到新对象
	Construct.apply(object, arguments);
	return object;
}
```
这样就写好了，我们来试一下可以用不

```
function Person(name, age) {
	this.name = name;
	this.age = age;
}

Person.construct = function () {
	// 声明一个对象，并将自动内置属性Class属性设置为Object
	let object = {};
	// 保存构造函数
	let Construct = Person;
	// 将构造函数的原型赋给新对象的`[[Prototype]]`属性
	object.__proto__ = Construct.prototype;
	// 绑定`this`到新对象
	Construct.apply(object, arguments);
	return object;
}

let obj = Person.construct('zhao', 10);
console.log(obj); // Person {name: 'zhao', age: 10}
```
和使用`new`达到的效果一毛一样

但是这里有个问题，**因为有时候构造函数会返回一个对象**(一般不会)

所以要做一些小处理

```
Person.construct = function () {
	// 声明一个对象，并将自动内置属性Class属性设置为Object
	let object = {};
	// 保存构造函数
	let Construct = Person;
	// 将构造函数的原型赋给新对象的`[[Prototype]]`属性
	object.__proto__ = Construct.prototype;
	// 绑定`this`到新对象
	let ret = Construct.apply(object, arguments);
	return typeof ret === 'object' ? ret : object;
}
```

这样就算是比较完美的实现了`new`

如果还是不理解，推荐大家去看 <a href="https://zhuanlan.zhihu.com/p/23987456?utm_medium=social&utm_source=wechat_session">这篇 </a>，讲的很清楚，一看就懂

---

#####参考
- <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new">https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new</a>
- <a href="https://juejin.im/post/590a99015c497d005852cf26">https://juejin.im/post/590a99015c497d005852cf26</a>
- <a href="http://www.cnblogs.com/aaronjs/archive/2012/07/04/2575570.html">http://www.cnblogs.com/aaronjs/archive/2012/07/04/2575570.html</a>

---
