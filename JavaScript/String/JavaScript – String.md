## JavaScript -- String

@(Interview)

本文目的是详细介绍**字符串的方法和属性**

`String` 全局对象是一个用于字符串或一个字符序列的构造函数


下面介绍的不论是方法还是属性，都是基本包装类型`String`上的，在调用字符串的属性和方法的时候，他会自动转换成基本包装类型，然后使用属性和方法

### 一丶属性

#### 1. `length`

每一个字符串都会有属性`length`返回字符串的长度


### 二丶方法

在构造函数`String`上有三种方法

#### 1. String.fromCharCode


### 三丶字符串实例

#### 1. String.prototype.fromCharCode

静态 `String.fromCharCode()` 方法返回使用指定的 `Unicode` 值序列创建的字符串。

##### (1). 语法

> `String.fromCharCode(num1, ..., numN) `

**参数：**
- `numN`：一组序列数字，表示 `Unicode` 值

**返回值：**
返回一个字符串，而不是一个 `String` 对象

##### (2). 使用

```
String.fromCharCode(65, 66, 67);
// ABC
typeof String.fromCharCode(65, 66, 67);
// string
```

#### 2. String.prototype.fromCodePoint

`String.fromCodePoint()` 静态方法返回使用指定的代码点序列创建的字符串。

##### (1). 语法

> `String.fromCodePoint(num1...numN])`

**参数：**
- `numN`：一串 `Unicode`  编码

**返回值：**
使用 `Unicode` 编码创建的字符串

##### (2). 使用

```
String.fromCodePoint(42);       // "*"
String.fromCodePoint(65, 90);   // "AZ"
String.fromCodePoint(0x404);    // "\u0404"
String.fromCodePoint(0x2F804);  // "\uD87E\uDC04"
String.fromCodePoint(194564);   // "\uD87E\uDC04"
String.fromCodePoint(0x1D306, 0x61, 0x1D307) // "\uD834\uDF06a\uD834\uDF07"
```


### 四丶字符串实例

这里讲的都是字符串实例，任何属性和方法都是 `String.prototype` 上的。

#### 1. String.prototype.charAt

`charAt()` 方法从一个字符串中返回指定的字符。

##### (1). 语法
> `str.charAt(index)`

**参数：**
- `index`：一个介于 0 和字符串长度减 1 之间的整数。如果没有提供索引，`charAt()`将使用 0

**如果指定的 `index`值 超过了该范围，则返回空字符串**

**返回：**
提供的索引所对应的字符

##### (2). 使用

```
		let anyString = 'zhao is shuai';

		console.log(anyString.charAt(2)); // a
		console.log(anyString.charAt(3)); // o
		console.log(anyString.charAt(5)); // i
		console.log(anyString.charAt(6)); // s
		console.log(anyString.charAt());  // 空字符串
		console.log(anyString.charAt(-1)); // 空字符串
		console.log(anyString.charAt(100));// 空字符串
```

#### 2. String.prototype.charCodeAt

和 `charAt` 方法差不多，但是他返回的是一个指定位置的字符对应的 `UTF-16` 代码单元值的数字

##### (1). 语法
> `str.charCodeAt(index)`

**参数：**
- `index`：一个大于等于 0，小于字符串长度的整数。如果不是一个数值，则默认为 0 

**返回值：**
返回值是一表示给定索引处字符的 `UTF-16` 代码单元值的数字；如果索引超出范围，则返回 `NaN`。

##### (2). 使用
```
		let anyString = 'zhao is shuai';

		console.log(anyString.charCodeAt(2)); // 97
		console.log(anyString.charCodeAt(3)); // 111
		console.log(anyString.charCodeAt(5)); // 105
		console.log(anyString.charCodeAt(6)); // 115
		console.log(anyString.charCodeAt());  // 122
		console.log(anyString.charCodeAt(-1)); // NaN
		console.log(anyString.charCodeAt(100));// NaN
		console.log(anyString.charCodeAt('ss'));// 122
```

#### 3. String.prototype.codePointAt

`codePointAt()` 方法返回 一个 `Unicode` 编码点值的非负整数。

##### (1). 语法

> `str.codePointAt(pos)`

**参数：**
- `pos`：这个字符串中需要转码的元素的位置

**返回值：**
返回值是在字符串中的给定索引的编码单元体现的数字，如果在索引处没找到元素则返回 `undefined`

##### (2). 使用

