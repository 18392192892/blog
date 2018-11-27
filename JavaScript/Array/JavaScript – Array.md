## JavaScript -- Array

@(Interview)

这篇博客是数组方法全整理，重点是数组的方法和属性，也会简单介绍数组的基本语法

### 前言

JavaScript的`Array`是用于构造数组的全局对象，借用MDN上的一段话来描述数组

> 数组是一种**类列表对象**，它的原型中提供了**遍历**和**修改元素**的相关操作。JavaScript数组的长度和元素类型都是**非固定的**。因为数组的长度可以随时改变，并且其数据在内存中也可以不连续，所以 JavaScript 数组**不一定是密集型**的，这取决于它的使用方式。一般来说，数组的这些特性会给使用带来方便，但如果这些特性不适用于你的特定使用场景的话，可以考虑使用类型数组 `TypedArray`
> 
> 只能用**整数**作为数组元素的索引，而不能用字符串。使用**非整数并通过方括号**或**点号**来访问或设置数组元素时，所操作的并不是数组列表中的元素，而是数组对象的**属性集合**上的变量。**数组对象的属性**和**数组元素列表**是分开存储的，并且数组的遍历和修改操作也不能作用于这些命名属性。

第二段告诉我们一个信息，我用一个例子来讲的明白些

```
let a = [2, 3]
// 这样我们声明了一个数组a，那么我们的数组元素就是2和3

a.push(4);
// 像这样我们通过点号来访问的push就是数组对象的属性
```

### 一丶语法

#### 1. 声明数组
声明一个数组总共有三种方法

```
// 第一种，语法为 [element0, element1, ..., elementN]
let a = [0, 1, 2];
// 第二种，语法为 new Array(element0, element1[, ...[, elementN]])
let b = new Array(1, 'dd', true);
// 第三种，语法为 new Array(arrayLength)
let c = new Array(6);
```
**参数**

上面的语法总共有两种参数

**elementN**

`Array`构造器会根据给定的元素创建一个 JavaScript 数组，但是**当仅有一个参数且为数字时除外**。注意，后面这种情况仅适用于用`Array`构造器创建数组，而不适用于用方括号创建的数组字面量

**arrayLength**

一个整数，此时将返回一个`length`的值等于`arrayLength`的数组对象(言外之意就是该数组此时并没有包含任何实际的元素，不能理所当然地认为它包含 `arrayLength` 个值为` undefined` 的元素)。如果传入的参数不是有效值，则会抛出异常。

> **注意：用数组构造函数创建数组的时候，new字符可以省略**

#### 2. 访问数组元素

JavaScript 数组的索引是从 0 开始的，第一个元素的索引为 0 ，最后一个元素的索引等于该数组的长度减1。**如果指定的索引是一个无效值，JavaScript 数组并不会报错，而是会返回 `undefined`**。

```
let arr = [0, 1, 2];
console.log(arr[0]); // 0
console.log(arr[1]); // 1
console.log(arr[arr.length - 1]); // 2
console.log(arr[3]); // undefined
```

之前对于数组的描述中说过，使用**非整数并通过方括号**或**点号**来访问或设置数组元素时，所操作的并不是数组列表中的元素，而是数组对象的**属性集合**上的变量，也就是说访问的其实是数组的对象或方法。

```
console.log(arr.0); // 报错
```
那么像这样的代码就会报错，因为使用了非法的属性名，通过**点号**访问的是数组对象的属性或方法，但是 JavaScript 有一个规则，以数字开头的属性不能用点号引用，必须用**方括号加引号**的方式。比如，如果一个对象有一个名为`3d`的属性，那么只能用方括号来引用它

```
renderer.3d.setTexture(model, 'character.png');     // 语法错误
renderer['3d'].setTexture(model, 'character.png');  // √
```

---

### 二丶属性

#### 1. Array.length

`length`是`Array`的实例属性。返回或设置一个数组中的元素个数。该值是一个无符号 32-bit 整数，并且总是大于数组最高项的下标

**`length`属性的值是一个 0 到 2的32次方 - 1 的整数**，不在此范围内无效
```
var namelistA = new Array(4294967296); // 2的32次方 = 4294967296 
var namelistC = new Array(-100) // 负号

console.log(namelistA.length); // RangeError: 无效数组长度 
console.log(namelistC.length); // RangeError: 无效数组长度 



var namelistB = []; 
namelistB.length = Math.pow(2,32)-1; // 设置一个最大值的数组
console.log(namelistB.length); // 4294967295
```

##### (1). length 和数字下标之间的关系

JavaScript 数组的 `length` 属性和其数字下标之间有着紧密的联系。数组内置的几个方法（例如 `join`、`slice`、`indexOf` 等）都会考虑 `length` 的值。另外还有一些方法（例如 `push`、`splice` 等）还会改变 `length` 的值。

```
var fruits = [];
fruits.push('banana', 'apple', 'peach');

console.log(fruits.length); // 3
```

`length`有许多妙用的方法，能使代码简单许多

##### (2). 扩大数组
使用一个合法的下标为数组元素赋值，并且该下标超出了当前数组的大小的时候，解释器会同时修改 `length` 的值，比如下面的代码
```
let arr = [0, 1, 2];
arr[5] = 'ddd';
console.log(arr.length); // 6
console.log(arr); // [0, 1, 2, , , 'ddd']
```
我们一开始声明的数组长度只有3，然后我们给数组的第6个位置上赋值，会发现数组的长度变为了 6 但是数组的第4和第5位置上为空。

也可以显示的给 `length` 赋一个更大的值，达到的效果是一样的

> **注意：这里的第4和第5的位置上为空，而不是`undefined`，但是当我们去通过方括号访问该位置的值时，会返回`undefined`，这是因为指定的索引是一个无效值**

##### (3). 截断数组

如果给 `length` 赋一个更小的值则会删掉一部分元素，将长度截断，比如
```
let arr = [0, 1, 2, 3, 4];
arr.length = 3;
console.log(arr.length); // 3
console.log(arr); // [0, 1, 2]
```
这样，数组中的3，4就会被删除

#### 2. get Array[@@species]

`Array[@@species]`访问器属性返回`Array`的构造函数

使用如下

```
console.log(Array[Symbol.species]); // function Array()
```

--- 

### 三丶方法

这里说的方法是构造函数`Array`上的方法，而不是数组实例的方法，换句话说不是原型上的方法，原型上的方法我们稍后介绍

#### 1. Array.from()

该方法是ES6提出的，该方法从一个类似数组或可迭代对象中创建一个新的数组实例

##### (1). 语法
> `Array.from(arrayLike[, mapFn[, thisArg]])`

