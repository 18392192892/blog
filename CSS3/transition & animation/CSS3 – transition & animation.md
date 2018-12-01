## CSS3 -- transition & animation

@(Interview)

`transition` 和 `animation` 是 CSS3 新添加的两种动画。

两种动画有相同点也有不同点，分别有自己的适用场景

### 一丶过渡与动画

我们首先要了解这两个词。

#### CSS3过度

CSS3过度是元素从一种样式逐渐改变为另一种的效果。要实现这一点，必须规定两项内容：
1. 指定要添加效果的CSS属性。
2. 指定效果的持续时间

#### CSS3动画

CSS3动画是根据 `@keyframes` 规则内指定的一个 CSS 样式和动画逐步从目前的样式更改为新的样式，指定至少这两个 CSS3 的动画属性绑定向一个选择器：
1. 规定动画的名称
2. 规定动画的时长

### 二丶transition

`transition` 就是所谓的过度。

#### 1. 基本介绍

`transition`是对于两种样式切换的时候的一种过度效果。

比如，当我们操作 DOM 的时候，要使一个 `<div>` 的宽度从 `40px` 变道 `100px`，如果不加`transition`，他会一帧跳到`100px`，中间不会有任何的效果。

而当我们使用了 `transition` 他就会根据给定的动画时间进行变化。

`transition` 有 4 个子属性分别是

- `transition-property`
- `transition-duration`
- `transition-timing-function`
- `transition-delay`

##### (1). transition-property

这个属性是指定应用**过度属性的名称**

这个属性有以下几种

- `none`：没有过度动画
- `all`：所有可被动画的属性都表现出过度动画
- `IDENT`：**属性名称**。由小写字母`a`到`z`，数字 `0` 到 `9`，下划线 `_`和破折号`-`。第一个非破折号字符不能是数字。同时，不能以两个破折号开头。

注意：不是所有属性都能设置过度效果的，只有**数字量化**的CSS属性才能过度。那么哪些属于能数字量化的CSS属性呢？如下：

颜色系列：
- `color`
- `background-color`
- `border-color`
- `outline-color`

数组系列就太多了：

- `width`
- `height`
- `top`
- `right`
- `bottom`
 
就不一一举例了

`position`，`display`这种一般就是无法进行过度的

##### (2). transition-duration

`transition-duration` 属性以秒或毫秒为单位指定**过渡动画所需的时间**。默认值为 0s ，表示不出现过渡动画。

可以指定多个时长，每个时长会被应用到由 `transition-property` 指定的对应属性上。如果指定的时长个数小于属性个数，那么时长列表会重复。如果时长列表更长，那么该列表会被裁减。两种情况下，属性列表都保持不变。

**如果设置了多个过渡属性，但每个属性的过渡时间都一样，你没必要为 `transition-duration`设多个值，只有设一个即可，该值会应用到所有过渡属性上**

##### (3). transition-timing-function

过度的速度函数，也就是速度曲线，有七个值

- `linear`：匀速过度
- `ease`：先快再慢
- `ease-in`：一直加速
- `ease-out`：一直减速
- `ease-in-out`：先加速后减速
- `cubic-bezier(n,n,n,n)`：自定义平滑曲线
- `steps`

最后一个属性有两个参数，第一个参数是分割的数量，第二个参数可选，是关键字 `start`，`end`，不设置的话默认就是 `end`，因此 `steps(4, start)` 等价于 `step-start(4)`，`steps(4, end)` 等价于 `step-end(4)`。例如 `steps(4, end)` 并非像贝塞尔曲线那样平滑过渡，相当于将过渡过程从头至尾分成4步，在每一步瞬间完成过渡。

##### (4). transition-delay

这个属性是延迟开始过度的时间，默认值为 0，表示不延迟，立即开始过度动画。

##### transition

这四个属性可以单独指定，也可以通过 `transition` 合起来直接一起指定了，我平时一般直接合起来使用。

如果合起来使用，他的顺序是这样的，

