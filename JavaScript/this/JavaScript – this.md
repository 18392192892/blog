## JavaScript -- this

@(Interview)

今天又遇到了JS的另一大难题 -- **`this`**，`this`用好可以解决许多问题，之前都是然然的，理不清关系。
还是用一篇博客讲清楚他吧

### 前言

#### 什么是`this`?

我所理解的`this`是一个**对象**(也确实是)，只不过这个对象**在不同的情况下它代表的内容是不同的**。

那么给出的定义是这样的
> **this是在执行上下文创建时确定的一个在执行过程中不可更改的变量。**

在别人的博客上看见了一段很有意思的话，理解`this`我觉的恰到好处

> 我们可以像平常说话一样来使用 this。例如：我会说“我妈很不爽，这（this）太糟糕了”，而不会说“我妈很不爽，我妈很不爽这件事太糟糕了”。理解了 this 的上下文，才会理解我们为什么觉得很糟糕。

把这个例子和JS联系起来，你就发现好懂多了，`this`就是一个代词，代表的就是当前的**上下文**，只不过在编程语言中代表的是**执行上下文**

#### 为什么要使用`this`?

使用`this`其实主要的目的还是**复用**，并且是很优雅的复用

比如：

```
function HelloWorld () {
	console.log(`Hello, I am ${this.name}`);
}

let me = {
	name: 'Zhao'
}

let li = {
	name: 'Li'
}

HelloWorld.call(me) // Hello, I am Zhao
HelloWorld.call(li) // Hello, I am Li
```

就像是传入了参数一样，传入了不同的`name`，反回不同的结果，可以在不同的对象中复用函数，不用针对每个对象写一个函数了

#### 怎么使用`this`?

它的语法很简单，就是和调用普通对象一样
```
var name = 10;
console.log(this.name) // 10
```

但是`this`的使用规则复杂且难懂，这也是我们这篇博客的目的，把这个问题讲清楚。

我打算分为**全局上下文**和**函数上下文**来讲

---

### 一丶全局上下文中的`this`

无论是否在严格模式下，在全局执行上下文中(在任何函数体外部)`this`都指代**全局对象**

#### 1. 在浏览器中全局对象为`window`

```
console.log(this === window); // true

var a = 39;
console.log(this.a) // 39
```

> **注意：**在ES5中，**全局变量为全局对象的属性**，但是在ES6中，新的声明方法(`let`,`const`,`class`)声明的变量**不属于全局对象**

```
let a = 40;
console.log(this.a) // undefined
console.log(window.a) // undefined
```

#### 2. 在`node`中全局对象为`global`

在`node`中，全局对象为`global`，但是有一个问题

在最外层访问不到这个全局对象，因为`node`最外层自动获取的是`module.export`

```
this.a = 10;
console.log(global.a); // undefined
console.log(module.exports.a) // 10
```

### 二丶函数上下文中的`this`

函数上下文才是`this`的重中之重，复杂的地方全在这里，需要细细讲一下

在看下面的内容之前，需要牢牢的记住一句话：**在函数内部，`this`的值取决于函数被调用的方式**。注意，这里说的是**调用**


**在函数上下文中，有四种绑定规则**

#### 1. 默认绑定：

什么是默认绑定呢，通过它的名字，我们就能大概知道是什么意思

就是在没有特殊规则的时候，`this`的绑定

**规则：**在非严格模式下，默认绑定的`this`指向全局对象，严格模式下`this`指向`undefined`

```
function foo() {
  console.log(this.a); // this指向全局对象
}
var a = 2;
foo(); // 2
function foo2() {
  "use strict"; // 严格模式this绑定到undefined
  console.log(this.a); 
}
foo2(); // TypeError:a undefined
```
而这里的**严格模式**说的是函数，比如：

```
function foo() {
  console.log(this.a); // foo函数不是严格模式 默认绑定全局对象
}
var a = 2;
function foo2(){
  "use strict";
  foo(); // 严格模式下调用其他函数，不影响默认绑定
}
foo2(); // 2
```

所以说，如果**函数体**不是严格模式，并不影响默认绑定，即使调用的地方是**严格模式**

总结一句话：**决定`this`默认绑定对象的是要执行的函数体是否处于严格模式**，严格指向`undefined`，非严格指向全局对象

其实通常在自己写代码的时候，一般不会像这样混用严格模式和非严格模式的，但是对于这个还是要了解


#### 2. 隐式绑定

**规则：**函数在**调用位置**，是否有上下文对象，如果有，那么`this`就会隐式绑定到这个对象上