参数有三个
- `arrayLike`：想要转换成数组的伪数组或可迭代对象
- `mapFn`(可选参数)：如果指定了该参数，新数组中的每个元素会执行该回调函数
- `thisArg`(可选参数)：可选参数，执行回调函数 `mapFn` 时 `this` 对象

该方法**返回一个新的数组实例**

`Array.from`可以通过两种类型来创建一个新数组，分别是**伪数组对象**和**可迭代对象**

##### (2). 使用
具体使用如下

**对字符串使用**
```
Array.from('foo'); // ["f", "o", "o"]
```
**对Set使用**
```
let s = new Set(['foo', window]); 
Array.from(s); 
// ["foo", window]
```
**对Map使用**
```
let m = new Map([[1, 2], [2, 4], [4, 8]]);
Array.from(m); 
// [[1, 2], [2, 4], [4, 8]]
```
**对伪数组对象使用**
```
function f() {
  return Array.from(arguments);
}

f(1, 2, 3);

// [1, 2, 3]
```

这个方法用在数组去重还是非常方便的，数组去重我们就不讨论了，在下篇博客里会有

##### (3). 源码

因为`Array.from`是ES6提出的新方法，那么肯定会有用ES5的实现方法，也就相当于源码了，接下来一起看一下


#### 2. Array.isArray()

该方法用于确定传递的值是否是一个 `Array`

##### (1). 语法
> `Array.isArray(obj)`

参数只有一个，就是需要检测的值`obj`

返回一个布尔值，如果对象是`Array`返回为`true`，否则为`false`

##### (2). 使用
这个使用方法就很简单了

```
// 下面的函数调用都返回 true
Array.isArray([]);
Array.isArray([1]);
Array.isArray(new Array());
// 鲜为人知的事实：其实 Array.prototype 也是一个数组。
Array.isArray(Array.prototype); 

// 下面的函数调用都返回 false
Array.isArray();
Array.isArray({});
Array.isArray(null);
Array.isArray(undefined);
Array.isArray(17);
Array.isArray('Array');
Array.isArray(true);
Array.isArray(false);
```

##### (3). 源码

`Array.isArray`的内部其实是使用`Object.prototype.toString`实现的

```
if (!Array.isArray) {
  Array.isArray = function(arg) {
    return Object.prototype.toString.call(arg) === '[object Array]';
  };
}
```

#### 3. Array.of()

`Array.of()` 方法创建一个具有可变数量参数的新数组实例，而不考虑参数的数量或类型。

其实`Array.of()`和`Array`很像，只有一个区别

 `Array.of()` 和 `Array` 构造函数之间的区别在于处理整数参数：`Array.of(7)` 创建一个具有单个元素 7 的数组，而 `Array(7)` 创建一个长度为7的空数组

那么其实它出现的目的就是**为了解决构造器因参数个数不同，导致的行为有差异的问题**

##### (1). 语法

> `Array.of(element0[, element1[, ...[, elementN]]])`

参数 `elementN` 表示元素内容

返回一个新的 `Array` 实例


##### (2). 使用

```
Array.of(1);         // [1]
Array.of(1, 2, 3);   // [1, 2, 3]
Array.of(undefined); // [undefined]
```

##### (3). 源码

```
if (!Array.of) {
  Array.of = function() {
    return Array.prototype.slice.call(arguments);
  };
}
```

---

### 四丶数组实例

所有数组实例都会从 `Array.prototype` 继承属性和方法。修改 `Array` 的原型会影响到所有的数组实例

那么接下来是重头戏了，将会介绍大量的 `Array` 原型上的方法，这些方法对于我们来说十分有用，可以很简单的解决当前的问题

根据MDN这些方法可大致分为三类

- 修改器方法
- 访问方法
- 迭代方法

我们将一个一个介绍

#### 1. 修改器方法(9 个)

这些方法会改变调用它们的对象自身的值，也就是说返回的是原来的数组

#### 1.1 Array.prototype.copyWithin()

该方法会将一个数组中指定位置的成员复制到相同数组的另一个位置，并且返回这个数组，**但是不修改数组的长度**，这个方法目前处于实验阶段，尽量不要在生产环境中使用

##### (1). 语法

> `arr.copyWithin(target[, start[, end]])`

有三个参数

- `target`：复制序列到该位置。如果是负数，`target`将从末尾开始计算
- `start`(可选)：开始复制元素的起始位置。如果是负数，`start`将从末尾开始计算，默认为0
- `end`(可选)：开始复制元素的结束位置。`copyWithin` 将会拷贝到该位置，但不包括 end 这个位置的元素。如果是负数， end 将从末尾开始计算，默认为`this.length`

这个方法会返回改变了的数组

##### (2). 使用

```
[1, 2, 3, 4, 5].copyWithin(-2);
// [1, 2, 3, 1, 2]
// target为负，从倒数开始算，因为没有start和end，所以将在3号位置向后依次复制为0号到数组结尾

[1, 2, 3, 4, 5].copyWithin(0, 3);
// [4, 5, 3, 4, 5]
// 在0号位置向后依次复制为从3号到数组结尾

[1, 2, 3, 4, 5].copyWithin(0, 3, 4);
// [4, 2, 3, 4, 5]
// 在0号位置向后依次复制为从3号到4号，不包括4号

[1, 2, 3, 4, 5].copyWithin(-2, -3, -1);
// [1, 2, 3, 3, 4]
// 在3号位置向后依次复制为从2号到4号，不包括4号
```

#### 1.2 Array.prototype.fill()

该方法用一个固定值填充一个数组中从起始索引到终止索引的全部元素，不包括终止索引，这个方法目前处于实验阶段，尽量不要在生产环境中使用

##### (1). 语法

> `arr.fill(value[, start[, end]])`

有三个参数
	- `value`：用来填充数组元素的值
	- `start`：起始索引，默认值为0，如果是负数，`start`将从末尾开始计算，
	- `end`：结束索引，默认值为`this.length`，如果是负数，`end`将从末尾开始计算，

这个方法会返回改变了的数组

##### (2). 使用
```
[1, 2, 3].fill(4);               
// [4, 4, 4]
// `value`为4，没有起始索引和结束索引，将全数组复制为4

[1, 2, 3].fill(4, 1);            
// [1, 4, 4]
// `value`为4，没有结束索引，将从1号位置一直填充到数组尾部

[1, 2, 3].fill(4, 1, 2);         
// [1, 4, 3]
[1, 2, 3].fill(4, 1, 1);         
// [1, 2, 3]
[1, 2, 3].fill(4, 3, 3);         
// [1, 2, 3]
[1, 2, 3].fill(4, -3, -2);       
// [4, 2, 3]

[1, 2, 3].fill(4, NaN, NaN);     
// [1, 2, 3]
// 传入无效的索引，不会填充

[1, 2, 3].fill(4, 3, 5);         
// [1, 2, 3]
// 传入的起始索引超出了数组长度，不会填充

Array(3).fill(4);                
// [4, 4, 4]

// Objects by reference.
var arr = Array(3).fill({}) 
// [{}, {}, {}];
arr[0].hi = "hi"; 
// [{ hi: "hi" }, { hi: "hi" }, { hi: "hi" }]

// 当我们使用对象填充的时候，填充数组的是这个对象的引用，所以改变其中一个对象的值，所有对象的值都会改变
```

