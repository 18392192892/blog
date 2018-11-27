## ESMAScript6 -- 箭头函数

@(Interview)

箭头函数是`ES6`的一个新属性，现在基本已经广泛使用，这篇博客讲一下他的一些使用方式

### 一丶基本语法

#### 1. 语法

使用箭头函数就是通过 `=>` 定义函数，只能通过**函数表达式**来创建箭头函数，**无法使用函数声明**创建函数

```
let f = v => v
```
变量`f`就是变量名，第一个`v`代表函数参数，第二个`v`代表函数的返回值

如果箭头函数**不需要参数或者需要多个参数**，就使用一个圆括号代表参数部分

```
let f = () => 5;
let sum = (num1, num2) => num1 + num2;
```

如果箭头函数的代码块部分**多于一条语句**，就要使用大括号讲他们括起来，并且使用`return`语句返回。

```
let sum = (num1, num2) => {
	num1 ++;
	return num1 + num2;
}
```

如果你想返回一个对象，不能直接写，因为**大括号会被解释为代码块**

```
let a = () => {id: id, name: 'zhao'} // 报错
```

可以加上**一个小括号**，也可以再写**一个大括号并且添加上`return`**

```
let a = () => ({id: id, name: 'zhao'})

let b = () => {
	return {
		id: id, 
		name: 'zhao'
	}
}
```

如果箭头函数**只有一行语句，且不需要返回值**，可以采用下面的写法，就不用写大括号了。

```
let fn = () => void doesNotReturn();
```

**解构赋值**和**`rest`**都可以与箭头函数结合

```
let a = (...rest) => rest
console.log(a(0, '222', 3)); // [0, "222", 3]

let b = ({name, age}) => ({name, age});
console.log(b({name: 'zhao', age: 20})); // {name: "zhao", age: 20}
```

### 二丶与普通函数的区别

与普通函数有四点不同

#### 1. 没有`this`

其实私以为，没有`this`这个说法并不是很准确，因为它的`this`可以说就是一个静态变量，**它必须通过查找作用域链来确定`this`的值**

这就意味着如果箭头函数被非箭头函数包含，`this`绑定的就是最近一层非箭头函数的`this`。

```
var age = 20;
function box () {
	let arr = () => {
		console.log(this.age)
	}
	arr();
}
let person = {
	age: 10,
	box: box
}
person.box(); // 10
box(); // 20
```

上面这个例子，就很明显了，当我们通过`person`调用`box`方法的时候，因为`box`的`this`是指向`person`的，所以`arr`在定义的时候确认了`this`为`person`。而直接调用`box`函数的时候，`box`的`this`是指向`window`的，所以`arr`定义的时候，`this`就为`window`。

**因为没有`this`，所以`call`，`bind`，`apply`这些一个都用不了**

```
	let a = () => {
		console.log(this.age)
	};

	let b = {
		age: 10
	}

	a.call(b); // undefined
```

虽然没有了`this`，但是有些地方却很方便，比如在一些回调函数中，之前如果不使用`bind`，那么回调函数一定是指向`window`的，现在就可以利用箭头函数，解决这个问题。

```
	let a = {
		age: 12,
		sayAge: function () {
			setTimeout(() => {
				console.log(this.age);
			}, 1000)
		}
	}
	a.sayAge(); // 12
```
如果不是箭头函数就无法访问`this.age`了

#### 2. 不可以当作构造函数使用

因为箭头函数没有`this`，所以`new`内部的`[[Call]]`方法无法执行，也就是无法绑定为新创建的对象，所以不可以使用`new`命令，否则会抛出一个错误


#### 3. 没有 `arguments`

箭头函数没有`arguments`，它也是指向外层函数的对应变量，如果要访问箭头函数的参数，可以通过命名参数或者`rest`参数的形式访问参数

#### 4. 没有`new.target`

因为不能使用`new`调用，所以也没有 `new.target` 值

#### 5. 没有原型

由于不能使用 `new` 调用箭头函数，所以也没有构建原型的需求，于是箭头函数也不存在 `prototype` 这个属性。

#### 6. 没有 `super`

连原型都没有，自然也不能通过 `super` 来访问原型的属性，所以箭头函数也是没有 `super` 的，不过跟 `this`、`arguments`、`new.target` 一样，这些值由外围最近一层非箭头函数决定。

#### 7. 没有 `yield`

不可以使用`yield`命令，因此箭头函数不能用作 `Generator` 函数

---


##### 参考：
- <a href="http://es6.ruanyifeng.com/#docs/function#%E7%AE%AD%E5%A4%B4%E5%87%BD%E6%95%B0">http://es6.ruanyifeng.com/#docs/function#%E7%AE%AD%E5%A4%B4%E5%87%BD%E6%95%B0</a>
- <a href="https://juejin.im/post/5b14d0b4f265da6e60393680">https://juejin.im/post/5b14d0b4f265da6e60393680</a>

---