```
    function foo() {
      console.log(this.a);
    }
    var a = "Oops, global";
    let obj2 = {
      a: 2,
      foo: foo
    };
    let obj1 = {
      a: 22,
      obj2: obj2
    };
    obj2.foo(); // 2 this指向调用函数的对象
    obj1.obj2.foo(); // 2 this指向最后一层调用函数的对象
```

上面一段代码，在调用`foo`的时候，都处于一个对象的上下文中，也就是通过对象来调用这个函数

这样的调用，`this`会被绑定在**最后一层调用函数**的对象上

为了说明这种绑定方式和函数的定义位置没有什么鸟关系，我们再来看个例子

```
function hello() {
	console.log(this);
}

let newOb = {
	fn: hello
}

newOb.fn(); // {fn: ƒ}
```
 
函数在全局环境下定义的，但是调用的位置是在对象的上下文，所以`this`绑定的就是这个对象。

##### 隐式丢失
隐式绑定有一个很大的问题，就是**隐式绑定丢失**

对于这个问题，还是先看个例子吧

```
let name = 'window';
let person = {
	name: 'zhao',
	sayName () {
		console.log(this.name);
	}
}

let a = person.sayName;

a(); // window
```
我们把`person`的`sayName`方法赋给了全局变量`a`

然后在执行`a`的时候，并没有执行隐式绑定，而是默认绑定。

这就是**隐式绑定丢失**问题，**因为在函数执行的时候，其实并没有上下文对象，也就是说，此时的`a()`其实是一个不带任何修饰的函数调用，因此应用了默认绑定。**

这类问题还会发生在回调函数中，而且极其隐蔽，

```
let newOb = {
	callback (fn) {
		fn();
	},
	
	hello () {
		console.log(this);
	}
}

newOb.callback(newOb.hello); // window
```
上面的代码给`callback`传入的式函数的引用，调用时也是没有上下文对象，所以为默认绑定

那我们如果非要将`this`绑定到`newOb`对象上呢，也有办法~

就是显式绑定

#### 3. 显式绑定

如果单纯的使用隐式绑定肯定是没有办法得到期望的绑定的，但是幸好我们可以在**某个对象上强制调用函数，从而将`this`绑定在这个对象上**

方法就是再熟悉不过的三个方法了：`call`，`apply`，`bind`

比如我们上面的那个例子

```
let newOb = {
	callback (fn) {
		fn.call(this);
	},
	
	hello () {
		console.log(this);
	}
}
newOb.callback(newOb.hello); // newOb
```
在`callback`方法中，将`fn`调用`call`方法后执行，就可以将`newOb`上下文传入要执行的函数，也就是说`hello`中的`this`指向的是`newOb`

这里的`call`，`apply`和`bind`我会单拎出来写篇博客，这里就不讲他们的语法规则了，这里大家所要知道的就是，在使用这些方法时表示的就是进行了**显式绑定**

再写个显而易见的例子

```
let name  = 'window';
function sayName () {
	console.log(this.name);
}
let person = {
	name: 'zhao'
}
sayName.call(person); // zhao
```

将`sayName`中的`this`强行绑定到`person`对象上，这就是显式绑定

#### 4. `new`绑定

**规则：**当一个函数用作构造函数时（使用new关键字），它的`this`被绑定到正在构造的新对象。

其实我理解的`new`绑定和显式绑定差不多，只不过这个过程，它自己完成了而已

`new`的内部原理我简单提一下，想看详细的再去翻我博客~

在使用`new`的时候，其实就是调用了函数内部的`[[Construct]]`方法，这个方法中有一步，**就是将`this`绑定到新生成的对象上**，怎么绑定的，就是使用了一个内部方法`[[Call]]`。就好像是**显式绑定**在内部自动执行了一样。

看个例子~

```
function Person () {
	this.name = 'zhao';
}
let a = new Person();
console.log(a.name) // zhao
```

使用了`new`以后，成功的将`this`绑定到了新生成的对象上

---

### 三丶`this`的优先级

如果在某个调用位置应用了上面多条规则，如何确定哪条规则生效呢？

优先级的问题只能是实践出真知了

##### 1. 首先，**默认绑定的优先级一定是最低的**，它可以先不管

##### 2. 那么**隐式绑定**和**显式绑定**哪个优先级高呢

```
function hello() {
	console.log(this.name);
} 

let zhao = {
	name: 'zhao',
	hello: hello
}

let li = {
	name: 'li'
}

zhao.hello.call(li); // li
```

上面的代码先调用`zhao.hello`，达到隐式绑定到`zhao`对象的目的，再使用`call`方法达到显式绑定到`li`对象的目的，结果输出的是`li`

