---
title: '利用postMessage解决webview中iframe数据交互问题'
date: 2016-12-21 15:49:41
tags:
    - js
---
最近在项目中遇到了一个在客户端中的webview中嵌入iframe页面的问题，但是嵌入的iframe无法自己撑开，所以需要webview中给iframe一个高度。

![iframe高度bug](/assets/blogImg/postMessage01.png)

这就比较尴尬了，因为webview肯定不知道iframe的高度，所以需要iframe给传一个高度出来。

经过一番查找之后，知道了一种很方便的方法-postMessage方法。

#### 先来看看他的语法：

> otherWindow.postMessage(data, origin, [transfer]);

otherWindow
- 其他窗口的一个引用，比如iframe的contentWindow属性、执行window.open返回的窗口对象、或者是命名过或数值索引的window.frames。这里即可以是父窗口，也可以子窗口，两者是可以相互传递数据的。

data
- 将要发送到其他window的数据。它将会被结构化克隆算法序列化(这个不懂是什么意思)。这意味着你可以不受什么限制的将数据对象安全的传送给目标窗口而无需自己序列化。基本是什么参数都可以，字符串、数字、对象、数组，都OK。

origin
- 字符串参数，指明目标窗口的源，协议+主机+端口号[+URL]，URL会被忽略，所以可以不写，这个参数是为了安全考虑，postMessage()方法只会将message传递给指定窗口，当然如果愿意也可以建参数设置为"*"，这样可以传递给任意窗口，如果要指定和当前窗口同源的话设置为"/"。

transfer(可选)
- 是一串和message 同时传递的 Transferable 对象. 这些对象的所有权将被转移给消息的接收方，而发送一方将不再保有所有权。（不懂是什么意思，一般没有什么用吧，望大神指点）

<br/>
#### 父页面获取iframe页面信息：
demo我放在github上的：
demo地址：[父页面获取iframe页面信息](https://htmlpreview.github.io/?https://github.com/yellowlemon/Demo/blob/master/postMessage/btoa/a.html)

**父页面：**
```html
<body>
    <p>我是父页面</p>
    <p>接收到的iframe信息是：<span id="content"></span></p>
    <iframe src="/btoa/b.html" frameborder="0" marginheight="0" marginwidth="0"></iframe>
    <script>
        // event 参数中有 data 属性，就是iframe页面发送过来的数据
         window.addEventListener("message", function(event) {
             // 把iframe页面发送过来的数据显示在父页面中
           document.getElementById("content").innerHTML= event.data;
         }, false);
    </script>
</body>
```

**iframe页面**
```html
<body>
    <p>我是iframe页面</p>
    <div>发送的信息是：<input id="iframe" type="text"><input id="sendBtn" type="button" value="发送"></div>
    <script>
        // 点击按钮后向父页面发送数据
        document.getElementById('sendBtn').onclick = function() {
            // window.parent代表父页面
            window.parent.postMessage(document.getElementById("iframe").value, '*');
        }
    </script>
</body>
```
<br/>
#### iframe页面获取父页面信息：
demo我放在github上的：
demo地址：[iframe页面获取父页面信息](https://htmlpreview.github.io/?https://github.com/yellowlemon/Demo/blob/master/postMessage/atob/a.html)

**父页面：**
```html
<body>
    <p>我是父页面</p>
    <div>发送的信息是：<input id="iframe" type="text"><input id="sendBtn" type="button" value="发送"></div>
    <iframe src="/atob/b.html" frameborder="0" marginheight="0" marginwidth="0"></iframe>
    <script>
        // 点击按钮后向iframe页面发送数据
        document.getElementById('sendBtn').onclick = function() {
            // window.frames[0]代表第一个iframe页面
            window.frames[0].postMessage(document.getElementById("iframe").value, '*');
        }
    </script>
</body>
```

**iframe页面**
```html
<body>
    <p>我是iframe页面</p>
    <p>接收到的父页面信息是：<span id="content"></span></p>
    <script>
        // event 参数中有 data 属性，就是父页面发送过来的数据
         window.addEventListener("message", function(event) {
             // 把父页面发送过来的数据显示在父页面中
           document.getElementById("content").innerHTML= event.data;
         }, false);
    </script>
</body>
```

通过这两个简单的例子我们会发现postMessage的强大之处，有了postMessage，我们甚至可以在两个iframe之间传送数据，只需要一个共同的父元素做中间页。

<br/>
#### 需要注意的地方：

**兼容性：**

![caniuse](/assets/blogImg/postMessage02.png)

通过图上可以看出，基本大部分的都能兼容的，但是需要注意一下这句话。

> Internet Explorer 8 and 9, and Firefox versions 6.0 and below only support strings as postMessage's message.(ie 8和9,和Firefox 6.0及以下版本只支持字符串作为postMessage的消息。)

所以如果需要支持这些版本的需要注意data只能是字符串。

**安全性：**

在线上进行交互时，最好做好安全措施，对于postMessage，最好采用“双向安全机制”。发送方发送数据的时候，确认接受方的源（所以最好不要用*），而接受方监听到message事件后，也可以用event.origin判断是否来自于正确可靠的发送方。并且最好做一下传递数据的数据类型校验。如以下代码：

```
function checkMessage(event) {
    // 只获取需要的域，并非所有都可以跨域
    if (event.origin != "need domain") {
        return false;
    }
    
    var data = event.data;
    // 传输数据类型校验
    if (typeof(data) !== 'object') {
        return false;
    }

    // data 的type中包含xxx则为xxx需要字段。
    return data.type === "xxx";
};
```

并且通过不同的type可以处理不同的数据，如以下代码：

```
switch (checkMessage(event)) {
    case 'height':
        $("#bet_"+e.data.id).css("height", (e.data.height+1) + "px");
        break;
    case 'jumpDown':
        window.location.href = downUrl;
        break;
    default:
        break;
}
```