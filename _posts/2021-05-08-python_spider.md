---
title: （python初探）python爬虫基础————爬取百度首页的图片并保存到本地（一）
tags: 学习新知识
---

**1.什么是爬虫？**

以百度首页为例，让我们通过Chrome先进入百度首页`https://www.baidu.com`
![baidu.png](https://i.loli.net/2021/05/10/DoLVpri35SjZga8.png)
右键单击该页面，选择查看网页源代码，如图所示：
![baidu_src.png](https://i.loli.net/2021/05/10/l5RKFurW1j9i3yw.png)
可以看到，网站都是由代码组成的，里面包含了很多数据，那么如果通过一种自动化的方法来获取网站中的数据，这就是爬虫。

网上各种各样的网站都是托管在服务器上的，服务器保存着这些网站数据，24小时运行，等待用户发送请求，在收到用户请求后，服务器会将数据发送给用户。
爬虫就是利用这一点，模拟用户向服务器发送请求，服务器会误以为是真正的用户发送的请求，于是将数据返回给爬虫。在接收到数据后，我们就可以对数据进行解析来获取我们想要的内容了。

当然实际上也不是都能这么简单的爬取所有网站的数据，因为爬虫会给服务器带来负担，因此部分网站会有反爬虫机制，不过这个不是本文的主要讨论范围。

**2.使用Chrome浏览器抓包**

前面说了爬虫的流程，为了能够爬取到我们想要的内容，就需要对网页源码进行分析，要知道应该怎么请求，要知道获取的数据是什么样的，因此要学会如何进行数据抓包。

这里使用Chrome浏览器（注：尽量不要使用国产浏览器，可能有Bug），还是到百度首页，右键单击后选则检查，可以看到如下页面：
![baidu_check.png](https://i.loli.net/2021/05/10/ul4MKFAIW9d5TUg.png)
在Element标签下对应的Html代码其实就是该网页的源码，想要查看网页中某个部分对应的代码，只要将鼠标放在该部位上，同样单击右键并选择检查，比如我们选择“百度一下”这个按钮：
![baiduyixia.png](https://i.loli.net/2021/05/10/3iLVm4yXuODEBsp.png)
通过这种方法我们就可以很轻易的找到我们所需数据对应的代码。

接下来我们点击一下Network标签：
![baidu_request.png](https://i.loli.net/2021/05/10/A6Du2sePzRyO1C7.png)
可以看到我们发送了许多请求，HTTP的请求方式有很多种，如GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE，其中最常见的是`GET`和`POST`，比如我们搜索`莱月昴`，浏览器又发送了很多请求，
点开其中一个查看
![baidu_486.png](https://i.loli.net/2021/05/10/hrtTIDn2Rwx6FZm.png)
其中请求URL为
![baidu_486URL.png](https://i.loli.net/2021/05/10/FAR2uQSz7cNjkGH.png)
`?`后面那些参数就是GET的请求参数，这些参数以键值对的形式实现，如`wd=%E8%8E%B1%E6%9C%88%E6%98%B4`就表示要搜索`莱月昴`相关内容，直接在浏览器中输入网址`https://www.baidu.com/s?wd=莱月昴`结果是一样的

POST请求通常用在需要进行信息提交的时候，如注册、登录。以登录百度为例，当我们在输入完账号密码点击登录后，浏览器会请求login接口，请求方法为POST。
![baidu_login.png](https://i.loli.net/2021/05/10/k913oUaAgIh4VJ8.png)

![baidu_login_request.png](https://i.loli.net/2021/05/10/rTIvp7alSXM6CW3.png)

接下来说请求头，浏览器在进行HTTP请求时，出了提交参数，还会包含一些如Accept、Host、cookie、User-Agent的头部信息，我们可以通过在爬虫中添加这些信息假装正规请求欺骗服务器。

最后说服务器响应，我们在上网的时候肯定都遇到过页面返回404或502，表示页面请求失败。而正常情况下，服务器会返回200的响应码表示请求成功。当然服务器还会返回响应头和响应体，如图所示。
![baidu_response_header.png](https://i.loli.net/2021/05/10/TjVF1E6w7baphdc.png)

![baidu_response.png](https://i.loli.net/2021/05/10/JZbqLyMkDFKIsCx.png)



参考：
- [python爬虫入门：什么是爬虫，怎么玩爬虫？](https://mp.weixin.qq.com/s?__biz=MzU2ODYzNTkwMg==&mid=2247484054&idx=1&sn=1e3da765f7b409a2af3bfd1b8c882091&chksm=fc8bba07cbfc33115826c7b8077839e96ac7fb9545df29f41755cd727603e0ad7d18d1658c1a&scene=178&cur_album_id=1321044729160859650#rd)
