## JavaScript -- Object

@(Interview)

`Object`是 JS 中最为重要的一个数据类型了，甚至 JS 中一切都是`Object`。

### 一丶属性类型

创建一个`object`类型可以有很多方法，用的最多的应该就是对象字面量了，比如像下面这样

```
let person = {
	name: 'zhao',
	age: 20
}
```

这样一个`person`对象就拥有两个属性，分别是`name`和`age`，这两个属性也拥有自己的一些特性，我们来说一下，属性的一些特性

#### 1. 数据属性

数据属性包含一个数据值的位置。在这个位置可以读取和写入值。数据属性有 4 个描述其行为的特性，分别如下

- `[[Configurable]]`：表示能否通过 `delete` 删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性**修改为访问器属性**。像我们前面那个例子中那样直接在对象上定义的属性，它们的这个特性默认值为 `true`。
- `[[Enumerable]]`：表示能否通过 `for-in` 循环返回属性。像前面例子中那样直接在对象上定义的属性，它们的这个特性默认值为 `true`
- `[[Writable]]`：表示能否修改属性的值。像前面例子中那样直接在对象上定义的属性，它们的这个特性默认值为 `true`
- `[[Value]]`：包含这个属性的数据值。读取属性值的时候，从这个位置读。写入属性值的时候，把新值保存在这个位置。这个特性的默认值为 `undefined`

一个对象的属性只要是**数据属性**，就会包含这样的四个属性。

要修改属性默认的特性，必须使用 `ES5` 的 `Object.defineProperty`方法，这个方法我们稍后会提到


#### 2. 访问器属性

访问器属性不包含数据值。它们包含一对儿 `getter` 和 `setter` 函数，不过这两个函数都不是必须的。在读取访问器属性时，会调用 `getter` 函数，**这个函数负责返回有效的值**。在写入访问器属性时，会调用 `setter` 函数并传入新的值，**这个函数负责决定如何处理数据**。

访问器属性也有四个特性，分别如下

- `[[Configurable]]`：表示能否通过 `delete` 删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为数据属性。对于直接在对象上定义的属性，这个特性的默认值为 `true`
- `[[Enumerable]]`：表示能否通过 `for-in` 循环返回属性。对于直接在对象上定义的属性，这个特性默认是 `true`。
- `[[Get]]`：在读取属性时调用的函数。默认值为 `undefined`
- `[[Set]]`：在写入属性时调用的函数。默认值为 `undefined`

访问器属性不能直接定义，必须使用 `Object.defineProperty` 来定义

所以说这两种属性没有特殊的区分要求，如果有`[[Value]]`和`[[Writable]]`就是数值属性，如果没有而含有`[[Get]]`和`[[Set]]`就是访问器属性

---

### 二丶Object构造函数

对于创建对象的方法，除了对象字面量以外还有一种方法就是通过 `Object` 构造函数创建对象，不过用的比较少

```
let a = new Object(value);
```

这里的 `value` 可以是任何值

`Object` 构造函数为给定值创建一个对象包装器。如果给定值是 `null` 或 `undefined`，将会创建并返回一个空对象，否则，将返回一个与给定值对应类型的对象。

当以非构造函数形式被调用时，`Object` 等同于 `new Object()`。

#### 1. 属性

构造函数 `Object` 的属性有两个

- `Object.length`：值为 1 
- `Object.prototype`：可以为所有 `Object` 类型的对象添加属性

#### 2. 方法

对于 `Object`，方法还是有挺多的，我们来一个一个说。

#### 2.1 Object.assign

`Object.assign()` 方法用于将所有**可枚举属性的值**从**一个或多个源**对象复制到目标对象。它将返回目标对象。

```
	let person = {
		name: 'zhao',
		age: 20
	}
	let people = Object.assign({class: '1603'}, person);
	console.log(people.name) // zhao
```

##### (1). 语法

> `Object.assign(target, ...sources)`

**参数：**

- `target`：目标对象
- `sources`：源对象

**返回值：**
目标对象

**注意：**如果目标对象中的属性具有相同的键，则属性将被源中的属性覆盖。后来的源的属性将类似地覆盖早先的属性。

##### (2). 使用

```
	let person = {
		name: 'zhao',
		age: 20
	}
	let people = Object.assign({class: '1603'}, person);
	console.log(people.name) // zhao
```

其实在实际使用的过程中有许多需要注意的地方

##### 深拷贝的问题

