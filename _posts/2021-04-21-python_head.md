---
title: （Python初探）python脚本开头`#!/usr/bin/env python`和`# -*- coding: utf-8 -*-`的作用
tags: 学习新知识
---

很多Python程序的开头两行会出现以下程序：
```bash
#!/usr/bin/env python
# -*- coding: utf-8 -*-
```
**那么这两行程序有什么用呢？**
首先，这两句都只在类Unix系统下才有意义~

先看第一行
```bash
#!/usr/bin/env python
```
它的作用是引导程序能够找到Python的解释器，使得不论文件保存在什么地方，程序都能执行，而不用指定Python的安装路径。
有时也会有程序用到如下程序
```bash
#!/usr/bin/python
```
两者的目的是相同的，区别在于：
前者可以用于用户没有将Python安装在默认的路径/usr/bin的情况，前者在遇到这种情况时，会先在环境变量中查找Python的安装路径，然后调用对应路径下的解释器程序。
后者则只能调用/usr/bin下的Python解释器

再看第二行
```bash
# -*- coding: utf-8 -*-
```
它的作用是告诉Python程序采用utf-8编码格式。因为对于Python2来说，解释器默认使用ascii编码，因此遇到中文字符会直接报错，故添加该行以正确解释中文字符。
但对于Python3来说，默认使用utf-8进行编码，因此省略该行也可正常解释中文字符。

参考：
- [Python文件头部的#!/usr/bin/env python是什么意思](https://blog.csdn.net/ai_XX/article/details/82998403 )
- 《跟老齐学Python：从入门到精通》
- [python中coding=utf8的作用](https://blog.csdn.net/wo198711203217/article/details/101204755 )
