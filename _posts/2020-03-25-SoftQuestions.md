---
layout: post
title: Soft
subtitle: mathtype6.9b
date: 2020-03-25
author: Weiami
header-img: img/post-bg-2016.jpg
catalog: false
tags:
    - mathtype
---

mathtype下载安装之后有30天的试用期，试用期到了之后mathtype变为精简版。之前在CSDN博客查看到解决方法，修改注册表，但是最近CSDN博客总是屏蔽相关内容，所以将解决方法写在博客，以防忘记。

解决方法：

1.关闭mathtype。

2.执行win+R快捷键，输入regedit.exe，打开注册表编辑器。

3.通过路径“计算机\HKEY_CURRENT_USER\Software\Install Options”找到Options6.9，将其删除。

4.重新打开mathtype，又有30天试用期。

**注**：感谢之前CSDN博客提供的解决方法，但是忘记博主。这个方法只能使mathtype有30天的试用期，十分难受。