`Obejct.assign` 是一个浅拷贝，如果源对象的属性值是一个指向对象的引用，它也只拷贝那个引用值

```
	let person = {
		name: 'zhao',
		age: 20,
		info: {
			class: '1603'
		}
	}
	let people = Object.assign({hh: 'yello'}, person);
	people.info.class = '1606';
	console.log(person.info.class) // 1606
```

##### 会修改目标对象

复制后会返回一个新对象，目标对象也会被修改

```
	let person = {
		name: 'zhao',
		age: 20,
	}

	let people = Object.assign(person, {hh: 'yello'});

	console.log(person.hh) // yello
```

##### 会覆盖相同的属性

如果属性名相同，则会合并

```
var o1 = { a: 1, b: 1, c: 1 };
var o2 = { b: 2, c: 2 };
var o3 = { c: 3 };

var obj = Object.assign({}, o1, o2, o3);
console.log(obj); // { a: 1, b: 2, c: 3 }
```

后面的源属性，会覆盖前面的源属性和目标属性

##### 继承属性和不可枚举属性是不能拷贝的

```
var obj = Object.create({foo: 1}, { // foo 是个继承属性。
    bar: {
        value: 2  // bar 是个不可枚举属性。
    },
    baz: {
        value: 3,
        enumerable: true  // baz 是个自身可枚举属性。
    }
});

var copy = Object.assign({}, obj);
console.log(copy); // { baz: 3 }
```

##### 原始类型会被包装为对象

```
var v1 = "abc";
var v2 = true;
var v3 = 10;
var v4 = Symbol("foo")

var obj = Object.assign({}, v1, null, v2, undefined, v3, v4); 
// 原始类型会被包装，null 和 undefined 会被忽略。
// 注意，只有字符串的包装对象才可能有自身可枚举属性。
console.log(obj); // { "0": "a", "1": "b", "2": "c" }
```

##### 异常会打断后续的拷贝任务

```
var target = Object.defineProperty({}, "foo", {
    value: 1,
    writable: false
}); // target 的 foo 属性是个只读属性。

Object.assign(target, {bar: 2}, {foo2: 3, foo: 3, foo3: 3}, {baz: 4});
// TypeError: "foo" is read-only
// 注意这个异常是在拷贝第二个源对象的第二个属性时发生的。

console.log(target.bar);  // 2，说明第一个源对象拷贝成功了。
console.log(target.foo2); // 3，说明第二个源对象的第一个属性也拷贝成功了。
console.log(target.foo);  // 1，只读属性不能被覆盖，所以第二个源对象的第二个属性拷贝失败了。
console.log(target.foo3); // undefined，异常之后 assign 方法就退出了，第三个属性是不会被拷贝到的。
console.log(target.baz);  // undefined，第三个源对象更是不会被拷贝到的。
```

#### 2.2 Object.create

`Object.create()` 方法创建一个新对象，使用现有的对象来提供新创建的对象的 `__proto__`。

##### (1). 语法

> `Obejct.create(proto, propertiesObject)`

**参数：**

- `proto`：新创建对象的原型对象
- `propertiesObject`(可选)：如果没有指定为 `undefined`，则是要添加到新创建对象的可枚举属性（即其自身定义的属性，而不是其原型链上的枚举属性）对象的属性描述符以及相应的属性名称。这些属性对应 `Object.defineProperties()` 的第二个参数。

**返回值：**

一个新对象，带着指定的原型对象和属性。

**例外：**

如果 `propertiesObject` 参数不是 `null` 或一个对象，则抛出一个异常

##### (2). 使用

```
	let person = {
		name: 'zhao',
		age: 20,
	}

	let a = 10;

	let people = Object.create(person);

	console.log(people.__proto__) // {name: "zhao", age: 20}
```

#### 2.3 Object.defineProperty

`Object.defineProperty()` 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象。

##### (1). 语法

> `Object.defineProperty(obj, prop, descriptor)`

**参数：**

- `obj`：要在其上定义属性的对象
- `prop`：要定义或修改的属性的名称
- `descriptor`：将被定义或修改的属性描述符

**返回值：**

被传递给函数的对象

这个就是我们之前说的可修改属性特性的方法

##### (2). 使用

##### 创建属性