#### 1.3 Array.prototype.pop()

`pop()`方法从数组中删除最后一个元素，并返回该元素的值，类似于弹出栈。这个方法更改数组的长度

##### (1). 语法

> `arr.pop()`

没有参数

返回值是删除的元素(当数组为空的时候返回`undefined`)

> **注意：`pop` 方法有意具有通用性。该方法和 `call()` 或 `apply()` 一起使用时，可应用在类似数组的对象上。`pop`方法根据 `length`属性来确定最后一个元素的位置。如果不包含 `length` 属性或 `length` 属性不能被转成一个数值，会将`length`置为0，并返回`undefined`**

##### (2). 使用

```
let myFish = ["angel", "clown", "mandarin", "surgeon"];

let popped = myFish.pop();

console.log(myFish); 
// ["angel", "clown", "mandarin"]

console.log(myFish.length);
// 3
 
console.log(popped); 
// surgeon
```

#### 1.4 Array.prototype.push()

`push()`方法将一个或多个元素添加到数组的末尾，并返回该数组的新长度，类似于推入栈，这个方法更改了数组的长度

##### (1). 语法

> `arr.push(element1, element2, ...elementn)`

只有一类参数，elementN是添加到数组末尾的元素

返回的是添加元素后的数组的`length`属性

> **注意：`push` 方法有意具有通用性。该方法和 `call()` 或 `apply()` 一起使用时，可应用在类似数组的对象上。`push` 方法根据 `length` 属性来决定从哪里开始插入给定的值。如果 `length` 不能被转成一个数值，则插入的元素索引为 0，包括 `length` 不存在时。当 `length` 不存在时，将会创建它。**

##### (2). 使用

```
var sports = ["soccer", "baseball"];
var total = sports.push("football", "swimming");

console.log(sports); 
// ["soccer", "baseball", "football", "swimming"]

console.log(total);  
// 4
```

可以使用这个方法来**合并数组**
```
var vegetables = ['parsnip', 'potato'];
var moreVegs = ['celery', 'beetroot'];

// 将第二个数组融合进第一个数组
// 相当于 vegetables.push('celery', 'beetroot');
Array.prototype.push.apply(vegetables, moreVegs);

console.log(vegetables); 
// ['parsnip', 'potato', 'celery', 'beetroot']
```

#### 1.5 Array.prototype.reverse()

`reverse()`方法将数组中元素的位置颠倒，第一个数组元素成为最后一个数组元素，最后一个数组元素成为第一个数组元素

##### (1). 语法

> `arr.reverse()`

该方法没有参数，返回颠倒后的数组


##### (2). 使用
```
var myArray = ['one', 'two', 'three'];
myArray.reverse(); 

console.log(myArray) // ['three', 'two', 'one']
```

#### 1.6 Array.prototype.shift()

`shift()`方法从数组中删除第一个元素，并返回该元素的值，类似于出队列，这个方法更改了数组的长度

##### (1). 语法

> `arr.shift()`

该方法没有参数

返回从数组中删除的元素，如果数组为空则返回`undefined`

###### (2). 使用

```
let myFish = ['angel', 'clown', 'mandarin', 'surgeon'];

console.log('调用 shift 之前: ' + myFish);
// "调用 shift 之前: angel,clown,mandarin,surgeon"

var shifted = myFish.shift(); 

console.log('调用 shift 之后: ' + myFish); 
// "调用 shift 之后: clown,mandarin,surgeon" 

console.log('被删除的元素: ' + shifted); 
// "被删除的元素: angel"
```

#### 1.7 Array.prototype.unshift()

`unshift()`方法将一个或多个元素添加到数组的开头，并返回该数组的新长度，类似于进队列，这个方法更改了数组的长度

##### (1). 语法

> `arr.unshift(element1, ..., elementN)`

这个方法有一种参数`elemenyN`，是要添加到数组开头的元素

返回值是添加元素后的 `length` 值

##### (2). 使用
```
var arr = [1, 2];

arr.unshift(0); //result of call is 3, the new array length
//arr is [0, 1, 2]

arr.unshift(-2, -1); // = 5
//arr is [-2, -1, 0, 1, 2]

arr.unshift( [-3] );
//arr is [[-3], -2, -1, 0, 1, 2]
```

#### 1.8 Array.prototype.sort()

`sort()`方法用原地算法对数组的元素进行排序，并返回数组。排序不一定是稳定的。默认的排序顺序是根据字符串 `Unicode` 码点

##### (1). 语法

> `arr.sort([compareFunction])`

参数只有一个

- `compareFunction`(可选)：用来指定按某种顺序进行排列的函数。如果省略，元素会按照转换为的字符串的各个字符的`Unicode`位点进行排序

该方法返回的是排序后的数组，需要注意的是，数组不进行复制，而是原地排序

这里我们详细说一下`compareFunction`

**如果没有指明`compareFunction`**，那么元素会按照转换为的字符串的诸个字符的`Unicode`位点进行排序。例如"Banana"会被排序到"cherry"之前。当数字按由小到大排序时，9 出现在 80 之前，但因为没有指明 `compareFunction`，比较的数字会先被转换为字符串，所以在`Unicode`顺序上 "80" 要比 "9" 要靠前。

**如果指明了`compareFunction`**，那么数组会按照调用该函数的返回值进行排序。如果传入两个参数`a`和`b`是将要进行比较的元素，那么：
- 如果 `compareFunction(a, b)` 小于 0 ，那么 a 会被排列到 b 之前；
- 如果 `compareFunction(a, b)` 等于 0 ， a 和 b 的相对位置不变。
- 如果 `compareFunction(a, b)` 大于 0 ， b 会被排列到 a 之前。
> `compareFunction(a, b)`必须总是对相同的输入返回相同的比较结果，否则排序的结果将是不确定的

##### (2). 使用

比较函数的大概格式如下

```
function compare(a, b) {
  if (a < b ) {           // 按某种排序标准进行比较, a 小于 b
    return -1;
  }
  if (a > b ) {
    return 1;
  }
  // a must be equal to b
  return 0;
}
```

**`sort`排序常见用法：**

