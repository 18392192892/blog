## JavaScript -- 数据类型检测

@(Interview)

在说数据类型检测前，我们当然要先了解一下数据类型了~

JavaScript里有许多数据类型，大概分一下类

首先是**基本类型**，有6种
- Number
- String
- Boolean
- Null
- Undefined
- Symbol (ES6新定义)

然后加一种**引用类型**
- Object

`Array`，`Function`，`Date`这些都可以归结为`Object`

我们就简单说一下这些类型，如果想要知道详细的类型介绍可以去看我的另一篇博客

那对于这么多的类型，在代码中该如何检测他们的类型呢，我将介绍4中类型检测的方法，每种方法都有它的优缺点和适用的时候，在代码实战中需要大家自己判断什么时候该用哪一种类型判断方法

### 一丶Typeof

typeof很好用，也有很大的缺点，用过的人都懂~

#### 1. 基本介绍

首先我们需要明确一个问题，`typeof`是一个**一元运算符**，和`+,-,*,/`一样，而不是一个函数，它的使用方法如下

> typeof a 或者 typeof (a)

这里的括号是**可选的**，带了括号经常给人一种错觉，认为是一个函数，其实它是一个操作符，所以我用的时候一般不带括号

> **但需要注意的是，因为`typeof`的优先级较高，所以语法中需要带括号**。比如
> ```
> var iData = 99;
> typeof iData + ' Wisen'; // 这样只会返回`iData`的类型，number
> typeof (iData + ' Wisen'); // 这样返回的是`iData`+' Wisen'的类型，string
> ```

那么对于不同的类型，`typeof`会返回不同的**字符串**，可以参照下表

| 类型             |     结果      | 
| :----------- | :-------------     |
| Undefined    |   "undefined" |
| Null         |   "object" |
| Boolean      |   "boolean" |
| Number       |   "number" |
| String       |   "string" |
| Symbol（ECMAScript 6 新增）       |   "symbol" |
| 函数对象       |   "function" |
| 任何其他对象    |   "object" |

可以看到返回的都是字符串，通过这个表格我们就可以很明显的发现了一个问题，`Null`的类型竟然是`object`？

这明显不是一个科学的答案，那到底是什么导致了这样的结果，我们后面会说到

再来看几个例子

```
// Numbers
typeof 37 === 'number';
typeof 3.14 === 'number';
// string
typeof "" === 'string';
typeof "bla" === 'string';
typeof (typeof 1) === 'string'; // typeof总是返回一个字符串
// Booleans
typeof true === 'boolean';
typeof false === 'boolean';
// Symbols
typeof Symbol() === 'symbol';
typeof Symbol('foo') === 'symbol';
// Undefined
typeof undefined === 'undefined';
typeof a === 'undefined';
// Object
typeof {a:1} === 'object';
typeof [1, 2, 4] === 'object';
typeof new Date() === 'object';
// 函数
typeof function(){} === 'function';
typeof class C{} === 'function'
typeof new Function() === 'function';
// 基本包装类型
typeof new Boolean(true) === 'object';
typeof new Number(1) === 'object';
typeof new String("abc") === 'object';
```
那么，挺好用的，对于基本类型的判别都准确无误。

但是又出现了一些问题，对于**对象具体类型**是无法判别的，只能返回一个`object`，而不能更细致的告诉你到底是什么对象

#### 2. 优点和缺陷

对于`typeof`有一个很大的优点就是**简单易用，好理解**，可以很快上手

但是缺陷也是非常多，我们来细说一下

##### (1). null
首先第一个问题，`null`在被`typeof`检测时返回的是一个`object`，这显然是不正确的。

**原理是这样的：**不同的对象在底层都表示为二进制，在 JavaScript 中二进制前三位都为 0 的话会被判断为 `object` 类型，而 `null` 的二进制表示是全部为 0 ，所以执行 `typeof` 时会错误的返回 `object`

##### (2). 无法检测对象的具体类型

通过上方的代码，我们也很容易发现，`typeof`在使用的时候是无法判别`Array`，`Date`这样的类型，并且用`new`操作符声明的**基本包装类型**也是无法判断的，统一会返回`object`

##### (3). 暂时性死区（删除这条）

这个问题在 ES6 之前是不存在的，在之前，`typeof`总是能保证为任何操作数返回一个字符串，换句话说，`typeof`在之前是一个绝对安全的操作符，绝不会报错

哪怕是使用一个为声明的变量
```
typeof a // "undefined"
```
这样也不会报错，而是返回一个字符串`undefine`

然而现在有了`let`和`const`两个新的声明变量的方法，导致了**暂时性死区**这样的问题，在同一个作用域中，如果没有声明，就使用了`typeof`，会导致报错

#### 3. 适用环境

`typeof`适合用在判断**基本数据类型**，如果确定传进来的是基本数据类型，那我们用`typeof`是绝对够用了，对于`null`，我们提出一种解决方法，就是使用`!`
```
function isNull(object) {
	if (!object && typeof object === 'object') {
		return 'null';
	}
}
```
这样就能很轻松的判断`null`了，

