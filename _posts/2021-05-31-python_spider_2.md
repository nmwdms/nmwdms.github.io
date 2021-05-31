---
title: （python初探）python爬虫基础——爬取百度首页的图片并保存到本地（三）
tags: 学习新知识
---

接上一节，介绍了我们要使用的工具后，接下来就要实战了，话不多说，直接上代码，代码的注释已经很清楚了，就不再多做解释了，有需要的话可以翻看前面两篇内容。
- [python爬虫基础——爬取百度首页的图片并保存到本地（一）](https://nmwdms.github.io/_posts/2021-05-08-python_spider/)
- [python爬虫基础——爬取百度首页的图片并保存到本地（二）](https://nmwdms.github.io/_posts/2021-05-10-python_spider_1/)

```bash
#!/usr/bin/env python
#-*- coding:utf-8 -*-

#导入相关模块
import requests
from bs4 import BeautifulSoup
import os
import time

#函数：主函数
def main():
    #要爬取的网址
    url = "https://www.baidu.com/"
    #得到网页源代码
    html = request_baidu(url)
    #使用BeautifulSoup解析源代码,解释器使用lxml
    soup = BeautifulSoup(html,'lxml')
    #保存图片
    save_img(soup)

#函数：得到网页源代码    
def request_baidu(url):
    try:
        #定义请求头信息
        header = {'user-agent' : 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36'}
        #模拟浏览器对网页发出请求
        response = requests.get(url,headers=header)
        #如果响应码为200，说明请求成功
        if response.status_code == 200:
            #返回源代码
            print("Request sucess!")
            return response.text
    #请求失败则返回None
    except requests.RequestException:
        print("Request fail!")
        return None

#函数：保存图片      
def save_img(soup):
    #保存图片的路径
    path = './images'
    #创建文件夹用于保存图片
    mkdir(path)
    #找到源代码中所有'img'标签的内容
    img_lst = soup.find_all('img')
    #遍历所有图片
    for index,img in enumerate(img_lst):
        #得到图片网址
        img_src = img.get('src')
        #如果图片不为png格式，直接进入下次循环
        if '.png' not in img_src:
            continue
        #如果网址中没有'https:'
        if 'https:' not in img_src:
            #在网址前面添加'https:'
            img_src = 'https:' + img_src
        #向图片网址发出请求
        img_reponse = requests.get(img_src)
        #定义图片名
        img_name = path + '/' + str(index+1) + '.png'
        #以二进制写方式打开文件
        with open(img_name,'wb') as file:
            #下载图片
            file.write(img_reponse.content)
        #关闭文件
        file.close()
        #显示下载进度
        print('第%d张图片下载完成' %(index+1))
    #显示完成下载
    print('抓取完成')

#函数：创建文件夹
def mkdir(path):
    #如果路径path已经存在
    if os.path.exists(path):
        #提示已存在文件夹
        print(path,'already exists!')
    #否则创建新文件夹
    else:
        #创建新文件夹
        os.makedirs(path)
        #提示文件夹创建成功
        print('Create new folder')

#代码运行入口
if __name__ == '__main__':
    #进入主函数
    main()
```