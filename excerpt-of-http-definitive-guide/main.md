### 注意

本文摘录《HTTP权威指南》（David Gourley, Brian Totty, Marjorie Sayer, Sailu Reddy, Ansbu Aggarwal 著, 陈涓、赵振平 译、人民邮电出版社发行、2012年10月第一版）中的一些关于HTTP的重要基本概念以及应用实践，为有需要者高效快速地了解这一英特网常用协议。

## 第一章 HTTP概述

### 1.1 资源

Web服务器是Web资源（Web resource）的宿主。Web资源是Web内容的源头。Web资源可以是静态文件资源，也可以是通过软件根据一定条件计算得到的动态资源。

每个资源都有其MIME 类型（MIME type）。最初设计MIME（Multipurpose Internet Mail Extension, 多用途英特网邮件扩展）是为了解决在不同的电子邮件系统之间搬移报文时存在的问题。由于其在邮件系统中良好的表现，HTTP也采纳了它用来描述多媒体内容。
下面列出几种常见的MIME类型：

* text/html : HTML格式的文本文档。
* text/plain ：普通的ASCII文本文档
* image/jpeg ：JPEG格式的图片

每个Web服务器资源都有一个名字，他们被称为统一资源标识符（Uniform Resource Identifier, URI）。URI在世界范围内唯一标识定位信息资源。URI是一种统称，其具体实现分为URL和URN。

URL

### 1.2 一次完整的HTTP事务

### 1.3 简单的报文实例

### 1.4 HTTP网络协议栈