经过实验，**显式绑定**的优先级更高，当这两个规则冲突时，优先考虑**显式绑定**。

##### 3. **隐式绑定**和**`new`绑定**

这个结果应该是不言而喻的，因为我刚才说过，`new`绑定其实就是在内部自动执行的**显式绑定**，在内部执行了一遍`call`

不信还是看代码

```
function hello() {
	console.log(this.name);
} 

let zhao = {
	name: 'zhao',
	hello: hello
}

let li = {
	name: 'li'
}

let a = new zhao.hello(); // undefined
```

输出的是`undefined`，`zhao.name`是`zhao`，所以结果是`a.name`

所以**`new`绑定**的优先级更高，当这两个规则冲突时，优先考虑**`new`绑定**

##### 4. **显式绑定**和**`new`绑定**

这两个比较就比较麻烦了，因为无法直接使用`call`和`apply`，会报错，那就必须使用`bind`来比较一下显示绑定和`new`绑定哪个优先级高

首先我们先给一个函数硬绑定
```
function hello() {
	console.log(this.name);
} 

let zhao = {
	name: 'zhao'
}

let li = {
	name: 'li'
}

let helloBind = hello.bind(zhao);
```
然后我们以`helloBind`作为构造函数`new`一个实例
```
let helloNew = new helloBind();
// undefined
```
可以发现执行结果为`undefined`而不是应该显示的`zhao`，这说明在调用`new`的时候，将`bind`指向的`this`进行了修改

那我们可以得出一个结论，`new`绑定的优先级是要高于显式绑定的

##### 5. 结论

经过比较，我们得出这样一个结论：

**`new`绑定 > 显式绑定 > 隐式绑定 > 默认绑定**

这就是`this`优先级的问题，当然`ES6`出来以后又有了一个新的问题

---

### 四丶箭头函数中的`this`

在箭头函数中，`this`的指向不遵循上面的规则，而是有一套自己的规则，MDN是这样描述的：

> 在箭头函数中，`this`与封闭词法上下文的`this`保持一致

意思就是，箭头函数的`this`**不再是调用的时候确定**，而是定义函数的时候就确定了`this`

看个例子：
```
var name = 'li';

var sayName = () => {
	console.log(this.name);
}    

let foo = {
	name: 'zhao',
	say: sayName
}

foo.say(); // li
```

可以看到`foo.say`的执行结果并不是预期的`zhao`，而是`li`，也就是全局的`name`属性，这是为什么？

因为函数`sayName`的词法上下文正是全局上下文，所以它的`this`在定义的时候就已经确定指向`window`了，尽管后面我们想通过隐式绑定让`this`绑定到`foo`上，可是结果发现不行

那如果我们通过显式绑定能否绑定成功呢
```
var name = 'li';

var sayName = () => {
	console.log(this.name);
}    

let foo = {
	name: 'zhao',
	say: sayName
}

sayName.call(foo); // li
```

可以看见，还是不行，更加确定箭头函数的`this`是在词法阶段绑定成功的

再来看一个例子

```
var name = 'li';

function foo() {
	this.name = 'zhao',
	this.say = () => {
		console.log(this.name);
	}
}

let a = new foo();
a.say(); // zhao
```
这个就按照预期输出了`zhao`

因为`say`函数在定义时，`this`指向的正是`new`创建的新对象，所以可以绑定

如果还是不好理解，我们将上面的代码变一下，就能看懂了

```
var name = 'li';

function foo() {
	var self = this;
	this.name = 'zhao';
	this.say = function () {
		console.log(self.name);
	}
}

let a = new foo();
a.say(); // zhao
```
通过一个变量`self`将`this`保存，然后利用闭包将`self`传给函数，最后输出。可以这样理解箭头函数中的`this`

有了这个规定，在回调函数中使用箭头函数还是一个不错的选择，完美解决了`this`被改的问题

---

#####参考
- <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this">https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this</a>
- <a href="https://juejin.im/post/5b3715def265da59af40a630">https://juejin.im/post/5b3715def265da59af40a630</a>
- <a href="https://juejin.im/entry/58fb0d7ba22b9d0065968314">https://juejin.im/entry/58fb0d7ba22b9d0065968314</a>
- <a href="https://juejin.im/post/59e066d551882578c3411908">https://juejin.im/post/59e066d551882578c3411908</a>
- <a href="https://juejin.im/post/59748cbb6fb9a06bb21ae36d">https://juejin.im/post/59748cbb6fb9a06bb21ae36d</a>

---