---
layout: post
title: 'springboot使用外部配置文件'
date: '2020-05-31'
author: 'yc'
tags: 后端
---

# springboot使用外部配置文件

## 问题需求

随着服务器增多，每次更换服务器或者更换数据库等，都会发生ip地址的改变，导致springboot的配置文件中保存的数据库地址，图片服务器地址失效，不得不重新打包，导致部署过程繁琐。

## 解决方案

springboot项目中指定了多个配置文件，通过`spring.profiles.active=dev`来指定配置文件。所以，使用外部同名配置文件来替换项目中的默认配置文件，就可以只重启服务完成配置信息的更改，而不用重新打包项目。

## 实际操作

通过查阅资料，发现springboot配置文件的优先级。

1、最高优先级是 项目.jar 的同一个文件夹中的config中的同名配置文件，如上述的active=dev，则配置文件的名称应为：application-dev.properties ，如果存在该配置文件，则优先加载该配置文件。

2、优先级第二高的是 项目.jar 的同一文件夹中的配置文件，如上述的active=dev，则配置文件的名称为：application-dev.properties，

3、优先级第三高的是 项目中自带的配置文件。

**配置文件的优先级，从第一到第三级，加载，如果config中存在同名配置文件，则会加载该配置文件，如果最高优先级和第二优先级都不存在的情况下，则加载默认的配置文件。**

## 测试及说明

1. 首先，在项目中的application-dev.properties中添加 test=dev，在项目中编写一个测试接口，返回test的值。

2. 将项目打包后，放在 d盘的data目录中。

3. 启动项目，并访问测试接口 ，可以看到 输出的结果为 dev

4. 在同级目录下，新建一个application-dev.properties，将test=dev11223344,重启项目，测试接口，可以看到 输出的结果为 dev11223344

   **可以看到第二优先级的配置文件将原始的配置文件覆盖了**

5. 同理，在该文件夹中新建一个config文件夹，在config文件夹中，我们新建一个同名配置文件，并将test=devINconfig，重启项目，并测试接口，可以看到输出的结果为devINconfig。和预期结果相同。





