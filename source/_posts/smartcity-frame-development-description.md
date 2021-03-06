---
title: SmartSpring框架 -- 03.开发说明
copyright: true
related_posts: true
categories: 框架
tags:
  - smartSpring
  - 框架
  - 开发
  - Dubbo
abbrlink: 8d7215c3
date: 2018-04-18 15:16:26
---

本篇主要讲解项目的基本结构以及各个主要功能点的开发示例。
> 由于本项目主要使用阿里的`Dubbo`来做为开发架构，因此也可用于Dubbo开发者学习。

<!-- more -->
### 项目结构
当我们使用这套框架搭一个初始化的项目亦或是一个成熟项目之后，它大概的结构是这样的：

```tree
.demo-parent 
|-- db-change ········································ 数据库表字段更改记录
|
|-- startup ·········································· 启动脚本，部分项目可能无
|
|-- demo-core ········································ 项目公共jar包，公共变量及方法，小项目舍弃 <!-- more -->
|   |-- src 
|   |   |-- main
|   |   |   |-- java 
|   |   |   |   `-- 包路径             
|   |   |   |       |-- constants ···················· 公共常量目录，应用所需常量，不包含api常量
|   |   |   |       `-- utils ························ 公用工具类目录        
|   |   |   `-- resources
|   |   `-- test
|   `-- pom.xml ······································ 构建配置信息
|
|-- demo-api ········································· 服务层接口，dubbo-api包，提供服务层接口
|   |-- src 
|   |   |-- main
|   |   |   |-- java
|   |   |   |   `-- 包路径             
|   |   |   |       |-- constants ···················· 常量目录，api对外常量            
|   |   |   |       |-- api ·························· dubbo-api接口目录             
|   |   |   |       `-- model ························ 接口出入参数模型目录，模型一般以Dto结尾
|   |   |   |           |-- 模块1              
|   |   |   |           `-- 模块2              
|   |   |   `-- resources
|   |   |       `-- dubbo ···························· duubo配置文件目录
|   |   |           `-- dubbo-consume-demo.xml ······· 提供给消费者的duubo接口，便于消费者集成
|   |   `-- test
|   `-- pom.xml ······································ 构建配置信息
|
|-- demo-app ········································· 应用层，restful接口war包，提供rest请求
|   |-- src
|   |   |-- main
|   |   |   |-- java
|   |   |   |   `-- 包路径            
|   |   |   |       |-- base ························· 基本信息目录           
|   |   |   |       |   |-- config          
|   |   |   |       |   |   |-- RestConfig.java ······ jersry框架里Servlet的配置类，一般不修改
|   |   |   |       |   |-- Config.java ·············· 利用spring-bean对配置信息的映射类
|   |   |   |       |   `-- ReturnCode.java ·········· 异常信息的枚举           
|   |   |   |       |-- biz ·························· 应用逻辑层目录，聚合接口，格式化参数
|   |   |   |       |-- rest ························· 应用控制层目录，接收前端参数，校验合法性
|   |   |   |       `-- vo ··························· 前后端交互的参数模型目录，一般以Vo结尾
|   |   |   |           |-- 模块1             
|   |   |   |           `-- 模块2 
|   |   |   |-- resources ···························· 项目资源目录
|   |   |   |   |-- config ··························· 配置文件目录            
|   |   |   |   |   |-- local ························ 本地调试配置信息目录           
|   |   |   |   |   |-- dev ·························· 开发服务器配置信息目录            
|   |   |   |   |   |-- test ························· 测试环境配置信息目录            
|   |   |   |   |   `-- prod ························· 生产环境配置信息目录            
|   |   |   |   |-- application-context.xml ·········· spring-bean的配置信息          
|   |   |   |   |-- dubbo-consumer.xml ··············· dubbo消费者的初始化配置信息            
|   |   |   |   `-- log4j.properties ················· log4j日志的配置信息           
|   |   |   `-- webapp  ······························ war包的信息配置目录
|   |   |       |-- WEB-INF
|   |   |       |   `-- web.xml ······················ web项目入口，初始化配置信息
|   |   |       `-- index.jsp ························ 项目初始化欢迎页
|   |   `-- test
|   |-- jetty.xml ···································· jetty描述文件，一般不做修改 
|   |-- pom.xml ······································ 项目构建配置信息
|   `-- readMe.txt ··································· 部署说明，注意事项
|
|-- demo-backend ····································· 应用层，项目后台管理系统war包
|   |-- src
|   |   |-- main
|   |   |   |-- java
|   |   |   |   `-- 包路径             
|   |   |   |       |-- base ························· 基本信息目录 
|   |   |   |       |   |-- Config.java ·············· 利用spring-bean对配置信息的映射类
|   |   |   |       |   `-- ReturnCode.java ·········· 异常信息的枚举           
|   |   |   |       |-- biz ·························· 应用逻辑层目录，聚合接口，格式化参数
|   |   |   |       |-- controller ··················· 应用控制层目录，接收前端参数，校验合法性
|   |   |   |       |-- filter ······················· 过滤器目录            
|   |   |   |       |   `-- LoginFilter.java ········· 登陆过滤器，管理登陆时的session信息
|   |   |   |       `-- vo ··························· 前后端交互的参数模型目录，一般以Vo结尾
|   |   |   |           |-- 模块1              
|   |   |   |           `-- 模块2 
|   |   |   |-- resources ···························· 资源目录，同上，不再详细说明
|   |   |   |   |-- config             
|   |   |   |   |   |-- local            
|   |   |   |   |   |-- dev            
|   |   |   |   |   |-- test             
|   |   |   |   |   `-- prod             
|   |   |   |   |-- application-context.xml            
|   |   |   |   |-- dubbo-consumer.xml             
|   |   |   |   `-- log4j.properties             
|   |   |   `-- webapp
|   |   |       |-- resources ························ 前端静态资源目录
|   |   |       |-- views ···························· jsp文件目录
|   |   |       |   |-- 模块1
|   |   |       |   |-- 模块2
|   |   |       |   `-- error ························ 异常请求jsp文件目录
|   |   |       |-- WEB-INF  ························· war包的信息配置目录
|   |   |       |   `-- web.xml ······················ web项目入口，初始化配置信息
|   |   |       `-- index.jsp ························ 初始化欢迎页jsp
|   |   `-- test
|   |-- jetty.xml ···································· jetty描述文件，一般不做修改 
|   |-- jetty-env.xml ································ jetty的配置文件，一般配置数据库连接信息
|   |-- pom.xml ······································ 构建配置信息
|   `-- readMe.txt ··································· 部署说明，注意事项
|
|-- demo-web ········································· 应用层，项目web门户系统war包，类同backend
|
`-- demo-service ····································· 业务层，dubbo-service包，实现api接口
    |-- src
    |   |-- assembly ································· 打包配置目录
    |   |   |-- bin ·································· 启动脚本目录
    |   |   |   |-- run.bat ·························· windows环境启动脚本
    |   |   |   |-- run.sh ··························· linux环境启动脚本 
    |   |   `-- assembly.xml ························· service打包配置信息，一般不做修改
    |   |-- main
    |   |   |-- java
    |   |   |   `-- 包路径             
    |   |   |       |-- base ························· 基本信息目录
    |   |   |       |   |-- Config.java ·············· 利用spring-bean对配置信息的映射类
    |   |   |       |   |-- Constants.java ··········· 常量类，提供本子项目所需使用的常量
    |   |   |       |   `-- ReturnCode.java ·········· 异常信息的枚举          
    |   |   |       |-- dao ·························· 业务持久化层，调用sql，操作数据库
    |   |   |       |-- entity ······················· 数据库表对应映射的实体类     
    |   |   |       |-- service ······················ 业务实现层，api接口的实现
    |   |   |       |-- InitApplication.java ········· 应用初始化类，在容器初始化后调用            
    |   |   |       |-- Provider.java ················ dubbo的启动类，运行main方法启动项目
    |   |   `-- resources
    |   |       |-- config ··························· 配置文件目录
    |   |       |   |-- local             
    |   |       |   |-- dev             
    |   |       |   |-- test             
    |   |       |   `-- prod 
    |   |       |-- mapper ··························· Mybatis映射的xml文件目录  
    |   |       |-- META-INF ························· jar包的信息配置目录
    |   |       |   `-- spring ······················· spring配置信息目录
    |   |       |       |-- dubbo-provider.xml ······· spring-bean的配置信息
    |   |       |       `-- spring-context.xml ······· duubo提供者的初始化配置信息
    |   |       |-- sql ······························ 初始化sql存放目录
    |   |       |   |-- initTabel.xml ················ 初始化表结构sql
    |   |       |   `-- initDate.xml ················· 初始化表数据sql
    |   |       |-- log4j.xml ························ log4j日志的配置信息
    |   |       |-- generatorConfig.xml ·············· mybatis反向生成代码配置文件
    |   |       |-- mybatis-generator.properties ····· mybatis反向生成代码配置信息
    |   |       `-- mybatis-config.xml ··············· mybatis配置信息
    |   `-- test
    |-- pom.xml ······································ 构建配置信息
    `-- readMe.txt ··································· 部署说明，注意事项
