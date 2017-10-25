---
layout:     post
title:      "Java Web Dev [1: Servlet]"
subtitle:   "\"Java网页开发初探1\""
date:       2017-10-4 14:10
author:     "Haoyang"
header-img: "img/sky-bg.jpg"
catalog:    true
tags:
    Java Web 总结
---

> 熟悉使用Java Servlet

## 介绍

在网页开发中，目前的一般开发模式都遵从MVC框架，即**Modal, View, Controller**。这样设计的好处有很多，比如在设计前端的时候不用处理数据的validate的问题，同时在后端处理数据的时候不用担心实际显示时候的逻辑。当然现在用很多例如Spring, Structs的框架有比JSP更加成熟的模版语言，但是这次的介绍的目的是熟悉使用Apache9在Java中的使用方法，所以从最基本的JSP开始，不过为了方便起见还是会简单介绍一下JSTL。

## 什么是Servlet

Servlet在client端是为了给server传递一个信号，然后通过Servlet把后端处理之后的数据传输回给前端，这样用户就可以看到一个response的view。比如在点击搜索按钮之后，用户输入的搜索的字符会被传输到后端，这时后端会与数据库交互然后把所找出的数据储存并且传输给前端，Servlet在这里的作用就是连接前端和后端。这么说可能有一些抽象，其实在我看来servlet更像是一个controller，主要用作处理数据，然后把数据发送到前端。

## 使用Servlet

我使用的是在Eclipse中的Dynamic Web App中的环境，所以不同的ide会有不同的方式生成Servlet java文件，这里仅介绍Eclipse环境中的Servlet。

1. 首先进入一个空白的Servlet:
```java
@WebServlet("/index")
@MultipartConfig
public class MyServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;

    /**
     * Default constructor.
     */
    public CS201PageServlet() {
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
    }

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
	}
```

--Haoyang
