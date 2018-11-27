## CSS样式优先级

@(Interview)

CSS的样式优先级是一个很重要的问题，如果了解清楚，平时的一些莫名其妙的CSS样式设置问题就会迎刃而解

### 一丶样式优先级规则
1. 优先级就是分配给指定的CSS声明的一个权重，它由 匹配的选择器中的 每一种选择器类型的 数值 决定。
2. 而当优先级与多个CSS声明中任意一个声明的优先级相等的时候，CSS中最后的那个声明将会被应用到元素上。
3. 当同一个元素有多个声明的时候，优先级才会有意义。因为每一个直接作用于元素的CSS规则总是会接管/覆盖（take over）该元素从祖先元素继承而来的规则。

### 二丶权重规则

#### 1.规则介绍
权重分为三个等级(`!important`不在此列)
第一等：**ID选择器**，权重100，例如`#id{...}`
第二等：**类选择器**，**伪类选择器**，**属性选择器**，权重10，例如`.class{...} :hover{...} [arrtibute=value]`
第三等：**标签选择器**，**伪元素选择器**，权重1，例如`div{...} ::after{...}`

**通配选择符**，**关系选择符**，**否定伪类**权重为0，对优先级没有影响，不予计算(但是，在`:not`内部声明的类选择器是会影响优先级的)

给元素添加的**内联样式** 总会覆盖外部样式表的任何样式 ，因此可看作是具有最高的优先级。

有一个例外：`!important`，只要`!important`出现，其他的样式全部忽略(包括**内联样式**)，此声明将覆盖任何其他声明。

#### 2.举个栗子

现在有下面这样一段代码
`HTML`
```
<body>
	<div class="my-class">
	    <div id="my-id">
	        <div>
	            <p>猜猜我是什么颜色？</p>
	        </div>
        </div>
    </div>
</body>
```
`CSS`
```
.my-class div div p {    
	color:green;
} 
.my-class #my-id div p {    
	color:red;
} 
div #my-id div p {    
	color:gary;
} 
div p {    
	color:blue;
} 
p {    
	color:yellow;
}
```
最后`p`标签的颜色是<span style="color: red">红色</span>，按照权重规则计算
第一组：10 + 1 + 1 + 1 = 13
第二组：10 + 100 + 1 + 1 = 112
第三组：1 + 100 + 1 + 1 = 103
第四组：1 + 1 = 2
第五组：1
第二组的权重最高，所以按照第二组渲染为红色

#### 3.深入分析
##### (1).优先级计算无视DOM树中的距离
只要权重值计算下来，渲染权重值高的样式，但是如果权重一样高，则渲染后定义的样式，不会因为DOM树的距离而改变
```
<html>
	<head>
		<style type="text/css">
			body h1 {
			  color: green;
			}
			html h1 {
			  color: red;
			}
		</style>
	</head>
	<body>
		<h1>Here is a title!</h1>
	</body>
</html>
```
上面代码的`H1`会被渲染成为红色，按照我之前的理解，`body`在DOM中要比`html`离`h1`更近，所以应该会按 `body h1` 中定义的绿色来显示文字，但是结果却是刚好相反的。
这段代码表示影响优先级的只是权重值而已，因为权重值相同，所以选择后定义的样式渲染，也就是选择了红色
##### (2). `:not`伪类不参与优先级计算
`:not`否定伪类在优先级计算中不会被看作是伪类，但是，会把`:not`里面的选择器当普通选择器计数。
```
<html>
	<head>
		<style type="text/css">
			div.outer p {
			  color:red;
			}
			div:not(.outer) p {
			  color: blue;
			}
		</style>
	</head>
	<body>
		<div class="outer">
			<p>This is in the outer div.</p>
			<div class="inner">
				<p>This text is in the inner div.</p>
		    </div>
		</div>
	</body>
</html>
```
结果为：
<span style="color: red">This is in the outer div.</span>
<span style="color: blue">This text is in the inner div.</span>

