## JavaScript -- call, apply 和bind

@(Interview)

说到这三个方法就不得不提到`this`，这三个方法与`this`都有着密切不可分的关系，`this`的详细介绍在我另一篇博客中，自行查看

这三个方法都是`Function.prototype`上的方法，其出现的目的就是**改变函数运行时的`this`指向**，他们三个很像，但也有一些不同，一篇给你讲明白

### 一丶call()

#### 1. 基本使用

`call()`方法调用一个函数，其具有一个指定的`this`值和**分别提供**的参数

**语法：**

> `fun.call(thisArg, arg1, arg2, ...)`

**参数：**

- `thisArg`：在`fun`函数运行时指定的`this`值。需要注意的是，指定的`this`值并不一定是该函数执行时真正的`this`值，有这几种特殊情况
1. 不传，或者传入`null`，`undefined`，函数中的`this`指向`window`对象(非严格模式)
2. 传递另一个函数的函数名，函数中的`this`指向**这个函数的引用**
3. 值为原始值(数字，字符串，布尔值)的`this`会指向该原始值的自动包装对象，如`String`，`Number`，`Boolean`
4. 传递一个对象，函数中的`this`指向这个对象

- `arg`： 指定的参数列表

**返回值：**

返回值是你调用的方法的返回值，若该方法没有返回值，则返回`undefined`

**例子：**

```
function a(){
    //输出函数a中的this对象
    console.log(this); 
}
//定义函数b
function b(){} 

let obj = {
	name: 'zhao'
}; //定义对象obj
a.call(); //window
a.call(null); //window
a.call(undefined);//window
a.call(1); //Number
a.call(''); //String
a.call(true); //Boolean
a.call(b);// function b(){}
a.call(obj); //obj
```
这是传入不同的参数，所返回不同的值

#### 2. 应用

对于`call`的使用，大部分用于实现**继承**

```
function Animal(name) {
  this.name = name;
  this.showName = function () {
    console.log(this.name);
  }
}

function Cat(name) {
  Animal.call(this, name); 
}
let cat = new Cat('Black Cat');

cat.showName(); // Black Cat
```

`Cat`继承了`Animal`

还有将**伪数组**转化为**数组**

```
let fakeArr = {
	0: 'a',
	1: 'b',
	length: 2
};

let arr = Array.prototype.slice.call(fakeArr);

console.log(arr); // ["a", "b"]
```
这段代码将伪数组`fakeArr`转换为了数组`arr`

还有检测类型
```
let a = 12;
console.log(Object.prototype.toString.call(a)); // [object Number]
```

对于`call`的应用太多了，这里就不一一举例了

#### 3. 实现`call`

对于`call`这种重要的方法，一定要刨根问祖，将它彻底搞懂。

对于`call`的实现，我们分几步完成

##### 第一步
先给出一个例子：
```
let foo = {
    value: 1
};

function bar() {
    console.log(this.value);
}

bar.call(foo); // 1
```

我们以这个为目标，实现一个`myCall`，让他完成`call`做的事情

首先，通过观察我们可以发现，`call`做到了两个事情：
1. 将`this`指向了`foo`
2. 执行了`bar`函数

那我们就要想办法做到这两个事情

很简单，既然要`this`指向`foo`，那我们只需要在`foo`中写一个和`bar`一模一样的，方法然后执行这个方法不就成了嘛

```
let foo = {
    value: 1,
    bar: function() {
		console.log(this.value);
	}
};

foo.bar(); // 1
```
但是这个方法有一个点需要注意，就是多了一个`foo.bar`，这肯定不行，我们需要最后将他删除。
所以需要的步骤也就是下面这样的：
1. 将函数设为对象的属性
2. 执行该函数
3. 删除该函数

这个函数名起什么都可以，因为最后反正也要删除它

```
Function.prototype.myCall = function (context) {
	// 给传入的对象设置一个fn函数，函数等于调用call的函数，this指向的正是那个函数
	context.fn = this;
	// 执行函数
	context.fn();
	// 删除函数
	delete context.fn;
}

// 测试一下
let foo = {
	value: 1
}

function bar() {
	console.log(this.value);
}

bar.myCall(foo); // 1
```
成功~

##### 第二步