```css
.transition {
	transition: <transition-property> | < transition-duration> | <ransition-timing-function> | <transition-delay>
}
```

#### 2. 触发方式

对于`transition`，必须有一个触发方式，否则动画不会自播放。

一般用的比较多的，就是伪类触发，`:hover，:focus，:active，:checked` 等，还有例如 `@media` 媒体查询，根据设备大小横屏竖屏切换时触发。

还可以通过 `js` 直接添加 `class` 触发，比如在 `click` 后添加一个 `class` 然后触发动画。


##### transitionend 事件

`transition` 既然涉及时间，自然就有事件。参照MDN有 `transitionend` 事件，顾名思义就是过渡结束时触发该事件。但该事件比较坑。例如过渡 `padding` 时，代码如下：

```css
#tempDiv {
    padding: 1px;
    transition-property: padding;
    transition-duration: 1s;
}
#tempDiv:hover {
    padding: 5px;
}
```

```javascript
function showMessage() {
    console.log('finished');    //过渡结束时触发打印log
}
var element = document.getElementById("tempDiv");
element.addEventListener("transitionend", showMessage, false);
```

这段代码会导致出现 4 条 `log`，因为过度的属性是`padding`，所以在 `padding-top`，`padding-right`，`padding-bottom`，`padding-left`过度结束的时候均触发了 `transitionend` 事件，所以就打印了 4次

如果上述 `CSS` 中将 `transition-property: padding;` 改为 `all`， 同样会触发4次。除非你改成 `transition-property: padding-top;` 这样才能只触发一次，但现实中只过渡一边的场景非常少，通常都是4边同时过渡，因此例如 `padding`，`margin`，`border`之类的属性，用 `transitionend` 事件会有多次捕捉的情况发生。

##### 隐式过度

`transition` 过渡时有时会出现一些比较暧昧的情形，比如设成 `em` 的属性，如你所知 `em` 是根据 `font-size` 来计算的。类似还有 `rem，vh，vw` 等都是根据另一个属性的值来计算得到它的值。举个例子 `padding:2em`，如果 `font-size` 被改变了，此时 `padding` 的“书面值”不变，仍旧是 `2em` ，但“实际值”将会发生变化并触发 `transition` 过渡。这被称作“隐式过渡”。多数浏览器会实现隐式过渡.

##### 开关过度和永久过度

开关过渡，顾名思义就是触发源的事件结束后会恢复到原始状态。永久过渡就是过渡后不恢复到原始状态。上面的例子都是开关过渡，当鼠标 `hover` 事件结束后，图片恢复原始尺寸。但永久过渡的话，鼠标 `hover` 事件结束后，图片仍旧保持放大后的尺寸。

```css
//开关过渡
.transition { 
    transition: all 1s ease-in-out;
}
.transition:hover {
    transform: scale(1.5);
}
//永久过渡
.forever { 
    transition: all 1s ease-in-out 999999s;
}
.forever:hover { 
    transform: scale(1.5);
    transition: all 1s ease-in-out;
}
```

这就是利用将回来的动画时间设置的特别长，导致回来的时间非常慢，好像就永远定在那里了一样

#### 3. transition 的局限

`transition`的优点在于简单易用，但是他有几个很大的局限

1. `transition` 需要事件触发，所以没法在网页加载时自动发生。
2. `transition` 是一次性的，不能重复发生，除非一再触发。
3. `transition` 只能定义开始状态和结束状态，不能定义中间状态，也就是说只有两个状态。
4. 一条 `transition` 规则，只能定义一个属性的变化，不能涉及多个属性。

那么为了优化这些，出现了 `Animation`

---

### 三丶animation

`animation` 就是我们平时所说的动画，要使用 `animation` 必须配合 `@keyframes` 使用。

#### 1. @keyframes

`@keyframes` 用来创建一个动画的效果

创建动画的原理式，将一套 CSS 样式逐渐变化为 另一套样式。在动画过程中，您能够多次改变这套 CSS 样式。以百分比来规定改变发生的时间，或者通过关键词 `from` 和 `to`，等价于 0% 和 100%。0% 是动画的开始时间，100% 动画的结束时间。

