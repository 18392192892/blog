## CSS -- 媒介查询 & 响应式

@(Interview)


### 一丶媒介查询的作用

```css
@media (media-feature-name: value) {
    /* 符合条件时应用的样式 */
}
```

上面是媒体查询的基本结构，根据查看网页的设备的某些重要信息（比如屏幕大小、分辨率、颜色位深等），**页面可以分别应用不同的样式甚至替换整个样式表**。 如果浏览器当前的条件与圆括号中的条件匹配，它就会采用花括号中的那些样式。如果不匹配，浏览器会忽略这些样式。

### 二丶媒介查询常用属性

- `width`，`min-width`，`max-width`：显示区域的宽度
- `height`，`min-height`，`max-height`：	显示区域的高度
- `device-width`，`min-device-width`，`max-device-width`：当前计算机或设备屏幕的宽度 （或打印输出时纸面的宽度）	
- `device-height`，`min-device-height`，`max-device-height`：屏幕或纸面的高度
- `orientation`：landscape（横向）或portrait（纵向）
- `device-aspect-ratio`，`min-device-aspect-ratio`，`max-device-aspect-ratio`：显示区域的宽高比（1/1是正方形）
- `color`，`min-color`，`max-color`：屏幕颜色的位深 （1位表示黑白，目前主流显示器都是24位）

