---
title: div盒子模型及html布局
date: 2022-04-05 20:37:25.000 +0800
type: book
category:
  - 前端
keywords:
  - html
  - div
  - float
  - position
  - absolute
---

## 简介

本文主要讲述 div 布局相关知识。div 布局以盒子模型为基础，描述 div 自身所占的范围大小；以 html 流式布局为指引，描述 div 的位置信息

## 盒子模型

描述元素各个部分所占范围大小
![img.png](/images/img.png)
上图为盒子模型图，其中：

### border 边框

意为盒子模型的边界，以下 margin、padding 都是以此为参考距离

```css
border: 5px solid red;
```

> 参数依次表示：边框宽度、线条格式、颜色

### margin 外边距

意为超出边界的范围，块级元素的垂直相邻外边距会合并，而行内元素实际上不占上下外边距。行内元素的的左右外边距不会合并。同样地，浮动元素的外边距也不会合并。允许指定负的外边距值，不过使用时要小心。 示例：

```css
margin: 10px;
margin: 10px 5px;
margin: 10px 5px 15px;
margin: 10px 5px 15px 20px;
```

> - 只有一个值时表示的是 4 个外边距
> - 两个值时，分别表示上和下、右和左外边距
> - 三个值时，分别表示上、右和左、下外边距
> - 四个值时，分别表示上、右、下、左外边距

### padding 内边距

意为元素边框与元素内容之间的空间。padding 值加上元素的 width 或 height 为元素实际的 width 或 height，实际表现为 padding 范围内同样存在背景

```css
padding: 10px;
padding: 10px 5px;
padding: 10px 5px 15px;
padding: 10px 5px 15px 20px;
```

> - 只有一个值时表示的是 4 个内边距
> - 两个值时，分别表示上和下、右和左内边距
> - 三个值时，分别表示上、右和左、下内边距
> - 四个值时，分别表示上、右、下、左内边距

## HTML 流式布局

描述元素在页面中所处的位置。当前元素的位置描述信息只会影响后方的元素，而不会对前方元素产生影响

### html 普通流

如果不改变元素的默认样式前提下，元素在 HTML 的普通流中会“占用”一个位置，“占用”位置的大小、位置则是由元素的盒子模型来决定。而后续讲的 Position、Float 属性就是控制元素怎么脱离这个普通流的属性。

### position

Position 的属性值共有四个：static、relative、absolute、fixed。

#### static

默认值，对于设置的 top、bottom、left、right 属性无效

#### relative

相对定位，以上讲到元素在页面的普通流中会“占用”一个位置，这个位置可以理解为默认位置，而相对定位就是将元素偏离元素的默认位置，但普通流中依然保持着原有的默认位置，并没有脱离普通流，只是视觉上发生的偏移

#### absolute

绝对定位，元素会循着节点树中的父（祖）元素来确定“根”，然后相对这个“根”元素来偏移。如果在其节点树中所有父（祖）元素都没有设置 position 属性值为 relative 或者 absolute 则该元素最终将对 body 进行位置偏移。应用了 position: absolute 的元素会脱离页面中的普通流并改变 Display 属性

### float

float 的属性值有 none、left、right，有几个要点：

- 只有横向浮动，并没有纵向浮动。
- 当元素应用了 float 属性后，将会脱离普通流，其容器（父）元素将得不到脱离普通流的子元素高度。
- 会将元素的 display 属性变更为 block。
- 浮动元素的后一个元素会围绕着浮动元素（典型运用是文字围绕图片），与应用了 position 的元素相比浮动元素并不会遮盖后一个元素。
- 浮动元素的前一个元素不会受到任何影响（如果你想让两个块状元素并排显示，必须让两个块状元素都应用 float）。

### position 与 float 兼容性

- 元素同时应用了 position: relative、float、（top / left / bottom / right）属性后，则元素先浮动到相应的位置，然后再根据（top / left / bottom / right）所设置的距离来发生偏移。
- 元素同时应用了 position: absolute 及 float 属性，则 float 失效。
- 第一个元素应用了 position 之后会覆盖着接下来的 float 元素（如果两个元素所处的位置相同）
  > 回顾：如果你不将 float 的元素的 position 设置成 relative 的话，你想通过设置 float 元素的 z-index 来的达到覆盖 position:absolute 是无效的。同理，float 元素下面存在 position: absolute 的子元素，如果你不将 float 的元素的 position 设置成 relative 的话，absolute 元素是不会定位到 float 元素的。
- 同时应用 position: absolute 和 float: left 会导致清除浮动无效（position: relative 则可以清除浮动）。

> 常用的清除浮动的方法有两种：
>
> - 通过在容器中添加一个标签，设置该标签的样式为 clear: both
> - 容器设置 overflow: hidden

## 总结

- 使用 div 的 width、height 及 padding 调整大小
- 使用 position 或 float 调整位置信息
