## HTML5 -- Web Storage & indexedDB

@(Interview)

这篇文章会把前端存储的一些方法讲清除

今天我们的主角有 4 个分别是`loccalStorage`，`sessionStorage`，`indexedDB`，`Web SQL`。

这都是现在前端存储的一些方法，我们将详细介绍他们的用法，并且比较他们之间的优缺点，当然少不了和`cookie`作比较

### 一丶localStorage

`Web Storage`是 `HTML5` 里面引入的一个类似于 `cookie` 的本地存储功能
`Web Storage`分为两种，`localStorage`和`sessionStorage`

先介绍`localStorage` 

#### 1. 基本使用

`localStorage` 是一种持久化的存储方式，也就是说如果不手动清除，数据就永远不会过期。它也是采用 `Key - Value` 的方式存储数据，底层数据接口是 `SQLite`，按域名将数据分别保存到对应数据库文件里。它能保存更大的数据（IE8上是10MB，Chrome是5MB），同时保存的数据不会再发送给服务器，避免带宽浪费。

> **注意：SQLite是一款轻型的数据库**

它的一些`api`如下

- `localStorage.length`：获得`storage`中的个数
- `localStorage.key(n)`：获得`storage`中第`n`个元素对的键值（第一个元素是0）
- `localStorage.getItem(key)`：获取键值`key`对应的值
- `localStorage.key`：获取键值`key`对应的值
- `localStorage.setItem(key, value)`：添加数据，键值为`key`，值为`value`
- `localStorage.removeItem(key)`：移除键值为`key`的数据
- `localStorage.clear()`：清除所有数据

> **注意：`localStorage`的所有 API 操作的都是字符串，所以如果想操作对象，需要将对象序列化**

因为`localStorage`是`H5`新提出来的，所以兼容性是硬伤，在使用之前，一定要判断是否有该方法

```
if (window.localStorage) {
	// 一些操作
} else {
	// 一些操作
}
```

#### 2. 特点

`Web Storage` 这两种都很有个性，首先`localStorage`有如下特点

- `localStorage`的生命周期可以一直存在，除非手动清除，否则将会永久保存
- `localStorage`不可跨域，但是可以跨页面
- `localStorage`的存储量可以达到`5MB`
- 因为是明文存储，所以毫无隐私性可言，绝对不能用于存储重要信息
- `localStorage`本质是在读写文件，所以如果数据过多会使浏览器加载速度在一定程度上变慢
- `localStorage`无法被爬虫程序爬取

这些特点里，有些相对较好，有些就不是很好

#### 3. 适用场景

因为`localStorage`可以永久存储，如果有一些数据，服务器难以承载其压力，但又要与用户信息绑定，就可以放在`localStorage`里

`localStorage`可以用于存储该浏览器对该页面的访问次数，当然，如果换个浏览器，这个次数就重新开始计数了。还可以用来存储一些固定不变的页面信息，这样就不需要每次都重新加载了，这个值也可以进行覆盖。

`localStorage`一般可以用来存储身份信息的认证`token`

`localStorage`还可以将不变的数据缓存，方便下次时候，提高网站的访问速度

---

### 二丶sessionStorage

这是另一种`Web Storage` -- `sessionStorage`

#### 1. 基本使用

`sessionStorage`的`API`和`localStorage`的一模一样！不是类似，而是一模一样！所以不做总结，往上翻

#### 2. 特点

`sessionStorage`和`localStorage`在特点上是有区别的，要不然API一样，特点一样，设置两个还有什么意义

- `sessionStorage`的生命周期是一个会话级别的，存在这里的数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁，也就是说用户关闭浏览器窗口后，数据立马会被删除
- `sessionStorage`不可以跨页面
- `sessionStorage`的存储量可以达到`5MB`
- 因为是明文存储，所以毫无隐私性可言，绝对不能用于存储重要信息
- `sessionStorage`本质是在读写文件，所以如果数据过多会使浏览器加载速度在一定程度上变慢
- `sessionStorage`无法被爬虫程序爬取

#### 3. 使用场景

因为`sessionStorage`是会话级别的，也就是关闭`Tab`就会删除数据，所以**建议存储一些当前页面刷新需要存储，且不需要在`tab`关闭时候留下的信息**，所以可以用`sessionStorage`来检测用户是否是刷新进入的页面，对于**音乐播放器恢复播放进度条**等功能还是挺使用的

---

### 三丶localStorage 与 sessionStorage区别

对于`Web Storage`这两种的区别，就只有他们的生命周期了

`localStorage`的存储时间如果不手动设置是永久，`sessionStorage`的存储时间是仅在当前会话下有效，关闭页面或浏览器后被清除


### 四丶IndexedDB

`indexedDB`相当于是一种存在于浏览器的数据库，还有一种是`Web SQL`，不过`Web SQL`的标准官方已经不打算维护了，所以不提它了就

#### 1 什么是 IndexedDB

`indexedDB` 是一种轻量级 `NOSQL` 数据库，是由浏览器自带。相比 `Web Sql` 更加高效，包括索引、事务处理和查询功能。在 `HTML5` 本地存储中，`IndexedDB` 存储的数据是最多的，不像`webStorage` 的 `4M`，**`IndexedDB` 存储空间是无上限且永久的**。

#### 2. 为什么要使用 IndexedDB

对于存储的问题，`IndexedDB`能够处理**更复杂和更结构化**的数据，有更大的储存空间，对于存储的数据有更多的交互控制

#### 3. IndexedDB的特点

1. 支持数据库操作
2. 储存空间大
3. 永久存储，删除缓存不干扰`IndexedDB`
4. 对于`IndexedDB`的操作是异步的

#### 4. 应用

如果有大量的数据需要保存就可以用到`IndexedDB`，比如一些博客的保存，文章新闻的保存，草稿箱，就可以通过`IndexedDB`来实现

--- 

### 五丶四种存储的比较

这里说的四种，当然还有`Cookie`

通过一张表格来表示他们之间的区别

| 名称       |  Cookie    |   localStorage   |  sessionStorage | IndexedDB |
| :--------: | :--------: | :------: | :------: | :------: | :------: |
| 大小限制    | 小于4kb |  5MB  |  5MB |  无限 |
| 生命周期    | 可以设置时间 |  永久  | 会话级别 |  永久 |
| 存储数据类型 |   字符串 |  字符 | 字符 | 任何结构化类型 |
| 能否跨域    |   NO |  NO | NO | NO |


---
##### 参考：
- <a href="https://juejin.im/post/582c7d330ce463006ce33838">https://juejin.im/post/582c7d330ce463006ce33838</a>
- <a href="https://juejin.im/entry/58636f96570c35006955615d">https://juejin.im/entry/58636f96570c35006955615d</a>
- <a href="https://juejin.im/entry/58561b1e128fe1006daee03a">https://juejin.im/entry/58561b1e128fe1006daee03a</a>
- <a href="https://juejin.im/entry/586b06a1570c350068ab9b52">https://juejin.im/entry/586b06a1570c350068ab9b52</a>
- <a href="https://juejin.im/entry/578444c979bc440050a89fab">https://juejin.im/entry/578444c979bc440050a89fab</a>

---
