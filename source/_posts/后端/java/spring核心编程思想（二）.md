---
title: Spring 核心编程思想（三）：Spring IoC 容器概述
tags:
  - spring
index_img: https://s3.bmp.ovh/imgs/2023/05/06/debcba54d0f154b7.jpg
# excerpt: spring
categories:
  - 后端
  - spring 核心编程思想
---

## 1. Spring IoC 容器概述

| 内容                    |
| ----------------------- |
| Spring IoC 依赖查找     |
| Spring IoC 依赖注入     |
| Spring IoC 依赖来源     |
| Spring IoC 配置元信息   |
| Spring IoC 容器         |
| Spring 应用上下文       |
| 使用 Spring IoC 容器    |
| Spring IoC 容器生命周期 |
| 面试题精选              |

## 2. Spring IoC 依赖查找

1. 根据Bean名称查找

   * 实时查找

   * 延迟查找

2. 根据Bean类型查找
   * 单个 Bean 对象
   * 集合 Bean 对象

3. 根据 Bean 名称 + 类型查找
   * 根据 Java 注解查找
   * 单个 Bean 对象
   * 集合 Bean 对象
