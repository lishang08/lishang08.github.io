---
layout:     post
title:      "DispatcherServlet noHandler Found"
subtitle:   " \"DispatcherServlet noHandler Found\""
date:       2017-05-06 12:58:00
author:     "Lishang"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 技术
---

## 问题
DispatcherServlet noHandler Found
<p id = "build"></p>
## Log
<p id = "build"></p>
[2316][WARN][http-bio-8089-exec-3] 12:47:14,787 method:org.springframework.web.servlet.DispatcherServlet.noHandlerFound(DispatcherServlet.java:1080) No mapping found for HTTP request with URI [/spring-quartz/] in DispatcherServlet with name 'springmvc'
<p id = "build"></p>
## 寻找解决方法

1. web.xml配置不正确？

仔细察看了web.xml， 没有发现什么问题
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         id="WebApp_ID" version="2.5">

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath*:applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath*:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>*.shtml</url-pattern>
    </servlet-mapping>

    <welcome-file-list>
        <welcome-file>list-schedule-job.shtml</welcome-file>
    </welcome-file-list>
</web-app>
```
<p id = "build"></p>
2. view的解析，好像也没有什么问题
<p id = "build"></p>
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         id="WebApp_ID" version="2.5">

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath*:applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath*:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>*.shtml</url-pattern>
    </servlet-mapping>

    <welcome-file-list>
        <welcome-file>list-schedule-job.shtml</welcome-file>
    </welcome-file-list>
</web-app>
```
<p id = "build"></p>
<p>3. Controller没写对？ --> 问题就出现在这里， 对于annotation-driven来说，每个Controller都必须在Class头添加@Controller</p>
<p id = "build"></p>
<p>Ok,问题解决！！！</p>

Marked on 20170506 by SamFu

