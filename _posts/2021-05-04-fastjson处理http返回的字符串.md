---
title: fastjson处理http返回的字符串
tag: 后端
---

写java的项目，经常会接触到返回的数据是序列化的字符串的情况，例如通过sql查询数据库的部分字段（好吧，数据库返回的是个对象或者map），或者调用一个api接口，有时并不像把这些返回的数据封装成一个对象。这时候，用fastjson处理还挺优雅的。

## fastjson

### 1、首先要引入fastjson库

```java
<!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.75</version>
</dependency>
```

### 2、几种常见的用法

```javascript
{
    "code": "200",
    "updateTime": "2021-05-04T21:37+08:00",
    "fxLink": "http://hfx.link/2ax1",
    "now": {
        "obsTime": "2021-05-04T21:23+08:00",
        "temp": "16",
        "feelsLike": "12",
        "icon": "150",
        "text": "晴",
        "wind360": "270",
        "windDir": "西风",
        "windScale": "3",
        "windSpeed": "17",
        "humidity": "18",
        "precip": "0.0",
        "pressure": "1009",
        "vis": "30",
        "cloud": "0",
        "dew": "-13"
    },
    "refer": {
        "sources": [
            "Weather China"
        ],
        "license": [
            "no commercial use"
        ]
    }
}
```

以上这段是调用天气预报接口获得的数据，而且结构还较为复杂，如果转成java类的话，会有多个类嵌套；这种场景下，fastjson是一个比较好的解决方案。例如我们想要取到text的值。

首先，先将字符串转成JSONObject对象。

`JSONObject object = JSON.parseObject(str);`

![屏幕截图_81_.png](https://i.loli.net/2021/05/04/c8ZSGldxfkqOvDu.png)

```

```

可以看到，在转成了jsonobject之后，就有了对应的get方法，我们可以根据对象的类型进行get操作；如果对应的字段是int类型，则可以通过getInteger()方法来获取；

通过分析，可以知道，

```
 "now": {
        "obsTime": "2021-05-04T21:23+08:00",
        "temp": "16",
        "feelsLike": "12",
        "icon": "150",
        "text": "晴",
        "wind360": "270",
        "windDir": "西风",
        "windScale": "3",
        "windSpeed": "17",
        "humidity": "18",
        "precip": "0.0",
        "pressure": "1009",
        "vis": "30",
        "cloud": "0",
        "dew": "-13"
    },
```

text(天气详情字段)出现在now包含的对象中，所以，我们可以再进行一次getObject操作，把now当做一个jsonobject对象来处理，这样，通过get("text")关键字，就可以取到对应的信息了。



以上就是个小例子来表述fastjson的用法了，以前没有get到这个方法，总是感觉每处理一个json字符串都要新建一个java对象太难受了，通过fastjson可以省去新建对象这个过程了，感觉代码的编写会更简洁一些，可读性也还ok，所以记录一下。