`call`是可以传递参数的，但是第一步形成的并不能传递参数
```
Function.prototype.myCall = function (context) {
	// 给传入的对象设置一个fn函数，函数等于调用call的函数，this指向的正是那个函数
	context.fn = this;
	// 执行那个函数
	context.fn();
	// 删除函数
	delete context.fn;
}

// 测试一下
let foo = {
	value: 1
}
function bar(name, age) {
	console.log(name);
	console.log(age);
	console.log(this.value);
}

bar.myCall(foo, 'zhao', 18);
// undefined
// undefined
// 1
```
传递的参数并没有打印

所以需要改一下，这里如果我们只是为了实现功能，可以这么写
```
Function.prototype.myCall = function (context) {
	// 给传入的对象设置一个fn函数，函数等于调用call的函数，this指向的正是那个函数
	context.fn = this;
	// 剔除第一个参数
	let arr = Array.from(arguments);
	arr.shift();
	// 执行那个函数
	context.fn(...arr);
	// 删除函数
	delete context.fn;
}
// 测试一下
let foo = {
	value: 1
}

function bar(name, age) {
	console.log(name);
	console.log(age);
	console.log(this.value);
}

bar.myCall(foo, 'zhao', 18);
// zhao
// 18
// 1
```
完美执行，代码也很漂亮，但是要注意的是，我们是为了探寻`call`的原理才自己去写的，`call`是ES3的方法，所以内部肯定没有扩展运算符和`from`方法。

另想办法，对于参数的取用，可以通过循环取用，对于传参，我们可以使用`eval`

```
Function.prototype.myCall = function (context) {
	// 给传入的对象设置一个fn函数，函数等于调用call的函数，this指向的正是那个函数
	context.fn = this;
	// 从第二个参数获取
	var arr = [];
	for(var i = 1, len = arguments.length; i < len; i++) {
	  arr.push('arguments[' + i + ']');
	}
	// 执行函数
	eval('context.fn(' + arr +')');
	// 删除函数
	delete context.fn;
}

// 测试一下
let foo = {
	value: 1
}

function bar(name, age) {
	console.log(name);
	console.log(age);
	console.log(this.value);
}

bar.myCall(foo, 'zhao', 18);
// zhao
// 18
// 1
```
好了，现在传参的问题解决了

##### 第三步

根据`call`的语法，我们知道，可以给他内部传递**基本类型**或者**不传递参数**

上面的版本如果传递基本类型或者不传递参数是一定会报错的

```
Function.prototype.myCall = function (context) {
	// 给传入的对象设置一个fn函数，函数等于调用call的函数，this指向的正是那个函数
	context.fn = this;
	// 从第二个参数获取
	var arr = [];
	for(var i = 1, len = arguments.length; i < len; i++) {
	  arr.push('arguments[' + i + ']');
	}
	// 执行函数
	eval('context.fn(' + arr +')');
	// 删除函数
	delete context.fn;
}

function bar(name, age) {
	console.log(this);
}

// 分别执行一下，看一看执行结果
bar.myCall(); // Uncaught TypeError: Cannot set property 'fn' of undefined
bar.myCall(undefined); // Uncaught TypeError: Cannot set property 'fn' of undefined
bar.myCall(null); // Uncaught TypeError: Cannot set property 'fn' of null

bar.myCall(1); // Uncaught TypeError: context.fn is not a function
```
可以看出来，不传参和传`null`和`undefined`的报错是**无法给他们添加`fn`方法**，传入**原始值**(`Number`，`String`这些)是在**调用`fn`的时候报错**

我们一个一个解决，先解决不传参的问题，在原版`call`中，不传参和传入`null`和传入`undefined`，`this`会被绑定到`window`上，这个好办

我们添加一行
```
var context = context || window
```
这样就行了~

然后是原始值的问题，这个就得需要分情况了，我们可以利用`switch`，再把不传参的情况也写进去，总体来说是这样

```
Function.prototype.myCall = function (context) {
	// 声明一个变量保存类型
	var context;
	// 检测传入的值，如果是基本类型生成基本包装类型，如果是null或undefined或不传参则为window
	switch (typeof context) {
		case 'number': context = new Number(context);
		break;
		case 'string': context = new String(context);
		break;
		case 'boolean': context = new Boolean(context);
		break;
		case 'symbol': context = Object(context);
		break;
		default: context = context || window;
		break;
	}
	// 给传入的对象设置一个fn函数，函数等于调用call的函数，this指向的正是那个函数
	context.fn = this;
	// 获取参数
	var arr = [];
	for(var i = 1, len = arguments.length; i < len; i++) {
	  arr.push('arguments[' + i + ']');
	}
	// 执行函数
	eval('context.fn(' + arr +')');
	// 删除函数
	delete context.fn;
}

let foo = {
	name: 'zhao'
}

function bar(name, age) {
	console.log(this);
}

bar.myCall(1); // Number
bar.myCall(); // Window
bar.myCall('zhao'); // String
bar.myCall(false); // Boolean
bar.myCall(undefined); // Window
bar.myCall(null); // Window
```
可以看到，已经解决了传入基本类型的的问题了~