数组元素为数字的升序和降序：

```
let a = [9, 10, 2, 5, 4, 6, 7, 8, 1, 3];
a.sort((a, b) => a - b);
console.log(a); // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
```

数组多条件排序：
```
    let array = [{
	 	id:10,
	 	age:2
	}, {
	 	id:5,
	 	age:4
	}, {
	 	id:6,
	 	age:10
	}, {
	 	id:9,
	 	age:6
	}, {
	 	id:2,
	 	age:8
	}, {
	 	id:10,
	 	age:9
	}];

 	array.sort(function(a,b){
		if (a.id === b.id) {// 如果id的值相等，按照age的值降序
	        return b.age - a.age
	     } else { // 如果id的值不相等，按照id的值升序
	         return a.id - b.id
	     }
	})

 	console.log(array);
```

#### 1.9 Array.prototype.splice()

`splice()`方法通过删除现有元素和添加新元素来修改数组，并返回原数组被删除的内容

##### (1). 语法

> `array.splice(index,howmany,item1,.....,itemX)`

有三个参数：

- `start`：指定修改的开始位置(从0计数)。如果超出了数组的长度，则从数组末尾开始添加内容；如果是负值，则表示从数组末位开始的第几位（从-1计数）；如果负数的绝对值大于数组的长度，则表示开始位置为第0位。
- `deleteCount`(可选)：整数，表示要移除的数组元素的个数。**如果`deleteCount`是 0 或者负数，则不移除元素。**这种情况下，至少应添加一个新元素。**如果`deleteCount`大于`start`之后的元素的总数，则从`start`后面的元素都将被删除。如果`deleteCount`省略，则相当于`(arr.length - start)`**
- `itemN`：要添加进数组的元素，从`start`位置开始。如果不指定，则`splice()`将只删除数组元素

这个方法有些复杂，它的删除或添加都取决于第二个参数是否为 0

> **splice方法使用deleteCount参数来控制是删除还是添加**
> 
> 如果`deleteCount`不为 0 ，后面还有`item`，则表示**替换**，会先将原数组索引位置的元素删除，再在该位置前添加`item`

返回值：由被删除的元素组成的一个数组。如果只删除了一个元素，则返回只包含一个元素的数组。如果没有删除元素，则返回空数组。

##### (2). 使用

从第 2 位开始删除 0 个元素，插入`drum`
```
let myFish = ["angel", "clown", "mandarin", "surgeon"]; 
// 从第 2 位开始删除 0 个元素，插入 "drum" 
let removed = myFish.splice(2, 0, "drum"); 
// 运算后的 myFish:["angel", "clown", "drum", "mandarin", "surgeon"] 
// 被删除元素数组：[]，没有元素被删除
```
从第 3 位开始删除 1 个元素
```
let myFish = ['angel', 'clown', 'drum', 'mandarin', 'sturgeon'];
let removed = myFish.splice(3, 1);
// 运算后的myFish：["angel", "clown", "drum", "sturgeon"]
// 被删除元素数组：["mandarin"]
```
从第 2 位开始删除 1 个元素，然后插入`trumpet`
```
let myFish = ['angel', 'clown', 'drum', 'sturgeon'];
let removed = myFish.splice(2, 1, "trumpet"); 
// 运算后的myFish: ["angel", "clown", "trumpet", "surgeon"] 
// 被删除元素数组：["drum"]
```
从第0位开始删除2个元素，然后插入`parrot`,`anemone`和`blue`
```
let myFish = ['angel', 'clown', 'trumpet', 'sturgeon'];
let removed = myFish.splice(0, 2, 'parrot', 'anemone', 'blue');
// 运算后的myFish： ["parrot", "anemone", "blue", "trumpet", "sturgeon"] 
// 被删除元素数组：["angel", "clown"]
```

#### 2. 访问方法(9 个)

访问方法**绝对不会改变调用它们的对象的值**，只会返回一个新的数组或者返回一个其它的期望值

#### 2.1 Array.prototype.concat()

`concat()`方法用于合并两个或多个数组。

##### (1). 语法

> `let new_array = old_array.concat(value1, value2, ....valueN)`

参数有一个

- `valueN`：将数组或值连接成新数组

返回值：

新的`Array`实例

> `concat`方法不会改变`this`或任何作为参数提供的数组，而是返回一个浅拷贝，它包含于原始数组相结合的相同元素的副本。原始数组的元素将复制到新数组中，如下所示：
> 
>  - 对象引用（而不是实际对象）：`concat`将对象引用复制到新数组中。 原始数组和新数组都引用相同的对象。 也就是说，如果引用的对象被修改，则更改对于新数组和原始数组都是可见的。 这包括也是数组的数组参数的元素。
>  - 数据类型如字符串，数字和布尔：concat将字符串和数字的值复制到新数组中。

##### (2). 使用

**连接两个数组**
```
let a = [0, 1, 2, 3];
let b = ['a', 'b', 'c'];
console.log(a.concat(b));
// [0, 1, 2, 3, 'a', 'b', 'c'];
```

**将值连接到数组**
```
let a = ['a', 'b', 'c'];
let b = a.concat(1, [2, 3]);

console.log(b); 
// ['a', 'b', 'c', 1, 2, 3];
```

**合并嵌套数组**
```
let a = [[4], 1, 2];
let b = [5, [6]];
let c = a.concat(b);
console.log(c);
// [[4], 1, 2, 5, [6]]
```

#### 2.2 Array.prototype.includes()

`includes()`方法用来判断一个数组是否包含一个指定的值，根据情况，如果包含则返回`true`，否则返回`false`

该方法目前处于实验阶段，尽量不要在生产环境中使用

##### (1). 语法

> `arr.includes(searchElement, fromIndex)`

有两个参数
- `searchElement`：需要查找的元素值
- `fromIndex`(可选)：从该索引处开始查找`searchElement`。如果为负值，则按升序从`array.length - fromIndex`的索引开始搜索。默认为 0。

返回值：一个`Boolean`

##### (2). 使用

```
[1, 2, 3].includes(2);     // true
[1, 2, 3].includes(4);     // false
[1, 2, 3].includes(3, 3);  // false
[1, 2, 3].includes(3, -1); // true
[1, 2, NaN].includes(NaN); // true
```
如果`fromIndex`大于等于数组长度，则一定返回`false`。该数组不会被搜索
```
let arr = ['a', 'b', 'c'];

arr.includes('c', 3);   //false
arr.includes('c', 100); // false
```
如果 `fromIndex` 为负值，计算出的索引将作为开始搜索searchElement的位置。如果计算出的索引小于 0，则整个数组都会被搜索。
```
let arr = ['a', 'b', 'c'];

arr.includes('a', -100); // true
arr.includes('b', -100); // true
arr.includes('c', -100); // true
```

