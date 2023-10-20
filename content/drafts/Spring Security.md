---
title: Spring Security
date: 2022/4/3 20:36:25
comments: true
categories: 
  - java
---
# Spring Security
## Servlet Applications
### 1.Architecture（体系结构）
#### DelegatingFilterProxy
DelegatingFilterProxy是Spring Security程序的入口。DelegatingFilterProxy通过实现Servlet中的filter来拦截请求，同时因为filter的注册时间在Spring容器的启动之前，所以DelegatingFilterProxy通过延迟加载的方式把请求发送到Spring中的Filter
#### FilterChainProxy
FilterChainProxy是Spring Security提供的一个特殊的过滤器，它允许通过SecurityFilterChain委托给许多过滤器实例，FilterChainProxy封装在DelegatingFilterProxy中
#### SecurityFilterChain
SecurityFilterChain中包含多个通过FilterChainProxy注册的Spring Security Filter，这些Filter通常是bean。FilterChainProxy中可以包含多个SecurityFilterChain，FilterChainProxy决定哪个SecurityFilterChain处理请求。
#### Security Filters
Security Filters负责具体的过滤行为，包括CorsFilter、CsrfFilter、UsernamePasswordAuthenticationFilter等, Security Filters被FilterChainProxy插入进SecurityFilterChain
#### Handling Security Exceptions
ExceptionTranslationFilter是Security Filters之一
### 2.Authentication
### 3.Authorization
## Reactive Applications

