## JavaScript -- 数组遍历方法

@(Interview)

数组遍历的方法有很多种，今天全部介绍一下

#### 1. `for`循环

`for`是最基本的循环方式，可以进行一系列的优化操作，可以另改变为三种

##### 方法一
最普通的`for`循环
```
for(i = 0; i < arr.length; i++) {
   
} 
```

##### 方法二
```
for(i = 0,len=arr.length; i < len; i++) {
   
}
```
将数组长度缓存，这样每次的循环就不会再读取到数组的长度

##### 方法三
```
for (let i = 0, item; item = arr[i]; i++) {
  console.log(item)
}
```
这个方法将取值和判断合并，通过不停的枚举每一项来循环，直到枚举到空值则循环结束，如果`arr[i]`的值为`undefined`，循环就会自动结束

**注意：这种循环方式，数组元素不能为`Boolean()`转化后为`false`的值，比如`undefined`，`null`**


#### 2. `for-in`循环

就是在`for`种加入`in`，这种方法一般用在遍历对象时，需要属性是**可枚举的**

```
for (let i in arr) {
    console.log(arr[key])
}
```

#### 3. `for-of`循环

```
for (let item of arr) {
    console.log(item)
}
```
---

剩下还有大约 12 种数组的遍历方法，在数组方法种寻找，但是大部分都不是只是为了循环，而是有一些特殊的操作，比如过滤器`filter`，`find`等。

---

经过一系列比较以后，效率的排序是这样的

> `for > for-of > forEach > filter > map > for-in`

#### 结论

虽说性能有高低，但是在用的时候还是需要看场景的，总有适合不适合。

比如有些地方虽然用`for`的效率要比`for-in`的效率高，但是用`for-in`要比`for`方便很多，那么**开发效率**所带来的好处，就会远高于这点的性能提升所带来的好处，