```
### 开发示例
*注：素材侧重于举例说明使用方法，有诸多不规范的地方（注释内容，硬编码等），请勿完全参照。*

#### 1. 定义常量&枚举
```java
public interface CommonEnum {
    /**
     * 单属性
     */
    enum Check implements StateEnum {
        /** 0 否 ，1 是 ，2 未通过 */
        TRUE(1), FALSE(0), NO_PASS(2);    
    }

    /**
     * 多属性
     */
    enum State implements ServiceEnum {
        DISABLE(0, "已禁用"),
        ENABLE(1, "已启用"),
        DELETE(2, "已删除");

        /** 状态 */
        private Integer state;
        /** 状态中文解释 */
        private String text;

        State(Integer state, String text) {
            this.state = state;
            this.text = text;
        }
    }    
}
```

#### 2. 定义异常返回值
``` java
public enum ReturnCode implements IReturnCode {
    PARAM_NULL_ID(41001, "missing id"),
    PARAM_INVALID_ID(41007, "invalid id");

    private int code;
    private String message;
    
    // 其余代码略   
}    
```
* 实现`IReturnCode`接口，添加异常业务类型（code，message）
* 如项目有core包，可全部定义在core中，避免重复业务
* 抛出异常有助于自己排查问题，也便于前端人员定位问题*

#### 3. 定义 *dubbo-api* 
* 定义接口类和接口方法，在api包 *resources.dubbo* 文件夹下*dubbo-consume-demo.xml* 中声明接口

    ```java
    public interface DemoService {
        /**
         * 打招呼
         */
        String sayHello(@NotBlank String name);
        
        /**
         * 获取Demo
         */
        DemoDto getDemo(@NotBlank Integer age);
    }
    ```
    ```xml
    <dubbo:reference id="demoService" interface="包路径.DemoService"
        protocol="dubbo" version="${dubbo.service.version.demo}" validation="true"/>
    ```
    `validation`为可选属性，缺省值是`false`，建议开启。开启验证后[ Hibernate Validator ](https://blog.csdn.net/danielzhou888/article/details/74740817)注解生效，会对接口参数进行校验

#### 4. *dubbo-Service*实现*dubbo-api*
``` java
@Service("demoService")
public class DemoServiceImpl implements DemoService {

