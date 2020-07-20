---
title: 使用spring-data-JPA分页
date: 2020-05-16 00:00:00 Z
tags:
- 后端
layout: post
---

# 使用Spring-data-JPA查询数据并分页

## 1、使用JPA 自带的查询接口实现分页

如果查询的数据是一张数据表中的数据，那么可以使用JPA自带的查询接口进行查询，例如 有一个表结构为：user

| id   | name  | gender |
| ---- | ----- | ------ |
| 001  | tom   | male   |
| 002  | jerry | male   |
| 003  | Mary  | female |

如果查询这张用户表中数据，并且返回分页的形式

```java
Page<User>findbyId(Pageable pageable,Integer id);
```

## 2、使用@Query方式，编写SQL语句进行查询并分页

如果需要查询的数据，是两张以上的表，并且需要返回分页的形式，这时候，JPA自带的分页方式就变得不好用了，这时候比较好的查询方式是编写SQL语句，用@Query注解

例如在user表的基础上，有一张成绩表，score



| id   | score |
| ---- | ----- |
| 001  | 100   |
| 002  | 50    |
| 003  | 70    |

如果需要查询姓名和成绩并且以分页的方式返回

用Query方法查询的方式如下：

```sql
@Query(value="select  t1.name" +
            "student_grade,password from user t1  join score t2 on t1.id= t2.id",
            countQuery="select count(*) from user t1  join score t2 on t1.id= t2.id ",nativeQuery = true)
```

以上需要注意的地方是，这个查询有两个部分，首先是带join的SQL联表查询，此外，需要注意的是，必须要添加 countQuery =“ ”，在其中编写count 的SQL语句，count语句的目的，是为了让分页组件能够知道数据的总数，这样，才能够实现分页的效果



#### 以下为注意事项

1、为了保证SQL的正确查询，需要给查询的表添加一个别名，在没有别名的情况下，有时候会报错

2、countQuery是必须带的一个参数，不然会报SQL查询失败的错误

3、至于为什么要加countQuery，除了解释为查询数据总数为了分页以外，没有找到一个较为官方的解释，查阅了好多资料，几乎没有发现好的解答，直到看到Stack Overflow上的一条回复

> You can use pagination with a native query. It is documented here: https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#_native_queries
>
> "*You can however use native queries for pagination by specifying the count query yourself: Example 59. Declare native count queries for pagination at the query method using @Query*"

结合这个解释和分页的逻辑分析，基本可以解释这个问题。

4、此外，@Query查询和@countQuery查询，必须是相同的查询条件，或者可以解释为 @countQuery到的总数，必须和Query到的数据数量相等。

5、通过@Query编写的SQL语句，字段的顺序要和Java中对象的变量的顺序相同，这样才能保证ORM后的结果匹配。

