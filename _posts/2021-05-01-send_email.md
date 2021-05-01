---
title: （python初探）python发送纯文本邮件
tags: 学习新知识
---
要使用python发送邮件，要使用到两个模块，分别是stmplib和email，两者都是python自带的。

stmp模块主要负责发送邮件，包括连接服务器、登录、发送等操作。

email模块主要用于构造邮件，指邮件中的一些信息，如发件人、收件人、邮件内容等。

**1.stmp模块**

其使用方法如下：
```bash
import stmp

stmp = smtplib.SMTP() #创建一个stmp实例
smtp.connect('smtp.163.com',25)	#连接服务器
smtp.login(username, password) #登录
smtp.sendmail(sender, receiver, msg.as_string()) #发送
smtp.quit() #退出
```
`connect('smtp.163.com',25)`中`smtp.163.com`表示指定连接的邮箱服务器host，不同的服务商对应不同的host，具体可以在网上查看；`25`表示的是连接服务器的端口号，stmp默认端口号均为25。

**2.email模块**

email模块下有mime包，全称“Multipurpose Internet Mail Extensions”，即多用途互联网邮件拓展，是目前互联网电子邮件普遍遵循的邮件技术规范。

要发送给纯文本邮件，需要导入mime包下的text模块，即：
```bash
from email.mime.text import MIMEText
```
当然mime下还有其他模块，如image模块用于发送图片，multpart模块表示多个对象的组合。

text模块的使用方法如下：
```bash
message = MIMEText('content','plain','utf-8') #设置邮件内容
message['Subject'] = 'title' #设置邮件主题
message['From'] = sender #设置邮件发送方信息
message['To'] = receivers[0] #设置邮件接收方信息
```
`MIMEText('content','plain','utf-8')`中`content`表示正文内容，`plain`表示mime的subtype，这里`plain`表示纯文本，还可以选择`html`；`utf-8`表示编码方式。

`receivers[0]`表示单个接收邮箱，也可以改为多个，用`[]`包裹，每个邮箱间用逗号连接。

发送纯文本邮件的实例程序如下：
```bash
#!/usr/bin/env python
#-*- coding:utf- -*-

#导入stmplib与email相关模块
import smtplib
from email.mime.text import MIMEText

#设置STMP服务器地址，因为这里使用的是网易邮箱，所以服务器地址是smtp.163.com
mail_host = 'smtp.163.com'
#设置用户名
mail_user = 'XXX@163.com'
#设置登录密码
mail_pass = 'XXX'

#设置发送邮箱
sender = "XXX@163.com"
#设置目标邮箱
receivers = ['XXX@qq.com']

#设置邮件信息
#设置邮件内容
message = MIMEText('content','plain','utf-8')
#设置邮件主题
message['Subject'] = 'title'
#设置邮件发送方信息
message['From'] = sender
#设置邮件接收方信息
message['To'] = receivers[0]

#登录并发送邮件
try:
	#连接到服务器
    smtpObj = smtplib.SMTP()
    smtpObj.connect(mail_host,25)
    #登录到服务器
    smtpObj.login(mail_user,mail_pass)
    #发送邮件
    smtpObj.sendmail(sender,receivers,message.as_string())
    #退出
    smtpObj.quit()
    #打印成功
    print('sucess')
except smtplib.SMTPException as e:
    #打印错误
    print('error',e)
```
注意：

1.在发送邮件前，要先开启自己邮箱的stmp功能，具体方法可在网上搜索
2.
3.设置登录密码时使用的密码并不是用户密码，而是在开启邮箱stmp功能后给到的授权码
4.
5.某些邮箱如qq邮箱需要SSL认证，stmp不能满足要求，需要使用stmp_ssl，所以在向此类邮箱发送邮件时可进行如下改动：
```bash
smtpObj = smtplib.SMTP()
smtpObj.connect(mail_host,25)
#######替换为########
smtpObj = smtplib.SMTP_SSL(mail_host)
```

参考：
- [简单三步，用 Python 发邮件](https://zhuanlan.zhihu.com/p/24180606)
- [python自动发邮件总结及实例说明](https://www.cnblogs.com/yufeihlf/p/5726619.html)