`includes()` 方法有意设计为通用方法。它不要求`this`值是数组对象，所以它可以被用于其他类型的对象 (比如类数组对象)。下面的例子展示了 在函数的`arguments`对象上调用的`includes()`方法。
```
(function() {
  console.log([].includes.call(arguments, 'a')); // true
  console.log([].includes.call(arguments, 'd')); // false
})('a','b','c');
```

#### 2.3 Array.prototype.join()

`join()`方法将一个数组(或者一个类数组对象)的所有元素连接成一个字符串并返回这个字符串

##### (1). 语法

> `arr.join(separator)`

有一个参数：
	- `separator`(可选)：指定一个字符串来分隔数组的每个元素，默认是`","`

返回值：

一个所有数组元素连接的字符串。如果`arr.length`为 0 ，则返回空字符串

> **注意：如果有某个元素为`null`或`undefined`则会转化成空字符串**

##### (2). 使用

```
let a = ['zhao', 'li', 'wang'];

console.log(a.join());
// zhao,li,wang

console.log(a.join(''));
// zhaoliwang

console.log(a.join('+'));
// zhao+li+wang
```

**如果数组的元素是数组呢**
```
let a = [0, 1, 2, [3, [4], 5]];
console.log(a.join());
// 0,1,2,3,4,5
```
可以发现，数组竟然可以递归调用，那么**普通对象**呢
```
let a = [0, 1, 2, {name: 'sss'}]
console.log(a.join());
// 0,1,2,[object Object]
```
会发现，普通对象返回的是它的类型

#### 2.4 Array.prototype.slice()

`slice()`方法返回一个新的数组对象，这一对象是一个由`start`和`end`(不包括`end`)决定的原数组的浅拷贝，原始数组不会被改变

##### (1). 语法

> `arr.slice(start, end)`

有两个参数：

- `start`(可选)：从该索引处开始提取数组中的元素，如果该参数为负数，则表示从原数组中的倒数第一个元素开始提取，`slice(-2)`表示提取原数组中的倒数第二个元素到最后一个元素。默认值为 0 
- `end`(可选)：在该索引处结束提取数组元素(从 0 开始)。`slice`会提取原数组中索引从`start`到`end`的所有元素(包含`start`，但不包括`end`)，如果该参数为负数，则它表示在原数组中的倒数第几个元素结束抽取。如果 `end` 被省略，则 `slice` 会一直提取到原数组末尾。如果 `end` 大于数组长度，slice 也会一直提取到原数组末尾。

返回值：

一个含有提取元素的新数组

> `slice` 不修改原数组，只会返回一个浅复制了原数组中的元素的一个新数组。原数组的元素会按照下述规则拷贝：
> - 如果该元素是个对象引用，`slice` 会拷贝这个对象引用到新的数组里。两个对象引用都引用了同一个对象。如果被引用的对象发生改变，则新的和原来的数组中的这个元素也会发生改变
> - 对于字符串、数字及布尔值来说，`slice` 会拷贝这些值到新的数组里。在别的数组里修改这些字符串或数字或是布尔值，将不会影响另一个数组。

##### (2). 使用

```
let fruits = ['Banana', 'Orange', 'Lemon', 'Apple', 'Mango'];
let citrus = fruits.slice(1, 3);

// fruits contains ['Banana', 'Orange', 'Lemon', 'Apple', 'Mango']
// citrus contains ['Orange','Lemon']
```

#### 2.5 Array.prototype.toString()

`toString()`返回一个字符串，表示指定的数组及其元素

##### (1). 语法

> `arr.toString()`

返回值：

一个表示指定的数组及其元素的字符串

##### (2). 使用

```
let a = [0, 1, 2, 3, 4];
console.log(a.toString());
// 0,1,2,3,4
```

#### 2.6 Array.prototype.toLocaleString()

这个方法比较特别，它返回一个表示数组的字符串。该字符串由数组中的每个元素的`toLocaleString()`返回值经调用`join()`方法连接组成

##### (1). 语法

> `array.toLocaleString()`

##### (2). 使用

```
let a=[{name:'OBKoro1'},23,'abcd',new Date()];
let str=a.toLocaleString(); // [object Object],23,abcd,2018/5/28 下午1:52:20 
```
可以看到，他是调用了每个元素的`toLocaleString()`方法组成的字符串

#### 2.7 Array.prototype.indexOf()

`indexOf()`方法返回在数组中可以找到一个给定元素的第一个索引，如果不存在，则返回 -1

##### (1). 语法

> `arr.indexOf(searchElement, fromIndex)`

有两个参数：

- `searchElement`：要查找的元素
- `fromIndex`(可选)：开始查找的位置。如果该索引值大于或等于数组长度，意味着不会在数组里查找，返回-1。如果参数中提供的索引值是一个负值，则将其作为数组末尾的一个抵消，即-1表示从最后一个元素开始查找，-2表示从倒数第二个元素开始查找 ，以此类推。 注意：如果参数中提供的索引值是一个负值，并不改变其查找顺序，查找顺序仍然是从前向后查询数组。如果抵消后的索引值仍小于0，则整个数组都将会被查询。其默认值为0.

返回值：

首个被找到的元素在数组中的索引位置; 若没有找到则返回 -1 

##### (2). 使用

```
let array = [2, 5, 9];
array.indexOf(2);     // 0
array.indexOf(7);     // -1
array.indexOf(9, 2);  // 2
array.indexOf(2, -1); // -1
array.indexOf(2, -3); // 0
```

#### 2.8 Array.prototype.lastIndexOf()

`lastIndexOf()` 方法返回指定元素在数组中的最后一个的索引，如果不存在则返回 -1。从数组的后面向前查找，从 `fromIndex` 处开始。

可以说和`Array.prototype.indexOf`刚好相反

##### (1). 语法

> `arr.lastIndexOf(searchElement, fromIndex)`

有两个参数：

- `searchElement`：被查找的元素
- `fromIndex`(可选)：从此位置开始逆向查找。默认为数组的长度减 1，即整个数组都被查找。如果该值大于或等于数组的长度，则整个数组会被查找。如果为负值，将其视为从数组末尾向前的偏移。即使该值为负，数组仍然会被从后向前查找。如果该值为负时，其绝对值大于数组长度，则方法返回 -1，即数组不会被查找。

返回值：

数组中最后一个元素的索引，如未找到返回-1


##### (2). 使用

```
let array = [2, 5, 9, 2];
let index = array.lastIndexOf(2);
// index is 3
index = array.lastIndexOf(7);
// index is -1
index = array.lastIndexOf(2, 3);
// index is 3
index = array.lastIndexOf(2, 2);
// index is 0
index = array.lastIndexOf(2, -2);
// index is 0
index = array.lastIndexOf(2, -1);
// index is 3
```