    @Autowired
    private DemoDao demoDao;

    @Override
    public String sayHello(String name) {
        return "Hello , " + name + " !";
    }

    @Override
    public DemoDto getDemo(Integer age) {
        return demoDao.getDemo(age);
    }
}
```
@Service注解后指定 <*bean id*>（与api.xml中定义的id需相同），spring.context.component-scan 扫描后即可自动托管bean对象

#### 5. 配置服务提供者
* 在 *pom.xml* 中依赖 api-jar 包
* 在 *resources.META-INF.spring* 目录（缺省目录）内新建配置文件`dubbo-provider.xml`

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns="http://www.springframework.org/schema/beans"
           xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
    		http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
    		http://code.alibabatech.com/schema/dubbo
            http://code.alibabatech.com/schema/dubbo/dubbo.xsd"
           default-lazy-init="true" default-autowire="byName">
        <description>Dubbo提供者配置</description>
    
        <!-- 提供方应用信息，用于计算依赖关系 -->
        <dubbo:application name="${dubbo.provider.application.name}"/>
    
        <!-- 使用zookeeper注册中心暴露服务地址 -->
        <dubbo:registry address="${dubbo.registry.address}" check="false"
            timeout="${dubbo.registry.timeout:30000}"/>
    
        <!-- 调用超时时间 和 服务版本信息 -->
        <dubbo:provider timeout="${dubbo.provider.timeout:30000}" version="${dubbo.service.version}"/>
    
        <!-- 定义服务所用协议信息-dubbo协议 -->
        <dubbo:protocol name="dubbo" port="${dubbo.dubbo.port}"/>
    
        <!-- 声明服务提供者接口 -->
        <dubbo:service interface="包路径.DemoService" ref="demoService" 
            validation="true" retries="0" protocol="dubbo"/>
    
    </beans>
    ```
    `retries`为可选属性，缺省值是`2`，设置为0后可避免服务堵塞情况下自动重试导致事务问题。