如果对象中不存在指定的属性，`Object.defineProperty()` 就创建这个属性。当描述符中省略某些字段时，这些字段将使用它们的默认值。拥有布尔值的字段的默认值都是 `false`。`value`，`get`和`set`字段的默认值为`undefined`。一个没有`get/set/value/writable`定义的属性被称为“通用的”，并被“键入”为一个数据描述符。

```
var o = {}; // 创建一个新对象

// 在对象中添加一个属性与数据描述符的示例
Object.defineProperty(o, "a", {
  value : 37,
  writable : true,
  enumerable : true,
  configurable : true
});

// 对象o拥有了属性a，值为37

// 在对象中添加一个属性与存取描述符的示例
var bValue;
Object.defineProperty(o, "b", {
  get : function(){
    return bValue;
  },
  set : function(newValue){
    bValue = newValue;
  },
  enumerable : true,
  configurable : true
});

o.b = 38;
// 对象o拥有了属性b，值为38

// o.b的值现在总是与bValue相同，除非重新定义o.b

// 数据描述符和存取描述符不能混合使用
Object.defineProperty(o, "conflict", {
  value: 0x9f91102, 
  get: function() { 
    return 0xdeadbeef; 
  } 
});
// throws a TypeError: value appears only in data descriptors, get appears only in accessor descriptors
```


#### 2.4 Object.defineProperties

`Object.defineProperties()` 方法直接在一个对象上定义新的属性或修改现有属性，并返回该对象。

##### (1). 语法

> `Object.defineProperties(obj, props)`

**参数：**
- `obj`：在其上定义或修改属性的对象
- `props`：要定义其可枚举属性或修改的属性描述符的对象。

**返回值：**

传递给函数的对象

这个就是在 `defineProperty`的基础上，使一次可以添加多个对象属性

##### (2). 使用

```
var obj = {};
Object.defineProperties(obj, {
  'property1': {
    value: true,
    writable: true
  },
  'property2': {
    value: 'Hello',
    writable: false
  }
  // etc. etc.
});
```

#### 2.5 Object.entries

`Object.entries()` 方法返回一个给定对象自身**可枚举属性**的键值对数组，其排列与使用 `for...in` 循环遍历该对象时返回的顺序一致（区别在于 for-in 循环也枚举原型链中的属性）。

##### (1). 语法

> `Object.entries(obj)`

**参数：**

- `obj`：可以返回其可枚举属性的键值对的对象

**返回值：**
- 给定对象自身可枚举属性的键值对数组

##### (2). 使用

```
	let person = {
		name: 'zhao',
		age: 20,
	}
	Object.defineProperties(person, {
		'class': {
			value: '1603',
			enumerable: false,
		}
	})
	console.log(Object.entries(person));
	// 0: (2) ["name", "zhao"]
    // 1: (2) ["age", 20]
    // 因为class属性是不可枚举的，所以无法通过entries方法遍历
```

#### 2.6 Object.keys

`Object.keys()` 方法会返回一个由一个给定对象的自身可枚举属性组成的数组，数组中属性名的排列顺序和使用 `for...in` 循环遍历该对象时返回的顺序一致 。

##### (1). 语法

> `Object.keys(obj)`

**参数：**
- `obj`：要返回其枚举自身属性的对象

**返回值：**

一个表示给定对象的所有可枚举属性的字符串数组，也就是只有属性名

##### (2). 使用

```
	let person = {
		name: 'zhao',
		age: 20,
	}
	Object.defineProperties(person, {
		'class': {
			value: '1603',
			enumerable: false,
		}
	})
	console.log(Object.keys(person)); // ["name", "age"]
```

#### 2.7 Object.values

`Object.values()` 方法返回一个给定对象自身的所有可枚举属性值的数组，值的顺序与使用`for...in` 循环的顺序相同 ( 区别在于 for-in 循环枚举原型链中的属性 )。

##### (1). 语法

> `Object.values(obj)`

**参数：**
- `obj`：被返回可枚举属性值的对象。

**返回值：**

一个包含对象自身的所有可枚举属性值的数组。

这个方法和`Object.keys`，`Object.entries`刚好组成了一套，这个只返回属性值而不返回属性名

##### (2). 使用

```
	let person = {
		name: 'zhao',
		age: 20,
	}
	Object.defineProperties(person, {
		'class': {
			value: '1603',
			enumerable: false,
		}
	})
	console.log(Object.values(person)); // ["zhao", 20]
```

#### 2.8 Object.freeze