有的同学看到了`Symbol`，没错，虽然我们不使用ES6的东西，但是ES6还是要兼容到的~

##### 第四步

到这儿还不算完，如果我们的函数是有返回值的怎么办，
```
function bar(name, age) {
	return {
		value: this.value,
		name: name,
		age: age
	}
}
```
比如这个函数

所以我们需要做一个简单的处理让函数的**执行结果可以返回**

```
// 执行函数
var result = eval('context.fn(' + arr +')');
return result
```
这样就好了~

全部代码

```
Function.prototype.myCall = function (context) {
	// 声明一个变量保存类型
	var context;
	// 检测传入的值，如果是基本类型生成基本包装类型，如果是null或undefined或不传参则为window
	switch (typeof context) {
		case 'number': context = new Number(context);
		break;
		case 'string': context = new String(context);
		break;
		case 'boolean': context = new Boolean(context);
		break;
		case 'symbol': context = Object(context);
		break;
		default: context = context || window;
		break;
	}
	// 给传入的对象设置一个fn函数，函数等于调用call的函数，this指向的正是那个函数
	context.fn = this;
	// 获取参数
	var arr = [];
	for(var i = 1, len = arguments.length; i < len; i++) {
	  arr.push('arguments[' + i + ']');
	}
	// 执行函数
	var result = eval('context.fn(' + arr +')');
	// 删除函数
	delete context.fn;
	// 返回执行结果
	return result
}

let foo = {
	value: 'yang',
}
function bar(name, age) {
	return {
		value: this.value,
		name: name,
		age: age
	}
}

console.log(bar.myCall(foo , 'zhao', 18)); // {value: "yang", name: "zhao", age: 18}
```
##### 第五步

上面的代码已经算是比较完美了，但是吹毛求疵的话还有最最最最后一个问题了，那就是，如果我们取的**函数名**在对象中**已经存在**该怎么办？

比如说，现在有这样一个对象

```
var foo = {
	value: 'yang',
	fn: function () {
		console.log('这是对象foo的方法')
	}
}
```

这时候如果我们执行我们写的`myCall`，乍一看是没有问题的，但仔细看看，就会发现，有个致命的问题：**它会将这个对象原本的`fn`函数先修改后删除**

我们需要一个取名函数，有什么办法呢，`Math.random`

```
function getName(obj) {
	var _objName = Math.random();
	if (obj.hasOwnProperty(_objName)) {
		getName(obj)
	} else {
		return _objName
	}
}
```
当函数名重复就会再次执行这个函数，直到对象实例上不包含这个函数名

全部代码

```
Function.prototype.myCall = function (context) {
    // 声明一个变量保存类型
    var _context;
    // 检测传入的值，如果是基本类型生成基本包装类型，如果是null或undefined或不传参则为window
    switch (typeof context) {
        case 'number': _context = new Number(context);
        break;
        case 'string': _context = new String(context);
        break;
        case 'boolean': _context = new Boolean(context);
        break;
        case 'symbol': _context = Object(context);
        break;
        default: _context = context || window;
        break;
    }
    // 取名函数
    function getName(obj) {
        var _objName = Math.random();
        if (obj.hasOwnProperty(_objName)) {
            getName(obj)
        } else {
            return _objName
        }
    }
    // 获取一个不重复的函数名
    var objName = getName(_context);
    // 给传入的对象设置一个fn函数，函数等于调用call的函数，this指向的正是那个函数
    _context[objName] = this;
    // 获取后面的参数
    var arr = [];
    for(var i = 1, len = arguments.length; i < len; i++) {
      arr.push('arguments[' + i + ']');
    }
    // 执行函数
    var result = eval('_context[objName](' + arr +')');
    // 删除函数
    delete _context[objName];
    // 返回执行结果
    return result
}

```

