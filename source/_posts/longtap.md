---
title: 禁止长按选中文本
date: 2017-01-06 17:21:02
tags:
    - 代码片段
---

最近在项目中遇到了需要长按选中礼物码的东西，需要把礼物码旁边的文本禁止掉，查了资料，发现了两种方法可以禁止。
<!-- more -->

#### css方法：

```css
.no-touch {
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    -khtml-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
}
```
添加一个类，对于需要禁止的dom添加这个类

#### JS方法：

```javascript
ontouchstart="return false;"
```
在需要禁止的dom上添加这段代码

两种方法对于安卓和IOS的支持我没有单独测试。两个同时使用，对于安卓和IOS可以起到禁止作用。

#### 备注：

1、在手机UC浏览器中，如果添加了这段meta标签，则全文无法长按调起菜单：

```html
<meta name="browsermode" content="application"/><!-- uc不能复制网页内容 需要复制去掉即可-->
```

2、在手机QQ浏览器（IOS版本）中，长按会弹出保存图片选项（还没有找到原因）：

![qq浏览器保存选项](/assets/blogImg/longtap01.jpg)

#### 其他未验证方法：

新增事件 contextmenu 可以实现：

```javascript
$('button').bind('contextmenu', function(e) {
  e.preventDefault();
})
```
