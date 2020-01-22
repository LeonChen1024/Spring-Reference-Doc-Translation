# 23. SpringApplication





[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/22-What-to-Read-Next.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-IV-Spring-Boot-features/24-Externalized-Configuration.md)     



 `SpringApplication` 类提供了一个便捷的方式来启动一个Spring 应用,即通过一个  `main()`  方法. 在多数情况下,你可以委派给 `SpringApplication.run` 静态方法来处理, 如下所示:

```java
public static void main(String[] args) {
	SpringApplication.run(MySpringConfiguration.class, args);
}
```



当你的应用启动的时候,你应该会看到与下面类似的输出:

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::   v2.1.6.RELEASE

2013-07-31 00:08:16.117  INFO 56603 --- [           main] o.s.b.s.app.SampleApplication            : Starting SampleApplication v0.1.0 on mycomputer with PID 56603 (/apps/myapp.jar started by pwebb)
2013-07-31 00:08:16.166  INFO 56603 --- [           main] ationConfigServletWebServerApplicationContext : Refreshing org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@6e5a8246: startup date [Wed Jul 31 00:08:16 PDT 2013]; root of context hierarchy
2014-03-04 13:09:54.912  INFO 41370 --- [           main] .t.TomcatServletWebServerFactory : Server initialized with port: 8080
2014-03-04 13:09:56.501  INFO 41370 --- [           main] o.s.b.s.app.SampleApplication            : Started SampleApplication in 2.992 seconds (JVM running for 3.658)
```

默认情况下, `INFO` 日志会被展示出来,包括一些相关的启动信息,比如用户启动了应用.如果你需要打印等级不是 `INFO`, 你可以设置它,如同 [Section 26.4, “Log 等级”](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-levels) 描述,

## 23.1 启动失败

如果你的应用启动失败了, 已经的注册的 `FailureAnalyzers` 会提供一个专有的异常信息还有相关的操作来解决这个问题. 比如,如果你在端口 ` 8080` 启动了一个 web 应用,但是这个已经被占用了,你应该会看到类似如下的信息:

```
***************************
APPLICATION FAILED TO START
***************************

Description:

Embedded servlet container failed to start. Port 8080 was already in use.

Action:

Identify and stop the process that's listening on port 8080 or configure this application to listen on another port.
```

 

>  Spring Boot 提供了大量的 `FailureAnalyzer` 实现,并且你也可以 [添加你自定义的](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/howto-spring-boot-application.html#howto-failure-analyzer).

如果没有错误分析器可以处理这个异常,你仍然可以显示完整的情况报告来更好的理解是什么出错了.为此,你需要 [启用 `debug` 属性](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-external-config.html) 或者 [启用 `DEBUG` 打印](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-levels) 给 `org.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener`.

例如,如果你是通过使用  `java -jar` 来运行你的应用的, 你可以通过如下方式启用 `debug` 属性:

```shell
$ java -jar myproject-0.0.1-SNAPSHOT.jar --debug
```

## 

## 23.2 自定义 Banner

 banner 是在启动的时候最开始打印的内容,它可以通过添加一个 `banner.txt` 文件到你的 classpath 或者设置 `spring.banner.location` 属性来定位这样的一个文件来修改.如果这个文件不是使用UTF-8 编码的,你可以设置 `spring.banner.charset`. 此外,你还可以添加一个 `banner.gif`, `banner.jpg`, 或者 `banner.png` 图片文件到你的 classpath 或者设置 `spring.banner.image.location` 属性. 图片会被转换位一个 ASCII 表示并打印到所有的文本banner的上面.

在你的 `banner.txt` 文件中, 你可以使用以下任意的占位符:



**Table 23.1. Banner 变量**

| 变量                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `${application.version}`                                     | 你的应用的版本号,和 `MANIFEST.MF` 中描述的一样. 比如, `Implementation-Version: 1.0` 会被打印为 `1.0`. |
| `${application.formatted-version}`                           | 你的应用的版本号,和 `MANIFEST.MF` 中描述的一样并且会被格式化为展示模式(用括号包裹并且前缀为  `v`). 比如(v1.0)`. |
| `${spring-boot.version}`                                     | 你所使用的 Spring Boot 版本.比如 `2.1.6.RELEASE`.            |
| `${spring-boot.formatted-version}`                           | 你使用的 Spring Boot 版本,会以指定格式输出 ( 用括号包裹并且前缀为 `v`). 比如 `(v2.1.6.RELEASE)`. |
| `${Ansi.NAME}` (or `${AnsiColor.NAME}`, `${AnsiBackground.NAME}`, `${AnsiStyle.NAME}`) | `NAME` 是一个 ANSI escape 代码的名称. 查阅 [`AnsiPropertySource`](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/ansi/AnsiPropertySource.java)来获取细节. |
| `${application.title}`                                       | 你的应用的标题,如同 `MANIFEST.MF` 中描述的. 比如`Implementation-Title: MyApp` 会打印 `MyApp`. |