这样就不会和对象内部的函数名字重复了

这就基本是完成了`call`的模拟，大家可以自己试一试。

但是还有个小问题，目前没有找到解决办法，因为我们是**先执行函数，然后删除**(好像说了句废话)，但是就是这个原因导致在执行函数的时候，是可以**通过对象访问到这个方法的**

不过无伤大雅，这并不阻碍我们理解`call`~

### 二丶apply

其实`apply`和`call`大同小异，只要我们理解了`call`，`apply`就不言而喻了

#### 1. 基本使用

`apply()`方法调用一个具有给定`this`值的函数，以及作为一个数组提供的参数

语法与 `call()` 方法的语法几乎完全相同，唯一的区别在于，`apply`的第二个参数必须是一个包含多个参数的数组（或类数组对象）。`apply`的这个特性很重要，

**语法：**

> `fun.apply(thisArg, argsArray)`

**参数：**
- `thisArg`：在`fun`函数运行时指定的`this`值。需要注意的是，指定的`this`值并不一定是该函数执行时真正的`this`值，有这几种特殊情况
1. 不传，或者传入`null`，`undefined`，函数中的`this`指向`window`对象(非严格模式)
2. 传递另一个函数的函数名，函数中的`this`指向**这个函数的引用**
3. 值为原始值(数字，字符串，布尔值)的`this`会指向该原始值的自动包装对象，如`String`，`Number`，`Boolean`
4. 传递一个对象，函数中的`this`指向这个对象

- `asgsArray`：一个数组或者类数组对象，其中的数组元素将作为单独的参数传给 `fun` 函数。如果该参数的值为`null` 或 `undefined`，则表示不需要传入任何参数。从`ECMAScript 5` 开始可以使用类数组对象。

**返回值：**

返回值是你调用的方法的返回值，若该方法没有返回值，则返回`undefined`

**例子：**

```
let foo = {
	value: 'zhao'
}
function hello(x, y, z) {
	console.log(this);
	console.log(x, y, z);
}
hello.apply(foo, [1, 2, 3]);
// {value: "zhao"}
// 1 2 3
```

#### 2. 应用

获取数组中的最大值和最小值
```
var num = [1,3,5,7,2,-10,11];
var maxNum = Math.max.apply(Math, num);
var minNum = Math.min.apply(Math, num);
console.log(maxNum); 
console.log(minNum); 
```

其实`call`能应用到的地方，`apply`就也可以实现，只不过根据要传入的参数形式不同，进行不同的选择罢了，这里就不再举例子了

#### 3. 实现

我们之前讨论过`call`的使用，所以对`apply`的实现和之前一样，只不过要改一个地方就行

因为传入的不再是一个个参数而是参数组成的数组，所以在接受参数的地方做一下改变就可以了

```
var arr = [];
for(var i = 0, len = arguments[1].length; i < len; i++) {
	 arr.push('arguments[1][' + i + ']');
}
```
我们只需要在这个`arguments`后面加个 1 就好了，因为那就是传进来的数组，别忘了从 0 开始循环

全部代码：

```
Function.prototype.myApply = function (context) {
	// 声明一个变量保存类型
	var context;
	// 检测传入的值，如果是基本类型生成基本包装类型，如果是null或undefined或不传参则为window
	switch (typeof context) {
		case 'number': context = new Number(context);
		break;
		case 'string': context = new String(context);
		break;
		case 'boolean': context = new Boolean(context);
		break;
		case 'symbol': context = Object(context);
		break;
		default: context = context || window;
		break;
	}
	// 取名函数
	function getName(obj) {
		var _objName = Math.random();
		if (obj.hasOwnProperty(_objName)) {
			getName(obj)
		} else {
			return _objName
		}
	}
	// 获取一个不重复的函数名
	var objName = getName(context);
	// 给传入的对象设置一个fn函数，函数等于调用call的函数，this指向的正是那个函数
	context[objName] = this;
	// 获取后面的参数
	var arr = [];
	for(var i = 0, len = arguments[1].length; i < len; i++) {
	  arr.push('arguments[1][' + i + ']');
	}
	// 执行函数
	var result = eval('context[objName](' + arr +')');
	// 删除函数
	delete context[objName];
	// 返回执行结果
	return result
}
```
### 三丶bind

#### 1. 基本使用

