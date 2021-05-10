---
title: （python初探）python爬虫基础——爬取百度首页的图片并保存到本地（二）
tags: 学习新知识
---

上一篇介绍了什么是爬虫以及如何查看网页信息，这一篇我们介绍一下进行爬虫的重要工具。


**1.urllib**

urllib是python的内置库，它主要有以下功能：
- 网页请求
- 响应获取
- 代理和cookie设置
- 异常处理
- URL解析
它包含了4个模块：request、error、parse、robotparser

request模块是使用较多的模块，下文主要介绍该模块；error用于异常处理；parse用于解析URL；robotparser用的比较少，用于解析网站的robot.txt

那么request模块如何使用呢？先看如下代码：
```bash
#导入request模块
import urllib.request

#访问百度
response = urllib.request.urlopen('http://www.baidu.com')
#打印服务器返回的内容
print(response.read().decode('utf-8'))
```

执行后结果如下图所示，可以看到我们已经获得了百度首页的源码~
![urllib_reponse.png](https://i.loli.net/2021/05/10/8GHhq7o9ATFw1nu.png)

代码中的urlopen方法具体使用方法为
```bash
urllib.request.urlopen(url, data=None, [timeout, ]*)
```
其中`url`即为我们要访问的地址；`data`是专门为post请求携带参数的，如登录时可以将用户名和密码封装称data发送；`timeout`用于设置请求超时时间，如果服务器超过一定时间没响应就不管它。

但是在某些情况下，直接使用urlopen并不能获取信息或不能获取完整的信息，那么我们需要伪装成浏览器请求，需要添加请求头信息，这时就要用到Request方法
```bash
urllib.request.Request(url, data=None, headers={}, method=None)
```
除了`url`和`data`，还可以设置请求头信息`headers`；此外还可以设置请求方式`method`，urlopen只能使用GET和POST方式，而Request可以设置我们自己的请求方式。
比如我们想要获取B站首页的源码，如果直接使用urlopen方式，会获得如下结果：
![urlopen_bilibili.png](https://i.loli.net/2021/05/10/4un2dLvGFUto6rs.png)

我们加上Request方法
```bash
#导入request模块
import urllib.request

#设置访问网址
url = 'https://www.baidu.com/'
#设置请求头信息，伪装成浏览器
header = {'user-agent' : 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36'}
#封装request
req = urllib.request.Request(url,headers=header)
#进行请求
response = urllib.request.urlopen(req)
#打印服务器返回的内容
print(response.read().decode('utf-8'))
```
执行结果如图所示，我们获得了B站首页源代码咯~
![Request_bilibili.png](https://i.loli.net/2021/05/10/JFoRqBXdpEcKCAL.png)

**2.Requests库**

接下来介绍一个新的库：Requests。注意不要把它和urllib中的Request方法搞混了，Requests不是python的内置库，所以需要自己安装，我们直接使用以下指令安装：
```bash
pip install requests
```
安装完成后就可以直接使用啦。

Requests使用起来比urllib更简洁一些，比如我们同样要访问百度首页：
```bash
#导入requests模块
import requests

#GET请求网址
response = requests.get('https://www.baidu.com/')
#设置编码方式，由于终端使用默认是gbk编码，会出现乱码，我们这里改成utf-8编码
response.encoding = 'utf-8'
#打印服务器返回的文本内容
print(response.text)
```
结果如下：
![Requests_baidu.png](https://i.loli.net/2021/05/10/AI4GehOnci8VN7J.png)

但我们可以发现，现在得到的结果与先前使用urllib得到的结果相比，少了很多内容，这里需要我们伪装成浏览器访问才能获得相同的结果，于是可将代码改为如下形式：
```bash
#导入requests模块
import requests

#GET请求网址
header = {'user-agent' : 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36'}
response = requests.get('https://www.baidu.com/',headers=header)
#设置编码方式，由于windows终端使用默认是gbk编码，会出现乱码，我们这里改成utf-8编码
response.encoding = 'utf-8'
#打印服务器返回的文本内容
print(response.text)
```
执行后得到的结果与urllib所得结果相同。可以看到使用Requests的代码与使用urllib代码相比会简单一些。但Requests的功能远不止这个，它还可以获取响应码、响应头以及其他很多功能，具体可查看文档
[Requests: 让 HTTP 服务人类](https://docs.python-requests.org/zh_CN/latest/)

**3.正则表达式**

在获取了网页源码后，我们要学会如何从中提取出我们想要的信息，毕竟我们不可能什么信息都要，这时候就需要用到正则表达式，它能够匹配特定格式的内容。
在正则表达式中，定义了一些特殊的符号来匹配不同的字符，如`\d`匹配0-9中的任意一个数字，`.`匹配除`\n`、`\r`的任意单个字符，具体可以参考[re --- 正则表达式操作](https://docs.python.org/zh-cn/3/library/re.html)

要使用正则表达式，需要用到re模块。通过以下代码，我们来看看如何使用它。
```bash
#导入re模块
import re

#待匹配内容
content = "I want to have 100 billion dollars"
#进行匹配
res = re.match('I\swa.*(\d+)\s.*s',content)
#打印结果
print(res.group(1))
```
这里我们通过正则表达式匹配得到了字符串中的一个数字0。当对代码进行如下修改时，能够得到数字100：
```bash
#导入re模块
res = re.match('I\swa.*?(\d+)\s.*s',content)
```
我们可以看到区别只在于在`want`后面的`.*`后面加上了一个`?`，分别对应正则表达式的`贪婪模式`和`非贪婪模式`，贪婪模式会尝试匹配尽可能多的字符，非贪婪模式则是尝试匹配尽可能少的字符。

另外，我们爬取到的源码不可能是一行就完事了，通常会包含换行，但`.*?`并不能匹配换行符，比如：
```bash
content = """I want to have 100 
billion dollars"""
```
应该如何处理呢？这时需要用用到re的匹配模式，如下所示，在match中加一个参数re.S：
```bash
#导入re模块
import re

#待匹配内容
content = """I want to have 100 
billion dollars"""
#进行匹配
res = re.match('I\swa.*?(\d+)\s.*s',content,re.S)
#打印结果
print(res.group(1))
```
当然，除了match方法，还可以用`search`方法，也能得到相同的结果：
```bash
#导入re模块
import re

#待匹配内容
content = """I want to have 100 
billion dollars"""
#进行匹配
res = re.search('I\swa.*?(\d+)\s.*s',content,re.S)
#打印结果
print(res.group(1))
```

但如果待匹配内容中有多个数字呢，我们想要获得所有数字，上面的方法就行不通了，这时可以使用`findall`方法，如：
```bash
#导入re模块
import re

#待匹配内容
content = """I want to have 100 billion dollars;
I want to have 100 billion dollars;
I want to have 100 billion dollars;
I want to have 100 billion dollars;"""
#进行匹配
res = re.findall('I\swa.*?(\d+)\s.*?s;',content,re.S)
#打印结果
print(res)
```
这样我们就得到了4个100了。

有时我们还会看到有人使用re.compile方法，该方法的主要目的就是把匹配模式封装便于以后复用。

**4.BeautifulSoup**

介绍完了正则表达式，我们会发现，如果每次都要去设计匹配模式会太麻烦了，也容易出错。于是就有一种高效的网页解析库来帮助我们更加便捷的提取我们想要的信息，这就是BeautifulSoup。

BeautifulSoup也是需要自己安装的，它在bs4这个包中，所以我们通过如下代码进行安装：
```bash
pip install bs4
```
它支持多种不同的解析器，其中我们目前最多的是lxml，因为它的速度快、解析能力强，lxml也需要自己安装：
```bash
pip install lxml
```
BeautifulSoup的具体使用方法可以查看文档[Beautiful Soup 4.4.0 文档](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/)

使用它我们可以完全不用管什么匹配模式了，总之就是非常简便！
