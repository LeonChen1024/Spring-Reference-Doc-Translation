# 19. 运行你的应用



[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/17-Spring-Beans-and-Dependency-Injection.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/19-Running-Your-Application.md)     



将你的应用以一个 jar 的形式打包并在一个内置的 HTTP 服务器中运行的一个最大的好处就是你可以像运行其他Java 代码一样运行你的应用.调试 Spring Boot 应用也是同样的轻松. 你不需要其他制定的 IDE 插件或者拓展程序.



> 这个章节只介绍了基于 jar 的打包.如果你选择将你的应用打包成 war 文件,你要参考你的服务器和 IDE 的文档.

## 19.1 从 IDE 运行

你可以像运行一个简单的 Java 应用一样在IDE 中运行 Spring Boot. 然而,你需要县导入你的项目.导入的步骤根据你的 IDE 和构建系统的不同而有所差异. 大部分 IDE 可以直接导入 Maven 项目. 比如, Eclipse 用户可以在 `File` 菜单中选择 `Import…` → `Existing Maven Projects` .

如果你不能直接将你的项目导入到你的 IDE 中,你可能可以通过构建插件来生成 IDE  metadata . Maven 包含给 [Eclipse](https://maven.apache.org/plugins/maven-eclipse-plugin/) 和 [IDEA](https://maven.apache.org/plugins/maven-idea-plugin/) 使用的插件. Gradle 提供了 [许多 IDEs](https://docs.gradle.org/4.2.1/userguide/userguide.html) 的插件.

> 如果你失误运行了一个web应用两次,你会看到一个 “Port already in use”  异常. STS 用户可以使用 `Relaunch` 按钮而不是 `Run` 按钮来确保其他存在的实例都被关闭了. 

## 19.2 作为一个打包后的应用

如果你使用 Spring Boot Maven 或者 Gradle 插件来创建一个可执行jar文件,你可以通过使用 `java -jar` 来运行它, 如下所示:

```shell
$ java -jar target/myapplication-0.0.1-SNAPSHOT.jar
```

它同样支持以开启远程调试的模式启动打包后的应用.这么做允许你在你的打包后应用中获得一个调试器,如下所示:

```shell
$ java -Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n \
       -jar target/myapplication-0.0.1-SNAPSHOT.jar
```



## 19.3 使用 Maven 插件运行

Spring Boot Maven 插件包含一个 `run`  可以用来快速编译和运行你的应用.应用使用拆分的模式运行,就像在你的 IDE 中一样. 下面的例子展示了一个常见的 Maven 指令用于运行一个 Spring Boot 应用:

```shell
$ mvn spring-boot:run
```

你可能还想使用 `MAVEN_OPTS`  操作系统环境变量, 如下所示:

```shell
$ export MAVEN_OPTS=-Xmx1024m
```

## 19.4 使用 Gradle 插件运行

 Spring Boot Gradle 插件同样包含了一个 `bootRun`  任务可以用来以拆分模式运行你的应用.  `bootRun` 任务会被添加到所有你提供了 `org.springframework.boot` 和 `java` 插件的地方,如下所示: 

```shell
$ gradle bootRun
```

你可能还想使用 `JAVA_OPTS` 操作系统环境变量,如下所示:

```shell
$ export JAVA_OPTS=-Xmx1024m
```

## 19.5 Hot Swapping (热部署)

因为 Spring Boot 应用是一个纯Java 应用,JVM hot-swapping 也是开相继用的. JVM hot swapping 受限于它可以替换的字节码. 想要一个更完整的解决方案,可以使, [JRebel](https://zeroturnaround.com/software/jrebel/) .

 `spring-boot-devtools` 模块同样包含了快速重启应用的支持. 可以查阅 [ 20章, *开发者工具*](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-devtools.html) 和 [Hot swapping “How-to”](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/howto-hotswapping.html) 来获取更多细节.







[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/18-Using-the-SpringBootApplication-Annotation.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/20-Developer-Tools.md)     











