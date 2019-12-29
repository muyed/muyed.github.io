---
title: elasticsearch-rest-client 6.3.2版本jar包冲突解决
date: 2019-12-28 22:34:29
tags: [elasticsearch,java]
---

### 问题复现

Maven 依赖：

```java
        <dependency>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>elasticsearch-rest-high-level-client</artifactId>
            <version>6.3.2</version>
        </dependency>
```

对es操作时出现异常：

```java
java.lang.NoSuchMethodError: org.elasticsearch.client.Request.<init>(Ljava/lang/String;Ljava/lang/String;)V
```

### 问题定位及分析

通常出现`java.lang.NoSuchMethodError` 异常说明项目中出现了jar包冲突，项目运行时调用了版本A中的某个类的方法, 但实际上classLoader加载的是版本B的类文件，而版本B中并没有实际调用的方法签名，导致异常出现。

从爆出的异常可以知道是`org.elasticsearch.client.Reques`这个类出现了冲突 ,通过用idea全局查找该类发了了有如下两个类全名完全相同的类存在于`elasticsearch-rest-client:6.4.3`和`elasticsearch-rest-high-level-client:6.3.2`这两个不同的jar包内

{% asset_img error.png %}



通过maven查看依赖数查看这两个包的依赖关系：

{% asset_img maven-tree.png %}

可以看到`elasticsearch-rest-high-level-client:6.3.2`依赖于`elasticsearch-rest-client:6.4.3`。

问题已经显而易见了` elasticsearch-rest-high-level-client:6.3.2`依赖于`elasticsearch-rest-client:6.4.3`,而这两个包都有`org.elasticsearch.client.Reques`这个类，导致调用出现异常。

### 问题解决

出现jar包冲突，通常的解决方案就是将版本不一致导致冲突的包排除，引入版本一致的依赖，具体到本次问题，将`elasticsearch-rest-high-level-client`的依赖包修为为：

```java
        <dependency>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>elasticsearch-rest-high-level-client</artifactId>
            <version>6.3.2</version>
            <exclusions>
                <exclusion>
                    <groupId>org.elasticsearch.client</groupId>
                    <artifactId>elasticsearch-rest-client</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>elasticsearch-rest-client</artifactId>
            <version>6.3.2</version>
        </dependency>
```

再次启动程序操作es，程序运行正常，对es操作成功!