#### 6. 启动服务提供者
* 运行 *Provider.java* 内的`main()`方法即可启动消费者
* 更改 *src.assembly.bin* 目录内启动脚本中`APP_MAINCLASS`的路径为 *Provider.java*

#### 7. 配置服务消费者
* 在 *pom.xml* 中依赖 api-jar 包
* 在 *resources* 目录内新建配置文件`dubbo-consumer.xml`，并托管给Spring管理（在*web.xml*配置或者 *application-context.xml* 配置都可以）

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://code.alibabatech.com/schema/dubbo
           http://code.alibabatech.com/schema/dubbo/dubbo.xsd"
           default-lazy-init="true" default-autowire="byName">

    <!-- 消费方应用名，用于计算依赖关系 -->
    <dubbo:application name="${dubbo.application.name}"/>

    <!-- 使用zookeeper注册中心暴露服务地址 -->
    <dubbo:registry address="${dubbo.registry.address}" check="false" 
        timeout="${dubbo.registry.timeout:3000}"/>

    <!-- 远程服务调用超时时间，是否检查 -->
    <dubbo:consumer timeout="${dubbo.consumer.timeout:3000}" check="false"/>

    <!-- 引入其他api包中的配置文件，声明其提供的所有接口 -->
    <import resource="classpath*:/dubbo/dubbo-consumer-demo.xml"/>
    
    <!-- 声明欲使用的接口 -->
    <dubbo:reference id="commentService" interface="包路径.CommentService"
        protocol="dubbo" version="${dubbo.service.version.comment}" validation="true"/>
    </beans>
    ```
* 在配置文件中配置各项属性，有默认值的可不再重复配置
    + `registry`中`check`属性是检查注册中心是否正常
    + `consumer`中`check`属性时检查对应服务是否正常注册
* 接口如果不是全部需要使用建议单独声明需要的接口，无效声明过多引起连接资源浪费

#### 8. 完成 *Restful* 接口
``` java
@Path("/demo")
@Produces(MediaType.APPLICATION_JSON)
@Component
public class DemoRestful {
    