```
		let anyString = 'zhao is shuai';

		console.log(anyString.codePointAt(2)); // 97
		console.log(anyString.codePointAt(3)); // 111
		console.log(anyString.codePointAt(5)); // 105
		console.log(anyString.codePointAt(6)); // 115
		console.log(anyString.codePointAt());  // 122
		console.log(anyString.codePointAt(-1)); // undefined
		console.log(anyString.codePointAt(100));// undefined
		console.log(anyString.codePointAt('ss'));// 122
``` 

#### 4. String.prototype.concat

`concat()` 方法将一个或多个字符串与原字符串连接合并，**形成一个新的字符串并返回**。

##### (1). 语法
> `str.concat(string2, string3, ..., stringN)`

**参数：**
- `stringN`：和原字符串链连接的多个字符串

**返回值：**
返回合并后的新字符串，`concat`方法并不影响原字符串

##### (2). 使用

```
		let hello = "Hello, ";
		console.log(hello.concat("Kevin", " have a nice day.")); // Hello, Kevin have a nice day.
		console.log(hello) // Hello, 
```

**原字符串是不会修改的**

#### 5. String.prototype.includes

`includes()` 方法用于判断一个字符串是否包含在另一个字符串中，根据情况返回 true 或 false。

##### (1). 语法

> `str.includes(searchString, position)`

**参数：**

- `searchString`：要在此字符串中搜索的字符串
- `position`(可选)：从当前字符串的哪个索引位置开始搜寻字符串，默认值为 0

**返回值：**

如果当前字符串包含被搜寻的字符串，就返回 `true`；否则返回 `false`。

##### (2). 使用

```
		let hello = "Hello, ";
		console.log(hello.includes('H')); // true
		console.log(hello.includes('e')); // true
		console.log(hello.includes('l')); // true
		console.log(hello.includes('s')); // false
		console.log(hello.includes('l', 4)); // false
```

#### 6. String.prototype.endsWith

`endsWith()` 方法用来判断当前字符串是否是以另外一个给定的子字符串“结尾”的，根据判断结果返回 `true` 或 `false`。

#### 1. 语法

> `str.endsWith(searchString, position);`

**参数：**
- `searchString` ：要搜索的子字符串
- `position`：可选。作为`str`的长度，默认值为 `str.length`

**返回值：**
如果传入的子字符串在搜索字符串的末尾返回`true`，否则返回`false`


#### 2. 使用

```
let str = "To be, or not to be, that is the question.";

alert( str.endsWith("question.") );  // true
alert( str.endsWith("to be") );      // false
alert( str.endsWith("to be", 19) );  // true
```

#### 7. String.prototype.indexOf

`indexOf()` 方法返回调用  `String` 对象中第一次出现的指定值的索引，开始在 `fromIndex` 进行搜索。

##### (1). 语法

> `str.indexOf(searchValue, fromIndex)`

**参数：**
- `searchValue`：一个字符串表示被查找的值
- `fromIndex`(可选)：表示调用方法的字符串中开始查找的位置，默认为 0 。

**返回值：**
指定值的第一次出现的索引，如果没有找到，返回 -1

##### (2). 使用

```
		let hello = "Hello, My name is zhao";
		console.log(hello.indexOf('Hello')); // 0
		console.log(hello.indexOf('My')); // 7
		console.log(hello.indexOf('is')); // 15
		console.log(hello.indexOf('zh')); // 18
```
可以通过该方法检测某个字符串是否存在于另一个字符串中

#### 8. String.prototype.lastIndexOf

`lastIndexOf()` 方法返回指定值在调用该方法的字符串中最后出现的位置，如果没找到则返回 -1。从该字符串的后面向前查找，从 `fromIndex` 处开始。

##### (1). 语法

> `str.lastIndexOf(searchValue, fromIndex)`

**参数：**
- `searchValue`：一个字符串，表示被查找的值
- `fromIndex`：从调用该方法字符串的此位置处开始查找。可以是任意整数。默认值为 str.length

**返回值：**
从右向左，指定值的第一次出现的索引，如果没有找到，返回 -1

##### (2). 使用

```
		let hello = "Hello, My name is zhao hello";
		console.log(hello.lastIndexOf('el')); // 0
		console.log(hello.lastIndexOf('y')); // 8
		console.log(hello.lastIndexOf('is')); // 15
		console.log(hello.lastIndexOf('zh')); // 18
```

