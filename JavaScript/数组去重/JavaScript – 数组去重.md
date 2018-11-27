## JavaScript -- 数组去重

@(Interview)

数组去重有多种方法，我们大概介绍一下

#### 1. 双重循环

##### (1). 第一版
```
Array.prototype.unique = function () {
  const newArray = [];
  let isRepeat;
  for (let i = 0; i < this.length; i++) {
    isRepeat = false;
    for (let j = 0; j < newArray.length; j++) {
      if (this[i] === newArray[j]) {
        isRepeat = true;
        break;
      }
    }
    if (!isRepeat) {
      newArray.push(this[i]);
    }
  }
  return newArray;
}
```
##### (2). 第二版
```
Array.prototype.unique = function () {
  const newArray = [];
  let isRepeat;
  for (let i = 0; i < this.length; i++) {
    isRepeat = false;
    for (let j = i + 1; j < this.length; j++) {
      if (this[i] === this[j]) {
        isRepeat = true;
        break;
      }
    }
    if (!isRepeat) {
      newArray.push(this[i]);
    }
  }
  return newArray;
}
```

#### 2. indexOf

##### (1). 第一版
这一版通过`filter`和`indexOf`
```
Array.prototype.unique = function () {
  return this.filter((item, index) => {
    return this.indexOf(item) === index;
  })
}
```
##### (2). 第二版
这一版通过`forEach`和`indexOf`
```
Array.prototype.unique = function () {
  const newArray = [];
  this.forEach(item => {
    if (newArray.indexOf(item) === -1) {
      newArray.push(item);
    }
  });
  return newArray;
}
```
**相同方法也可以用`includes`替代`indexOf`**，就不重复写了


#### 3. sort
##### (1). 第一版
```
Array.prototype.unique = function () {
  const newArray = [];
  this.sort();
  for (let i = 0; i < this.length; i++) {
    if (this[i] !== this[i + 1]) {
      newArray.push(this[i]);
    }
  }
  return newArray;
}
```
##### (2). 第二版
```
Array.prototype.unique = function () {
  const newArray = [];
  this.sort();
  for (let i = 0; i < this.length; i++) {
    if (this[i] !== newArray[newArray.length - 1]) {
      newArray.push(this[i]);
    }
  }
  return newArray;
}

```
#### 4. reduce

```
Array.prototype.unique = function () {
  const newArray = [];
  this.forEach(item => {
    if (!newArray.includes(item)) {
      newArray.push(item);
    }
  });
  return newArray;
}
```

#### 5. Map

##### (1). 第一版
```
Array.prototype.unique = function () {
  const newArray = [];
  const tmp = new Map();
  for(let i = 0; i < this.length; i++){
    if(!tmp.get(this[i])) {
      tmp.set(this[i], 1);
      newArray.push(this[i]);
    }
  }
  return newArray;
}
```

##### (2). 第二版
```
Array.prototype.unique = function () {
  const tmp = new Map();
  return this.filter(item => {
    return !tmp.has(item) && tmp.set(item, 1);
  })
}
```

#### 6. Set

##### (1). 第一版
```
Array.prototype.unique = function () {
  const set = new Set(this);
  return Array.from(set);
}
```

##### (2). 第二版
```
Array.prototype.unique = function () {
  return [...new Set(this)];
}
```

#### 7. 对象键值对

```
Array.prototype.unique = function () {
  let obj = {};
  return this.filter(function(item, index, array){
      return obj.hasOwnProperty(typeof item + JSON.stringify(item)) ? false : (obj[typeof item + JSON.stringify(item)] = true)
  })
}
```

---

#### 各种方法的比较

我们用各种方法测试同一个数组：

```
let array = [1, 1, '1', '1', null, null, undefined, undefined, new String('1'), new String('1'), /a/, /a/, NaN, NaN];
```

| 方法	     |   结果	 |   说明   |
| :--------: | :--------:| :------ |
| for循环    |   [1, "1", null, undefined, String, String, /a/, /a/, NaN, NaN] |  对象和 NaN 不去重  |
| indexOf    |  [1, "1", null, undefined, String, String, /a/, /a/, NaN, NaN] |  对象和 NaN 不去重  |
| sort    |   [/a/, /a/, "1", 1, String, 1, String, NaN, NaN, null, undefined] |  对象和 NaN 不去重 数字 1 也不去重  |
| includes    |   	[1, "1", null, undefined, String, String, /a/, /a/, NaN] |  对象不去重  |
| 优化后的键值对方法    |   [1, "1", null, undefined, String, /a/, NaN] |  全部去重  |
| Set    |   [1, "1", null, undefined, String, String, /a/, /a/, NaN] |  对象不去重 NaN 去重  |

这几种方法可以根据你的需求去选择，没有必要纠结带来的性能问题，因为一些快速好的方法，带来的开发效率远比那点性能提升要有用的多