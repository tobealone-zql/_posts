---

title: 8-13-SentinelAPI详解
date: 
description: # 描述
tags: 
  - Spring Cloud alibaba 基础学习
---

暂无说明

<!-- more -->

## 1. 测试Spu

### 1.1 关闭Sentinel对微服务的保护，以便测试

![image-20210324230522026](8-13-SentinelAPI详解/image-20210324230522026.png)



### 1.2 建立TestController

![image-20210324230703795](8-13-SentinelAPI详解/image-20210324230703795.png)

### 1.3. 添加请求流控

![image-20210324230842069](8-13-SentinelAPI详解/image-20210324230842069.png)

![image-20210324230906065](8-13-SentinelAPI详解/image-20210324230906065.png)

### 1.4. 查看测试结果

#### 1.4.1 多次请求http://127.0.0.1:9010/test/testSentinelApi?id=1

#### 1.4.2 查看结果

![image-20210324231006528](8-13-SentinelAPI详解/image-20210324231006528.png)

## 2. 测试Tracer

### 2.1 添加IllegalAccessException异常

![image-20210324231921868](8-13-SentinelAPI详解/image-20210324231921868.png)

### 2.2 测试

#### 2.2.1 访问

````
http://127.0.0.1:9010/test/testSentinelApi
````

### 2.2.2 查看结果

![image-20210324232550452](8-13-SentinelAPI详解/image-20210324232550452.png)

### 2.3 添加降级规则

![image-20210324232114226](8-13-SentinelAPI详解/image-20210324232114226.png)

![image-20210324232237219](8-13-SentinelAPI详解/image-20210324232237219.png)

### 2.4 频繁访问

````
http://127.0.0.1:9010/test/testSentinelApi?id=1
````

### 2.5 查看结果

![image-20210324232316046](8-13-SentinelAPI详解/image-20210324232316046.png)

## 3. 测试CententUntil

### 3.1 指定来源

![image-20210324233420834](8-13-SentinelAPI详解/image-20210324233420834.png)

### 3.2 添加流控规则

![image-20210324233822768](8-13-SentinelAPI详解/image-20210324233822768.png)

### 3.3 测试

![image-20210324233843320](8-13-SentinelAPI详解/image-20210324233843320.png)

### 3.4 尝试指定其他来源

![image-20210324233912655](8-13-SentinelAPI详解/image-20210324233912655.png)

### 3.5 测试，未被限流

![ ](8-13-SentinelAPI详解/image-20210324233930647.png)

## ## 4总结Sentinel API

#### 4.1  API类型

![image-20210324234137385](8-13-SentinelAPI详解/image-20210324234137385.png)

### 4.2 Sphu作用

定义监控资源，保护资源

### 4.2 Tracer 作用

对指定的异常进行统计

### 4.3 ContextUtil 作用

指定来源，标记调用