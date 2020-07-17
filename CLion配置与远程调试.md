---
title: CLion配置与远程调试
date: 2020-07-17 16:08:56
categories:
	- C++
tags:
	- C++
	- 常用工具
---

### CLion远程部署

首先添加远程路径与当前路径的映射关系。

如下图所示。

<img src="CLion配置与远程调试/01.png" alt="image-20200717161209164" style="zoom:50%;" />

<img src="CLion配置与远程调试/02.png" alt="image-20200717161411593" style="zoom: 50%;" />

<img src="CLion配置与远程调试/03.png" alt="image-20200717191014703" style="zoom:50%;" />

配置后之后，可以使用同步功能来实现服务器和本地的文件的同步。

<img src="CLion配置与远程调试/04.png" alt="image-20200717191125145" style="zoom:50%;" />

然后添加服务器的工具链。

![image-20200717191326471](CLion配置与远程调试/05.png)

一定要将`CMake`设置为对应的工具链。这里的`Generation path`为构建目录，默认有一个路径，可以修改成自己的路径。

<img src="CLion配置与远程调试/06.png" alt="image-20200717191429298" style="zoom: 33%;" />

然后重新加载。

<img src="CLion配置与远程调试/07.png" alt="image-20200717191806999" style="zoom:50%;" />

最后就可以`build`对应的目标了。

<img src="CLion配置与远程调试/08.png" alt="image-20200717191927843" style="zoom:50%;" />