那对于`object`这样的复杂类型，该怎么判断呢，下面介绍一个强大的方法

---

### 二丶Object.prototype.toString()

每个对象都有一个`toString()`方法，可以用来检测对象类型

#### 1. 基本介绍

可以通过`toString()`来获取每个对象的类型，为了每个对象都能通过`Object.prototype.toString()`来检测，需要使用函数的`call`或`apply`方法。
使用方法如下：
> `Object.prototype.toString.call(null) // [object Null]`

很完美的解决了`typeof`的问题，它的原理是这样的

**所有`typeof`返回值为”object“的对象都有一个内置属性：`[[Class]]`，Object.prototype.toString()获取到这个内置属性，然后拼接成`'[object' + [[Class]] + ']'` 这样的字符串并返回，利用这个方法，再配合`call`来获取任何值的数据类型**

理论上这个方法，可以判断所有类型的**内置对象**，只要这种方法可以识别内置对象中的内置属性`[[Class]]`，就可以判断它的类型，可以说是非常强大，我们简单的举几个例子吧

```
// 以下是14种：
var number = 1;          // [object Number]
var string = '123';      // [object String]
var boolean = true;      // [object Boolean]
var und = undefined;     // [object Undefined]
var nul = null;          // [object Null]
var obj = {a: 1}         // [object Object]
var array = [1, 2, 3];   // [object Array]
var date = new Date();   // [object Date]
var error = new Error(); // [object Error]
var reg = /a/g;          // [object RegExp]
var promise = new Promise((resolve, reject) => {
	resolve();
})                      // [object Promise]
var func = function a(){}; // [object Function]
console.log(Object.prototype.toString.call(Math)); // [object Math]
console.log(Object.prototype.toString.call(JSON)); // [object JSON]

function checkType() {
    for (var i = 0; i < arguments.length; i++) {
        console.log(Object.prototype.toString.call(arguments[i]))
    }
}

checkType(number, string, boolean, und, nul, obj, array, date, error, reg, promise, func)
```
这些所有的类型都可以正确的判断，可见这个方法是多么的强大，目前这个方法基本可以解决你的问题了，但是还不够，我全都要！

#### 2. 优点和缺陷

优点可以总结为如下几点
 - 功能强大，基本所有情况都能判断
 - 兼容性好，不会出现兼容性的问题
 - 使用起来也很方便(虽然比`typeof`麻烦一些)

缺陷是什么呢，我们来看一段代码
```
function Person() {
	this.name = 'zhao';
}
var p = new Person();

function People() {
	this.name = 'ze';
}
var q = new People();

console.log(Object.prototype.toString.call(p)); // [object Object]
console.log(Object.prototype.toString.call(q)); // [object Object]
```
可以看出来，`Object.prototype.toString`对于自己创建的对象类型是无法判断的，只会返回`[object Object]`。
还有一点就是再`IE6`下，`undefined`和`null`均为`Object`

#### 3. 使用环境

`Object.prototype.toString`**基本可以用在任何场景**了，所有的类型都可以判断，但是如果想要判断某个实例的构造函数，那就需要再介绍两个方法了

#### 4. 神奇的地方(补充)
这是补充的地方，之前没有注意过，今天学姐突然问我 `Array.prototype.toString`也可以这么用啊，但是不能检测`null`和`undefined`的，剩下类型都可以检测。

哦呦？有趣，我去查一查他们到底有什么区别





---

### 三丶instanceof

#### 1. 基本介绍

> `instanceof`运算符用于测试构造函数的`prototype`属性是否出现在对象的原型链中

使用方法如下

> `object instanceof constructor`

`object`是要检测的对象，`constructor`是某个构造函数

```
let a = [];
console.log(a instanceof Array); // true

function Person () {
}

let person = new Person();
console.log(person instanceof Person) // true
console.log(person instanceof Object) // true
```
可以看出来，只要是出现在原型链上的原型对象，都会返回`true`

但是当我们在**创建实例之后**改变原型对象的指向，就会出现错误
```
let a = [];
console.log(a instanceof Array); // true

function Person () {
}

let person = new Person();

Person.prototype = {}

console.log(person instanceof Person) // false
console.log(person instanceof Object) // true
```

#### 2. 优点和缺陷

优点如下：

- 可以检测**引用类型**
- 可以检测**自定义对象的类型**

缺陷有大概两点

首先，`instanceof` 无法检测**基本类型**

```
console.log(5 instanceof Number); // false
```
注意，这里说的是**基本类型**，而不是**基本包装类型**，也就是说，如果你调用`Number`构造函数，那还是可以检测的

```
let a = new Number(5);
console.log(a instanceof Number); // true
```
这就说明，在使用`instanceof`的时候，**基本类型并不会像调用方法时一样自动建立基本包装类型**

那又因为一般我们在声明一个基本类型的时候是不会调用对应基本包装类型的构造函数的，所以我们认为`instanceof`是无法检测基本类型的