> 如果你想要使用编程来生成一个 banner 可以使用 `SpringApplication.setBanner(…)` 方法. 使用 `org.springframework.boot.Banner` 接口并且实现你自己的 `printBanner()` 方法.

你还可以使用 `spring.main.banner-mode` 属性来定义是不是应该要在 `System.out` (`console`) 上打印, 还是发送到配置的记录器 (`log`), 或者完全不生成 (`off`).

打印的banner 以一个单例 bean 的形式注册名为: `springBootBanner`.

> YAML 映射 `off` 为 `false`, 所以确保在两边加上引号如果你想要在你的应用中禁用 banner ,如下所示:
>
> ```
> spring:
> 	main:
> 		banner-mode: "off"
> ```

## 23.3 自定义 SpringApplication

如果 `SpringApplication` 默认属性不符合你的胃口,你可以使用创建一个本地实例并且对它自定义的方式来替换.比如,要关闭 banner, 你可以使用:

```java
public static void main(String[] args) {
	SpringApplication app = new SpringApplication(MySpringConfiguration.class);
	app.setBannerMode(Banner.Mode.OFF);
	app.run(args);
}
```

> 传递给构造器 `SpringApplication`  的参数是 Spring bean 的配置源. 在大多数情况下,他们会引用 `@Configuration` 类, 但是他们也可以引用 XML 配置或者应该扫描的包.

还可以通过 `application.properties` 来配置 `SpringApplication` . 详情查阅 [Chapter 24, Externalized Configuration](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-external-config.html) .

要获取完整的配置可选列表,查看 [`SpringApplication` Javadoc](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/api/org/springframework/boot/SpringApplication.html).

## 23.4 Fluent Builder API

如果你需要按层级构建一个 `ApplicationContext`  (多个拥有父/子关系的 contexts ) 或者如果你倾向于使用 “fluent” 构建 API, 你可以使用 `SpringApplicationBuilder`.

 `SpringApplicationBuilder` 允许你链式的调用多个方法并且包含了 `parent` 和 `child` 方法可以创建一个层级关系,如下所示:

```java
new SpringApplicationBuilder()
		.sources(Parent.class)
		.child(Application.class)
		.bannerMode(Banner.Mode.OFF)
		.run(args);
```



> 当创建一个层级 `ApplicationContext` 的时候是有一些限制的. 比如,Web 组件**必须**包含子context, 并且父子context 必须使用相同的 `Environment` .更多细节查阅 [`SpringApplicationBuilder` Javadoc](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/api/org/springframework/boot/builder/SpringApplicationBuilder.html) .



## 23.5 应用事件和监听

