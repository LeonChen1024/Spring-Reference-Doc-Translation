#  20. 开发者工具



[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/19-Running-Your-Application.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/21-Packaging-Your-Application-for-Production.md)     



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



### 20.2.3 监控其他的路径

你可能想要在你对一些不在 classpath 中的文件进行修改的时候也会触发重启和重载. 为了实现这个目的,使用 `spring.devtools.restart.additional-paths`  属性来配置额外的监控路径.你可以是使用`spring.devtools.restart.exclude` 属性来 [提前描述](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-devtools.html#using-boot-devtools-restart-exclude) 额外的路径的改变是否会触发一个完全重启或者一个 [live reload](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-devtools.html#using-boot-devtools-livereload).

### 20.2.4 禁用重启

如果你不想使用重启功能,你可以通过使用 `spring.devtools.restart.enabled` 属性来禁用它. 在多数情况下,你可以在你的 `application.properties` 中设置这个属性(这么做仍然会初始化重启类加载器,但是它不会监控文件的变化).

如果你想要完全禁用这个重启支持(比如,因为它和某个库有冲突), 你需要在 `SpringApplication.run(…)` 之前设置 `spring.devtools.restart.enabled` `System` 属性值为 `false` . 如下所示:

```java
public static void main(String[] args) {
	System.setProperty("spring.devtools.restart.enabled", "false");
	SpringApplication.run(MyApp.class, args);
}
```

### 20.2.5 使用触发文件

如果是在支持持续编译改变的文件的 IDE 下工作的,你可能会想要只在指定的时间进行重启. 为了实现这个目的,你可以使用一个 “触发文件”, 它是一个特殊的文件必须在你想要触发一个重启任务的时候去修改它.改变这个文件指挥触发重启检测,重启只会发生在检测到了 Devtool 并且它是启用的状态.触发文件可以手动更新或者使用 IDE 插件更新.

为了使用触发文件,设置 `spring.devtools.restart.trigger-file`  属性的值为你的触发文件的路径.

> 你可能想要设置  `spring.devtools.restart.trigger-file` 作为一个 [全局设置](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-devtools.html#using-boot-devtools-globalsettings), 这样你所有的项目都会有相同的行为方式.



### 20.2.6 自定义重启加载器

如同前面 [重启 vs 重载](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-devtools.html#using-spring-boot-restart-vs-reload) 章节中描述的, 重启功能是通过使用两个类加载器实现的. 对于大多数的应用, 这个方式可以正常的工作.然而,它有的时候会导致类加载异常.

默认情况下,在你的 IDE 中打开的项目是加载到 "重启"类加载器中,其他常规的 `.jar` 文件是加载到 “基础” 类加载器中. 如果你是在一个多模块项目中工作,并且并不是所有的模块都导入到了 IDE 中,你可能需要自定义一些东西.你可以通过创建一个 `META-INF/spring-devtools.properties` 文件来实现.

 `spring-devtools.properties` 文件可以包含前缀为 `restart.exclude` 和 `restart.include` 的属性.  `include` 元素是应该要被加载到 "重载"类加载器的元素, `exclude` 中的元素是要被加载到 “基础” 类加载器的条目. 属性的值是一个从classpath 的正则表达式,如下所示:

```properties
restart.exclude.companycommonlibs=/mycorp-common-[\\w\\d-\.]+\.jar
restart.include.projectcommon=/mycorp-myproj-[\\w\\d-\.]+\.jar
```



> 所有的属性的键都必须是唯一的.只要属性是以  `restart.include.` 或者 `restart.exclude.` 开头就可以了.

> classpath 中所有的 `META-INF/spring-devtools.properties` 都会被加载.你可以在你的项目中打包文件,或者在项目中使用的库里.

### 20.2.7 已知的限制

重启功能在有通过标准  `ObjectInputStream` 反序列化的对象的情况下无法正常使用. 如果你需要反序列化数据,你可能需要使用  Spring 的 `ConfigurableObjectInputStream` 以及  `Thread.currentThread().getContextClassLoader()`  .

不幸的是,有些第三方库反序列化的时候并没有考虑到上下文的类加载器. 如果你发现了这样的问题,你需要向原作者请求修复.

## 20.3 LiveReload

 `spring-boot-devtools` 模块包含了内置的 LiveReload 服务器可以用来在一个资源改变的时候触发浏览器刷新.LiveReload 浏览器拓展程序在 [livereload.com](http://livereload.com/extensions/) 中免费支持 Chrome, Firefox 还有 Safari .

如果你不想要在你的应用运行的时候启动 LiveReload 服务, 你可以设置 `spring.devtools.livereload.enabled` 属性值为 `false`.

> 你一次只可以运行一个 LiveReload 服务.在你启动你的应用之前,确保没有其他的 LiveReload 服务在运行.如果你从 IDE 中启动了多个应用,只有一个 LiveReload 是可以正常运行的

## 20.4 全局设置

你可以配置一个全局的 devtool 设置,通过添加一个名为 `.spring-boot-devtools.properties` 的文件到你的 `$HOME` 文件夹中 (记得文件名是以"."开头). 这个文件里的所有的属性都会被添加到你机器中使用devtool 的Spring Boot 应用中. 比如,配置全局使用[触发文件](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-devtools.html#using-boot-devtools-restart-triggerfile) 来重启的属性, 你可以添加以下属性:

**~/.spring-boot-devtools.properties.** 

```properties
spring.devtools.reload.trigger-file=.reloadtrigger
```



>  `.spring-boot-devtools.properties` 中使用的属性不会影响 [指定配置文件](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-external-config.html#boot-features-external-config-profile-specific-properties) 的加载.



## 20.5 远程应用

 Spring Boot 开发者工具不仅仅局限于本地开发. 你还可以使用一些远程应用的功能.远程支持是一个可选项.为了开启它,你需要确保 `devtools` 被包含在了重新打包的文件中, 如下所示:

```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
			<configuration>
				<excludeDevtools>false</excludeDevtools>
			</configuration>
		</plugin>
	</plugins>
</build>
```

然后你需要设置一个 `spring.devtools.remote.secret` 属性, 如下所示:

```properties
spring.devtools.remote.secret=mysecret
```

> 在远程应用中启用  `spring-boot-devtools` 是一个有安全风险的操作.你不应该在生产部署中启用这个功能.

远程 devtools 支持主要有两个方面 : 一个服务端的节点接受连接一个客户端应用在你的 IDE 中运行.服务端组件当你设置了 `spring.devtools.remote.secret` 属性的时候会自动启用.客户端组件必须手动开启.

### 20.5.1 运行远程客户端应用

远程应用客户端是设计在 IDE 中运行的.你需要运行 `org.springframework.boot.devtools.RemoteSpringApplication` 并使用和远程项目相同的 classpath . 应用只要一个远程连接的 URL 作为参数就可以了.

比如,如果你使用 Eclipse 或 STS 并且你有一个项目叫做 `my-app` 并且已经部署到云上了,你可以按如下操作:

- 选择 `Run`  菜单中的 `Run Configurations…` .
- 创建一个新的 `Java Application` “启动配置”.
- 浏览 `my-app` .
- 使用 `org.springframework.boot.devtools.RemoteSpringApplication` 作为主类.
- 添加 `https://myapp.cfapps.io` 到 `Program arguments` (或者你的远程 URL ).

 一个运行中的远程端大概如下所示:

```
  .   ____          _                                              __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _          ___               _      \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` |        | _ \___ _ __  ___| |_ ___ \ \ \ \
 \\/  ___)| |_)| | | | | || (_| []::::::[]   / -_) '  \/ _ \  _/ -_) ) ) ) )
  '  |____| .__|_| |_|_| |_\__, |        |_|_\___|_|_|_\___/\__\___|/ / / /
 =========|_|==============|___/===================================/_/_/_/
 :: Spring Boot Remote :: 2.1.6.RELEASE

2015-06-10 18:25:06.632  INFO 14938 --- [           main] o.s.b.devtools.RemoteSpringApplication   : Starting RemoteSpringApplication on pwmbp with PID 14938 (/Users/pwebb/projects/spring-boot/code/spring-boot-devtools/target/classes started by pwebb in /Users/pwebb/projects/spring-boot/code/spring-boot-samples/spring-boot-sample-devtools)
2015-06-10 18:25:06.671  INFO 14938 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@2a17b7b6: startup date [Wed Jun 10 18:25:06 PDT 2015]; root of context hierarchy
2015-06-10 18:25:07.043  WARN 14938 --- [           main] o.s.b.d.r.c.RemoteClientConfiguration    : The connection to http://localhost:8080 is insecure. You should use a URL starting with 'https://'.
2015-06-10 18:25:07.074  INFO 14938 --- [           main] o.s.b.d.a.OptionalLiveReloadServer       : LiveReload server is running on port 35729
2015-06-10 18:25:07.130  INFO 14938 --- [           main] o.s.b.devtools.RemoteSpringApplication   : Started RemoteSpringApplication in 0.74 seconds (JVM running for 1.105)
```



> 因为远程端使用了和真实应用相同的 classpath 它可以直接读取到应用属性.这就是 `spring.devtools.remote.secret` 属性如何读取并传递到服务端做认证的.

> 通常建议使用  `https://` 作为连接协议,这样连接会被加密并且密码不会被拦截.

> 如果你需要使用代理来访问远程应用,配置  `spring.devtools.remote.proxy.host` 和 `spring.devtools.remote.proxy.port` 属性

### 20.5.2 远程更新

远程端监控你应用的 classpath 中的变化,它和本地 [本地重启](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-devtools.html#using-boot-devtools-restart) 使用了相同的方式. 所有更新的资源会推送到远程应用并且(如果需要)触发重启.如果你在迭代一个功能使用了云服务而你的本地没有的时候这个功能就非常的游泳.通常情况下远程更新和重启会比一次完整的重新构建和发布更快.

> 文件只有在远程端运行的时候才会被监控.如果你改变了一个文件在你启动远程端之前,它不会被推送到远程服务器.



[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/19-Running-Your-Application.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/21-Packaging-Your-Application-for-Production.md)     