可以看到和`indexOf`的寻找顺序正好相反


#### 2.9 Array.prototype.flat()

`flat()`方法会递归到指定深度将所有子数组连接，并返回一个新数组。也就是差不多相当于数组的扁平化。

##### (1). 语法

> `var newArray = arr.flat(depth)`

参数：
- `depth`(可选)：指定嵌套数组中的结构深度，默认值为 1

返回值：

一个将子数组连接的新数组

> 如果数组中有空值，`flat`方法会忽略到该值

##### (2). 使用

```
var arr1 = [1, 2, [3, 4]];
arr1.flat(); 
// [1, 2, 3, 4]

var arr2 = [1, 2, [3, 4, [5, 6]]];
arr2.flat();
// [1, 2, 3, 4, [5, 6]]

var arr3 = [1, 2, [3, 4, [5, 6]]];
arr3.flat(2);
// [1, 2, 3, 4, 5, 6]

var arr4 = [1, 2, , 4, 5];
arr4.flat();
// [1, 2, 4, 5]
```



#### 3. 迭代方法(12 个)

在下面的众多遍历方法中，有很多方法都需要指定一个**回调函数**作为参数。

在每一个数组元素都分别执行完回调函数之前，**数组的length属性会被缓存在某个地方**，所以，如果你在回调函数中为当前数组添加了新的元素，**那么那些新添加的元素是不会被遍历到的。**

此外，如果在回调函数中对当前数组进行了其它修改，比如**改变某个元素的值或者删掉某个元素**，那么随后的遍历操作可能会**受到未预期的影响**。

总之，不要尝试在遍历过程中对原数组进行任何修改，虽然规范对这样的操作进行了详细的定义，但为了可读性和可维护性，请不要这样做。

#### 3.1 Array.prototype.forEach()

`forEach()`方法对数组的每个元素执行一次提供的函数

##### (1). 语法

> ```
> array.forEach(callback(currentValue, index, array) {
>      // do something
> }, this)
> ```

参数：

- `callback`：数组中的每个元素执行的函数，该函数接受三个参数
- `currentValue`：数组中正在处理的当前元素
- `index`(可选)：数组中正在处理的当前元素的索引
- `array`(可选)：`forEach()`方法正在操作的数组
- `thisArg`(可选)：可选参数，当执行回调 函数时用作`this`的值

返回值：

`undefined`

> **注意：**
> `forEah`遍历的范围在第一次调用 `callback` 前就会确定。调用 `forEach` 后添加到数组中的项不会被 `callback` 访问到
> 
> 如果已经存在的值被改变，则传递给 `callback` 的值是 `forEach` 遍历到他们那一刻的值，已删除的项不会被遍历到。
> 
> 如果已访问的元素在迭代时被删除了，之后的元素将被跳过

##### (2). 使用

```
const items = ['item1', 'item2', 'item3'];

items.forEach(function(item){
  console.log(item)
});

// item1
// item2
// item3
```

**如果数组在迭代时被修改了，则其他元素会被跳过**

下面的例子输出"one", "two", "four"。当到达包含值"two"的项时，整个数组的第一个项被移除了，这导致所有剩下的向上移一个位置。因为元素 "four"现在在数组更新前的位置，"three"会被跳过。 `forEach()` 不会在迭代之前创建数组的副本。

```
var words = ["one", "two", "three", "four"];
words.forEach(function(word) {
  console.log(word);
  if (word === "two") {
    words.shift();
  }
});
// one
// two
// four
```


#### 3.2 Array.prototype.every()

`every()`方法测试数组的所有元素是否都通过了指定函数的测试，如果都通过返回`true`，有一个不通过，返回`false`，类似于 且

##### (1). 语法

> `arr.every(callback, thisArg)`

有无个参数：
- `callback`：用来测试每个元素的函数
- `currentValue`：数组正在处理的元素
- `index`(可选)：数组中正在处理的元素的索引值
- `array`(可选)：当前数组
- `thisArg`(可选)：执行`callback`时使用的`this`值

> `every` 方法为数组中的每个元素执行一次 `callback` 函数，**直到它找到一个使 `callback` 返回 `false` 的元素**
> 如果发现了一个这样的元素，**`every` 方法将会立即返回 `false`**。否则，`callback` 为每一个元素返回 `true`，`every` 就会返回 `true`。`callback` 只会为那些已经被赋值的索引调用。**不会为那些被删除或从来没被赋值的索引调用。**
> `every` 遍历的元素范围在**第一次调用 `callback` 之前**就已确定了。在调用 `every` 之后添加到数组中的元素不会被 `callback` 访问到。如果数组中存在的元素被更改，则他们传入 `callback` 的值是 `every` 访问到他们那一刻的值。**那些被删除的元素或从来未被赋值的元素将不会被访问到。**
> **空数组返回`true`**

##### (2). 使用

**检测所有数组元素的大小**

```
function isBigEnough(element, index, array) {
  return (element >= 10);
}
var passed = [12, 5, 8, 130, 44].every(isBigEnough);
// passed is false
passed = [12, 54, 18, 130, 44].every(isBigEnough);
// passed is true
```
	

#### 3.3 Array.prototype.some()


`some()`方法测试数组中的某些元素是否通过由提供的函数实现的测试。如果至少有一个通过则返回`true`，否则返回`false`，类似于 或


##### (1). 语法

> `arr.some(callback, thisArg)`

有五个参数：
- `callback`：用来测试每个元素的函数
- `currentValue`：数组正在处理的元素
- `index`(可选)：数组中正在处理的元素的索引值
- `array`(可选)：当前数组
- `thisArg`(可选)：执行`callback`时使用的`this`值

返回值：

如果有任意一个通过，则返回`true`，否则返回`false`

> `some` 为数组中的每一个元素执行一次 `callback` 函数，直到找到一个使得 `callback` 返回一个“真值”。
> **如果找到了这样一个值，`some` 将会立即返回 `true`。否则，`some` 返回 `false`。**
> `callback` 只会在那些”有值“的索引上被调用，不会在那些被删除或从来未被赋值的索引上调用。
> `some` 遍历的元素的范围在第一次调用 `callback`. 时就已经确定了。在调用 `some` 后被添加到数组中的值不会被 `callback` 访问到。
> 如果数组中存在且还未被访问到的元素被 `callback` 改变了，则其传递给 `callback` 的值是 `some` 访问到它那一刻的值。

##### (2). 使用

**测试数组元素的值**