`bind()`方法创建一个新的函数，当这个新函数被调用时，`bind()` 的第一个参数将作为它运行时的 `this`，之后的一序列参数将会在传递的实参前传入作为它的参数。

由此我们可以首先提出`bind`函数的两个特点：
- 返回一个函数
- 可以传入参数

**语法：**

> `fun.bind(thisArg, arg1, arg2, ...argn)`

**参数：**
- `thisArg`：调用绑定函数时作为this参数传递给目标函数的值
- `argn`：当绑定函数被调用时，这些参数将置于实参之前传递给被绑定的方法

> 这里的`tnisArg`和`call`的情况一样

**返回值：**

返回由指定的`this`值和初始化参数改造的**原函数拷贝**

**例子：**
```
var obj = {
	name: 'zhao'
};

// 当点击网页时触发并执行
function hello(a, b){
	console.log(this.name, a, b);
}

var helloBind = hello.bind(obj);
helloBind('p1', 'p2'); // {name: 'zhao'}, 'p1', 'p2'
```

#### 2. 应用

**创建绑定函数**

```
function hello (age1, age2, age3) {
	console.log(age1, age2, age3);
	console.log(this);
}

let foo = {
	name: 'zhao'
}

let d = hello.bind(foo, 1, 2, 3);


d(); 
// 1 2 3 
// {name: "zhao"}
```

#### 3. 实现

实现`bind`需要借助前面的`call`和`apply`

##### 第一步

先实现最简单的，让我们先返回一个函数，并且返回的函数调用`apply`达到改变`this`绑定的问题

```
Function.prototype.myBind = function(context) {
	var self = this;
	return function () {
		self.apply(context);
	}
}

let foo = {
	value: 1
};

function bar() {
	console.log(this.value);
}

let bindFoo = bar.myBind(foo);

bindFoo(); // 1
```

这里利用了个**闭包**，将函数返回

##### 第二步

因为`bind`可以在第一次调用的时候传入参数，然后在调用返回的函数的时候也可以传入参数

这个方法需要我们使用类似于**函数柯里化**的原理来实现

看代码

```
Function.prototype.myBind = function(context) {
	// 利用闭包取得调用bind的函数，并传给返回的函数
	var self = this;
	// 获取第二个以后的参数，并形成一个参数
	var args = Array.prototype.slice.call(arguments, 1);
	// 返回一个函数，可以通过apply来实现this的绑定
	return function () {
		var bindArgs = Array.prototype.slice.call(arguments);
		self.apply(context, args.concat(bindArgs));
	}
}

let foo = {
	value: 1
};

function bar(name, age) {
	console.log(name);
	console.log(age);
	console.log(this.value);
}

let bindFoo = bar.myBind(foo, 'zhao');

bindFoo(18); 
// zhao
// 18
// 1
```
这样就可以分两次传递参数了

完成了这两点还有一个比较特殊的情况

##### 第三步

`bind`有这样一个特点
一个绑定函数也能使用 `new` 操作符创建对象：这种行为就像把原函数当成构造器。提供的 `this` 值被忽略，同时调用时的参数被提供给模拟函数。

**也就是说当 `bind` 返回的函数作为构造函数的时候，`bind` 时指定的 `this` 值会失效，但传入的参数依然生效**。举个例子：

```
let foo = {
	value: 1
};
var value = 2;

function bar(name, age) {
	console.log(name);
	console.log(age);
	console.log(this.value);
}

let bindFoo = bar.bind(foo, 'zhao');

let a = new bindFoo(18); 
// zhao
// 18
// undefined
```

可以发现，这个`this.value`并没有显示 1 或 2，而是`undefined`，说明这个时候的`this`是指向`new`新创建的那个对象，那我们应该在**返回的函数**中，做一些手脚

```
Function.prototype.myBind = function(context) {
	// 利用闭包取得调用bind的函数，并传给返回的函数
	var self = this;
	// 获取第二个以后的参数，并形成一个参数
	var args = Array.prototype.slice.call(arguments, 1);

	var fbound = function () {
	    var bindArgs = Array.prototype.slice.call(arguments);
	    // 这里判断调用这个函数的环境中的this，如果是new调用的，那this是新创建的对象，所以self是他的构造函数，所以传入this，如果是普通调用this会指向window，那么self不是构造函数，就返回context
	    self.apply(this instanceof self ? this : context, args.concat(bindArgs));
	}
  
	// 因为fbound的原型对象为undefined，所以在这里赋值，让new可以进行绑定
	fbound.prototype = this.prototype;
	// 返回一个函数，可以通过apply来实现this的绑定
	return fbound
}

let foo = {
	value: 1,
};
var value = 2;

function bar(name, age) {
	console.log(name);
	console.log(age);
	console.log(this.value);
}

let bindFoo = bar.myBind(foo, 'zhao');

let a = new bindFoo(18); 
// zhao
// 18
// undefined
```
实现了我们的要求

