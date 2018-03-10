---
layout: post
title: Spring Cloud微服务（基于Spring boot开发）
categories: Java
tags: SpringCloud

---

### 1）Hystrix（服务器雪崩怎么办？） ###

熔断机制

无微服务时配置tomcat的server.xml

- 服务熔断
- 限流机制
- 服务降级

### 2）Spring Cloud Config(分布式配置中心) ###

- 痛点分析（配置中心）
配置形式不可控（property、xml、数据库、系统环境）、配置分散、配置环境修改麻烦、配置刷新机制
- 示例
sys->配置->config server->更新添加配置
- 特性演示
- 落地
- 架构剖析