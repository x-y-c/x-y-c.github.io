---
title: springboot配置文件敏感信息加密
date: 2020-01-09 00:00:00 Z
tags:
- springboot
layout: post
---

2020了诶！

JXU1MThEJXU4OUMxMjAxOSV1RkYwQzIwMjAldThCRjcldTVCRjkldTYyMTEldTU5N0QldTRFMDAldTcwQjkldTUxM0Y=

# springboot配置文件敏感信息加密

#### 问题描述

1、在springboot中，数据库的地址，数据库用户名，数据库密码等，都放在springboot 配置文件中，如果以明文显示，会有一定的风险，经过查阅资料，jasypt可以对配置文件内容进行加密。

#### 基本操作

- 添加依赖 

```
<dependency>
			<groupId>com.github.ulisesbocchio</groupId>
			<artifactId>jasypt-spring-boot-starter</artifactId>
			<version>1.16</version>
		</dependency>

```



- 在application.properties中配置秘钥

  ```
  jasypt.encryptor.password: demo
  ```

- 通过测试用例算出需要加密的密文

  ```java
  import org.jasypt.encryption.StringEncryptor;
  import org.junit.Test;
  import org.junit.runner.RunWith;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.boot.test.context.SpringBootTest;
  import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
  
  @RunWith(SpringJUnit4ClassRunner.class)
  @SpringBootTest(classes = JasyptApplication.class)
  public class JasyptApplicationTests {
  
      @Autowired
      private StringEncryptor stringEncryptor;
  
  	@Test
  	public void contextLoads() {
          //加密方法
          System.out.println(stringEncryptor.encrypt("123456"));
          System.out.println(stringEncryptor.encrypt("123456"));
          //解密方法
          System.out.println(stringEncryptor.decrypt("uaNBj4ZmzCD83uedRYUXqQ=="));
          System.out.println(stringEncryptor.decrypt("oKBQENfbbQiMyPvECAgPGA=="));
  	}
  
  }
  
  ```

  经过测试可以发现，每次加密产生的密文都不相同，不一样的密文配合秘钥解码后的结果是一样的。密文只有对应的秘钥才能解析出明文

- 在application.properties文件中添加配置项，需要jasypt来解码的密文需要用“ENC(......)”括起来。

  ###### 这样就简单的完成了对配置文件中的敏感信息的加密。

  