`Object.freeze()` 方法可以冻结一个对象，冻结指的是不能向这个对象添加新的属性，不能修改其已有属性的值，不能删除已有属性，以及不能修改该对象已有属性的可枚举性、可配置性、可写性。该方法返回被冻结的对象。

##### (1). 语法

> `Object.freeze(obj)`

**参数：**
- `obj`：要被冻结的对象

**返回值：**
- 被冻结的对象

如果一个被冻结的对象的一个属性的值是个**对象**，则这个对象中的属性**是可以修改的**，**除非它也是个冻结对象**。数组作为一种对象，被冻结，其元素不能被修改。没有数组元素可以被添加或移除。


##### (2). 使用

```
	let person = {
		name: 'zhao',
		age: 20,
	}
	let a = Object.freeze(person);

	person.name = 'li'
	console.log(person.name) // zhao
	console.log(person === a); // true
```

可以看到，冻结返回的对象就是原对象本身

#### 2.9 Object.isFrozen

`Object.isFrozen()` 方法判断一个对象是否被冻结。

##### (1). 语法

> `Object.isFrozen(obj)`

**参数：**
- `obj`：被检测的对象

**返回值：**

表示给定对象是否被冻结的 `Boolean`

**注意：**
一个对象是冻结的是指它不可扩展，所有属性都是不可配置的，且所有数据属性（即没有getter或setter组件的访问器的属性）都是不可写的。

#### (2). 使用

判断对象是否冻结参考的标准有很多

```
// 一个对象默认是可扩展的,所以它也是非冻结的.
Object.isFrozen({}); // === false

// 一个不可扩展的空对象同时也是一个冻结对象.
var vacuouslyFrozen = Object.preventExtensions({});
Object.isFrozen(vacuouslyFrozen) //=== true;

// 一个非空对象默认也是非冻结的.
var oneProp = { p: 42 };
Object.isFrozen(oneProp) //=== false

// 让这个对象变的不可扩展,并不意味着这个对象变成了冻结对象,
// 因为p属性仍然是可以配置的(而且可写的).
Object.preventExtensions(oneProp);
Object.isFrozen(oneProp) //=== false

// ...如果删除了这个属性,则它会成为一个冻结对象.
delete oneProp.p;
Object.isFrozen(oneProp) //=== true

// 一个不可扩展的对象,拥有一个不可写但可配置的属性,则它仍然是非冻结的.
var nonWritable = { e: "plep" };
Object.preventExtensions(nonWritable);
Object.defineProperty(nonWritable, "e", { writable: false }); // 变得不可写
Object.isFrozen(nonWritable) //=== false

// 把这个属性改为不可配置,会让这个对象成为冻结对象.
Object.defineProperty(nonWritable, "e", { configurable: false }); // 变得不可配置
Object.isFrozen(nonWritable) //=== true

// 一个不可扩展的对象,拥有一个不可配置但可写的属性,则它仍然是非冻结的.
var nonConfigurable = { release: "the kraken!" };
Object.preventExtensions(nonConfigurable);
Object.defineProperty(nonConfigurable, "release", { configurable: false });
Object.isFrozen(nonConfigurable) //=== false

// 把这个属性改为不可写,会让这个对象成为冻结对象.
Object.defineProperty(nonConfigurable, "release", { writable: false });
Object.isFrozen(nonConfigurable) //=== true

// 一个不可扩展的对象,值拥有一个访问器属性,则它仍然是非冻结的.
var accessor = { get food() { return "yum"; } };
Object.preventExtensions(accessor);
Object.isFrozen(accessor) //=== false

// ...但把这个属性改为不可配置,会让这个对象成为冻结对象.
Object.defineProperty(accessor, "food", { configurable: false });
Object.isFrozen(accessor) //=== true

// 使用Object.freeze是冻结一个对象最方便的方法.
var frozen = { 1: 81 };
Object.isFrozen(frozen) //=== false
Object.freeze(frozen);
Object.isFrozen(frozen) //=== true

// 一个冻结对象也是一个密封对象.
Object.isSealed(frozen) //=== true

// 当然,更是一个不可扩展的对象.
Object.isExtensible(frozen) //=== false
```

#### 2.10 Object.getOwnPropertyDescriptor

`Object.getOwnPropertyDescriptor()` 方法返回指定对象上一个自有属性对应的属性描述符。（自有属性指的是直接赋予该对象的属性，不需要从原型链上进行查找的属性）

##### (1). 语法

> `Object.getOwnPropertyDescriptor(obj, prop)`

