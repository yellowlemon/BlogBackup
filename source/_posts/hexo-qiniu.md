---
title: 'Hexo折腾之图片存储'
date: 2016-12-07 14:58:16
tags:
    - hexo
---
hexo博客弄好了，写博客遇到了一个最大的问题，就是图片的存放问题，看了一下litten的博客，他是把图片都存在自己服务器的。可是自己的服务器存储是用的系统存储，应该没有那么大的空间，怎么办了？？

那就只有看看网上的云存储有什么比较好的了，最早的时候，我用的图床是贴图库，之前他可以无限流量，不限时间，但是现在保存时间只能有6个月了。

那肯定不行啊，所以只有换一个了，查了资料，发现了七牛云存储，有一个专门的hexo的七牛插件：[hexo-qiniu-sync](https://github.com/gyk001/hexo-qiniu-sync)，但是这个插件有个问题，就是只会把本地文件同步到七牛。这跟我的设想完全不符。

最后我发现了一个神器——极简图床
<!-- more -->
![极简图床](/assets/blogImg/hexo-qiniu01.jpg)

下面就来讲一讲如何配置七牛以及极简图床：

注册的流程就不说了，注册成功以后，需要做的是：

* 注册七牛账号并添加资源，然后充值￥10(为了后面的自定义域名绑定)。
* 选中对应资源绑定加速域名，即将绑定的域名必须为中国大陆以备案。推荐使用二级域名。以免出现，域名绑定成功，通过外链无法访问资源的尴尬（因为 Hexo 部署到 GitHub/Coding 的时候已经对该域名解析里指定过CNAME，再次添加CNAME会报错）。
* 强烈推荐使用 Chrome + [极简图床（Chrome 插件）](http://yotuku.cn/)+ 上面配置好的七牛账号，完成 Markdown 编辑器里面的外链图片插入工作。

极简图床对于七牛的配置也非常简单，只需要填入一些参数即可

![极简图床+七牛](/assets/blogImg/hexo-qiniu02.jpg)

并且极简图床支持markdown语法的复制，可以一步到位，几乎无伤~真是超赞。