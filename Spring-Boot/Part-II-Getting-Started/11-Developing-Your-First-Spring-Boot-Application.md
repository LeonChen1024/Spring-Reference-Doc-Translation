# 11. 开发你的第一个 Spring Boot 应用

这个章节描述了如何开发一个简单的 “Hello World!” web 应用使用了一些 Spring Boot 的关键特性. 我们使用 Maven 来构建这个项目,因为大多数的 IDE 都支持它.

> [spring.io](https://spring.io/) 网站包含了许多的 “Getting Started” 使用了 Spring Boot 的 [指导](https://spring.io/guides) .如果你需要解决一个特定的问题,先查看他们.
>
> 你可以缩短下面的步骤通过使用 [start.spring.io](https://start.spring.io/) 并在依赖搜索里选择 "Web" . 这么做会生成一个新的项目结构,你可以 [直接开始编程](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/getting-started-first-application.html#getting-started-first-application-code). 查阅 [Spring Initializr documentation](https://docs.spring.io/initializr/docs/current/reference/html/#user-guide) 来获取详细内容.

在我们开始之前,打开一个终端并运行下面的命令来确保你已经安装了有效的 Java 和 Maven 版本:

```shell
$ java -version
java version "1.8.0_102"
Java(TM) SE Runtime Environment (build 1.8.0_102-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.102-b14, mixed mode)
```



```shell
$ mvn -v
Apache Maven 3.5.4 (1edded0938998edf8bf061f1ceb3cfdeccf443fe; 2018-06-17T14:33:14-04:00)
Maven home: /usr/local/Cellar/maven/3.3.9/libexec
Java version: 1.8.0_102, vendor: Oracle Corporation
```

> 这个例子需要在它自己的文件夹中创建.后面的指令假设你已经创建了一个合适的文件并且是当前文件夹.

## 11.1 创建 POM 

我们需要从创建一个 Maven `pom.xml` 文件开始. `pom.xml` 是用来构建你的项目的配方. 打开你喜欢的文本编辑器并且添加下面的内容:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>myproject</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.6.RELEASE</version>
	</parent>

	<!-- Additional lines to be added here... -->

</project>
```

前面的步骤会给你一个可以使用的构建环境.你可以通过运行 `mvn package` 来测试它 (就目前而言,你可以忽略 “jar will be empty - no content was marked for inclusion!” 警告).

> 到了这一步,你可以把项目导入到 IDE 中(最现代的 Java IDE 都包含了内置的 Maven 支持). 从便捷性考虑,我们继续使用纯文本编辑器来进行这个示例.

## 11.2 添加 Classpath 依赖

Spring Boot 提供了一定数量的  “Starters” 来让你将 jar 添加到你的 classpath 中. 我们的示例应用已经在 POM 的 `parent` 元素中使用了 `spring-boot-starter-parent` . `spring-boot-starter-parent` 是一个特殊的 starter ,它提供了有用的默认Maven 配置. 它同时提供了一个 [`依赖管理`](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#using-boot-dependency-management) 部分这样你可以"幸福的"管理依赖并忽略 `version` 标签 .

其他的 “Starters” 提供了当你在开发某些特殊类型的应用时需要的依赖. 因为我们是在开发一个 web 应用,我们添加了一个 `spring-boot-starter-web` 依赖. 在这之前,我们可以通过下面的命令来查看当前我们拥有的内容:

```shell
$ mvn dependency:tree

[INFO] com.example:myproject:jar:0.0.1-SNAPSHOT
```

 `mvn dependency:tree` 命令打印一个树来表示你项目中的依赖 . 你可以看到 `spring-boot-starter-parent` 本身并没有提供任何的依赖. 为了添加必须的依赖,编辑你的  `pom.xml` 并且添加 `spring-boot-starter-web` 依赖到  `parent` 的下面:

```xml
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
</dependencies>
```



## 11.3 编写代码

为了完成我们的应用,我们需要创建一个 Java 文件.默认情况下, Maven 从 `src/main/java` 目录下编译文件, 所以你需要创建这样的一个文件夹结构然后添加一个文件为 `src/main/java/Example.java` 并包含以下代码:

```java
import org.springframework.boot.*;
import org.springframework.boot.autoconfigure.*;
import org.springframework.web.bind.annotation.*;

@RestController
@EnableAutoConfiguration
public class Example {

	@RequestMapping("/")
	String home() {
		return "Hello World!";
	}

	public static void main(String[] args) {
		SpringApplication.run(Example.class, args);
	}

}
```

尽管这里没有太多的代码,但是却发生了很多事情.我们将会在接下去的几个章节中介绍几个重要的部分.

### 11.3.1  @RestController 和 @RequestMapping 注解

我们的 `Example` 类的第一个注解是 `@RestController`. 这是一个模板注解. 它给阅读Spring代码的用户提供了一个提示说这个类扮演了一个特殊的角色.在这个例子中,我们的类是一个 web `@Controller`, 所以 Spring 会将它用作处理web 请求.

 `@RequestMapping` 注解提供了一个"路由" 信息. 它告诉 Spring 所有的带有 `/` 路径的 HTTP 请求 要映射到 `home` 方法. `@RestController` 注解告诉 Spring 将结果字符串直接返回给调用者.

>  `@RestController` 和 `@RequestMapping` 注解都是 Spring MVC 的注解. (他们并不是 Spring Boot 特有的.) 查看Spring 参考文档中的 [MVC 章节](https://docs.spring.io/spring/docs/5.1.8.RELEASE/spring-framework-reference/web.html#mvc) 来获取更多细节.



### 11.3.2  @EnableAutoConfiguration 注解

第二个类级注解是 `@EnableAutoConfiguration`. 这个注解告诉 Spring Boot 来"猜测"你想要怎么配置 Spring, 依据是你添加的 jar 依赖.因为 `spring-boot-starter-web` 添加了 Tomcat 和 Spring MVC,  auto-configuration 假设你正在开发一个web 应用并根据这个条件配置 Spring.

**Starters 和 Auto-configuration**

Auto-configuration 是设计用来和 “Starters” 配合使用的,但是这两个概念并没有直接绑定. 你可以自由的选择 starters 之外的 jar 依赖. Spring Boot 仍然会尽力自动配置你的应用.



### 11.3.3  “main” 方法

我们应用的最后一部分是 `main` 方法. 这就是一个依据 Java 惯例的一个标准的方法来作为应用入口. 我们的 main 方法通过调用 Spring Boot’s `SpringApplication` 类的 `run` 方法委派  `SpringApplication`  来作处理. `SpringApplication` 引导我们的应用,启动 Spring, 并且反过来启动自动配置的Tomcat web 服务器. 我们需要将 `Example.class` 作为一个参数传入到 `run` 方法来告诉 `SpringApplication` 它是主要的 Spring 组件.  `args` 数组同时传入该方法来暴露任意的命令行参数.



## 11.4 运行这个示例

到了这一步,你的应用应该可以工作了.因为你使用了 `spring-boot-starter-parent` POM, 你有一个有用的 `run` 方式让你可以用来启动这个应用. 在项目根目录中输入 `mvn spring-boot:run` 来启动这个应用. 你应该会看到与下面类似的输出:

```
$ mvn spring-boot:run

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v2.1.6.RELEASE)
....... . . .
....... . . . (log output here)
....... . . .
........ Started Example in 2.222 seconds (JVM running for 6.514)
```

如果你打开了一个 web 浏览器访问 `localhost:8080`, 你应该会看到如下输出:

```
Hello World!
```

要优雅的结束这个应用,使用 `ctrl-c`.

## 

## 11.5 创建一个可执行的 Jar

我们通过创建一个包含了所有自身所需的在生产环境也可执行的 jar 文件完成了这个例子.可执行 jar (有时候也叫做 “fat jars”) 是一个包含了编译后类还有所有你代码中所有的 jar 依赖的文件归档.

**可执行 jars 和 Java**

Java 并没有提供一个标准的方式来加载嵌套的jar文件 (jar 文件他们自身就包含在一个 jar 中). 如果你要发布独立的应用这可能会存在问题.

为了解决这个问题,许多开发者使用 “uber” jar. 一个 uber jar 会打包所有的应用依赖中的类到一个归档里. 使用这种解决方式的问题在于会导致很难看出你的应用中包含了那些库.它还会导致当一个不同 jar 中的相同文件名(内容不同)被使用时会出现问题.

Spring Boot 采用了一个 [不同的方式](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/executable-jar.html) 让你可以直接嵌套jar 文件.

为了创建一个可执行 jar 文件,我们需要添加 `spring-boot-maven-plugin` 到我们的 `pom.xml`. 为了达到这个效果, 插入下面几行到你的 `dependencies`节点后:

```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```

>  `spring-boot-starter-parent` POM 包含了 `<executions>` 配置来绑定 `repackage` 目标 . 如果你不使用 parent POM, 你需要自己定义这个配置. 查阅 [plugin documentation](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/maven-plugin/usage.html) 来获取更多细节.

保存你的 `pom.xml` 并且从命令行运行 `mvn package` , 如下:

```shell
$ mvn package

[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building myproject 0.0.1-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] .... ..
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ myproject ---
[INFO] Building jar: /Users/developer/example/spring-boot-example/target/myproject-0.0.1-SNAPSHOT.jar
[INFO]
[INFO] --- spring-boot-maven-plugin:2.1.6.RELEASE:repackage (default) @ myproject ---
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
```

如果你去看 `target` 文件夹, 你应该会看到 `myproject-0.0.1-SNAPSHOT.jar`. 这个文件大约有 10 MB左右大小. 如果你想要看里面的东西,你可以使用 `jar tvf`, 如下:

```shell
$ jar tvf target/myproject-0.0.1-SNAPSHOT.jar
```

你应该还会看到一个更小的文件名为 `myproject-0.0.1-SNAPSHOT.jar.original` 在 `target` 文件夹里.这个是 Maven 在它被 Spring Boot重新打包之前生成的.

为了运行这个应用,使用 `java -jar` 命令,如下:

```
$ java -jar target/myproject-0.0.1-SNAPSHOT.jar

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v2.1.6.RELEASE)
....... . . .
....... . . . (log output here)
....... . . .
........ Started Example in 2.536 seconds (JVM running for 2.864)
```

如同前面一样,要退出这个应用,按 `ctrl-c`.





# Reference

https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/getting-started-first-application.html