#### 9. String.prototype.localeCompare

`localeCompare()` 方法返回一个数字来指示一个参考字符串是否在排序顺序前面或之后或与给定字符串相同。

##### (1). 语法

> `referenceStr.localeCompare(compareString, locales, options)`

**参数：**
- `compareString`：用来比较的字符串


#### 10. String.prototype.match

当一个字符串与一个正则表达式匹配时， `match()` 方法检索匹配项。

##### (1). 语法

> `str.match(regexp)`

**参数：**
- `regexp`：一个正则表达式对象。如果传入一个非正则表达式对象，则会隐式地使用`new RegExp(obj)`将其转换为一个 `RegExp`。如果你未提供任何参数，直接使用`match()`，那么你会得到一个包含空字符串的 `Array`

**返回值：**

如果字符串匹配到了表达式，会返回一个数组，数组的第一项是进行匹配完整的字符串，之后的项是用圆括号捕获的结果。如果没有匹配到，返回 `null`


##### (2). 使用

```
var str = 'For more information, see Chapter 3.4.5.1';
var re = /see (chapter \d+(\.\d)*)/i;
var found = str.match(re);

console.log(found);

// logs [ 'see Chapter 3.4.5.1',
//        'Chapter 3.4.5.1',
//        '.1',
//        index: 22,
//        input: 'For more information, see Chapter 3.4.5.1' ]

// 'see Chapter 3.4.5.1' 是整个匹配。
// 'Chapter 3.4.5.1' 被'(chapter \d+(\.\d)*)'捕获。
// '.1' 是被'(\.\d)'捕获的最后一个值。
// 'index' 属性(22) 是整个匹配从零开始的索引。
// 'input' 属性是被解析的原始字符串。
```

#### 11. String.prototype.normalize

> `normalize()` 方法会按照指定的一种 Unicode 正规形式将当前字符串正规化.

#### 12. String.prototype.padEnd

> `padEnd`方法会用一个字符串填充当前字符串（如果需要的话则重复填充），返回填充后达到指定长度的字符串。从当前字符串的末尾（右侧）开始填充。

##### (1). 语法

> `str.padEnd(targetLength, padString)`

**参数：**
- `targetLength`：当前字符串需要填充到的目标长度。如果这个数值小于当前字符串的长度，则返回当前字符串本身
- `padString`(可选)：填充字符串。如果字符串太长，使填充后的字符串长度超过了目标长度，则只保留最左侧的部分，其他部分会被截断。如果不传，则是空字符

**返回值：**

在原字符串末尾填充指定的填充字符串直到目标长度所形成的新字符串。

##### (2). 使用

```
		let hello = 'abc';

		hello.padEnd(10) // abc     
		hello.padEnd(10, 'foo') // abcfoofoof
		hello.padEnd(6, '123456') // abc123
		hello.padEnd(1) // abc
```

#### 13. String.prototype.padStart

`padStart()` 方法用另一个字符串填充当前字符串(重复，如果需要的话)，以便产生的字符串达到给定的长度。填充从当前字符串的开始(左侧)应用的。

##### (1). 语法

> `str.padStart(targetLength, padString)`

**参数：**
- `targetLength`：当前字符串需要填充到的目标长度。如果这个数值小于当前字符串的长度，则返回当前字符串本身。
- `padString`(可选)：填充字符串。如果字符串太长，使填充后的字符串长度超过了目标长度，则只保留最左侧的部分，其他部分会被截断。如果不传，则是空字符

**返回值：**

在原字符串开头填充指定的填充字符串直到目标长度所形成的新字符串

##### (2). 使用

```
		let hello = 'abc';

		console.log(hello.padStart(10)) //           abc     
		console.log(hello.padStart(10, 'foo')) // foofoofabc
		console.log(hello.padStart(6, '123456')) // 123abc
		console.log(hello.padStart(1)) // abc
		
```

#### 14. String.prototype.repeat

`repeat()` 构造并返回一个新字符串，该字符串包含被连接在一起的指定数量的字符串的副本。**就是重复该字符串**

##### (1). 语法

> `str.repeat(count);`


**参数：**
- `count`：介于 0 和正无穷大之间的整数。表示在新构造的字符串中重复了多少遍字符串

**返回值：**

包含指定字符串的指定数量副本的新字符串。