**参数：**
- `obj`：需要查找的目标对象
- `prop`：目标对象内属性名称

**返回值：**

**如果指定的属性存在于对象上**，则返回其属性描述符对象（property descriptor），否则返回 undefined。

##### (2). 使用

```
	let person = {
		name: 'zhao',
		age: 20,
	}

	console.log(Object.getOwnPropertyDescriptor(person, 'name'))
	// {value: "zhao", writable: true, enumerable: true, configurable: true}
```

#### 2.11 Object.getOwnPropertyNames

`Object.getOwnPropertyNames()` 方法返回一个由指定对象的所有自身属性的属性名（包括不可枚举属性但不包括Symbol值作为名称的属性）组成的数组。

##### (1). 语法

> `Object.getOwnPropertyNames(obj)`

**参数：**
- `obj`：一个对象，其自身的可枚举和不可枚举属性的名称被返回

**返回值：**
- 在给定对象上找到的自身属性对应的字符串数组

这个方法和 `Object.keys`的区别就是这个方法不可枚举的也可以访问到，但是 `Symbol` 值的属性名无法访问

##### (2). 使用

```
	let person = {
		name: 'zhao',
		age: 20
	}
	Object.defineProperty(person, 'name', {
		enumerable: false
	})
	console.log(Object.getOwnPropertyNames(person))
	// ["name", "age"]
```

#### 2.12 Object.getOwnPropertySymbols

`Object.getOwnPropertySymbols()` 方法返回一个给定对象自身的所有 Symbol 属性的数组。

##### (1). 语法

> `Object.getOwnPropertySymbols(obj)`

**参数：**
- `obj`：要返回 Symbol 属性的对象。

**返回值：**
在给定对象自身上找到的所有 `Symbol` 属性的数组。


##### (2). 使用

```
	let s1 = Symbol('s1');
	let s2 = Symbol('s2');
	let s3 = Symbol('s3');


	let person = {
		name: 'zhao',
		age: 20,
		[s1]: 's1',
		[s2]: 's2',
		[s3]: 's3'
	}

	console.log(Object.getOwnPropertySymbols(person))
	// [Symbol(s1), Symbol(s2), Symbol(s3)]
```

#### 2.13 Object.getPrototypeOf

`Object.getPrototypeOf()` 方法返回指定对象的原型（内部[[Prototype]]属性的值）。

##### (1). 语法

> `Object.getPrototypeOf(object)`

**参数：**

- `object`：要返回其原型的对象

**返回值：**

给定对象的原型。如果没有继承属性，则返回 `null` 。

##### (2). 使用

```
	let person = {
		name: 'zhao',
		age: 20,
	}

	let a = Object.create(person);

	console.log(Object.getPrototypeOf(a)); // {name: "zhao", age: 20}
```

#### 2.14 Object.setPrototypeOf