在这段代码中，选择器`div.outer p`和选择器`div:not(.outer) p`的优先级相同，`:not`被忽略掉了，`:not(.outer)`中的`.outer`正常计数，所以如果调换位置，`inner`元素会变为红色
```
div:not(.outer) p {
	color: blue;
}
div.outer p {
	color:red;
}
```
<span style="color: red">This is in the outer div.</span>
<span style="color: red">This text is in the inner div.</span>

##### (3).基于形式的优先级
优先级是基于选择器的形式进行计算的。在下面的例子中，尽管选择器`*[id="foo"]`选择了一个`ID`，但是它还是作为了一个**属性选择器**来计算自身的优先级

有如下样式声明：
```
* #foo {
  color: green;
}
*[id="foo"] {
  color: red;
}
```
应用在下面的HTML中：
```
<p id="foo">I am a sample text.</p>
```
最终的结果是：
<span style="color: green">I am a sample text.</span>

虽然匹配了相同的元素，但是**ID选择器**拥有更高的权重值，所以第一条样式声明生效

##### (4).直接给目标元素添加样式和目标元素继承样式对比
为目标元素直接添加样式，永远比继承样式的优先级高
```
#parent {
  color: green;
}
h1 {
  color: red;
}
```
当它应用在下面的HTML时：
```
<html>
	<body id="parent">
	  <h1>Here is a title!</h1>
	</body>
</html>
```
浏览器会将它渲染成：
<span style="color: red">Here is a title!</span>

因为`h1`选择器明确的定位到了元素，但是绿色仅仅是继承自其父级，所以会被渲染成红色

### 三丶例外的!important
#### 1. !important简介
`!important`是一个很特殊的规则，”跳出三界外，不在五行中“说的就是它，它出现就会覆盖一切其他声明，一切以它为准
> **注意：**当两条相互冲突的带有`!important`规则的声明被应用到相同的元素上时，拥有更大优先级的声明将会被采用

但是使用`!important`是一个坏习惯，应该尽量避免，因为这样破坏了样式表中的固有的级联规则，使得调试和找bug变得更加困难了。

**一些经验法则：**
- **一定**要优先考虑使用样式规则的优先级来解决问题而不是`!important`
- **只有**在需要覆盖全局或外部CSS的特定页面中使用`!important`
- **永远不要**在全局范围的CSS上使用`!important`
- **永远不要**在你的插件中使用`!important`

**取而代之，可以**
1. 更好的利用CSS级联属性
2. 使用更具体的规则。在选择的元素之前，增加一个或者多个其他选择，使选择器变得更加具体，并获得更高的优先级
```
<div id="test">
  <span>Text</span>
</div>
```
```
div#test span { color: green }
span { color: red }
div span { color: blue }
```
上面这段代码，不论css语句是什么顺序，文本都是绿色的，因为这一条规则是最有针对性，权重值最高的，所以优先级最高

#### 2.什么情况下可以使用!important
既然`!important`会对代码造成维护困难，那我们要在什么时候使用它呢
1. 你的网站上有一个设定了全局样式的CSS文件
2. 或者有一些很恶劣的内联样式
3. 可以确定设置的该样式对其他样式不会有影响

#### 3.怎样覆盖!important
非常简单，只需再添加一条带`!important`的CSS规则，要么给这个选择器**器更高的优先级**，要么添加相同的选择器但是把它放在**更加靠后**的位置

一些拥有更高优先级的例子：
```
table td    { height: 50px !important; }
.myTable td { height: 50px !important; }
#myTable td { height: 50px !important; }
```
或者使用相同的选择器，但是置于已有的样式之后： 
```
td { height: 50px !important; }
```


---

##### 参考：
- <a href="https://developer.mozilla.org/zh-CN/docs/Web/CSS/Specificity">https://developer.mozilla.org/zh-CN/docs/Web/CSS/Specificity</a>
- <a href="https://www.cnblogs.com/starof/p/4387525.html">https://www.cnblogs.com/starof/p/4387525.html</a>
- <a href="https://blog.csdn.net/sinat_36521655/article/details/80140221">https://blog.csdn.net/sinat_36521655/article/details/80140221</a>