##### (2). 使用
```
		let hello = 'abc';

		console.log(hello.repeat(0)) // 
		console.log(hello.repeat(1)) // abc
		console.log(hello.repeat(6)) // abcabcabcabcabcabc
		console.log(hello.repeat(3.5)) // abcabcabc
```


#### 15. String.prototype.replace

`replace()` 方法返回一个由替换值替换一些或所有匹配的模式后的新字符串。模式可以是一个字符串或者一个正则表达式, 替换值可以是一个字符串或者一个每次匹配都要调用的函数。**字符串替换**


##### (1). 语法

> `str.replace(regexp|substr, newSubStr|function)`

**参数：**
- `regexp`：一个`RegExp`对象或者其字面量。该正则所匹配的内容会被第二个参数的返回值替换掉
- `substr`：一个要被`newSubStr`替换的字符串。其被视为一整个字符串，而不是一个正则表达式。仅仅是第一个匹配会被替换
- `newSubStr`：用于替换掉第一个参数在原字符串中的匹配部分的字符串。该字符串中可以内插一些特殊的变量名。参考下面的使用字符串作为参数。
- `function`：一个用来创建新子字符串的函数，该函数的返回值将替换掉第一个参数匹配到的结果。

**返回值：**

一个部分或全部匹配由替代模式所取代的新的字符串。

##### (2). 使用

```
		let hello = 'abc zhao';
		console.log(hello.replace('abc', '123')); // 123 zhao
		console.log(hello.replace('abc', () => '123')); // 123 zhao
```

如果第一个参数是字符串，那么他只能用来替换第一个匹配的字符串，如果是正则，将字符串中的所有字符替换


#### 16. String.prototype.search

`search()` 方法执行正则表达式和 `String` 对象之间的一个搜索匹配

##### (1). 语法
> `str.search(regexp)`

**参数：**
- `regexp`：一个正则表达式（regular expression）对象。如果传入一个非正则表达式对象，则会使用 new RegExp(obj) 隐式地将其转换为正则表达式对象。

**返回值：**

如果匹配成功，则 `search()`返回正则表达式在字符串中首次匹配项的索引，否则，返回 -1 

##### (2). 使用
```
function testinput(re, str){
  var midstring;
  if (str.search(re) != -1){
    midstring = " contains ";
  } else {
    midstring = " does not contain ";
  }
  console.log (str + midstring + re);
}
```

#### 17. String.prototype.slice

`slice()` 方法提取一个字符串的一部分，并返回一新的字符串。

##### (1). 语法
> `str.slice(beginSlice, endSlice)`

**参数：**
- `beginSlice`：从该索引（以 0 为基数）处开始提取原字符串中的字符。如果值为负数，会被当做 sourceLength + beginSlice 看待，这里的sourceLength 是字符串的长度 (例如， 如果beginSlice 是 -3 则看作是: sourceLength - 3)
- `endSlice`(可选)：在该索引（以 0 为基数）处结束提取字符串。如果省略该参数，slice会一直提取到字符串末尾。如果该参数为负数，则被看作是 sourceLength + endSlice，这里的 sourceLength 就是字符串的长度(例如，如果 endSlice 是 -3，则是, sourceLength - 3)。

**返回值：**

返回一个从原字符串中提取出来的新字符串


##### (2). 使用

```
let str1 = 'The morning is upon us.';
let str2 = str1.slice(4, -2);console.log(str2); // OUTPUT: morning is upon u
```

#### 18. String.prototype.split

`split()`方法使用指定的分隔符字符串将一个`String`对象分割成字符串数组，以将字符串分隔为子字符串，以确定每个拆分的位置

##### (1). 语法

> `str.split(separator, limit)`

**参数：**
- `separator`：指定表示每个拆分应发生的点的字符串。separator 可以是一个字符串或正则表达式。 如果纯文本分隔符包含多个字符，则必须找到整个字符串来表示分割点。如果在str中省略或不出现分隔符，则返回的数组包含一个由整个字符串组成的元素。如果分隔符为空字符串，则将str原字符串中每个字符的数组形式返回。
- `limit`：一个整数，限定返回的分割片段数量。当提供此参数时，split 方法会在指定分隔符的每次出现时分割该字符串，但在限制条目已放入数组时停止。如果在达到指定限制之前达到字符串的末尾，它可能仍然包含少于限制的条目。新数组中不返回剩下的文本。

**返回值：**

返回源字符串以分隔符出现位置分隔而成的一个 `Array`

##### (2). 使用

