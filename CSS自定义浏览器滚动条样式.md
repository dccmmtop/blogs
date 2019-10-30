---
tags: [css]
date: 2018-10-18 09:12:33
---

### WebKit 浏览器 CSS 设置滚动条

主要有下面 7 个属性:

1.  ::-webkit-scrollbar 滚动条整体部分，可以设置宽度啥的
2.  ::-webkit-scrollbar-button 滚动条两端的按钮
3.  ::-webkit-scrollbar-track 外层轨道
4.  ::-webkit-scrollbar-track-piece 内层轨道，滚动条中间部分（除去）
5.  ::-webkit-scrollbar-thumb 拖动条，滑块
6.  ::-webkit-scrollbar-corner 边角
7.  ::-webkit-resizer 定义右下角拖动块的样式

具体所指如下图:

![](http://ogbkru1bq.bkt.clouddn.com/1539825277.png)

上面是滚动条的主要设置属性，还有更详尽的 CSS 属性伪类，可以更丰富滚动条样式。

```
:horizontal 水平方向的滚动条
:vertical 垂直方向的滚动条
:decrement 应用于按钮和内层轨道(track piece)。它用来指示按钮或者内层轨道是否会减小视窗的位置(比如，垂直滚动条的上面，水平滚动条的左边)
:increment 和 decrement 类似，用来指示按钮或内层轨道是否会增大视窗的位置(比如，垂直滚动条的下面和水平滚动条的右边)
:start 也应用于按钮和滑块。它用来定义对象是否放到滑块的前面。
:end 类似于 start 伪类，标识对象是否放到滑块的后面。
:double-button 该伪类以用于按钮和内层轨道。用于判断一个按钮是不是放在滚动条同一端的一对按钮中的一个。对于内层轨道来说，它表示内层轨道是否紧靠一对按钮。
:single-button 类似于 double-button 伪类。对按钮来说，它用于判断一个按钮是否自己独立的在滚动条的一段。对内层轨道来说，它表示内层轨道是否紧靠一个 single-button。
:no-button 用于内层轨道，表示内层轨道是否要滚动到滚动条的终端，比如，滚动条两端没有按钮的时候。
:corner-present 用于所有滚动条轨道，指示滚动条圆角是否显示。
:window-inactive 用于所有的滚动条轨道，指示应用滚动条的某个页面容器(元素)是否当前被激活。(在 webkit 最近的版本中，该伪类也可以用于::selection 伪元素。webkit 团队有计划扩展它并推动成为一个标准的伪类)
```

另外，:enabled、:disabled、:hover 和 :active 等伪类同样可以用于滚动条中。

值得一提的是，WebKit 伪类和伪元素的实现很强大，虽然类目有些多，但是我们可以把滚动条当成一个页面元素来定义，也差不多可以用上一些高级的 CSS3 属性，比如渐变、圆角、RGBA 等等，当然有些地方也可以用图片，然后图片也可以转换成 Base64，总之，可以尽情发挥了。

写个实例 Demo（请在 webkit 浏览器下观看）。

```css
/_ 设置滚动条的样式 _/ ::-webkit-scrollbar {
  width: 12px;
}

/_ 滚动槽 _/ ::-webkit-scrollbar-track {
  -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
  border-radius: 10px;
}

/_ 滚动条滑块 _/ ::-webkit-scrollbar-thumb {
  border-radius: 10px;
  background: rgba(0, 0, 0, 0.1);
  -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.5);
}
::-webkit-scrollbar-thumb:window-inactive {
  background: rgba(255, 0, 0, 0.4);
}
```

Firefox 浏览器滚动条样式插件
吐槽下，作为三大浏览器的火狐居然没有相关 CSS。

火狐不支持滚动条样式调整，火狐浏览器未开放针对滚动条样式的设定。且也不支持 css 代码关于这些浏览器属性的控制。如果非要样式效果，只能滚动效果用 JS 来做，使用图片代替按钮。

不过博主发现，本地的 Firefox 可以通过安装 Stylish 扩展定制滚动条样式
