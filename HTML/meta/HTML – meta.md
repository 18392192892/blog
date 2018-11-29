## HTML -- meta

@(Interview)

### 一丶meta是什么

首先要了解一些**元数据**的概念。

**元数据**用来构建HTML文档的基本结构，以及就如何处理文档向浏览器提供信息和指示，**它们本身不是文档内容，但提供了关于后面文档内容的信息**。

比如`<title>`、`<base>`、`<meta>`都是元数据元素

`<meta>` 元素可提供有关页面的元信息（meta-information），比如针对搜索引擎和更新频度的描述和关键词

`<meta>`元素可以定义文档的各种元数据，提供各种文档信息，通俗点说就是可以理解为提供了关于网站的各种信息。html文档中可以包含多个 `<meta>` 元素，每个 `<meta>` 元素只能用于一种用途，如果想定义多个文档信息，则需要在head标签中添加多个meta元素。


### 二丶meta做什么用的

meta标签共有两个属性，分别是 `http-equiv`属性和 `name`属性

#### 1. name 属性

`name` 属性主要用于描述网页，比如网页的关键词，叙述等。与之对应的属性值为 `content`，`content` 中的内容是对 `name` 填入类型的具体描述，便于搜索引擎抓取。
`meta` 标签中 `name` 属性语法格式是：

```
<meta name="参数" content="具体的描述">
```

其中`name`属性共有以下几种参数

##### (1). keywords

说明：用于告诉搜索引擎，你网页的关键字。

```
<meta name="keywords" content="Lxxyx,博客，文科生，前端">
```

##### (2). description(网站内容的描述)

说明：用于告诉搜索引擎，你网站的主要内容。

```
<meta name="description" content="文科生，热爱前端与编程。目前大二，这是我的前端博客">
```

##### (3). viewport(移动端的窗口)

这个属性常用于设计移动端网页。在用bootstrap,AmazeUI等框架时候都有用过viewport

```
<meta name="viewport" content="width=device-width, initial-scale=1">
```

##### (4). robots(定义搜索引擎爬虫的索引方式)

说明：robots用来告诉爬虫哪些页面需要索引，哪些页面不需要索引。

```
<meta name="robots" content="none">
```

##### (5). author(作者)

说明：用于标注网页作者

```
<meta name="author" content="Lxxyx,841380530@qq.com">
```

##### (6). generator(网页制作软件)

说明：用于标明网页是什么软件做的

```
<meta name="generator" content="Sublime Text3">
```

##### (7). copyright(版权)

说明：用于标注版权信息

```
<meta name="copyright" content="Lxxyx"> //代表该网站为Lxxyx个人版权所有。
```

##### (8). revisit-after(搜索引擎爬虫重访时间)

说明：如果页面不是经常更新，为了减轻搜索引擎爬虫对服务器带来的压力，可以设置一个爬虫的重访时间。如果重访时间过短，爬虫将按它们定义的默认时间来访问。

```
<meta name="revisit-after" content="7 days" >
```

##### (9). renderer(双核浏览器渲染方式)

说明：renderer是为双核浏览器准备的，用于指定双核浏览器默认以何种方式渲染页面。比如说360浏览器。

```
<meta name="renderer" content="webkit"> //默认webkit内核
<meta name="renderer" content="ie-comp"> //默认IE兼容模式
<meta name="renderer" content="ie-stand"> //默认IE标准模式
```

#### 2. http-equiv属性

`http-equiv` 属性与 `content` 属性结合使用, `http-equiv` 属性为指定所要模拟的标头字段的名称，`content` 属性用来提供值。

格式如下：
```
<meta http-equiv="参数" content="具体的描述">
```

其中 `http-equiv` 属性主要有以下几种参数：

##### (1). content-Type(设定网页字符集)

这个值有两种设置方式，最新的 HTML5 设置了新的方式，推荐使用 H5的方法

说明：用于设定网页字符集，便于浏览器解析与渲染页面

```
<meta http-equiv="content-Type" content="text/html;charset=utf-8">  //旧的HTML，不推荐

<meta charset="utf-8"> //HTML5设定网页字符集的方式，推荐使用UTF-8
```


##### (2). X-UA-Compatible(浏览器采取何种版本渲染当前页面)

说明：用于告知浏览器以何种版本来渲染页面。（一般都设置为最新模式，在各大框架中这个设置也很常见。）

```
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1"/> //指定IE和Chrome使用最新版本渲染当前页面
```


##### (3). cache-control(指定请求和响应遵循的缓存机制)

说明：指导浏览器如何缓存某个响应以及缓存多长时间

关于缓存的知识可以去看我另一篇博客

```
<meta http-equiv="cache-control" content="no-cache">
```

##### (4). expires(网页到期时间)

说明:用于设定网页的到期时间，过期后网页必须到服务器上重新传输。

```
<meta http-equiv="expires" content="Sunday 26 October 2016 01:00 GMT" />
```


##### (5). refresh(自动刷新并指向某页面)

说明：网页将在设定的时间内，自动刷新并调向设定的网址。

```
<meta http-equiv="refresh" content="2；URL=http://www.lxxyx.win/"> //意思是2秒后跳转向我的博客
```

##### (6). Set-Cookie(cookie设定)

说明：如果网页过期。那么这个网页存在本地的cookies也会被自动删除。

```
<meta http-equiv="Set-Cookie" content="name, date"> //格式

<meta http-equiv="Set-Cookie" content="User=Lxxyx; path=/; expires=Sunday, 10-Jan-16 10:00:00 GMT"> //具体范例

```