    @Path("/a")
    @GET
    @AppAuthorization
    public String demo(@PathParam("name") String name, @PathParam("age") Integer age) {
        // 校验参数，抛出自定义的异常
        if (ParamVerifyUtil.isEmpty(id)) {
            throw new IllegalParamsException(ReturnCode.PARAM_INVALID_ID);
        }
        
        // 校验参数并自动抛出统一异常
        ParamVerifyUtil.verifyEmpty(age);
        
        // 使用AppAuthorization注解后，可通过以下方法获取userId
        String userId = BasicContext.get().getUserId（）;
        
        // 调用biz接口
        xxBiz.xx();
        
        return “hello word”;
    }
}
```

* 在方法上加注注解 `@javax.ws.rs.GET`/`@javax.ws.rs.POST` ，声明请求方式
    + get请求对用的参数注解为 `@QueryParam`
    + post请求对应的参数注解为 `@FormParam`
* 如果请求体是文件流，应在方法上加注注解 `@Consumes(MediaType.MULTIPART_FORM_DATA)`
* 如果需要对请求权限进行验证，需要加上自定义App权限验证注解`@AppAuthorization`，<br> 使用后框架会对请求进行验证，并根据入参token解析userId
* 两种校验参数方式根据业务自由选择，入参均支持常用数据类型<br>`ps: jersey框架与Hibernate Validator框架融合时有版本兼容问题，暂未解决，所以暂时使用手动验证。`
* 如果请求参数过多，可封装成实体类，使用注解 `@BeanParam`标注实体，<br>实体类的各个参数需加对应请求的注解 `@QueryParam` or `@FormParam` ，例如：

    ```java
    @POST
    public String demo(@BeanParam DemoFormVo demo) {
        return "hello word";
    }
        
    public class DemoFormVo {
        @FormParam
        private Integer age;
        
        @FormParam
        private String name;
    }
    ```

#### 9. 完成*Controller* 接口
``` java
@Controller
@RequestMapping("/demo")
public class DemoControlle {
    
    @ResponseBody
    @RequestMapping(value = "/demo", method = RequestMethod.GET)
    public String demo(@Validated  @ModelAttribute DemoFormVo demo) {
        return "hello word";
    }
}
    
public class DemoFormVo {
    @Max(value = 150)
    private Integer age;
    
    @NotBlank
    private String name;
}
```

* 使用方式与Restful接口类似，不同的是注解使用SpringMVC的注解
    + 实体类的注解使用`@ModelAttribute`，且不在需要加注参数注解
    + 使用[ Hibernate Validator ](https://blog.csdn.net/danielzhou888/article/details/74740817)框架的注解进行参数合法性验证

#### 10. 完成*biz*层逻辑
``` java
@Service
public class DemoBiz {

    @Autowired
    private DemoService demoService;
    
    @Autowired
    private CommentService commentService;

