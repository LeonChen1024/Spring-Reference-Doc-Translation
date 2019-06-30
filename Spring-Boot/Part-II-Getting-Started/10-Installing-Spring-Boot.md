# 10. Installing Spring Boot





[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-II-Getting-Started/9-System-Requirements.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-II-Getting-Started/11-Developing-Your-First-Spring-Boot-Application.md)     



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



## 10.2 安装 Spring Boot CLI

Spring Boot CLI (Command Line Interface) 是一个命令行工具,你可以用它来快速进行原型构建.它使得你可以运行 [Groovy](http://groovy-lang.org/) 脚本, 这意味着你可以使用一个类 Java 的语法而不用使用那么多的样板代码.

你不一定需要使用 CLI来使用 Spring Boot, 但是它肯定是实现一个 Spring 应用的最快方式.

### 10.2.1 手动安装



你可以从 Spring 软件库下载 Spring CLI :

- [spring-boot-cli-2.1.6.RELEASE-bin.zip](https://repo.spring.io/release/org/springframework/boot/spring-boot-cli/2.1.6.RELEASE/spring-boot-cli-2.1.6.RELEASE-bin.zip)
- [spring-boot-cli-2.1.6.RELEASE-bin.tar.gz](https://repo.spring.io/release/org/springframework/boot/spring-boot-cli/2.1.6.RELEASE/spring-boot-cli-2.1.6.RELEASE-bin.tar.gz)

 [snapshot distributions](https://repo.spring.io/snapshot/org/springframework/boot/spring-boot-cli/) 中的快照也同样可以使用.

一旦下载成功后,按照解压后的 [INSTALL.txt](https://raw.github.com/spring-projects/spring-boot/v2.1.6.RELEASE/spring-boot-project/spring-boot-cli/src/main/content/INSTALL.txt) 的安装说明来操作. 总的来说,.zip 文件中会有一个 `spring` 脚本 (`spring.bat` 给 Windows 使用) 在 `bin/ ` 目录中. 或者,你可以使用 `java -jar` 来执行 `.jar` 文件 (一个帮助你确认 classpath 设置正确的脚本).



### 10.2.2 使用 SDKMAN 安装!

SDKMAN! (软件开发套件管理工具) 可以用来管理多个版本的各种二进制 SDK, 包括 Groovy 还有 Spring Boot CLI. 可以从  [sdkman.io](https://sdkman.io/) 获取 SDKMAN! 并通过下面的命令来安装 Spring Boot :

```shell
$ sdk install springboot
$ spring --version
Spring Boot v2.1.6.RELEASE
```

如果你为 CLI 开发功能并且希望可以轻松的访问你构建的版本,使用下列命令:

```shell
$ sdk install springboot dev /path/to/spring-boot/spring-boot-cli/target/spring-boot-cli-2.1.6.RELEASE-bin/spring-2.1.6.RELEASE/
$ sdk default springboot dev
$ spring --version
Spring CLI v2.1.6.RELEASE
```

前面的指令安装了一个本地的 `spring` 实例叫做 `dev` . 它指向你指定的构建地址,这样你每次重新构建 Spring Boot,`spring` 将会自动更新.

你可以通过下列指令来看到这个效果:

```shell
$ sdk ls springboot

================================================================================
Available Springboot Versions
================================================================================
> + dev
* 2.1.6.RELEASE

================================================================================
+ - local version
* - installed
> - currently in use
================================================================================
```

### 10.2.3 OSX Homebrew 安装

如果你是在 Mac 上并且使用了 [Homebrew](https://brew.sh/), 你可以通过下列指令来安装 Spring Boot CLI:

```shell
$ brew tap pivotal/tap
$ brew install springboot
```

Homebrew 将 `spring` 安装在 `/usr/local/bin`.

> 如果你没有看到该结果,你的 brew 可能已经过时了.在这种情况下,运行 `brew update` 并重试.



### 10.2.4 使用 MacPorts 安装

如果你是在 Mac 平台上并使用 [MacPorts](https://www.macports.org/), 你可以通过下面的命令来安装 Spring Boot CLI :

```shell
$ sudo port install spring-boot-cli
```

### 10.2.5 命令行完成

Spring Boot CLI 包含了提供给 [BASH](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) 和[zsh](https://en.wikipedia.org/wiki/Z_shell) 使用的脚本. 你可以 `source` 脚本 (也叫做 `spring`) 在任意的 shell 或者将它放在你个人或系统的 bash 中来完成初始化.在 Debian 系统中 , 系统级的脚本位于 `/shell-completion/bash` 并且在这个目录中的脚本在新的 shell 启动的时候会全部运行.比如, 手动运行脚本如果你是通过 SDKMAN!安装的, 可以使用下面的脚本:

```shell
$ . ~/.sdkman/candidates/springboot/current/shell-completion/bash/spring
$ spring <HIT TAB HERE>
  grab  help  jar  run  test  version
```

> 如果你是通过 HomeBrew 或者 MacPorts 来安装的 Spring Boot CLI,命令行完成脚本会自动注册到你的 shell 中.



### 10.2.6 使用 Windows Scoop 安装

如果你是使用  Windows 并使用了 [Scoop](https://scoop.sh/), 你可以通过下面命令来安装 Spring Boot CLI:

```shell
> scoop bucket add extras
> scoop install springboot
```

Scoop 会将 `spring` 安装到 `~/scoop/apps/springboot/current/bin`.

> 如果你没有看见 app manifest, 你的 scoop 可以已经过时了.这种情况下,运行 `scoop update` 并重试.



### 10.2.7 快速开始 Spring CLI 示例

你可以使用下面的 web 应用来测试你的安装情况. 创建一个 `app.groovy` 文件来开始这个测试, 如下:

```java
@RestController
class ThisWillActuallyRun {

	@RequestMapping("/")
	String home() {
		"Hello World!"
	}

}
```

然后通过 shell 来运行它:

```shell
$ spring run app.groovy
```



> 第一次运行你的应用时是很慢的,当依赖下载完了.之后的运行将会快很多.

在你喜欢的web 浏览器中打开 `localhost:8080` . 你应该会看到下面的输出:

```
Hello World!
```



## 10.3 从早期的 Spring Boot 版本升级

如果你是从早期的 Spring Boot 的release 版本升级而来的, 可以查看 [项目wiki中的“migration guide” ](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide),它提供了详细的升级指南.同时查看 [“release notes”](https://github.com/spring-projects/spring-boot/wiki) 来了解每一个release 版本中的一些 “新功能和值得注意的地方” .

当升级到一个新的发布版本,一些属性可能被重命名或者移除.Spring Boot 提供了一种分析你的应用环境并在启动时打印诊断信息的方式,但同时也支持在运行时迁移属性.要打开这个功能,添加下面的依赖到你的项目中:

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-properties-migrator</artifactId>
	<scope>runtime</scope>
</dependency>
```

> 在环境后期添加的属性比如  `@PropertySource`, 将不会被计算在内.

> 一旦你完成了迁移,请确保从你的项目依赖中删除这个模块.

为了升级一个已经存在的  CLI , 要使用恰当的包管理命令 (比如, `brew upgrade`) 或者, 如果你是手动安装的 CLI,按照 [standard instructions](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/getting-started-installing-spring-boot.html#getting-started-manual-cli-installation) 来进行升级, 记得更新你的  `PATH` 环境变量来删除旧的引用.







[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-II-Getting-Started/9-System-Requirements.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-II-Getting-Started/11-Developing-Your-First-Spring-Boot-Application.md)     





# Resource

https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/getting-started-installing-spring-boot.html