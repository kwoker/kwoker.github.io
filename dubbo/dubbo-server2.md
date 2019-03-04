# 二.Dubbo环境搭建 #
 
## 2.1 Dubbo的背景  ##
生产者主要发布服务
引入依赖
  ```xml
  <dependencies>
      <!-- public -->
      <dependency>
          <groupId>com.itmayiedu</groupId>
          <artifactId>itmayiedu-interface</artifactId>
          <version>0.0.1-SNAPSHOT</version>
      </dependency>
      <dependency>
          <groupId>com.alibaba</groupId>
          <artifactId>dubbo</artifactId>
          <version>2.5.6</version>
      </dependency>
      <!-- 添加zk客户端依赖 -->
      <dependency>
          <groupId>com.github.sgroschupf</groupId>
          <artifactId>zkclient</artifactId>
          <version>0.1</version>
      </dependency>
  </dependencies>
  ```
  	
  	
  	
## 2.2、定义一个Service ##
 
  ```java
  public interface UserService {
    public String getList(Integer id);
  }
  ```
  
### 2.2.1 并且实现Service ###

  ```java
  public class UserServiceImpl implements UserService {
   	public String getList(Integer id) {
   		System.out.println("客户端有人来消费了....");
   		if (id==1) {
   			return "余胜军";
   		}
   		if (id==2) {
   			return "扎克伯格";
   		}
   		if (id==3) {
   			return "马化腾";
   		}
   		return "没有找到";
   	}	
  }
  ```
### 2.2.2 发布服务 ###
新建配置文件provider.xml

  ```
   <?xml version="1.0" encoding="utf-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans           http://www.springframework.org/schema/beans/spring-beans.xsd           http://code.alibabatech.com/schema/dubbo           http://code.alibabatech.com/schema/dubbo/dubbo.xsd ">
       <!-- 提供方应用信息，用于计算依赖关系 -->
       <dubbo:application name="provider" />
       <!-- 使用zookeeper注册中心暴露服务地址 -->
       <dubbo:registry address="zookeeper://127.0.0.1:2181" />
       <!-- 用dubbo协议在29014端口暴露服务 -->
       <dubbo:protocol name="dubbo" port="29014" />
       <!-- 声明需要暴露的服务接口 -->
       <dubbo:service interface="com.itmayiedu.service.UserService"
           ref="orderService" />
       <!-- 具体的实现bean -->
       <bean id="orderService" class="com.itmayiedu.service.impl.UserServiceImpl" />
   </beans>
  ```
 启动代码
 
  ```java
   public class TestMember {
    	public static void main(String[] args) throws IOException {
    		// 发布服务
    		ClassPathXmlApplicationContext app = new ClassPathXmlApplicationContext("provider.xml");
    		app.start();// 加载
    		System.out.println("服务发布成功...");
    		System.in.read(); // 让程序阻塞
    	}
   }
  ```
### 2.2.3 消费者 ###
  ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
     	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
     	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">
     	<!-- 消费方应用名，用于计算依赖关系，不是匹配条件，不要与提供方一样 -->
     	<dubbo:application name="consumer" />
     	<!-- 使用multicast广播注册中心暴露发现服务地址 -->
     	<dubbo:registry protocol="zookeeper" address="zookeeper://127.0.0.1:2181" />
     	<!-- 生成远程服务代理，可以和本地bean一样使用demoService -->
     	<dubbo:reference id="userService" interface="com.itmayiedu.service.UserService" />
    </beans>
  ```
  ```java
    ClassPathXmlApplicationContext app = new ClassPathXmlApplicationContext("consumer.xml");
         		UserService userService = (UserService) app.getBean("userService");
         		String name = userService.getList(1);
         		System.out.println("name:" + name);
  ```
  
### 2.3 Dubbo支持哪些协议?
#### 2.3.1 Dubbo协议
   Dubbo缺省协议采用单一长连接和NIO异步通讯，适合于小数据量大并发的服务调用，以及服务消费者机器数远大于服务提供者机器数的情况。Dubbo缺省协议不适合传送大数据量的服务，比如传文件，传视频等，除非请求量很低。
#### 2.3.2 Hessian协议
   - Hessian协议用于集成Hessian的服务，Hessian底层采用Http通讯，采用Servlet暴露服务，Dubbo缺省内嵌Jetty作为服务器实现。Hessian是Caucho开源的一个RPC框架：http://hessian.caucho.com，其通讯效率高于WebService和Java自带的序列化。
   
    - 基于Hessian的远程调用协议:
  
       - 连接个数：多连接
       - 连接方式：短连接
       - 传输协议：HTTP
       - 传输方式：同步传输
       - 序列化：Hessian二进制序列化
       - 适用范围：传入传出参数数据包较大，提供者比消费者个数多，提供者压力较大，可传文件。
       - 适用场景：页面传输，文件传输，或与原生hessian服务互操作
   
#### 2.3.3 HTTP协议
 
   - 此协议采用spring 的HttpInvoker的功能实现，
     - 连接个数：多个
     - 连接方式：长连接
     - 连接协议：http
     - 传输方式：同步传输
     - 序列化：表单序列化
     - 适用范围：传入传出参数数据包大小混合，提供者比消费者个数多，可用浏览器查看，可用表单或URL传入参数，暂不支持传文件。
     - 适用场景：需同时给应用程序和浏览器JS使用的服务。

#### 2.3.3 HTTP协议
   - 采用JDK标准的java.rmi.*实现，采用阻塞式短连接和JDK标准序列化方式
   - Java标准的远程调用协议：
     - 连接个数：多连接
     - 连接方式：短连接
     - 传输协议：TCP
     - 传输方式：同步传输
     - 序列化：Java标准二进制序列化
     - 适用范围：传入传出参数数据包大小混合，消费者与提供者个数差不多，可传文件。
     - 适用场景：常规远程服务方法调用，与原生RMI服务互操作