```
function isBiggerThan10(element, index, array) {
  return element > 10;
}

[2, 5, 8, 1, 4].some(isBiggerThan10);  // false
[12, 5, 8, 1, 4].some(isBiggerThan10); // true
```

**判断数组元素中是否存在某个值**
```
var fruits = ['apple', 'banana', 'mango', 'guava'];

function checkAvailability(arr, val) {
  return arr.some(function(arrVal) {
    return val === arrVal;
  });
}

checkAvailability(fruits, 'kela');   // false
checkAvailability(fruits, 'banana'); // true
```

#### 3.4 Array.prototype.filter()

`filter()` 方法创建一个新数组, 其包含通过所提供函数实现的测试的所有元素。 

##### (1). 语法

> `var new_array = arr.filter(callback(currentValue, index, array), thisArg)`

有五个参数：

- `callback`：用来测试每个元素的函数
- `currentValue`：数组正在处理的元素
- `index`(可选)：数组中正在处理的元素的索引值
- `array`(可选)：当前数组
- `thisArg`(可选)：执行`callback`时使用的`this`值

返回值：

一个新的通过测试的元素的集合的数组，如果没有通过测试的元素则返回空数组

> `filter` 为数组中的每个元素调用一次 `callback` 函数，**并利用所有使得 `callback` 返回 `true` 或 等价于 `true` 的值 的元素创建一个新数组。**`callback` 只会在已经赋值的索引上被调用，对于那些已经被删除或者从未被赋值的索引不会被调用。那些没有通过 callback 测试的元素会被跳过，不会被包含在新数组中。
> `filter` 遍历的元素范围在第一次调用 `callback` 之前就已经确定了。在调用 `filter` 之后被添加到数组中的元素不会被 `filter` 遍历到。如果已经存在的元素被改变了，则他们传入 `callback` 的值是 `filter` 遍历到它们那一刻的值。被删除或从来未被赋值的元素不会被遍历到。

##### (2). 使用

**筛选排除掉所有的小值**
```
function isBigEnough(element) {
  return element >= 10;
}
var filtered = [12, 5, 8, 130, 44].filter(isBigEnough);
// filtered is [12, 130, 44]
```

#### 3.5 Array.prototype.map()

`map()` 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。

##### (1). 语法

> `var new_array = arr.map(function callback(currentValue, index, array) {}, 
thisArg)`

有五个参数：
- `callback`：用来测试每个元素的函数
- `currentValue`：数组正在处理的元素
- `index`(可选)：数组中正在处理的元素的索引值
- `array`(可选)：当前数组
- `thisArg`(可选)：执行`callback`时使用的`this`值

返回值：
一个新数组，每个元素都是回调函数的结果

##### (2). 使用

**求数组中每个元素的平方根**	
```
var numbers = [1, 4, 9];
var roots = numbers.map(Math.sqrt);
// roots的值为[1, 2, 3], numbers的值仍为[1, 4, 9]
```


#### 3.6 Array.prototype.find()

`find()` 方法返回数组中满足提供的测试函数的第一个元素的值。否则返回 `undefined`。

##### (1). 语法

> `arr.find(callback, thisArg)`

有五个参数：

- `callback`：用来测试每个元素的函数
- `currentValue`：数组正在处理的元素
- `index`(可选)：数组中正在处理的元素的索引值
- `array`(可选)：当前数组
- `thisArg`(可选)：执行`callback`时使用的`this`值

返回值：

返回数组中第一个满足函数要求的值，没有则返回`undefined`


##### (2). 使用

```
let a = [0, '1', 2, 3, '4'];
console.log(a.find(x => typeof x === 'string'));
// 1
```

#### 3.7 Array.prototype.findIndex()

`findIndex()` 方法返回数组中满足提供的测试函数的第一个元素的索引。否则返回-1。

这个方法和`find()`相同，只不过`find`返回值，这个方法返回索引

##### (1). 语法

> `arr.findIndex(callback, thisArg)`

有五个参数：

- `callback`：用来测试每个元素的函数
- `currentValue`：数组正在处理的元素
- `index`(可选)：数组中正在处理的元素的索引值
- `array`(可选)：当前数组
- `thisArg`(可选)：执行`callback`时使用的`this`值

返回值：

返回数组中第一个满足函数要求的索引，没有则返回`-1`

##### (2). 使用

```
let a = [1, 2, '3', 4, '5'];
console.log(a.findIndex(x => typeof x === 'string'));
// 2
```

#### 3.8 Array.prototype.reduce()

`reduce()` 方法对累计器和数组中的每个元素（从左到右）应用一个函数，将其简化为单个值。

##### (1). 语法

> `arr.reduce(callback, initialValue)`

参数： 

- `callback`：执行数组中每个值的函数，包含四个参数
- `accumulator`：累计器累计回调的返回值，它是上一次调用时返回的累积值，或`initialValue`
- `currentValue`：数组中正在处理的元素
- `currentIndex`(可选)：数组中正在处理的当前元素的索引。如果提供了`initialValye`，则索引号为 0 ，否则索引为 1
- `array`(可选)：调用`reduce()`的数组
- `initialValue`(可选)：作为第一次调用 `callback` 函数时的第一个参数的值。 如果没有提供初始值，则将使用数组中的第一个元素。 在没有初始值的空数组上调用 `reduce` 将报错。

返回值：

函数累计处理的结果

这里的四个参数仔细讲一下

> 回调函数第一次执行时，`accumulator`和`currentValue`的取值有两种情况：**如果调用`reduce()`时提供了`initialValue`，`accumulator`取值为`initialValue`，`currentValue`取数组中的第一个值。如果没有提供`initialValue`，那么`accumulator`取数组中的第一个值，`currentValue`取数组中的第二个值**

##### (2). 使用

```
let a = [1, 2, 3, 4, 5];
console.log(a.reduce((all, now) => all + now, 10));
// 25
```

#### 3.9 Array.prototype.reduceRight()

这个方法很简单，你把 `Array.prototype.reduce()`反着看就会了，这个就是从右到左的调用


#### 3.10 Array.prototype.entries()

`entries()`方法返回一个新的 **Array Iterator**对象，该对象包含数组中每个索引的键值对

这是一个实验性API，尽量不要在生产环境中使用	

##### (1). 语法

> `arr.entries()`

无参数。

返回一个新的`Array`迭代器对象。`Array Iterator` 是对象，它的原型上有一个 `next` 方法，可用于遍历迭代器取得原数组的 `[key, value]`

##### (2). 使用

**`Array Iterator`**
```
let arr = ["a", "b", "c"];
let iterator = arr.entries();
console.log(iterator);

/*Array Iterator {}
         __proto__:Array Iterator
         next:ƒ next()
         Symbol(Symbol.toStringTag):"Array Iterator"
         __proto__:Object
*/
```

