---

title: 7-01-使用Feign实现远程HTTP调用
date:
description: # 描述
tags: 
  - Spring Cloud alibaba 基础学习
---

暂无说明

<!-- more -->

## 1. 项目结构

![image-20210308232116890](7-01-使用Feign实现远程HTTP调用 - 副本 (3)/image-20210308232116890.png)

## 2. content-center添加依赖

````
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.5.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>com.zql.user-center</groupId>
    <artifactId>content-center</artifactId>
    <version>1.0-SNAPSHOT</version>

    <name>content-center</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.1.5</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
            <scope>provided</scope>
        </dependency>
<!--        spring-boot-starter-xxxx-->
<!--        xxxx-spring-boot-starter-->
<!--        spring-cloud-starter-{spring cloud子项目的名称}-[{模块名称}]-->
<!--        feign spring-cloud-starter-openfeign-->
<!--        sentinel spring-cloud-starter-alibaba-sentinel-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
<!--        <dependency>-->
<!--            <groupId>org.apache.rocketmq</groupId>-->
<!--            <artifactId>rocketmq-spring-boot-starter</artifactId>-->
<!--            <version>2.0.3</version>-->
<!--        </dependency>-->
<!--        <dependency>-->
<!--            <groupId>org.springframework.cloud</groupId>-->
<!--            <artifactId>spring-cloud-starter-stream-rocketmq</artifactId>-->
<!--        </dependency>-->

<!--        <dependency>-->
<!--            <groupId>io.jsonwebtoken</groupId>-->
<!--            <artifactId>jjwt-api</artifactId>-->
<!--            <version>0.10.7</version>-->
<!--        </dependency>-->
<!--        <dependency>-->
<!--            <groupId>io.jsonwebtoken</groupId>-->
<!--            <artifactId>jjwt-impl</artifactId>-->
<!--            <version>0.10.7</version>-->
<!--            <scope>runtime</scope>-->
<!--        </dependency>-->
<!--        <dependency>-->
<!--            <groupId>io.jsonwebtoken</groupId>-->
<!--            <artifactId>jjwt-jackson</artifactId>-->
<!--            <version>0.10.7</version>-->
<!--            <scope>runtime</scope>-->
<!--        </dependency>-->

<!--        <dependency>-->
<!--            <groupId>com.github.binarywang</groupId>-->
<!--            <artifactId>weixin-java-miniapp</artifactId>-->
<!--            <version>3.5.0</version>-->
<!--        </dependency>-->

<!--        <dependency>-->
<!--            <groupId>org.springframework.boot</groupId>-->
<!--            <artifactId>spring-boot-starter-aop</artifactId>-->
<!--        </dependency>-->

<!--        <dependency>-->
<!--            <groupId>org.springframework.cloud</groupId>-->
<!--            <artifactId>spring-cloud-starter-zipkin</artifactId>-->
<!--        </dependency>-->

<!--        <dependency>-->
<!--            <groupId>org.springframework.boot</groupId>-->
<!--            <artifactId>spring-boot-starter-actuator</artifactId>-->
<!--        </dependency>-->
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <!--整合spring cloud-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--整合spring cloud alibaba-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>0.9.0.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.6</version>
                <configuration>
                    <configurationFile>
                        ${basedir}/src/main/resources/generator/generatorConfig.xml
                    </configurationFile>
                    <overwrite>true</overwrite>
                    <verbose>true</verbose>
                </configuration>
                <dependencies>
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>5.1.22</version>
                    </dependency>
                    <dependency>
                        <groupId>tk.mybatis</groupId>
                        <artifactId>mapper</artifactId>
                        <version>4.1.5</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>

</project>
````

## 3. 增加UserCenterFeignClient类

````
package com.zql.contentCenter.feignclient;


import com.zql.contentCenter.domain.dto.user.UserDTO;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@FeignClient(name = "user-center")
public interface UserCenterFeignClient {


    @GetMapping("/user/{id}")
    public UserDTO getUser(@PathVariable Integer id);

}

````

## 4. ShareService修改调用方式

````
package com.zql.contentCenter.service;


import com.zql.contentCenter.dao.content.ShareMapper;
import com.zql.contentCenter.domain.dto.content.ShareDTO;
import com.zql.contentCenter.domain.dto.user.UserDTO;
import com.zql.contentCenter.domain.entity.content.Share;
import com.zql.contentCenter.feignclient.UserCenterFeignClient;
import org.springframework.beans.BeanUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

import java.net.URI;
import java.util.List;
import java.util.Optional;
import java.util.concurrent.ThreadLocalRandom;
import java.util.stream.Collector;
import java.util.stream.Collectors;
import java.util.stream.Stream;

@Service
public class ShareService {

    @Autowired
    private ShareMapper shareMapper;


    @Autowired
    private UserCenterFeignClient userCenterFeignClient;

    public ShareDTO getById(Integer id) {


        Share share = shareMapper.selectByPrimaryKey(id);
        UserDTO userDTO = userCenterFeignClient.getUser(share.getUserId());

        ShareDTO shareDTO = new ShareDTO();

        BeanUtils.copyProperties(share,shareDTO);
        shareDTO.setUserDTO(userDTO);

        return shareDTO;


    }
}

````



## 4. 启动类加注解

````
package com.zql.contentCenter;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.cloud.openfeign.EnableFeignClients;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;
import tk.mybatis.spring.annotation.MapperScan;

@MapperScan("com.zql")
@SpringBootApplication
@EnableFeignClients
public class ContentCenterApplication {

    public static void main(String[] args) {

        SpringApplication.run(ContentCenterApplication.class, args);
    }

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {

        return new RestTemplate();
    }
}

````







