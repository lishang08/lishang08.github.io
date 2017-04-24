2017-04-25-Quartz中cron的使用.markdown

---
layout:     post
title:      "Quartz中cron的使用"
subtitle:   " \"Quartz中cron的使用\""
date:       2017-04-25 00:26:00
author:     "Lishang"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Quartz
---

Quartz 中cron的输入是有格式要求的，正确的格式应该如下：

<bean id="*****"
       	class="org.springframework.scheduling.quartz.CronTriggerBean">
      	<property name="jobDetail" ref="job****" />
      	<!-- 每5分钟一次调度 -->
      	<property name="cronExpression" value="0 0/5 * * * ?" />
</bean>


如果出现格式错误，会报如下错误：

type Exception report

message Request processing failed; nested exception is java.lang.RuntimeException: CronExpression '*/5 * * * *' is invalid.

description The server encountered an internal error that prevented it from fulfilling this request.

exception

org.springframework.web.util.NestedServletException: Request processing failed; nested exception is java.lang.RuntimeException: CronExpression '*/5 * * * *' is invalid.
	org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:894)
	org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:789)
	javax.servlet.http.HttpServlet.service(HttpServlet.java:650)
	javax.servlet.http.HttpServlet.service(HttpServlet.java:731)
	org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:52)
	org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:88)
	org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:76)
root cause

java.lang.RuntimeException: CronExpression '*/5 * * * *' is invalid.
	org.quartz.CronScheduleBuilder.cronSchedule(CronScheduleBuilder.java:111)
	net.aimeizi.quartz.utils.ScheduleUtils.createScheduleJob(ScheduleUtils.java:84)
	net.aimeizi.quartz.utils.ScheduleUtils.createScheduleJob(ScheduleUtils.java:61)
	net.aimeizi.quartz.service.impl.ScheduleJobServiceImpl.insert(ScheduleJobServiceImpl.java:57)
	net.aimeizi.quartz.controller.ScheduleJobController.saveScheduleJob(ScheduleJobController.java:106)
	sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	java.lang.reflect.Method.invoke(Method.java:498)
	org.springframework.web.bind.annotation.support.HandlerMethodInvoker.invokeHandlerMethod(HandlerMethodInvoker.java:176)
	org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter.invokeHandlerMethod(AnnotationMethodHandlerAdapter.java:436)
	org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter.handle(AnnotationMethodHandlerAdapter.java:424)
	org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:923)
	org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:852)
	org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:882)
	org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:789)
	javax.servlet.http.HttpServlet.service(HttpServlet.java:650)
	javax.servlet.http.HttpServlet.service(HttpServlet.java:731)
	org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:52)
	org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:88)
	org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:76)
root cause

java.text.ParseException: Unexpected end of expression.
	org.quartz.CronExpression.buildExpression(CronExpression.java:494)
	org.quartz.CronExpression.<init>(CronExpression.java:276)
	org.quartz.CronScheduleBuilder.cronSchedule(CronScheduleBuilder.java:107)
	net.aimeizi.quartz.utils.ScheduleUtils.createScheduleJob(ScheduleUtils.java:84)
	net.aimeizi.quartz.utils.ScheduleUtils.createScheduleJob(ScheduleUtils.java:61)
	net.aimeizi.quartz.service.impl.ScheduleJobServiceImpl.insert(ScheduleJobServiceImpl.java:57)
	net.aimeizi.quartz.controller.ScheduleJobController.saveScheduleJob(ScheduleJobController.java:106)
	sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	java.lang.reflect.Method.invoke(Method.java:498)
	org.springframework.web.bind.annotation.support.HandlerMethodInvoker.invokeHandlerMethod(HandlerMethodInvoker.java:176)
	org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter.invokeHandlerMethod(AnnotationMethodHandlerAdapter.java:436)
	org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter.handle(AnnotationMethodHandlerAdapter.java:424)
	org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:923)
	org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:852)
	org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:882)
	org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:789)
	javax.servlet.http.HttpServlet.service(HttpServlet.java:650)
	javax.servlet.http.HttpServlet.service(HttpServlet.java:731)
	org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:52)
	org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:88)
	org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:76)
