# SpringBoot

### 1.SpringBoot概念介绍

​	Spring Boot 是由 Pivotal 团队提供的全新框架，其设计目的是用来简化新
Spring 应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，
从而使开发人员不再需要定义样板化的配置。通过这种方式， Spring Boot 致力
于在蓬勃发展的快速应用开发领域(rapid application development)成为领导
者.  

### 2.SpringBoot特点

​		创建独立 Spring 应用程序、 嵌入式 Tomcat， Jetty 容器、 无需部署 WAR 包、简化 Maven 及 Gradle 配置、 尽可能的自动化配置 Spring、 直接植入产品环境下的实用功能，比如度量指标、健康检查及扩展配置、 无需代码生成及 XML 配置等。
​		Spring Boot 不止对 web 应用程序做了简化，还提供一系列的依赖包来把其它一些工作做成开箱即用。

### 3.SpringBoot快速入门

环境:Jdk 1.8+ ,Spring Boot 2.2.x
通过 Spring Boot 开发 web 项目，基于 maven 创建普通项目  

#### 1.导入SpringBoot坐标依赖

设置 spring boot 的 parent ，该依赖指定了springboot中的版本问题,避免出现jar包版本冲突问题

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.2.RELEASE</version>
</parent>
```

导入 spring boot 的 web 支持  ，内镶嵌了tomcat,jetty服务器,无需部署 WAR 包、  

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

添加 Spring boot 的插件  

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
```

#### 2.添加 HelloController.java  

```java
@Controller
public class HelloController {
    @RequestMapping("hello")
    @ResponseBody
    public String hello(){
        return "hello Spring Boot";
    }
}
```

#### 3.创建启动程序

```java
@SpringBootApplication //springboot启动程序入口
public class Start {

    public static void main(String[] args) {
        SpringApplication.run(Start.class,args);
    }
}
```

直接运行 main 方法即可 通过浏览器访问资源。  

![img](file:///C:\Users\12071\AppData\Roaming\Tencent\Users\1207182223\QQ\WinTemp\RichOle\Y6[XJ]7A%H5SFWU8J1{E]J4.png)

### 4.SpringBoot核心注解

#### 	1.@SpringBootApplication  

​	该注解标识该类为springboot程序执行入口，SpringBootApplication 注解组合了@SpringBootConfiguration 、 @EnableAutoConfiguration 、 @ComponentScan  

##### 	一：@SpringBootConfiguration  ：可以看出该注解也是一个组合注解，组合了@Configuration 注解,对于 SpringBoot 应用， @SpringBootConfiguration 注解属于 Boot 项目的配置注解也是属于一个组合注解， Spring Boot 项目中推荐使用@SpringBootConfiguration 注解,因为其包含了@Configuration 注解！！！  

##### 	二：@EnableAutoConfiguration  ：@EnableAutoConfiguration 注解组合了@AutoConfigurationPackage、 @Import注解,使用该注解，代表 Spring Boot 应用在启动后默认启用自动配置，比如项目中引入了 SpringMvc 环境， 需要加入@EnableMvc 注解 ，如果声明该@EnableAutoConfiguration，启用过程由应用程序自动配置，我们仅需要引入相关依赖即可达到目的!  

##### 	三：@ComponentScan  ：组件扫描注解， 如果没有进行配置，默认扫描@SpringBootApplication 所在类的同级目录以及所在子目录!  

### 5.SpringBootWeb项目构建

#### 	1.SpringBoot集成Freemarker

##### 	 	1.导入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
```

##### 		2.Freemarker 全局属性配置  

​	    spring-boot 自动配置中引入了freemarker 默认配置,提供了视图未freemarker 模板下的
相关 bean 与 properties 文件基本配置。  

application.yml配置

```properties
spring:
# freemarker配置
  freemarker:
    charset: UTF-8
    suffix: .ftl
    template-loader-path: classpath:/views/

```

application.properties配置

```properties
## 配置详细解释 见 https://my.oschina.net/wangnian/blog/666641
spring.freemarker.template-loader-path=classpath:/views/
spring.freemarker.charset=UTF-8
spring.freemarker.content-type=text/html
spring.freemarker.suffix=.ftl
```

##### 		3.添加 Controller 转发到 index.ftl 视图  

```java
@Controller
class TestController {
    @RequestMapping("index")
    public String index(Model model){
    	model.addAttribute("msg","hello spring boot freemarker");
    return "index";
}
}
```

##### 		4.视图index.ftl文件创建

![![img](file:///C:\Users\12071\AppData\Roaming\Tencent\Users\1207182223\QQ\WinTemp\RichOle\Y){8`6N%)ET1@ZU~)KZ~YI0.png)]()

##### 		5.启动springBoot在访问完成

### 6.SpringBoot拦截器，文件上传配置

#### 	一.拦截器配置

​	在 SpringMvc 应用中通过定义拦截器，可以实现对应用中指定资源进行访问
控 制 目 的 ( 用 户 非 法 资 源 请 求 拦 截 等 操 作 ) ， 实 现 方 式 可 以 通 过 继 承
HandlerInterceptorAdptor 或者实现 HandlerInterceptor 接口 并在 xml 中
进行配置来生效拦截器。 同样道理，在 Spring Boot 应用中拦截器实现同样也是
继承 HandlerInterceptorAdptor 或实现 HandlerInterceptor 接口方式，生效
时将对应 Bean 加入配置项中即可  	

##### 	1.LoginInterceptor 代码准备  

```java
public class LoginInterceptor extends HandlerInterceptorAdapter {
	@Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse
        response, Object handler) throws Exception {
        System.out.println("拦截器被执行。 。。 ");
        return true;
    }
}
```

##### 	2.配置拦截器生效  

```java
@Configuration //声明该类为配置类
public class MvcConfig extends WebMvcConfigurerAdapter{
    // 配置登录拦截器 bean
    @Bean
    public LoginInterceptor loginInterceptor(){
    	return new LoginInterceptor();
    }
    // 重写拦截器添加方法 将登录拦截器加入 mvc 环境
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // 指定拦截资源 与放行资源	registry.addInterceptor(loginInterceptor()).addPathPatterns("/**").excludePa
        thPatterns("/login","/exit");
    }
}
```

##### 	3.启动项目

![img](file:///C:\Users\12071\AppData\Roaming\Tencent\Users\1207182223\QQ\WinTemp\RichOle\AK1YHUHMIK[$N@SR4OOPAEM.png)

#### 	二.文件上传配置

##### 		1.文件上传 UploadController 代码添加  

```java
@Controller
public class UploadController {
    @RequestMapping("uploadFile")
    public ModelAndView uploadFile(HttpServletRequest request){
        ModelAndView mv=new ModelAndView("result");
        MultipartHttpServletRequest mhsr=(MultipartHttpServletRequest)request;
        MultipartFile mf= mhsr.getFile("file");
        try {
            if(null!=mf&&!mf.isEmpty()){
            String
            path=request.getSession().getServletContext().getRealPath("/WEBINF/upload");
            String fileName= mf.getOriginalFilename();
            File file=new File(path,fileName);
            mf.transferTo(file);mv.addObject("msg", "文件上传成功!");
        }
        } catch (Exception e) {
            e.printStackTrace();
            mv.addObject("msg", "文件上传失败!");
        }
        	return mv;
    }
}
```

##### 		2.文件上传全局配置  

application.properties中配置

```properties
## 文件上传配置 
spring.http.multipart.maxFileSize=10Mb
```

##### 	3.启动上传文件

### 7.SpringBoot中统一异常,事务处理

#### 	一：Spring Boot 事物支持  

​	二：Spring Boot 全局异常处理  