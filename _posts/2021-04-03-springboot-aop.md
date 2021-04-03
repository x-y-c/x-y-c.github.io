---
title: springboot aop
tags: [后端,springboot]
---

# springboot aop

最近又重新学习了springboot，解锁了很多好玩的东西，比如aop。

# aop需要准备的pom

如果想要使用aop做切面的编程，首先要保证是springboot的项目，然后在项目的pom文件中，需要添加

```java
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
            <version>2.4.2</version>
        </dependency>
```

对于版本，可以在maven官网上找到使用最多的版本，进行配置。

# 面向切面编程的具体操作

## 1、切点

### 1.1 切点的定义

切点的作用，就是用来描述哪些方法需要用到aop编程。比如 xxx目录下的xxx类的xxx方法，要对它进行面向切面编程。所以，对切点的选定是第一步。

### 1.2 切点的定位描述

在代码中，通过`@Pointcut()`注解来定义切点的位置。

即(例如)：

```
"execution(* com.example.demo.controller..*.*(..))"
```

- 第一个*号：用来描述切点的返回值类型， *就代表任意类型

- `com.example.demo.controller` 用来描述切点的包名，即controller包下的

- ..  跟在controller后边的..用来表示当前包和子包

- 第二个*号： 用来描述类名， *号表示这个包下的所有类

- 第三个.* :用来描述类中的方法名，. 即代表该类中的    *即代表任意方法名，所以，连在一起就是该类下的任意方法名。

- （..）表示方法中的参数类型，..表示任意的参数类型

  *这样，如果遇见同方法名，但是参数不同的情况，通过（..）就可以进行定位*

### 1.3 切面的类型

在选好切点以后，需要根据想添加的功能，进行切面的选择

#### @Before：前置通知。即在方法前要执行的内容。例如，现有一个方法叫做 login(String username,String password);

那么前置通知(前置方法)可以在执行login方法之前，先执行@Before的代码，在执行完@Before的代码后，再继续执行login方法。

通过

```java
 RequestAttributes requestAttributes = RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = (HttpServletRequest) requestAttributes.resolveReference(RequestAttributes.REFERENCE_REQUEST);
        Enumeration<String> enumeration = request.getParameterNames();
```

可以获取到用户传入的参数

所以，通过前置通知，可以对参数进行验证，或者打印日志，或者统计访问量等。（甚至可以修改前端传来的参数。。。）

代码示例：

```
@Component
@Aspect
public class aop {
    @Pointcut("execution(* com.example.demo.controller..* (..))")
    public void pointcut() {}//描述切点

    @Before("pointcut()")//定义切面类型，并且定位切点
    public void beforetest(JoinPoint joinPoint) { //joinpoint 即拦截到的参数，但是由于是对controller的切面，所以，这一层的参数实际在request中；
        Object[] args = joinPoint.getArgs();
        System.out.println("before!!!!!");
        System.out.println(args);
        RequestAttributes requestAttributes = RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = (HttpServletRequest) requestAttributes.resolveReference(RequestAttributes.REFERENCE_REQUEST);
        Enumeration<String> enumeration = request.getParameterNames();//这里获取到参数的key，value
        while (enumeration.hasMoreElements()) {
            String parameter = enumeration.nextElement();
            System.out.println(parameter);
            System.out.println(request.getParameter(parameter));
            //这里只是简单的对参数进行了打印，但是实际可以做更复杂的操作；
        }
    }
    //这里，完成了前置方法，接下来，代码会回到原来的方法中继续执行；
```

```
before!!!!!
[Ljava.lang.Object;@30ca16ec
aaaa
1111111

```

通过返回结果，可以看到，通过request拿到了所有的参数；

####  @After 后置通知(后置方法)

和前置方法相似，在原方法执行结束后，会执行后置方法；

#### @Around 环绕通知（环绕方法）

包围一个连接点的通知，如方法调用等。这是最强大的一种通知类型。环绕通知可以在方法调用前后完成自定义的行为，它也会选择是否继续执行连接点或者直接返回它自己的返回值或抛出异常来结束执行。

*环绕通知最强大，也最麻烦，是一个对方法的环绕，具体方法会通过代理传递到切面中去，切面中可选择执行方法与否，执行几次方法等。环绕通知使用一个代理ProceedingJoinPoint类型的对象来管理目标对象，所以此通知的第一个参数必须是ProceedingJoinPoint类型。在通知体内调用ProceedingJoinPoint的proceed()方法会导致后台的连接点方法执行。*

即：环绕通知，可以在原有方法执行前和执行后，各做一些自定义的操作。

```
  @Around("pointcut()")
    public Object around(ProceedingJoinPoint pjp) throws Throwable {
        //这是在原有的代码执行前,先休眠1秒钟：
        long before = System.currentTimeMillis();
        Thread.sleep(1000);

        //开始执行原有代码
        Object proceed = pjp.proceed();
    //  proceed是获取到的执行结果，现在对执行结果中，加上执行时间；
        long after = System.currentTimeMillis();
        String result = (String) proceed;
        result = result +"-----执行时间为："+ (after - before) + "";
        return result;
    }
```

再来看看原有的代码：

```
@RestController
@RequestMapping("/test")
public class TestController {
    @GetMapping("/run")
    public String test1(String aaaa) {
        String s = "this is test1";
        return s;
    }
}
//正常的返回，应该为 this is test1
```

但是在通过环绕通知后，返回的结果为：

` this is test1-----执行时间为：1047` 

在环绕方法中，最重要的是  Object proceed = pjp.proceed()；

这行代码的目的是让原有代码执行，如果不调用这行代码，那么，原有方法不会执行。

**以上就是使用aop的过程了**

1. 首先新建一个切面的代码文件，并在class上使用 @Component  @Aspect注解
2. 然后，确定切点，编写切点的描述表达式；
3. 确定切面的类型，并编写相关代码。