除了常规的 Spring 框架事件,比如 [`ContextRefreshedEvent`](https://docs.spring.io/spring/docs/5.1.8.RELEASE/javadoc-api/org/springframework/context/event/ContextRefreshedEvent.html), 一个 `Spring 应用` 会发送一些其他的事件.

> 有一些事件是会在 `ApplicationContext` 创建之前触发的,所以你不能以 `@Bean` 的方式来注册那些监听. 你可以通过 `SpringApplication.addListeners(…)` 方法或者 `SpringApplicationBuilder.listeners(…)` 方法来注册.
>
> 如果你想要自动注册那些监听, 无论创建应用的方式是怎样, 你可以添加一个 `META-INF/spring.factories` 文件到你的项目中,并且通过使用 `org.springframework.context.ApplicationListener` 键来引用这个监听, 如下所示:
>
> ```
> org.springframework.context.ApplicationListener=com.example.project.MyListener
> ```

当你的应用运行的时候,应用事件会以如下顺序发送:

1.  `ApplicationStartingEvent` 会在除了注册监听和initializers 之外的进程之前运行.
2.  `ApplicationEnvironmentPreparedEvent` 会在已知 `Environment` 在哪个 context 运行但是context 还没创建的时候发送.
3.  `ApplicationPreparedEvent` 会在刷新开始之前bean加在之后发送.
4.  `ApplicationStartedEvent` 会在 context 刷新完了但是应用还有命令行运行器调用之前.
5.  `ApplicationReadyEvent` 会在应用和命令行运行器调用之后发送.它表明应用已经可以接受请求了.
6.  `ApplicationFailedEvent` 如果启动有异常发生的话将会发送它.



> 你大多数情况下不需要使用应用事件,但是知道他们存在是很有必要的.在内部,Spring Boot 使用事件来处理大量的任务.

应用事件通过 Spring 框架的事件推送机制发送. 这个机制中的一部分确保了事件分发到子 context 的监听器的时候也会分发到任意祖先 context 的监听器. 这么做的结果是,如果你的应用使用了一个层级分布的 `SpringApplication` 实例,一个监听器可能会接收到多次相同类型的应用事件实例.

为了让你的监听器可以区分一个事件是给他的 context 还是给他的后代 context,它应该要注入它的context并且对比注入的 context 和事件的 context. context 可以通过 `ApplicationContextAware` 来注入,或者, 如果监听器是一个bean,使用 `@Autowired`.

## 23.6 Web 环境

 `SpringApplication` 会通过你的行为来创建正确类型的 `ApplicationContext` . 用来判断一个 `WebApplicationType` 的算法是非常简单的:

- 如果存在 Spring MVC, 会使用 `AnnotationConfigServletWebServerApplicationContext` 
- 如果不存在 Spring MVC 但是存在 Spring WebFlux, 会使用 `AnnotationConfigReactiveWebServerApplicationContext`
- 否则, 使用 `AnnotationConfigApplicationContext` 

这意味着如果你使用 Spring MVC 并且这个项目里还有  Spring WebFlux 的 `WebClient`, 会默认使用 Spring MVC . 你可以通过调用 `setWebApplicationType(WebApplicationType)` 来重写它.

还可以通过调用  `setApplicationContextClass(…)` 来完全控制 `ApplicationContext` 使用的类型.

> 通常在使用 `SpringApplication` 配合 JUnit 测试的时候要调用  `setWebApplicationType(WebApplicationType.NONE)` .



## 23.7 访问应用参数

如果你需要访问传递到  `SpringApplication.run(…)` 的应用参数, 你可以注入一个`org.springframework.boot.ApplicationArguments` bean.  `ApplicationArguments` 接口提供了可以访问原始 `String[]` 参数以及解析 `option` 和 `non-option` 参数, 如下所示:

```java
import org.springframework.boot.*;
import org.springframework.beans.factory.annotation.*;
import org.springframework.stereotype.*;

@Component
public class MyBean {

	@Autowired
	public MyBean(ApplicationArguments args) {
		boolean debug = args.containsOption("debug");
		List<String> files = args.getNonOptionArgs();
		// if run with "--debug logfile.txt" debug=true, files=["logfile.txt"]
	}

}
```

> Spring Boot 还注册了一个 `CommandLinePropertySource` 在 Spring `Environment`. 这使得你还可以通过使用 `@Value` 注解来注入一个应用参数.

## 23.8 使用 ApplicationRunner 或者 CommandLineRunner

如果你想要在 `SpringApplication` 应用启动的时候运行一段指定代码, 你可以实现 `ApplicationRunner` 或者 `CommandLineRunner` 接口. 两个接口都使用相同的工作方式并且提供了一个 `run` 方法, 会在 `SpringApplication.run(…)` 完成之前调用.

 `CommandLineRunner` 接口提供了以一个简单的字符串数组的形式访问应用参数的方式,而 `ApplicationRunner` 使用了 `ApplicationArguments` 接口. 下面的例子展示了 `CommandLineRunner` 和一个  `run` :

```java
import org.springframework.boot.*;
import org.springframework.stereotype.*;

@Component
public class MyBean implements CommandLineRunner {

	public void run(String... args) {
		// Do something...
	}

}
```

如果有一些 `CommandLineRunner` 或者 `ApplicationRunner` bean 必须要以一个指定的顺序进行调用,你可以选择实现`org.springframework.core.Ordered` 接口或者使用 `org.springframework.core.annotation.Order` 注解.

## 23.9 应用退出

每个 `SpringApplication` 都会注册一个JVM的关闭钩子来确保 `ApplicationContext` 可以在退出的时候优雅的关闭. 所有标准的 Spring 生命周期回调 (比如 `DisposableBean` 接口或者 `@PreDestroy` 注解)都可以使用.

此外, bean 可以实现 `org.springframework.boot.ExitCodeGenerator` 接口如果他们想要在 `SpringApplication.exit()` 被调用的时候返回一个指定的退出码. 这个退出码可以被传递到 `System.exit()` 来作为一个状态码返回,如下所示:

```java
@SpringBootApplication
public class ExitCodeApplication {

	@Bean
	public ExitCodeGenerator exitCodeGenerator() {
		return () -> 42;
	}

	public static void main(String[] args) {
		System.exit(SpringApplication.exit(SpringApplication.run(ExitCodeApplication.class, args)));
	}

}
```

同样的, `ExitCodeGenerator` 接口可以被异常实现.当这个异常发生的时候, Spring Boot 返回 `getExitCode()` 方法中的值作为退出码.

## 23.10 Admin 特性

通过指定  `spring.application.admin.enabled`  属性可以启用 admin-related 特性. 这回暴露[SpringApplicationAdminMXBean`](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/admin/SpringApplicationAdminMXBean.java)  在 `MBeanServer` 平台上. 你可以使用这个功能来远程管理你的 Spring Boot 应用. 这个特性对所有的服务包裹的实现都是很有用的.

> 如果你想要知道应用运行在哪个 HTTP 端口上,可以通过 `local.server.port` 键来获取.

**警告**

当启用了这个特性的时候要小心, MBean 会暴露一个方法可以关闭应用.





[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/22-What-to-Read-Next.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-IV-Spring-Boot-features/24-Externalized-Configuration.md)     



















