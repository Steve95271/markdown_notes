# Servlet 简介

## 静态资源和动态资源

### 静态资源

- **无需通过程序运行**去生成的资源。资源在程序运行之前就存在。例如已经写好的 html、css、javascript、视频、图片、音频等资源。

- 静态资源获取流程

  ![JavaWeb-note-2](/Users/fengyingcong/Git-repos/JavaWeb/img/JavaWeb-note-2.jpg)

  



### 动态资源

- **需要运行程序**生成的资源。程序运行前无法确定数据。例如servlet、thymeleaf。
- 动态资源不是指视图上的动画效果或者是人机交互的效果。



## Servlet

Servlet - 处理客户端的请求并做出响应的一套技术标准。

### Tomcat

在接收到请求后，Tomcat 服务器会对其转换成 **HttpServletRequest** 对象。

​	对象中包含了：

  - 请求行
  - 请求头
  - 请求体



此外，Tomcat 还会创建一个 **HttpServletResponse** 对象用于储存返回给客户端的数据。客服端接收到后可以将这个对象解析成响应报文（响应行、响应头、响应体）。