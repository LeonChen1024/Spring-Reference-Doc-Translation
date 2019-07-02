#  20. 开发者工具



[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/18-Using-the-SpringBootApplication-Annotation.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/20-Developer-Tools.md)     



Spring Boot 包含了一系列额外工具来让你的应用开发体验更愉快一些. `spring-boot-devtools` 模块可以被用在任何项目中来提供一个额外的开发时特性. 为了使用开发工具支持, 添加模块依赖到你的构建配置中,如下所示是 Maven 和 Gradle 的清单:

**Maven.** 

```xml
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-devtools</artifactId>
		<optional>true</optional>
	</dependency>
</dependencies>
```



**Gradle.** 

```groovy
configurations {
	developmentOnly
	runtimeClasspath {
		extendsFrom developmentOnly
	}
}
dependencies {
	developmentOnly("org.springframework.boot:spring-boot-devtools")
}
```



> 开发工具在你运行一个完整的打包应用的时候会自动禁用.如果你的应用是以 `java -jar` 或者从一个特殊的类加载器启动的话,那么它会被认为是一个"生产应用".如果这个情况不适合你(比如,你是从一个容器中运行你的应用),可以考虑派出开发工具或者设置 `-Dspring.devtools.restart.enabled=false` 系统属性.



> 将这个依赖在Maven 中标志为可选项或者在 Gradle 中使用一个自定义的 `developmentOnly` 配置都可以很好的防止开发工具被引用了你的项目的模块使用.



> 重新打包文件默认不包含开发工具.如果你想要使用一个  [远程开发工具功能](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-devtools.html#using-boot-devtools-remote), 你需要在构建属性中禁用  `excludeDevtools` .这个属性同时适用于 Maven 和 Gradle 插件. 



## 20.1 默认属性

Spring Boot 提供的一些库会使用缓存来提高性能. 比如, [模板引擎](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-developing-web-applications.html#boot-features-spring-mvc-template-engines) 会缓存编译后的模板来避免重复的解析模板文件.同样的,Spring MVC 可以添加 HTTP 缓存头来响应那些静态资源.

虽然缓存在生产环境是非常有好处的,但是它在开发的时候就会有反作用,它会阻止你看到你刚刚才对应用进行的修改.出于这一点,spring-boot-devtools 默认禁用了缓存选项.

缓存选项通常在你的 `application.properties` 文件中配置. 比如,Thymeleaf 提供了 `spring.thymeleaf.cache` 属性.  `spring-boot-devtools` 模块自动提供了合适的开发时配置而不是让你手动去设置这些属性.

因为你在开发 Spring MVC 和 Spring WebFlux 应用的时候需要更多的 web 请求信息, 开发者工具将启用 `DEBUG` 日志给 `web` 日志组. 这会给你有关于传入请求,  handler 正在处理它, 响应结果输出等信息. 如果你想要打印出所有的请求细节 (包括隐式的敏感信息),你可以打开 `spring.http.log-request-details` 配置属性.



> 如果你不想使用默认属性,你可以在 `application.properties` 文件中设置  `spring.devtools.add-properties` 的值为 `false`  .

> 要获取 devtool 提供的完整的属性列表,查阅  [DevToolsPropertyDefaultsPostProcessor](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-devtools/src/main/java/org/springframework/boot/devtools/env/DevToolsPropertyDefaultsPostProcessor.java).



## 20.2 自动重启

使用了 `spring-boot-devtools` 的应用当 classpath 中的文件改变的时候会自动重启. 当你在 IDE 上使用的时候这会是一个有用的功能,因为它对代码的改变提供了一个非常快的反馈. 默认情况下,classpath 下的人以文件都指向了一个被监控文件改变的文件夹. 请注意某些资源,比如静态资源和试图模板,[不需要重启应用](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-devtools.html#using-boot-devtools-restart-exclude).



```
触发重启

对于 DevTools 监控的 classpath 资源, 触发重启的唯一条件是更新 classpath. 导致 classpath 更新的方式是取决于你使用的 IDE .在Eclipse中, 保存一个修改过的文件会导致classpath 更新并触发一个重启. 在 IntelliJ IDEA 中, 构建项目 (Build -> Build Project) 拥有同样的效果.
```



> 只要启用了 forking , 你就可以使用受支持的构建插件启动你的应用(Maven 和 Gradle),因为 DevTool 需要一个独立的应用类加载器来正常的运行.默认情况下,Gradle 和 Maven 会在他们检测到 DevTool 在他们的 classpath 中的时候会启用这种做法.



> 自动重启在使用 LiveReload 的时候可以正常使用.[阅读 LiveReload 章节](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-devtools.html#using-boot-devtools-livereload) 来获取更多细节.如果你使用了 JRebel ,禁用自动重启以支持动态类重载.其他的 devtool 功能(比如 LiveReload 和 属性重写) 仍然可以正常使用.



> DevTool 依赖于应用的上下文的关闭钩子来在他重启的时候关闭应用.如果你禁用了关闭钩子(`SpringApplication.setRegisterShutdownHook(false)`)它将无法正常运行.



> 当决定一个 classpath 中的条目改变的时候是否应该触发重启,DevTool 自动忽略了名为  `spring-boot`,`spring-boot-devtools`, `spring-boot-autoconfigure`, `spring-boot-actuator`, 和 `spring-boot-starter` 的项目.



> DevTool 需要自定义  `ApplicationContext` 使用的  `ResourceLoader` . 如果你的应用已经提供了,它会被包裹.直接重写 `ApplicationContext` 的 `getResource` 方法是没有用的.



```
重启和重载

Spring Boot 提供的重载技术是通过两个类加载器来实现的. 那些不会变的类(比如,第三方 jar)会被加载到一个基础类加载器中.你正在开发的类会被加载到一个重启类加载器中. 当应用重启的时候,重启类加载器会被丢弃并创建一个新的出来.这个方式意味着应用重启比通常的"冷启动"要更快,因为基础类加载器已经可以使用并运行了.

如果你发现重启你的应用的速度不够快或者你碰到了类加载问题,你可以考虑使用重载技术比如 ZeroTurnaround 的JRebel .它们通过重写加载的类来工作.

```



### 20.2.1 打印环境的改变

默认情况下,每一次你重启了应用,会有一个报告展示环境等的改变.这个报告展示了你应用的自动配置的改变比如你添加或删除了 bean 和配置设置属性.

为了禁用打印这份报告,设置如下属性:

```properties
spring.devtools.restart.log-condition-evaluation-delta=false
```

### 

### 20.2.2 排除属性

某些资源改变的时候并不需要触发重启. 比如,Thymeleaf 模板可以直接编辑. 默认情况下,改变 `/META-INF/maven`, `/META-INF/resources`, `/resources`, `/static`, `/public`, 或 `/templates` 中的资源不需要触发重启但是要触发 [live reload](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-devtools.html#using-boot-devtools-livereload). 如果你想要自定义这些排除项,你可以使用 `spring.devtools.restart.exclude` 属性.比如,你只想要 `/static` 和  `/public` 你可以设置如下属性:

```prop
spring.devtools.restart.exclude=static/**,public/**
```



> 如果你想要保留默认排除项并额外增加一些,使用 `spring.devtools.restart.additional-exclude` 来代替



### 20.2.3 Watching Additional Paths

























