为了获得最佳的浏览器支持，您应该始终定义 0% 和 100% 选择器。

> `@keyframes animationname {keyframes-selector {css-styles;}}`

- `animationname`：必需，定义动画的名称
- `keyframes-selector`：必需，动画时长的百分比
- `css-styles`：必需，一个或者多个合法的 CSS 样式属性

具体使用如下

```css
@keyframes mymove {
	0% {
		width: 100px;
	} 
	50% {
		width: 200px;
	}
	100% {
		width: 100px;
	}
}
```
这是一个循环改变宽度的动画

这相当于是定义了一个动画，那我们该如何去触发这个动画呢。那就需要 `animation`了

#### 2. 基本介绍

`animation` 的属性有许多，合起来可以控制一个动画的执行。

他有如下属性

- `animation-name`
- `animation-duration`
- `animation-timing-function`
- `animation-delay`
- `animation-iteration-count`
- `animation-direction`
- `animation-fill-mod`

除了后面两个其余的和 `transition` 的属性是一样的。

##### (1). animation-name

`animation-name` 属性为 `@keyframes` 动画规定名称

这个属性就是 `@keyframes` 设定的名字，用来触发动画，默认是 `none` 也就是没有动画

##### (2). animation-duration
这个属性定义动画完成一个周期所需要的时间，以秒或毫秒计

规定完成动画所花费的时间，默认是 0 。意味着没有动画效果。

##### (3). animation-timing-function

`animation-timing-function`规定动画 速度曲线

有这么几个值

- `linear`：动画从头到尾的速度是相同的
- `ease`：默认，动画以低俗开始，然后加快，在结束前变慢
- `ease-in`：动画以低速开始
- `ease-out`：动画以低速结束
- `ease-in-out`：动画以低速开始和结束
- `cubic-bezier(n,n,n,n)`：在 cubic-bezier 函数中自己的值。可能的值是从 0 到 1 的数值。

##### (4). animation-delay

`animation-delay` 属性定义动画何时开始。

`animation-delay` 值以秒或毫秒计。

就当与动画延迟执行

##### (5). animation-iteration-count

规定动画播放的次数

他就只有两个值

- `n`：定义动画播放次数的数值
- `infinite`：规定动画应该无限次播放

##### (6). animation-direction

`animation-direction` 属性定义是否应该轮流反向播放动画。

他有两个值

- `normal`：默认值，动画应该正常播放
- `alternate`：动画应该轮流反向播放

如果 `animation-direction` 值是 `alternate`，则动画会在奇数次数（1、3、5 等等）正常播放，而在偶数次数（2、4、6 等等）向后播放。

**注意：如果把动画设置为只播放一次，则该属性没有效果**

##### (7). animation-fill-mod

默认情况下，动画结束后，元素的样式将回到起始状态，animation-fill-mode属性可以控制动画结束后元素的样式。

他有四个值：
- `none`：默认值，回到动画没开始的状态
- `forwards`：动画结束后动画停留在结束状态
- `backwords`：动画回到第一帧的状态
- `both`：根据 `animation-direction` 轮流应用 `forwards` 和 `backwards` 规则

##### animation

如果组合起来使用，他的顺序是这样的

> `animation: name duration timing-function delay iteration-count direction`

`animation` 是不需要触发的，设定了就会存在，而且设置了一个动画，可以在多个元素上使用

--- 

### 四丶transition 和 animation 的区别

他俩的区别已经不言而喻了，在我看来，这俩就不是一个东西，`animation`是添加动画，`transition`是添加过度效果，虽然都是以动画的效果表示出来，但是运用的场景完全不一样

那在细节上也有一些差距，我们来对比一下

- `transition`需要触发，`animation`不需要触发
- `transition`不能循环，`animation`可以很轻松就循环
- `transition`不能控制动画中间的情况，`animation`可以控制中间的效果
- `transition`一个定义只能使用一次，`animation`定义的`@keyframes`可以定义一次多次使用



