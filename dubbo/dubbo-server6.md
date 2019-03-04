## 什么是Dubbo？
 ```
   Duubbo是一个RPC远程调用框架， 分布式服务治理框架
   什么是Dubbo服务治理？
   服务与服务之间会有很多个Url、依赖关系、负载均衡、容错、自动注册服务。 
 ```
 
## Dubbo有哪些协议？
 ```
   默认用的dubbo协议、Http、RMI、Hessian。
 ```
 
## Dubbo整个架构流程
```
  分为四大模块
  生产者、消费者、注册中心、监控中心
  生产者：提供服务
  消费者: 调用服务
  注册中心:注册信息(redis、zk)
  监控中心:调用次数、关系依赖等。
  首先生产者将服务注册到注册中心（zk），使用zk持久节点进行存储，消费订阅zk节点，一旦有节点变更，zk通过事件通知传递给消费者，消费可以调用生产者服务。
  服务与服务之间进行调用，都会在监控中心中，存储一个记录。
```

## Dubbox与Dubbo区别？
```
  Dubox使用http协议+rest风格传入json或者xml格式进行远程调用。
  Dubbo使用Dubbo协议。
```

## RPC远程调用框架
``` 
  SpringCloud、dubbo、Dubbox、thint、Hessian…
  Rpc其实就是远程调用，服务与服务之间相互进行通讯。
  目前主流 用http+json
```

## SpringCloud与Dubbo区别？
 - 相同点:
    - dubbo与springcloud都可以实现RPC远程调用。
    - dubbo与springcloud都可以使用分布式、微服务场景下。
 - 区别:
    - dubbo有比较强的背景,在国内有一定影响力。
    - dubbo使用zk或redis作为作为注册中心。
    - springcloud使用eureka作为注册中心。
    - dubbo支持多种协议，默认使用dubbo协议。
    - Springcloud只能支持http协议。
    - Springcloud是一套完整的微服务解决方案。
    - Dubbo目前已经停止更新,SpringCloud更新速度快。
