### 主要技术

- 后端：Spring、SpringMVC、SpringData、SpringCloud、SpringBoot
- 数据库：Mysql、Mongodb
- 其他：redis、kafka、阿里云 OSS、腾讯防水校验、环信推送
- 前端：Vue、iView、less

---

# 整体架构

#### ![Topology-Image](/Topology-Image/143836_eac248e5_2182501.png)

# 逻辑架构

#### ![Topology-Image](/Topology-Image/143856_66257325_2182501.png)

# 部署架构

#### ![Topology-Image](/Topology-Image/141710_07923003_2182501.png)

# 依赖关系

#### ![Topology-Image](/Topology-Image/194510_89803a9d_2182501.png)

## 模块介绍

1. cloud

- 提供 SpringCloud 微服务注册中心功能，为基础模块，必须部署
- 依赖服务：无

2. ucenter-api

- 提供用户相关的接口（如登录、注册、资产列表）,该模块为基础为基础模块，必须部署
- 依赖服务：mysql,kafka,redis,mongodb,短信接口，邮箱账号

3. otc-api

- 提供场外交易功能接口，没有场外交易的可以不部署
- 依赖服务：mysql,redis,mongodb,短信接口

4. exchange-api

- 提供币币交易接口，没有币币交易的项目可以不部署
- 依赖服务：mysql,redis,mongodb,kafka

5. chat

- 提供实时通讯接口，基础模块，需要部署
- 依赖服务：mysql,redis,mongodb

6. admin

- 提供管理后台的所有服务接口，必须部署
- 依赖服务：mysql,redis,mongodb

7. wallet

- 提供充币、提币、获取地址等钱包服务，为基础模块，必须部署
- 依赖服务：mysql,mongodb,kafka,cloud

8. market

- 提供币种价格、k 线、实时成交等接口服务，场外交易不需要部署
- 依赖服务：mysql,redis,mongodb,kafka,cloud

9. exchange

- 提供撮合交易服务，场外交易不需要部署
- 依赖服务：mysql,mongodb,kafka

## 重点业务介绍

    后端框架的核心模块为 exchange,market模块。

    其中exhcnge模块完全采用Java内存处理队列,大大加快处理逻辑,中间不牵涉数据库操作,保证处理速度快,其中项目启动后采用继承ApplicationListener方式，自动运行；

    启动后自动加载未处理的订单,重新加载到JVM中，从而保证数据的准确，exchange将订单处理后，将成交记录发送到market;

    market模块主要都是数据库操作，将用户变化信息持久化到数据库中。主要难点在于和前端交互socket推送，socket推送采用两种方式，web端socket采用SpringSocket，移动端采用Netty推送,其中netty推送通过定时任务处理。

## 环境搭建

- Centos 6.8
- MySQL 5.5.16
- Redis-x64-3.2.100
- Mongodb 3.6.13
- kafka_2.11-2.2.1
- nginx-1.16.0
- JRE 8u241
- JDK 1.8
- Vue
- Zookeeper

## 服务部署准备

1. 项目用了 Lombok 插件，无论用什么 IDE 工具，请务必先安装 Lombok 插件
2. 项目用了 QueryDsl，如果遇见以 Q 开头的类找不到，请先编译一下对应的 core 模块，例如 core、exchange-core、xxx-core 这种模块
3. 找不到的 jar 包在项目 jar 文件夹下
4. jdk 版本 1.8 以上
5. 初始化 sql 在 sql 文件夹中配置文件
   配置文件打开这个设置会自动建表
   #jpa
   spring.jpa.hibernate.ddl-auto=update

## 修改服务配置文件

请根据服务实际部署情况修改以下配置。配置文件位置如下，如果配置文件中没有某一项配置，说明该模块未使用到该项功能，无需添加：

```
各个模块/src/main/resources/dev/application.properties
```

mysql 数据库:

```
spring.datasource.**
```

reids

```
redis.**
```

mongodb(主要存储 K 线图相关数据)

```
spring.data.mongodb.uri
```

kafka

```
spring.kafka.bootstrap-servers
```

阿里云 OSS，图片资源上传

```
aliyun.**
```

短信配置

```
sms.**
```

邮件认证

```
spring.mail.**
```

腾讯防水校验

```
water.proof.app.**
```

### 服务启动

1.  maven 构建打包服务

2.  将各个模块 target 文件夹下的 XX.jar 上传到自己的服务器

3.  先启动 cloud 模块，再启动 market，exchange 模块，剩下的没有顺序。

4.  启动服务

    例：

    ```
    nohup  java  -jar  /自己的jar包路径/cloud.jar  >/dev/null 2>&1 &
    ```

    ```
    nohup  java  -jar  /web/app/cloud.jar  >/dev/null 2>&1 &
    nohup  java  -jar  /web/app/exchange.jar  >/dev/null 2>&1 &
    nohup  java  -jar  /web/app/market.jar  >/dev/null 2>&1 &
    nohup  java  -jar  /web/app/exchange-api.jar  >/dev/null 2>&1 &
    nohup  java  -jar  /web/app/ucenter-api.jar  >/dev/null 2>&1 &
    nohup  java  -jar  /web/app/otc-api.jar  >/dev/null 2>&1 &
    nohup  java  -jar  /web/app/chat.jar  >/dev/null 2>&1 &
    nohup  java  -jar  /web/app/wallet.jar  >/dev/null 2>&1 &
    nohup  java  -jar  /web/app/admin.jar  >/dev/null 2>&1 &
    ```

## 商业版演示图

#### ![Topology-Image](/Topology-Image/01.png)

#### ![Topology-Image](/Topology-Image/02.png)

#### ![Topology-Image](/Topology-Image/03.png)

#### ![Topology-Image](/Topology-Image/04.png)

#### ![Topology-Image](/Topology-Image/05.png)

#### ![Topology-Image](/Topology-Image/06.png)

#### ![Topology-Image](/Topology-Image/07.png)