主要是在返回的函数中进行了判断当下 `this` 的指向，因为在调用 `new` 的时候，`this` 会指向新创建的对象，所以刚好通过这一点可以来识别是否为构造函数

而`fbound.prototype = this.prototype`这一步主要是因为`fbound.prototype`是`undefined`，所以将调用`bind`的函数的原型赋值过去

那么这有一个小问题，就是直接赋值，导致后面如果修改`fbound.prototype`，会连`this.prototype`一起修改，这不是我们想要的结果，所以我们可以通过继承来小小的改变一下

```
Function.prototype.myBind = function(context) {
	// 利用闭包取得调用bind的函数，并传给返回的函数
	var self = this;
	// 获取第二个以后的参数，并形成一个参数
	var args = Array.prototype.slice.call(arguments, 1);

	var fbound = function () {
	    var bindArgs = Array.prototype.slice.call(arguments);
	    // 这里判断调用这个函数的环境中的this，如果是new调用的，那this是新创建的对象，所以self是他的构造函数，所以传入this，如果是普通调用this会指向window，那么self不是构造函数，就返回context
	    self.apply(this instanceof self ? this : context, args.concat(bindArgs));
	}
	// 继承用函数
	var fNOP = function () {};
	// 因为fbound的原型对象为undefined，所以在这里通过继承，让new可以进行绑定
	fNOP.prototype = this.prototype;
	fbound.prototype = new fNOP();
	// 返回一个函数，可以通过apply来实现this的绑定
	return fbound
}
```

这样第三部算是完成了，后面的就都是小问题了~

##### 第四步

如果调用`bind`的不是函数会报错，所以我们需要写一个，很简单就一句话

```
Function.prototype.myBind = function(context) {
	// 如果不是函数，报错
	if (typeof this !== "function") {
      throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
    }
	
	// 利用闭包取得调用bind的函数，并传给返回的函数
	var self = this;
	// 获取第二个以后的参数，并形成一个参数
	var args = Array.prototype.slice.call(arguments, 1);

	var fbound = function () {
	    var bindArgs = Array.prototype.slice.call(arguments);
	    // 这里判断调用这个函数的环境中的this，如果是new调用的，那this是新创建的对象，所以self是他的构造函数，所以传入this，如果是普通调用this会指向window，那么self不是构造函数，就返回context
	    self.apply(this instanceof self ? this : context, args.concat(bindArgs));
	}
	// 继承用函数
	var fNOP = function () {};
	// 因为fbound的原型对象为undefined，所以在这里通过继承，让new可以进行绑定
	fNOP.prototype = this.prototype;
	fbound.prototype = new fNOP();
	// 返回一个函数，可以通过apply来实现this的绑定
	return fbound
}
```
这就算是完整版的`bind`了

---

### 四丶apply，call，bind的区别

通过上面的讲解，他们三个的区别已经不言而喻了吧，这里总结一下

首先他们三个的共同点很明确：**动态的改变函数调用时的`this`指向**

那么首先`apply`，`call`和`bind`的区别

- `call`和`apply`改变了函数的`this`上下文后便**立即执行**该函数，而`bind`是返回**改变了上下文后的一个函数**

然后是`apply`和`call`的区别

- `call`和`apply`的区别在于传入的第二个参数，`call`给调用它的函数传参是一个个传入的，而`apply`给调用它的函数传参是将所有参数放入一个数组，然后将数组传入

---


#####参考
- <a href="https://juejin.im/post/57dc97f35bbb50005e5b39bd">https://juejin.im/post/57dc97f35bbb50005e5b39bd</a>
- <a href="https://juejin.im/post/59093b1fa0bb9f006517b906">https://juejin.im/post/59093b1fa0bb9f006517b906</a>
- <a href="https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fmqyqingfeng%2FBlog%2Fissues%2F11">https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fmqyqingfeng%2FBlog%2Fissues%2F11</a>

--- 