    public DemoVo demo(DemoFormVo form) {
    
        // 调用demoService接口
        DemoDto dto = demoService.getDemo(form.getName());
        
        // 识别业务异常，及时抛出
        if(dto == null){
            logger.error（“xx业务异常”）；
            throw new BusinessException(ReturnCode.PARAM_NULL_ID);
        }
        
        // 通过GsonUtil转换得到DemoVo
        DemoVo vo = GsonUtil.convert(dto, DemoVo.class);
        
        // 调用其他dubbo-api接口
        Comment comment = commentService.getComment(form.getAge());
        
        // 封装返回值
        vo.setComment(comment);
        return vo；
    }
}
```
及时识别业务异常，记录日志并抛出

#### 11. 使用*Redis*缓存
* 在*application-context.xml*中引入bean配置 `<import resource="classpath*:cache/*.xml"/>` ，并在配置文件中配置以下属性
    + redis.host  
    + redis.port 
    + redis.database 
    + redis.password 
    + redis.maxIdle     连接池状态为idle的实例个数，缺省值300，可不配置
    + redis.maxWait     连接池最大等待时间（s），缺省值3000，可不配置
    + redis.maxTotal    连接池最大连接数，缺省值50，可不配置
* 调用*RedisUtils*工具类里封装的方法即可。

#### 12. 处理自定义异常
* 在*web.xml* 里配置SpringMVC的` servlet-class`。<br> 缺省配置的处理异常 class 为 `com.iflytek.sgy.social.web.core.exception.ExceptionDispatcherServlet` 
* 如果需要处理自定义异常（*注：此功能在 V 0.5.0版本后方可使用*），则需要编写实现类继承<br>`com.iflytek.sgy.social.web.core.exception.AbstractExceptionDispatcherServlet`，重载`handleOtherException()`方法，覆盖缺省配置。

    ``` java
    public class PayExceptionDispatcherServlet extends AbstractExceptionDispatcherServlet {

    @Override
    protected Result handleOtherException(Exception ex) {
        if (ex instanceof PayErrorException) {
            logger.error(" ========== pay error ========== ", ex);
            
            IPayError error = ((PayErrorException) ex).getPayError();
            ReturnCode returnCode = error.getPayError();
            
            // 返回失败结果
            return Result.fail(returnCode.getCode(), returnCode.getErrorMsg());
        }
    }

    ```

#### 13. 实现集中管理 *APP Session*
*注：此功能在 V 0.5.0版本后方可使用。*

* 实现接口`com.iflytek.sgy.social.app.core.base.TokenTemplate`，重载抽象方法

    ``` java
    @Component
    public class AppToken implements TokenTemplate {
        
        /**
         * 获取token
         * @param userId 用户UUID
         */
        @Override
        public String getToken(String userId) {
            return null;
        }
        
        /**
         * 从token中解析userId
         * @param token TOKEN
         */
        @Override
        public String parseToken(String token) {
            return null;
        }
        
        /**
         * 生成token
         * @param userId 用户UUID
         */
        @Override
        public String makeToken(String userId) {
            return null;
        }
        
        /**
         * 销毁token
         * @param userId 用户UUID
         */
        @Override
        public String destroyToken(String userId) {
            return null;
        }
    }
        
    ```
* 在 *application-context.xml* 里加上配置

    ``` xml
    <import resource="classpath*:aop/*.xml"/>
    
    <!--配置token管理的实现-->-
    <bean id="appToken" class="包路径.AppToken"/>
    <bean class="com.iflytek.sgy.social.app.core.base.TokenManager"
          p:tokenTemplate-ref="appToken"/>
    ```
* 更改*web.xml* 里` servlet-class` 为 `com.iflytek.sgy.social.app.core.extension.ServletContainer`

#### 14. 自动执行*SQL*初始化脚本
*注：此功能在 V 0.5.0版本后方可使用。*

* 实现`ApplicationListener`接口，重载`onApplicationEvent()`方法，在方法里调用`executeSqlInit()`

    ``` java
    @Component
    public class InitApplication implements ApplicationListener<ContextRefreshedEvent> {
    
        @Autowired
        private ExecuteSqlDao executeSqlDao;
    
        @Override
        public void onApplicationEvent(ContextRefreshedEvent event) {
            // 获取ExecuteSqlDao实例，调用执行sql初始化方法
            ExecuteSqlEvent.getInstance(executeSqlDao).executeSqlInit();
        }
    
    }
    ```
* 在 *resources.sql* 目录下建立 initTable.xml（初始化建表）和 initData.xml（初始化数据）

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <tables>
        <table>
            <!-- 表名，例如T_NAFC_RESOURCE -->
            <check>T_TABELE_NAME</check>
            <sql> </sql>
        </table>
    </tables>
    ```
    + `check`标签是表名
    + `sql`标签是待执行*sql*，建表语句 or 数据初始化语句

#### 15. 配置常用*maven*插件
*注：此功能在 V 0.5.0版本后方可使用。*

* *dubbo-service* 定制化打包插件已预定义直接使用即可

    ``` xml
    <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
    </plugin>
    ```
* *jetty* 插件和 *tomcat* 插件已预定义直接使用即可，缺省端口是8090，如需改变请覆盖配置

    ``` xml
    <properties>
        <jetty.port>8091</jetty.port>
        <tomcat.port>8092</tomcat.port>
    </properties>
    
    <plugins>
        <!-- jetty插件 -->
        <plugin>
            <groupId>org.mortbay.jetty</groupId>
            <artifactId>maven-jetty-plugin</artifactId>
        </plugin>
        <!-- tomcat插件 -->
        <plugin>  
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>tomcat-maven-plugin</artifactId>
        </plugin>
    </plugins>
    ```

#### 16. 打包发布
* 根据环境执行不同环境的打包命令，不同环境对于不同的log过滤等级。<br> 命令 `mvn clea package -Pdeploy`
    + *local* -- DEBUG
    + *dev* -- DEBUG
    + *test* -- INFO
    + *prod* -- ERROR
    + *deploy* -- ERROR
    
* *注：以下功能在 V 0.5.0版本后方可使用。*
    + 除local环境打包后读取classpath下的配置文件，其他环境默认读取`/iflytek/zhcs/config/`目录下的配置，<br> 如需读取其他目录，可在pom.xml里覆盖配置 `environment.path`    
