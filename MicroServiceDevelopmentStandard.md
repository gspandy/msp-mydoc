#微服务开发规范

## 目录：

+ 创建微服务
	- [微服务命名](#service-name)
	- [微服务注册](#service-register)
+ 开发微服务
	- [项目配置（pom文件配置）](#project-config)
		- 依赖及仓库配置
		- groupId和artifactId命名
	- [服务配置](#service-config)
	- [API设计](#api-design)
	- [日志格式](#service-log)
+ [部署微服务](#service-deploy)

---

## 创建微服务
### <a name="service-name"> </a> 微服务命名

微服务名称（service-name）采用三段式的命名规则，中间使用中横线分隔，即`xxxx-xxxx-xxxx`形式。

1. 一级服务名为组织名称，如lenovo，二级服务名为应用或项目的名称，如pcsd，三级服务名为功能模块的名称，如madp。整体为lenovo-pcsd-madp
2. 使用英文拼写，单词间使用`_`（英文下划线）进行分隔，不要使用空格。
3. 全部使用小写字母。 

```
正例：
lenovo-pcsd-madp
lenovo-pcsd-micro_platform
```

```
反例：
lenovo-pcsd-it-madp [错误说明：格式错误]
lenovo-pc sd-madp    [错误说明：空格]
lenovo-pcsd-MADP   [错误说明：大写]
```

###  <a name="service-register"> </a> 微服务注册

1. 需要登录微服务平台 <http://microservice.lenovo.com>，先创建app。
2. 在创建的app下，按上述命名规范创建微服务。平台将会自动生成ServiceKey并在基本信息页面中显示。


## 开发微服务
### <a name="project-config"> </a>项目配置（pom文件配置）
#### 依赖及仓库配置

在项目的pom文件添加以下父依赖,用于实现微服务基础框架构建、健康检查、路由追踪等能力。

```xml

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.parent</groupId>
    <artifactId>parent</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>servicemanager</module>
        <module>core</module>
        <module>adauth</module>
    </modules>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.3.RELEASE</version>
        <relativePath/>
    </parent>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.8</java.version>
    </properties>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Dalston.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional>
        </dependency>

        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.24</version>
        </dependency>

    </dependencies>
    <!--maven仓库地址-->
    <distributionManagement>
        <!-- 配置快照版本发布的仓库 -->
        <snapshotRepository>
            <id>msp-snapshots</id>
            <name>Madp Snapshots Repository</name>
            <url>http://10.96.83.23:8082/nexus/content/repositories/msp-snapshots/</url>
        </snapshotRepository>
        <!-- 配置release版本发布的仓库 -->
        <repository>
            <id>msp-releases</id>
            <name>Madp Releases Repository</name>
            <url>http://10.96.83.23:8082/nexus/content/repositories/msp-releases/s</url>
            <uniqueVersion>false</uniqueVersion>
        </repository>
    </distributionManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

#### groupId和artifactId命名

在pom文件中，请按照以下规则命名groupId和artifactId：

1. groupId:定义当前maven项目隶属的实际项目或应用，和微服务命名规范类似，按照组织-项目/应用进行命名，例如：pcsd-madp。
2. artifactId:请使用service-name，定义artifactId


### <a name="service-config"></a> 服务配置

服务配置分为两部分，为本地配置和远程配置。

1.本地配置文件：bootstrap.yml。用来配置服务本身的属性，比如微服务名称，需要连接的配置服务器。

**注意：**本地配置中spring.application.name的值 = portal上填写的service-name
    
```yml
spring:
  application:
    name: service-name  # 应用名称必须保证全局唯一
  cloud:
    config:
      discovery:
        enabled: true
        service-id: config   # location of the configuration server
```



2.远程配置文件：{service-name}.yml。放在微服务平台的git仓库,用来配置业务逻辑中用到的配置信息。

**注意：**配置文件的文件名 = 本地配置中spring.application.name的值 = portal上填写的service-name
不同环境不同配置内容。

通用配置： {service-name}.yml

```yml

info:
  owner: MSP team
  description: your service's description!

server:
  port: {在微服务平台分配的范围内指定端口}

serviceConfig:
  url:
  

```

### <a name="api-design"></a> API设计

1. 为便于管理，请按照格式将/service-name/apiName/v[1-9]+[0-9]*/作为Rest API的前缀
2. 在RestControler中请勿添加/service-name

```
正例：
/service-name/apiName/v1/users/zhangsan/age
/service-name/apiName/v1/imgs/homepage/color
```

```
反例：
/apiName/v1/users/zhangsan/age               [错误说明：缺少service-name]
/service-name/v1/users/zhangsan/age          [错误说明：缺少apiName]
/service-name/apiName/1.2/users/zhangsan/age [错误说明：版本格式错误]
```
   

### <a name="service-log"></a> 日志格式

1. **路径：**为方便日志收集，请将日志输出至路径/var/logs/msp/{service-name}.log，可以根据需要对日志文件进行切割。
2. **格式：**为方便日志分析，请按照以下格式生成日志，其中Exception字段可以不输出，也可以自定义其他日志。
	
```json 
{
    "service-name":"madp-auth",
    "apiName":"username_by_im_username",
    "AppKey":"",
    "ClientIp":"10.122.12.240",
    "Server":"10.122.12.231",
    "ServerPort":8081,
    "URI":"/madp-auth/login/users/username",
    "RequestMethod":"GET",
    "RequestParameters": ["31550af5fbae3dbbc14bdf98d","QzczRTIzMyQDvbS5jb20ubWFkcA=="],
    "Method":"userInfoByUserName",
    "ResponseStatus":200,
    "Exception":{
        "description":"service meet some mistakes",
        "error":"Internal Server Error"
    }
    "YourPrivateLogs":{
    		
    }
}
```

## <a name="service-deploy"></a> 部署微服务

您可以选择将微服务部署在您自行管理的服务器或微服务平台的服务器上。

* 如果希望部署在自行管理的服务器上，并且需要微服务平台提供的日志统计与分析、服务监控与守护等能力，需要开通服务器的账号以便微服务平台进行相关部署。

* 如果希望部署在微服务平台服务器上，需要将代码提交到微服务平台的git代码仓库，由微服务平台进行自动化构建及部署。