`Object.setPrototypeOf()` 方法设置一个指定的对象的原型 ( 即, 内部[[Prototype]]属性）到另一个对象或  null。

##### (1). 语法

> `Object.setPrototypeOf(obj, prototype)`

**参数：**
- `obj`：要设置其原型的对象
- `prototype`：该对象的新原型(一个对象 或 `null`)

**返回值：**

设置原型后的对象`obj`


##### (2). 使用

```
	let person = {
		name: 'zhao',
		age: 20,
	}
	let a = {
		class: '106'
	};

	console.log(Object.setPrototypeOf(a, person)); // {class: "106"}
```

#### 2.15 Object.is

`Object.is()` 方法判断两个值是否是相同的值。

##### (1). 语法

> `Object.is(value1, value2);`

**参数：**
- `value1`：需要比较的第一个值。
- `value2`：需要比较的第二个值。

**返回值：**

表示两个参数是否相同的`Boolean` 。

这个方法和`==`和`===`都不相同，它有一套自己的判断规则

- 两个值都是 undefined
- 两个值都是 null
- 两个值都是 true 或者都是 false
- 两个值是由相同个数的字符按照相同的顺序组成的字符串
- 两个值指向同一个对象
- 两个值都是数字并且
 - 都是正零 +0
 - 都是负零 -0
 - 都是 NaN
 - 都是除零和 NaN 外的其它同一个数字


##### (2). 使用

```
Object.is('foo', 'foo');     // true
Object.is(window, window);   // true

Object.is('foo', 'bar');     // false
Object.is([], []);           // false

var test = { a: 1 };
Object.is(test, test);       // true

Object.is(null, null);       // true

// 特例
Object.is(0, -0);            // false
Object.is(-0, -0);           // true
Object.is(NaN, 0/0);         // true
```

#### 2.16 Object.preventExtensions

`Object.preventExtensions()` 方法让一个对象变的不可扩展，也就是永远不能再添加新的属性。

##### (1). 语法
> `Object.preventExtensions(obj)`

**参数：**
- `obj`：将要变得不可扩展的对象。

**返回值：**
已经不可扩展的对象。

**这里的不可扩展只是针对对象本身，而不包括对象的原型对象**

##### (2). 使用

```
	let person = {
		name: 'zhao',
		age: 20,
	}
	Object.preventExtensions(person);

	person.class = '2016';

	console.log(person.class); // undefined
```

#### 2.17 Object.isExtensible

`Object.isExtensible()` 方法判断一个对象是否是可扩展的（是否可以在它上面添加新的属性）。

##### (1). 语法

> `Object.isExtensible(obj)`

**参数：**

- `obj`：需要检测的对象

**返回值：**

 表示给定对象是否可扩展的一个Boolean 。

##### (2). 使用

```
// 新对象默认是可扩展的.
var empty = {};
Object.isExtensible(empty); // === true

// ...可以变的不可扩展.
Object.preventExtensions(empty);
Object.isExtensible(empty); // === false

// 密封对象是不可扩展的.
var sealed = Object.seal({});
Object.isExtensible(sealed); // === false

// 冻结对象也是不可扩展.
var frozen = Object.freeze({});
Object.isExtensible(frozen); // === false
```

#### 2.18 Object.seal

`Object.seal()` 方法封闭一个对象，阻止添加新属性并将所有现有属性标记为不可配置。当前属性的值只要可写就可以改变。

##### (1). 语法

> `Object.seal(obj)`

**参数：**
- `obj`：将要被密封的对象

**返回值：**
被密封的对象

**注意：**
密封一个对象会让这个对象变的**不能添加新属性**，且所有已有属性会变的**不可配置**。属性不可配置的效果就是属性变的**不可删除**，以及一个数据属性**不能被重新定义成为访问器属性**，或者反之。**但属性的值仍然可以修改**。尝试删除一个密封对象的属性或者将某个密封对象的属性从数据属性转换成访问器属性，结果会**静默失败**或抛出TypeError

##### (2). 使用
```
	let person = {
		name: 'zhao',
		age: 20,
	}
	Object.seal(person);

	person.class = '2016';
	person.name = 's'

	console.log(person); // {name: "s", age: 20}
```

#### 2.19 Object.isSealed

`Object.isSealed()` 方法判断一个对象是否被密封。

##### (1). 语法

> `Object.isSealed(obj)`

**参数：**

- `obj`：要被检查的对象

**返回值：**

表示给定对象是否被密封的一个`Boolean`

**注意：**
如果这个对象是密封的，则返回 `true`，否则返回 `false`。密封对象是指那些不可 扩展 的，且所有自身属性都不可配置且因此不可删除（但不一定是不可写）的对象。


##### (2). 使用

```
// 新建的对象默认不是密封的.
var empty = {};
Object.isSealed(empty); // === false

// 如果你把一个空对象变的不可扩展,则它同时也会变成个密封对象.
Object.preventExtensions(empty);
Object.isSealed(empty); // === true

// 但如果这个对象不是空对象,则它不会变成密封对象,因为密封对象的所有自身属性必须是不可配置的.
var hasProp = { fee: "fie foe fum" };
Object.preventExtensions(hasProp);
Object.isSealed(hasProp); // === false

// 如果把这个属性变的不可配置,则这个对象也就成了密封对象.
Object.defineProperty(hasProp, "fee", { configurable: false });
Object.isSealed(hasProp); // === true

// 最简单的方法来生成一个密封对象,当然是使用Object.seal.
var sealed = {};
Object.seal(sealed);
Object.isSealed(sealed); // === true

// 一个密封对象同时也是不可扩展的.
Object.isExtensible(sealed); // === false

// 一个密封对象也可以是一个冻结对象,但不是必须的.
Object.isFrozen(sealed); // === true ，所有的属性都是不可写的
var s2 = Object.seal({ p: 3 });
Object.isFrozen(s2); // === false， 属性"p"可写

var s3 = Object.seal({ get p() { return 0; } });
Object.isFrozen(s3); // === true ，访问器属性不考虑可写不可写,只考虑是否可配置
```

#### 小总结

其中有三个方法我们需要注意一下，分别是 `Object.freeze`，`Object.seal`，`Object.preventExtensions`，分别代表的是**冻结对象**，**密封对象**，**将对象设置成不可扩展**。

- **不可扩展**：不可给对象添加新属性，但是现有属性可以修改，属性的特性也可以修改
- **密封**：不可给对象添加新属性，属性的特性不可修改，但是现有属性可以修改
- **冻结**：不可给对象添加新属性，属性的特性不可修改，现有属性不可修改


针对上面的说法，所以有以下的关系
- 冻结的对象一定是密封的，一定不可扩展
- 密封对象不一定是冻结的，一定不可扩展
- 不可扩展对象不一定是密封的，不一定是冻结的

---

### 三丶Object实例

通过`Object`构造函数生成一个实例，这个实例上也有一些属性和方法

#### 1. 属性

##### 1.1 Object.prototype.constructor

指向该对象的构造函数

#### 2. 方法

方法不多，我们大概看一下

#### 2.1 Object.prototype.hasOwnProperty

`hasOwnProperty()` 方法会返回一个布尔值，指示对象自身属性中是否具有指定的属性

##### (1). 语法

> `obj.hasOwnProperty(prop)`

**参数：**
- `prop`：要检测的属性字符串名称或者`Symbol`

**返回值：**
用来判断某个对象是否含有指定的属性的	 `Boolean`

##### (2). 使用

```
o = new Object();
o.prop = 'exists';

function changeO() {
  o.newprop = o.prop;
  delete o.prop;
}

o.hasOwnProperty('prop');   // 返回 true
changeO();
o.hasOwnProperty('prop');   // 返回 false
```

#### 2.2 Object.prototype.isPrototypeOf

`isPrototypeOf()` 方法用于测试一个对象是否存在于另一个对象的原型链上。

##### (1). 语法

> `prototypeObj.isPrototypeOf(object)`

**参考：**
- `object`：在该对象的原型链上搜寻

**返回值：**
`Boolean`，表示调用对象是否在另一个对象的原型链上。

#### (2). 使用

```
function Foo() {}
function Bar() {}
function Baz() {}

Bar.prototype = Object.create(Foo.prototype);
Baz.prototype = Object.create(Bar.prototype);

var baz = new Baz();

console.log(Baz.prototype.isPrototypeOf(baz)); // true
console.log(Bar.prototype.isPrototypeOf(baz)); // true
console.log(Foo.prototype.isPrototypeOf(baz)); // true
console.log(Object.prototype.isPrototypeOf(baz)); // true
```

#### 2.3 Object.prototype.propertyIsEnumerable

`propertyIsEnumerable()` 方法返回一个布尔值，表示指定的属性是否可枚举。

##### (1). 语法

> `obj.propertyIsEnumerable(prop)`

**参数：**
- `prop`：需要测试的属性名

**返回值：**
用来表示指定的属性名是否可枚举的Boolean 。

##### (2). 使用

```
var o = {};
var a = [];
o.prop = 'is enumerable';
a[0] = 'is enumerable';

o.propertyIsEnumerable('prop');   //  返回 true
a.propertyIsEnumerable(0);        // 返回 true
```

#### 2.4 Object.prototype.toString

`toString()` 方法返回一个表示该对象的字符串。


> `object.toString()`

**返回值：**
表示该对象的字符串

#### 2.5 Object.prototype.toLocaleString

`toLocaleString()` 方法返回一个该对象的字符串表示。此方法被用于派生对象为了特定语言环境的目的（locale-specific purposes）而重载使用。

> `obj.toLocaleString()`

**返回值：**
表示对象的字符串。

#### 2.6 Object.prototype.valueOf

`valueOf()` 方法返回指定对象的原始值

##### (1). 语法

> `object.valueOf()`

**返回值：**
返回值为该对象的原始值。

JS许多内置对象都重写了`valueOf`方法，我们来看一下

- `Array`：返回数组对象本身。
- `Boolean`：	布尔值。
- `Date`：	存储的时间是从 1970 年 1 月 1 日午夜开始计的毫秒数 UTC。
- `Function`：	函数本身。
- `Number`：	数字值。
- `Object`：	对象本身。这是默认情况。
- `String`：	字符串值。