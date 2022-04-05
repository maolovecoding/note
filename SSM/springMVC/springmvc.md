# SpringMVC 概述

## springmvc概述

### 1.1 springmvc基本说明

SpringMVC是基于spring的，是spring的一个模块，做web开发使用的。springmvc叫做spring web mvc，说明也是spring的核心技术，做web开发，springmvc内部是使用mvc架构模式。

Springmvc是一个容器，管理对象的，使用IOC核心技术，springmvc管理界面层中的控制器对象。

springMVC底层也是Servlet，以Servlet为核心，接收请求，处理请求，显示结果给用户。

处理用户请求：

用户客户端发起请求： ---> springmvc ----> spring ----> mybatis ----> 数据库

### 1.2 SpringMVC中的核心Servlet（DispatcherServlet）

DispatcherServlet是框架的一个Servlet对象。是一个已经写好的供我们使用的servlet，负责接收请求，响应处理结果

DispatcherServlet的父类是HttpServlet。

DispatcherServlet也叫做前端控制器（front controller）