**`iterator.next()`**

```
var arr = ["a", "b", "c"]; 
var iterator = arr.entries();
console.log(iterator.next());

/*{value: Array(2), done: false}
          done:false
          value:(2) [0, "a"]
           __proto__: Object
*/
// iterator.next()返回一个对象，对于有元素的数组，
// 是next{ value: Array(2), done: false }；
// next.done 用于指示迭代器是否完成：在每次迭代时进行更新而且都是false，
// 直到迭代器结束done才是true。
// next.value是一个["key":"value"]的数组，是返回的迭代器中的元素值。
```

**`iterator.next方法运行`**
```
var arr = ["a", "b", "c"];
var iter = arr.entries();
var a = [];

for(var i=0; i< arr.length+1; i++){    // 注意，是length+1，比数组的长度大
    var tem = iter.next();             // 每次迭代时更新next
    console.log(tem.done);             // 这里可以看到更新后的done都是false
    if(tem.done !== true){             // 遍历迭代器结束done才是true
        console.log(tem.value);
        a[i]=tem.value;
    }
}
    
console.log(a);                         // 遍历完毕，输出next.value的数组
```
**使用`for...of`**
```
var arr = ["a", "b", "c"];
var iterator = arr.entries();
// undefined

for (let e of iterator) {
    console.log(e);
}

// [0, "a"] 
// [1, "b"] 
// [2, "c"]
```

#### 3.11 Array.prototype.keys()

`keys()` 方法返回一个包含数组中每个索引键的 `Array Iterator` 对象。

##### (1). 语法

> `arr.keys()`

无参数

返回一个新的`Array`迭代器对象，迭代器对象只包含了数组所有元素的**索引**(包括元素不存在)

##### (2). 使用

```
let a = [1, 2, , 4, 5];
console.log([...a.keys()]);
// [0, 1, 2, 3, 4]
```
返回了5个元素的索引

#### 3.12 Array.prototype.values()

`values()` 方法返回一个新的 `Array Iterator` 对象，该对象包含数组每个索引的**值**

##### (1). 语法

> `arr.values()`

无参数

返回一个新的`Array`迭代器对象，迭代器对象只包含了数组所有元素的**值**(元素不存在则为`undefined`)

##### (2). 使用

```
let a = [1, 2, , 4, 5];
console.log([...a.values()]);
// [1, 2, undefined, 4, 5]
```

返回了5个元素的值

---

### 五丶总结

数组方法总共有 33 个(除去一个快删除的`toSource`和一个实验性api`flatMap`)

其中 `Array` 构造函数有 三个方法分别是

- `Array.from`：用来将伪数组和可迭代对象形成一个真正的数组对象
- `Array.isArray`：用来判断一个对象是否为数组
- `Array.of`：用来构造数组，解决了原生构造函数`Array`因为参数个数不同而产生的歧义的问题

`Array`实例的方法也就是`Array`原型上的方法总共有 30 种，其实有 32 种，有两种未算在内。这些方法分为

- 修改器方法 (9 种)
- 访问方法 (9 种)
- 迭代方法 (12 种)

其中修改器方法**在原数组上修改，不会重新创建数组**，共 9 种为：

-  `Array.prototype.push`：在数组的尾部添加元素
-  `Array.prototype.pop`：删除数组的最后一个元素
-  `Array.prototype.shift`：删除数组的第一个元素
-  `Array.prototype.unshift`：在数组的头部添加元素
-  `Array.prototype.reverse`：颠倒数组中元素的排列顺序
- `Array.prototype.sort`：对数组元素进行排序，并返回当前数组
- `Array.prototype.copyWithin`：在数组内部，将一段元素序列拷贝到另一段元素序列上，覆盖原有的值
- `Array.prototype.fill`：将数组指定位置的所有元素都替换为固定的值
- `Array.prototype.splice`：一个很强大的方法，在任意位置添加或删除任意个元素

访问方法是**绝对不会改变原数组的，只会返回一个新的数组或者其他的期望值**，共 9 种为：

- `Array.prototype.concat`：拼接数组，返回一个由当前数组和其它若干个数组或非数组组合而成的新数组
- `Array.prototype.slice`：截断数组，抽取当前数组中的一段元素组合成一个新数组
- `Array.prototype.flat`：数组扁平化，递归到指定深度后将所有子数组连接，并返回一个新数组
- `Array.prototype.join`：连接所有数组元素组成一个字符串
- `Array.prototype.toString`：返回一个由所有数组元素组合而成的字符串
- `Array.prototype.toLocalString`：返回一个由所有数组元素组合而成的本地化后的字符串
- `Array.prototype.includes`：判断当前数组是否包含某指定的值，如果是返回`true`，不是返回`false`
- `Array.prototype.indexOf`：返回数组中第一个与指定值相等的元素的索引
- `Array.prototype.lastIndexOf`：返回数组中最后一个与指定值相等的元素的索引

迭代方法大部分需要**指定一个回调函数**作为参数，不要尝试在遍历过程中对原数组进行任何修改，否则可能会引起预期之外的错误，共有 12 种为：

- `Array.prototype.forEach`：会对数组中的每个元素执行一次回调函数
- `Array.prototype.every`：检测数组中每一个元素，如果所有元素都满足函数的规则，返回`ture`，否则返回`false`
- `Array.prototype.some`：检测数组中每一个元素，如果至少有一个元素满足函数的规则，返回`ture`，否则返回`false`
- `Array.prototype.filter`：将所有在过滤函数中返回`true`的数组元素放进一个新数组中并返回
- `Array.prototype.map`：将所有元素执行回调函数后返回值构成一个新数组并返回
- `Array.prototype.find`：找到第一个满足测试函数的元素并返回那个元素的值，如果找不到，则返回`undefined`
- `Array.prototype.findIndex`：找到第一个满足测试函数的元素并返回那个元素的索引，如果找不到，则返回 -1
- `Array.prototype.reduce`：从左到右为每个数组元素执行一次回调函数，并把上次回调函数的返回值放在一个暂存器中传给下次回调函数，并返回最后一次回调函数的返回值。
- `Array.prototype.reduceRight`：从右到左为每个数组元素执行一次回调函数，并把上次回调函数的返回值放在一个暂存器中传给下次回调函数，并返回最后一次回调函数的返回值。
- `Array.prototype.keys`：返回一个数组迭代器对象，该迭代器会包含所有数组元素的键。
- `Array.prototype.values`：返回一个数组迭代器对象，该迭代器会包含所有数组元素的值。
- `Array.prototype.entries`：返回一个数组迭代器对象，该迭代器包含所有数组元素的键值对


---

##### 参考
- <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array">https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array</a>