第二点，`instanceof`在多全局对象中是无法使用的。

在浏览器中，我们的脚本可能需要在多个窗口之间进行交互。

**多个窗口意味着多个全局环境，不同的全局环境拥有不同的全局对象，从而拥有不同的内置类型构造函数。**

这可能会引发一些问题。比如，表达式 `[] instanceof window.frames[0].Array` 会返回false，因为 `Array.prototype !== window.frames[0].Array.prototype`，并且数组从前者继承。

如果你的页面不包含多个`iframe`或者多个`window`那其实没什么，但是如果你的脚本开始处理多个`iframe`或多个`window`就一定要注意这个问题

#### 3. 适用环境

一般使用`instanceof`来检测引用类型的对象，和自定义的对象，局限性还是挺高的

---

### 四丶constructor

#### 1. 基本介绍

`constructor`是一个高效率的方法，同时也是一个危险的方法

`constructor`本来是原型对象上的一个指向对应构造函数的属性，恰好我们就可以利用这个属性对值的类型进行判断，他**返回一个构造函数**

```
let arr = [];
console.log([].constructor) // Array
```

注意，该方法是可以检测基本类型的
```
let s = 'zhao';
console.log(s.constructor) // String
```
这里能有返回值，是因为基本类型在调用方法的时候，会**自动创建一个基本包装类型**

这也算是一个比较全面的方法，但是并不建议大家用，因为他的缺陷也很明显

#### 2. 优点和缺陷

优点如下：

- 基本能检测出所有的类型(`undefined`和`null`除外)
- 可以检测**自定义对象的类型**

缺陷：

##### (1). `constructor`无法检测`undefined`和`null`这两个类型

因为这两个类型没有`constructor`属性，所以会报错

```
let a = null;
let b = undefined;
console.log(a.constructor) // Uncaught TypeError
console.log(b.constructor) // Uncaught TypeError
```

所以在使用这个方法时，就一定要排除`null`和`undefined`这两个方法

否则比较危险

##### (2). `constructor`很容易被修改

大家都知道`cosntructor`是原型对象上的一个属性，所以如果修改了原型对象，`constructor` 就不会存在了，如果用到了原型链，那就很难检测到实例的构造函数到底是谁了

```
function Person() {
}

function Man() {
}

Man.prototype = new Person();

let zhao = new Man();

console.log(zhao.constructor) // Person
```

上面的代码就会返回那个不是我们想要的构造函数

所以要使用是需要将`constructor`重新设置为之前的构造函数，这是一件很麻烦的事

所以不是很建议大家用这个方法

#### 3. 适用环境

在你确定要检测的变量不是`null`和`undefinde`，并且`constructor`存在的时候可以放心大胆的使用


--- 

### 五丶总结

总共介绍了四种判断数据类型的方法，这里总结一下

#### 1. typeof
> 调用方法：`typeof xxx`
> 返回值：一个表示类型的字符串
> 优点：轻巧易用，可以很方便的算出一个变量的类型
> 缺点：
> 1. 不能检测引用类型的值
> 2. 无法正确检测`null`的类型
> 
> 适用环境：确定要检测的值为基本类型且不为`null`

#### 2. Object.prototype.toString
> 调用方法：`Object.prototype.toString(xxx)`
> 返回值：`[object + 类型]`
> 优点：可以检测所有类型
> 缺点：不能检测自定义对象的类型
> 
> 适用环境：基本所有地方都可以适用

#### 3. instanceof
> 调用方法：`object instanceof constructor`
> 返回值：布尔值，对象的原型链上有这个原型对象对象的构造函数返回`true`，否则返回`false`
> 优点：
> 1. 可以检测引用类型
> 2. 可以检测自定义类型
> 
> 缺点：
> 1. 不能检测基本类型
> 2. 容易被修改
> 3. 不能多个全局环境下运行
> 
> 适用环境：单个全局环境下检测引用类型时

#### 4. constructor
> 调用方法：`xxx.constructor`
> 返回值：调用该属性的对象的构造函数
> 优点：
> 1. 可以检测除`null`和`undefinded`外的所有类型
> 2. 可以检测自定义类型
> 
> 缺点：
> 1. 不能检测`null`和`undefined`
> 2. 容易被修改
> 3. 不能多个全局环境下运行
> 
> 适用环境：确定`constructor`没有被修改且检测对象不为`null`和`undefined`的情况


---

#####参考
- <a href="https://blog.csdn.net/lhjuejiang/article/details/79623973">https://blog.csdn.net/lhjuejiang/article/details/79623973</a>
- <a href="https://juejin.im/post/59c7535a6fb9a00a600f77b4">https://juejin.im/post/59c7535a6fb9a00a600f77b4</a>
- <a href="https://juejin.im/post/5aba32d9f265da239e4e1b6c">https://juejin.im/post/5aba32d9f265da239e4e1b6c</a>
- <a href="https://juejin.im/post/5b0554c86fb9a07acb3d3ddc">https://juejin.im/post/5b0554c86fb9a07acb3d3ddc</a>

---