```
		let str1 = 'The morning is upon us';
		let str2 = str1.split(' ');
		let str3 = str1.split(' ', 2);

		console.log(str2); // ["The", "morning", "is", "upon", "us"]
		console.log(str3); // ["The", "morning"]
```

#### 19. String.prototype.startsWith

`startsWith()` 方法用来判断当前字符串是否是以另外一个给定的子字符串“开头”的，根据判断结果返回 true 或 false。

##### (1). 语法

> `str.startsWith(searchString, position);`

**参数：**
- `searchString`：要搜索的子字符串。
- `position`：在` str `中搜索 `searchString` 的开始位置，默认值为 0，也就是真正的字符串开头处。

**返回值：**

如果能传入的字符串是该字符串开头，则返回`true`，否则返回`false`

##### (2). 使用

```
let str = "To be, or not to be, that is the question.";

console.log(str.startsWith("To be"));         // true
console.log(str.startsWith("not to be"));     // false
console.log(str.startsWith("not to be", 10)); // true
```

#### 20. String.prototype.substr

`substr()`方法返回一个字符串中从指定位置开始到指定字符数的字符

##### (1). 语法

> `str.substr(start, length)`

**参数：**
- `start`：开始提取字符的位置。如果为负值，则被看作 strLength + start，其中 strLength 为字符串的长度（例如，如果 start 为 -3，则被看作 strLength + (-3)）。
- `length`： 可选。提取的字符数。

**返回值：**
提取出来的字符

##### (2). 使用
```
let a = 'zhaozeyu is so cool';
console.log(a.substr(2, 4)); // aoze
console.log(a.substr(2, 10)); // aozeyu is 
console.log(a.substr(-1, 6)); // 
```

#### 21. String.prototype.substring

`substring()`方法返回一个字符串在开始索引到结束索引之间的一个子集, 或从开始索引直到字符串的末尾的一个子集

##### (1). 使用
> `str.substring(indexStart, indexEnd)`

**参数：**
- `indexStart`：一个 0 到字符串长度之间的整数。
- `indexEnd`：可选。一个 0 到字符串长度之间的整数。

**返回值：**
提取出来的字符串

##### (2). 使用

```
let a = 'zhaozeyu is so cool';
console.log(a.substring(2)); // aozeyu is so cool
console.log(a.substring(1, 10)); // haozeyu i
```

#### 22. String.prototype.toLocaleLowerCase

`toLocaleLowerCase()` 方法根据任何特定于语言环境的案例映射，返回调用字符串值转换为小写的值。

##### (1). 语法

> `str.toLocaleLowerCase()`

**返回值：**

根据任何特定于语言环境的案例映射，将表示调用字符串的新字符串转换为小写。

##### (2). 使用
```
let a = 'HHHH, GGGG';
let b = 'hhhh, gggg';
console.log(a.toLocaleLowerCase()); // hhhh, gggg
console.log(b.toLocaleLowerCase()); // hhhh, gggg
```

#### 23. String.prototype.toLocaleUpperCase

toLocaleUpperCase() 使用本地化（locale-specific）的大小写映射规则将输入的字符串转化成大写形式并返回结果字符串。

##### (1). 语法

> `str.toLocaleUpperCase()`

**返回值：**

根据任何特定于语言环境的案例映射，将表示调用字符串的新字符串转换为大写。

##### (2). 使用
```
let a = 'HHHH, GGGG';
let b = 'hhhh, gggg';
console.log(a.toLocaleUpperCase()); // HHHH, GGGG
console.log(b.toLocaleUpperCase()); // HHHH, GGGG
```

#### 24. String.prototype.toLowerCase

转换成小写

#### 25. String.prototype.toUpperCase

转换成大写

#### 26. String.prototype.toSource

沙雕方法，只有火狐支持


#### 27. String.prototype.trim

删除字符串两端的**空白字符**

#### 28. String.prototype.trimLeft

删除字符串左端的空白字符

#### 29. String.prototype.trimRight

删除字符串右端的空白字符

#### 30. String.prototype.toString

`String` 对象覆盖了 `Object` 对象的 `toString` 方法；并没有继承 `Object.toString()`。对于 `String` 对象，`toString` 方法返回该对象的字符串形式，和 `String.prototype.valueOf()` 方法返回值一样。

#### 31. String.prototype.valueOf

`String` 对象的 `valueOf` 方法返回一个 `String` 对象的原始值。该值等同于`String.prototype.toString()`。