**什么是Dubbox?**

**Dubbox环境搭建**

## 生产者环境搭建 ##

**定义一个接口**
  ```java
    @Path("/user")
    public interface UserService {
     	@GET
     	@Path("/getUser/{id : \\d+}")
     	@Consumes({ MediaType.APPLICATION_JSON })
     	public String getUser(@PathParam("id") Integer id);
    }
  ```
**配置文件**
  ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
     	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
     	xsi:schemaLocation="http://www.springframework.org/schema/beans           http://www.springframework.org/schema/beans/spring-beans.xsd           http://code.alibabatech.com/schema/dubbo           http://code.alibabatech.com/schema/dubbo/dubbo.xsd ">
     	<!-- 提供方应用信息，用于计算依赖关系 -->
     	<dubbo:application name="dubbox-provider" />
     	<!-- 使用zookeeper注册中心暴露服务地址 -->
     	<dubbo:registry address="zookeeper://127.0.0.1:2181" />
     	<!-- 用rest协议在8080端口暴露服务 -->
     	<dubbo:protocol name="rest" port="8081" />
     	<!-- 声明需要暴露的服务接口 -->
     	<dubbo:service interface="com.itmayiedu.service.UserService" ref="userService" />
     	<!-- 和本地bean一样实现服务 -->
     	<bean id="userService" class="com.itmayiedu.service.impl.UserServiceImpl" />
    </beans>
 
```
**测试访问地址：**
http://127.0.0.1:8081/user/getUser/2

## 消费者环境搭建 ##
**Java代码**
  ```java
    @Path("/user")
    public interface UserService {
     	@GET
     	@Path("/getUser/{id : \\d+}")
     	@Consumes({ MediaType.APPLICATION_JSON })
     	public String getUser(@PathParam("id") Integer id);
    }
```
**dubbo-consumer.xml**
  ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
     	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
     	xmlns:util="http://www.springframework.org/schema/util"
     	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
     	http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd
     	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-3.0.xsd">
        <!-- 提供方应用信息，用于计算依赖关系 -->
     	<dubbo:application name="consumer"  />
     	<dubbo:registry address="zookeeper://127.0.0.1:2181" />
     	<!-- 生成远程服务代理，可以像使用本地bean -->
     	<dubbo:reference interface="com.itmayiedu.service.UserService"
     		id="userService" check="false" />
    </beans>
```
**Doubbox依赖**
  ```xml
    <dependencies>
     		<dependency>
     			<groupId>com.alibaba</groupId>
     			<artifactId>dubbo</artifactId>
     			<version>2.8.4</version>
     		</dependency>
     		<!-- 添加zk客户端依赖 -->
     		<dependency>
     			<groupId>com.github.sgroschupf</groupId>
     			<artifactId>zkclient</artifactId>
     			<version>0.1</version>
     		</dependency>
     		<dependency>
     			<groupId>com.itmayiedu</groupId>
     			<artifactId>itmayiedu-dubbo-service</artifactId>
     			<version>0.0.1-SNAPSHOT</version>
     		</dependency>
     		<dependency>
     			<groupId>org.jboss.resteasy</groupId>
     			<artifactId>resteasy-jaxrs</artifactId>
     			<version>3.0.7.Final</version>
     		</dependency>
     		<dependency>
     			<groupId>org.jboss.resteasy</groupId>
     			<artifactId>resteasy-client</artifactId>
     			<version>3.0.7.Final</version>
     		</dependency>
     		<dependency>
     			<groupId>javax.validation</groupId>
     			<artifactId>validation-api</artifactId>
     			<version>1.0.0.GA</version>
     		</dependency>
     		<!-- 如果要使用json序列化 -->
     		<dependency>
     			<groupId>org.jboss.resteasy</groupId>
     			<artifactId>resteasy-jackson-provider</artifactId>
     			<version>3.0.7.Final</version>
     		</dependency>
     		<!-- 如果要使用xml序列化 -->
     		<dependency>
     			<groupId>org.jboss.resteasy</groupId>
     			<artifactId>resteasy-jaxb-provider</artifactId>
     			<version>3.0.7.Final</version>
     		</dependency>
     		<!-- 如果要使用netty server -->
     		<dependency>
     			<groupId>org.jboss.resteasy</groupId>
     			<artifactId>resteasy-netty</artifactId>
     			<version>3.0.7.Final</version>
     		</dependency>
     		<!-- 如果要使用Sun HTTP server -->
     		<dependency>
     			<groupId>org.jboss.resteasy</groupId>
     			<artifactId>resteasy-jdk-http</artifactId>
     			<version>3.0.7.Final</version>
     		</dependency>
     		<!-- 如果要使用tomcat server -->
     		<dependency>
     			<groupId>org.apache.tomcat.embed</groupId>
     			<artifactId>tomcat-embed-core</artifactId>
     			<version>8.0.11</version>
     		</dependency>
     		<dependency>
     			<groupId>org.apache.tomcat.embed</groupId>
     			<artifactId>tomcat-embed-logging-juli</artifactId>
     			<version>8.0.11</version>
     		</dependency>
     		<dependency>
     			<groupId>com.esotericsoftware.kryo</groupId>
     			<artifactId>kryo</artifactId>
     			<version>2.24.0</version>
     		</dependency>
     		<dependency>
     			<groupId>de.javakaffee</groupId>
     			<artifactId>kryo-serializers</artifactId>
     			<version>0.26</version>
     		</dependency>
     		<dependency>
     			<groupId>de.ruedigermoeller</groupId>
     			<artifactId>fst</artifactId>
     			<version>1.55</version>
     		</dependency>
     		<dependency>
     			<groupId>com.fasterxml.jackson.core</groupId>
     			<artifactId>jackson-core</artifactId>
     			<version>2.3.3</version>
     		</dependency>
     		<dependency>
     			<groupId>org.mortbay.jetty</groupId>
     			<artifactId>jetty</artifactId>
     			<version>7.0.0.pre5</version>
     		</dependency>
     	</dependencies>
```

