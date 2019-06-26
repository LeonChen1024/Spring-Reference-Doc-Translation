# 10. Installing Spring Boot

Spring Boot 可以在"经典"的 Java 开发工具中使用,也可以通过命令行工具来安装.不管是什么方式,你都需要 [Java SDK v1.8](https://www.java.com/) 或者更高.在你开始之前,你要通过下面的名利来检查你当前的 Java 版本:

```shell
$ java -version
```



如果你刚刚开始进行 Java 开发或者你想要体验一下 Spring Boot,你可以会想先使用  [Spring Boot CLI](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/getting-started-installing-spring-boot.html#getting-started-installing-the-cli) (Command Line Interface) . 如果不是,清阅读给 "经典" 的安装说明.



## 10.1 给 Java 开发者的安装说明

你可以像使用其他所有的 Java 标准库一样使用 Spring Boot .这样做的话,清在你的 classpath 中包含适合的 `spring-boot-*.jar` 文件. Spring Boot 不需要集成任何特殊的工具,所以你可以使用任意的 IDE 或者文本编辑器.同样的,Spring Boot 应用也没有任何特殊的地方,所以你可以像使用其他 Java 程序一样运行和debug 一个 Spring Boot 应用.

虽然你可以复制 Spring Boot jar 文件,我们通常还是推荐你使用支持依赖管理的构建工具(比如  Maven 或 Gradle)



## 10.1.1 Maven 安装

Spring Boot 兼容 Apache Maven 3.3 或者更高.如果你还没有安装 Maven ,你可以按照  [maven.apache.org](https://maven.apache.org/) 的说明进行安装.

> 在很多操作系统中, Maven 可以通过包管理来进行安装.如果你使用 OSX Homebrew ,试试  `brew install maven`.  Ubuntu 用户可以运行 `sudo apt-get install maven`.  Windows 用户可以使用 提高权限到管理员 在  [Chocolatey](https://chocolatey.org/)  中运行  `choco install maven` .

Spring Boot 依赖使用  `org.springframework.boot` `groupId`. 通常情况下, 你的 Maven POM 文件继承自 `spring-boot-starter-parent` 项目并且声明一个或多个的[“Starters”](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#using-boot-starter). Spring Boot 还提供了一个可选的 [Maven plugin](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/build-tool-plugins-maven-plugin.html) 来创建可运行的 jar 文件.

下面展示了一个通常的 pom.xml 文件:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>myproject</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<!-- Inherit defaults from Spring Boot -->
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.6.RELEASE</version>
	</parent>

	<!-- Add typical dependencies for a web application -->
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
	</dependencies>

	<!-- Package as an executable jar -->
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



> `spring-boot-starter-parent` 是使用 Spring Boot 的一个很好的方式, 但是它可能不适用于所有的情况. 有的时候你可能需要继承一个不同的 POM, 或者你可能不喜欢我们的默认设置. 在这些情况下, 查阅 [ 13.2.2  章节, “在没有 Parent POM的情况下使用 Spring Boot ”](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#using-boot-maven-without-a-parent)  来获得一个使用  `import` 作用域的可选方案.



## 10.1.2 Gradle 安装



Spring Boot 兼容 Gradle 4.4 及更高版本. 如果你没有安装 Gradle ,你可以跟着 [gradle.org](https://gradle.org/) 的说明来进行安安装.

Spring Boot 依赖可以通过 `org.springframework.boot` `group` 来声明. 通常情况下, 你的项目声明一个或多个的 [“Starters”](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#using-boot-starter). Spring Boot 提供了一个有用的 [Gradle plugin](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/build-tool-plugins-gradle-plugin.html) 可以用来简化依赖声明以及创建一个可执行的 jar 文件.

**Gradle Wrapper**

Gradle Wrapper 提供了一个当你在需要构建项目的时候"包含" Gradle 好用的方法. 它是一个小脚本和库使得你可以将它和代码一起提交来引导你的构建进程. 查阅 docs.gradle.org/4.2.1/userguide/gradle_wrapper.html](https://docs.gradle.org/4.2.1/userguide/gradle_wrapper.html) 来获取更多相关内容.

入门 Spring Boot 和 Gradle 的更多内容可以在Gradle 插件的参考指南的 [开始章节](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/gradle-plugin/reference/html/#getting-started) 中找到.

## 

## 10.2 安装 Spring Boot CLI

Spring Boot CLI (Command Line Interface) 是一个命令行工具,你可以用它来快速进行原型构建.它使得你可以运行 [Groovy](http://groovy-lang.org/) 脚本, 这意味着你可以使用一个类 Java 的语法而不用使用那么多的样板代码.















# Resource

https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/getting-started-installing-spring-boot.html