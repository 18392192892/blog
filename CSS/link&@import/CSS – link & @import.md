## CSS -- link & @import

@(Interview)

在平时设计制作中，有四种引入方式分别是**内联样式**，**嵌入样式**，**链接样式**，**导入样式**

这四种方式大家应该都很熟悉了，今天的重点是**链接样式**和**导入样式**的区别，也就是 `link`和`@import`

### 一丶link

链接方式指的是使用 HTML 头部的 标签引入外部的 CSS 文件。

```
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
```

这是最常见的也是最推荐的引入 CSS 的方式。使用这种方式，所有的 CSS 代码只存在于单独的 CSS 文件中，所以具有良好的可维护性。并且所有的 CSS 代码只存在于 CSS 文件中，CSS 文件会在第一次加载时引入，以后切换页面时只需加载 HTML 文件即可。

### 二丶@import

导入方式指的是使用 CSS 规则引入外部 CSS 文件。

```
<style>
    @import url(style.css);
</style>
```

或者写在css样式中

```
@charset "utf-8";
@import url(style.css);
*{ margin:0; padding:0;}
.notice-link a{ color:#999;}
```

### 三丶link与@import的区别

#### 1. 从属关系区别

- `@import` 是 `CSS` 提供的语法规则，**只有导入样式表的作用**。
- `link`是 `HTML` 提供的标签，不仅可以加载 `CSS` 文件，还可以定义 `RSS`、`rel` 连接属性等。

#### 2. 加载顺序区别

- 加载页面时，`link` 标签引入的 `CSS` 被同时加载；
- `@import` 引入的 `CSS` 将在**页面加载完毕后被加载**。

#### 3. 兼容性区别

- `@import` 是 CSS2.1 才有的语法，故只可以在 `IE5+` 才能识别。
- `link`标签作为 HTML元素不存在兼容性的问题

#### 4. DOM可控性区别

- `link`可以通过 JS 操作 DOM ，插入link标签来改变样式。
- 由于DOM方法是基于文档的，所以无法使用`@import`的方式插入样式。

