# 13. 构建系统

我们强烈推荐你选择一个支持 [*依赖管理*](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#using-boot-dependency-management) 的构建系统并且可以使用 “Maven Central” 中的仓库. 我们推荐你选择 Maven 或者 Gradle. 使用其他的构建系统 (比如Ant)也是可以运行 Spring Boot 的,但是他们没有得到很好的支持.

## 13.1 依赖管理

每个 Spring Boot 的正式版本都会提供一个它支持的依赖列表. 在使用的时候, 你不需要给构建配置中的这些依赖提供版本号,因为 Spring Boot 会替你做这些管理. 当你升级了 Spring Boot ,这些依赖会以统一的方式一起升级.

>  如果你需要,你仍然可以指定一个版本并覆盖 Spring Boot 的推荐版本.

策划列表中包含了所有你可以再 Spring Boot 中配合使用的 Spring 模块,同时包含了可以使用的第三方库. 这个列表是一个标准可用的 [材料清单 (`spring-boot-dependencies`)](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#using-boot-maven-without-a-parent) 可以在 [Maven](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#using-boot-maven-parent-pom) 和 [Gradle](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#using-boot-gradle) 中使用.



> 每一个 Spring Boot 发布版都会关联 一个基础的 Spring 框架.我们高度推荐你不要修改这个版本.



## 13.2 Maven

Maven 用户可以继承 `spring-boot-starter-parent` 项目来获取明智的默认配置. 这个 parent 项目提供了以下特性:

-  默认使用了 Java 1.8 作为编译版本.
- 使用 UTF-8 进行编码.
- 一个 [一个依赖管理节点](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#using-boot-dependency-management), 继承自 spring-boot-dependencies pom, 它管理了常见的依赖版本. 这个以来管理使得你可以在你自己的 pom 中忽略那些依赖的 <version> 标签.
- 可以通过 `repackage` 使用执行 id 来 [`重新打包` ](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/maven-plugin/repackage-mojo.html) .
- 明智的 [资源过滤](https://maven.apache.org/plugins/maven-resources-plugin/examples/filter.html).
- 明智的插件配置 ([exec plugin](https://www.mojohaus.org/exec-maven-plugin/), [Git commit ID](https://github.com/ktoso/maven-git-commit-id-plugin), 还有 [shade](https://maven.apache.org/plugins/maven-shade-plugin/)).
- 对 `application.properties` 和 `application.yml` 包括特定的配置文件 (比如, `application-dev.properties` 和 `application-dev.yml`)进行明智的资源过滤

要注意,因为 `application.properties` 和 `application.yml`文件接收 Spring 风格的占位符 (`${…}`), Maven 过滤会将其修改为 `@..@` 占位符. (你可以通过重写Maven 属性设置文件 `resource.delimiter`.)



### 13.2.1 继承 Starter Parent



为了将你的项目配置成继承自 `spring-boot-starter-parent`, 按照以下方式设置 `parent` 节点:

```xml
<!-- Inherit defaults from Spring Boot -->
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.1.6.RELEASE</version>
</parent>
```

> 你只需要在这个依赖中给 Spring Boot 指定版本即可.如果你导入了额外的starters,你可以安全的忽略他们的版本号.

对于这种设置方式,你同样可以通过在你的项目中重写属性来修改依赖版本.比如,要升级到另一个 Spring Data release 版本, 你可以添加下面的代码到你的 `pom.xml`:

```xml
<properties>
	<spring-data-releasetrain.version>Fowler-SR2</spring-data-releasetrain.version>
</properties>
```

> 
>
> ### 

### 13.2.2 不依靠Parent POM 来使用 Spring Boot

不是每一个人都喜欢继承自 `spring-boot-starter-parent` POM.你可以有自己的标准 parent 或者你更想要明确声明所有的 Maven 配置.

如果你不想使用 `spring-boot-starter-parent`, 如果你仍然想保留依赖管理的好处(但不包含插件管理)可以在依赖中使用 `scope=import` , 如下:

```xml
<dependencyManagement>
	<dependencies>
		<dependency>
			<!-- Import dependency management from Spring Boot -->
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-dependencies</artifactId>
			<version>2.1.6.RELEASE</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
	</dependencies>
</dependencyManagement>
```

前面的例子并不允许你通过属性来重写单独的依赖,如同上面说的. 为了达到这个效果,你要在你的项目中添加一个条目到 `dependencyManagement`  中,并且要放在 `spring-boot-dependencies` 条目之前. 比如,要升级到另一个版本的 Spring Data ,你可以添加下面的元素到你的 `pom.xml`:

```xml
<dependencyManagement>
	<dependencies>
		<!-- Override Spring Data release train provided by Spring Boot -->
		<dependency>
			<groupId>org.springframework.data</groupId>
			<artifactId>spring-data-releasetrain</artifactId>
			<version>Fowler-SR2</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-dependencies</artifactId>
			<version>2.1.6.RELEASE</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
	</dependencies>
</dependencyManagement>
```



> 
>
> ### 



### 13.2.3 使用 Spring Boot Maven 插件

Spring Boot 包含了一个 [Maven 插件](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/build-tool-plugins-maven-plugin.html) ,它可以将项目打包成一个可执行的 jar 文件. 如果你想使用它的话可以在你的 `<plugins>` 节点下添加插件, 如下所示: 

```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build
```

> 
>
> ## 



## 13.3 Gradle

想要在 Spring Boot 中使用 Gradle, 请参考 Spring Boot的 Gradle 插件文档:

- Reference ([HTML](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/gradle-plugin/reference/html) and [PDF](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/gradle-plugin/reference/pdf/spring-boot-gradle-plugin-reference.pdf))
- [API](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/gradle-plugin/api)

 ## 13.4 Ant

使用 Apache Ant+Ivy 也是可以构建一个 Spring Boot 项目的.  `spring-boot-antlib` “AntLib” 模块也可以帮助 Ant 生成一个可执行 jar 文件.

要声明依赖,一个典型的 `ivy.xml` 文件如下所示:

```xml
<ivy-module version="2.0">
	<info organisation="org.springframework.boot" module="spring-boot-sample-ant" />
	<configurations>
		<conf name="compile" description="everything needed to compile this module" />
		<conf name="runtime" extends="compile" description="everything needed to run this module" />
	</configurations>
	<dependencies>
		<dependency org="org.springframework.boot" name="spring-boot-starter"
			rev="${spring-boot.version}" conf="compile" />
	</dependencies>
</ivy-module>
```

一个典型的 `build.xml`  如下所示:

```xml
<project
	xmlns:ivy="antlib:org.apache.ivy.ant"
	xmlns:spring-boot="antlib:org.springframework.boot.ant"
	name="myapp" default="build">

	<property name="spring-boot.version" value="2.1.6.RELEASE" />

	<target name="resolve" description="--> retrieve dependencies with ivy">
		<ivy:retrieve pattern="lib/[conf]/[artifact]-[type]-[revision].[ext]" />
	</target>

	<target name="classpaths" depends="resolve">
		<path id="compile.classpath">
			<fileset dir="lib/compile" includes="*.jar" />
		</path>
	</target>

	<target name="init" depends="classpaths">
		<mkdir dir="build/classes" />
	</target>

	<target name="compile" depends="init" description="compile">
		<javac srcdir="src/main/java" destdir="build/classes" classpathref="compile.classpath" />
	</target>

	<target name="build" depends="compile">
		<spring-boot:exejar destfile="build/myapp.jar" classes="build/classes">
			<spring-boot:lib>
				<fileset dir="lib/runtime" />
			</spring-boot:lib>
		</spring-boot:exejar>
	</target>
</project>
```

> 
>
> ## 

## 13.5 Starters

Starters 是一个你可以在你的应用中使用的方便的依赖描述的集合. 你可以获得所需的所有 Spring 和相关技术的一站式服务,而无需查看示例并复制黏贴所有的依赖描述. 比如,如果你想要开始使用 Spring  和 数据库的JPA,在你的项目依赖中包含 `spring-boot-starter-data-jpa` 就可以了.

 starters 包含了很多你需要用来开始一个项目并可以流畅的运行的依赖, 并支持一系列的管理依赖功能.



**名字里有什么**

所有 **官方** 的starters 都遵循一个相似的命名规则; `spring-boot-starter-*`,  `*` 是一个特殊类型的应用. 这个命名结构是为了帮助你在需要的时候找到一个 starter.  Maven 集成在了许多 IDEs中并允许你通过名字搜索依赖.比如,在有些 Eclipse 中或安装了 STS 插件的 Eclipse,你可以在 POM 编辑器中按下 `ctrl-space` 并输入 “spring-boot-starter” 来获得一个完整的列表.

正如 “[创建你自己的Starter](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-developing-auto-configuration.html#boot-features-custom-starter)” 章节中描述的一样, 第三方的starters 不应该以 `spring-boot` 开头, 因为这是 Spring Boot 官方的 artifacts. 因此,一个第三方的 starter 通常以项目名作为开头. 比如,一个第三方 starter项目叫做 `thirdpartyproject` 通常会命名为 `thirdpartyproject-spring-boot-starter`.

下面的 starters 应用都是由 Spring Boot 提供的,他们都放在 `org.springframework.boot` 组下:

**Table 13.1. Spring Boot starters 应用**

| Name                                          | Description                                                  | Pom                                                          |
| --------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `spring-boot-starter`                         | Core starter, including auto-configuration support, logging and YAML | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter/pom.xml) |
| `spring-boot-starter-activemq`                | Starter for JMS messaging using Apache ActiveMQ              | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-activemq/pom.xml) |
| `spring-boot-starter-amqp`                    | Starter for using Spring AMQP and Rabbit MQ                  | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-amqp/pom.xml) |
| `spring-boot-starter-aop`                     | Starter for aspect-oriented programming with Spring AOP and AspectJ | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-aop/pom.xml) |
| `spring-boot-starter-artemis`                 | Starter for JMS messaging using Apache Artemis               | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-artemis/pom.xml) |
| `spring-boot-starter-batch`                   | Starter for using Spring Batch                               | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-batch/pom.xml) |
| `spring-boot-starter-cache`                   | Starter for using Spring Framework’s caching support         | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-cache/pom.xml) |
| `spring-boot-starter-cloud-connectors`        | Starter for using Spring Cloud Connectors which simplifies connecting to services in cloud platforms like Cloud Foundry and Heroku | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-cloud-connectors/pom.xml) |
| `spring-boot-starter-data-cassandra`          | Starter for using Cassandra distributed database and Spring Data Cassandra | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-cassandra/pom.xml) |
| `spring-boot-starter-data-cassandra-reactive` | Starter for using Cassandra distributed database and Spring Data Cassandra Reactive | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-cassandra-reactive/pom.xml) |
| `spring-boot-starter-data-couchbase`          | Starter for using Couchbase document-oriented database and Spring Data Couchbase | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-couchbase/pom.xml) |
| `spring-boot-starter-data-couchbase-reactive` | Starter for using Couchbase document-oriented database and Spring Data Couchbase Reactive | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-couchbase-reactive/pom.xml) |
| `spring-boot-starter-data-elasticsearch`      | Starter for using Elasticsearch search and analytics engine and Spring Data Elasticsearch | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-elasticsearch/pom.xml) |
| `spring-boot-starter-data-jdbc`               | Starter for using Spring Data JDBC                           | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-jdbc/pom.xml) |
| `spring-boot-starter-data-jpa`                | Starter for using Spring Data JPA with Hibernate             | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-jpa/pom.xml) |
| `spring-boot-starter-data-ldap`               | Starter for using Spring Data LDAP                           | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-ldap/pom.xml) |
| `spring-boot-starter-data-mongodb`            | Starter for using MongoDB document-oriented database and Spring Data MongoDB | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-mongodb/pom.xml) |
| `spring-boot-starter-data-mongodb-reactive`   | Starter for using MongoDB document-oriented database and Spring Data MongoDB Reactive | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-mongodb-reactive/pom.xml) |
| `spring-boot-starter-data-neo4j`              | Starter for using Neo4j graph database and Spring Data Neo4j | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-neo4j/pom.xml) |
| `spring-boot-starter-data-redis`              | Starter for using Redis key-value data store with Spring Data Redis and the Lettuce client | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-redis/pom.xml) |
| `spring-boot-starter-data-redis-reactive`     | Starter for using Redis key-value data store with Spring Data Redis reactive and the Lettuce client | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-redis-reactive/pom.xml) |
| `spring-boot-starter-data-rest`               | Starter for exposing Spring Data repositories over REST using Spring Data REST | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-rest/pom.xml) |
| `spring-boot-starter-data-solr`               | Starter for using the Apache Solr search platform with Spring Data Solr | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-solr/pom.xml) |
| `spring-boot-starter-freemarker`              | Starter for building MVC web applications using FreeMarker views | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-freemarker/pom.xml) |
| `spring-boot-starter-groovy-templates`        | Starter for building MVC web applications using Groovy Templates views | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-groovy-templates/pom.xml) |
| `spring-boot-starter-hateoas`                 | Starter for building hypermedia-based RESTful web application with Spring MVC and Spring HATEOAS | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-hateoas/pom.xml) |
| `spring-boot-starter-integration`             | Starter for using Spring Integration                         | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-integration/pom.xml) |
| `spring-boot-starter-jdbc`                    | Starter for using JDBC with the HikariCP connection pool     | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-jdbc/pom.xml) |
| `spring-boot-starter-jersey`                  | Starter for building RESTful web applications using JAX-RS and Jersey. An alternative to [`spring-boot-starter-web`](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#spring-boot-starter-web) | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-jersey/pom.xml) |
| `spring-boot-starter-jooq`                    | Starter for using jOOQ to access SQL databases. An alternative to [`spring-boot-starter-data-jpa`](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#spring-boot-starter-data-jpa) or [`spring-boot-starter-jdbc`](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#spring-boot-starter-jdbc) | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-jooq/pom.xml) |
| `spring-boot-starter-json`                    | Starter for reading and writing json                         | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-json/pom.xml) |
| `spring-boot-starter-jta-atomikos`            | Starter for JTA transactions using Atomikos                  | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-jta-atomikos/pom.xml) |
| `spring-boot-starter-jta-bitronix`            | Starter for JTA transactions using Bitronix                  | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-jta-bitronix/pom.xml) |
| `spring-boot-starter-mail`                    | Starter for using Java Mail and Spring Framework’s email sending support | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-mail/pom.xml) |
| `spring-boot-starter-mustache`                | Starter for building web applications using Mustache views   | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-mustache/pom.xml) |
| `spring-boot-starter-oauth2-client`           | Starter for using Spring Security’s OAuth2/OpenID Connect client features | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-oauth2-client/pom.xml) |
| `spring-boot-starter-oauth2-resource-server`  | Starter for using Spring Security’s OAuth2 resource server features | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-oauth2-resource-server/pom.xml) |
| `spring-boot-starter-quartz`                  | Starter for using the Quartz scheduler                       | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-quartz/pom.xml) |
| `spring-boot-starter-security`                | Starter for using Spring Security                            | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-security/pom.xml) |
| `spring-boot-starter-test`                    | Starter for testing Spring Boot applications with libraries including JUnit, Hamcrest and Mockito | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-test/pom.xml) |
| `spring-boot-starter-thymeleaf`               | Starter for building MVC web applications using Thymeleaf views | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-thymeleaf/pom.xml) |
| `spring-boot-starter-validation`              | Starter for using Java Bean Validation with Hibernate Validator | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-validation/pom.xml) |
| `spring-boot-starter-web`                     | Starter for building web, including RESTful, applications using Spring MVC. Uses Tomcat as the default embedded container | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-web/pom.xml) |
| `spring-boot-starter-web-services`            | Starter for using Spring Web Services                        | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-web-services/pom.xml) |
| `spring-boot-starter-webflux`                 | Starter for building WebFlux applications using Spring Framework’s Reactive Web support | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-webflux/pom.xml) |
| `spring-boot-starter-websocket`               | Starter for building WebSocket applications using Spring Framework’s WebSocket support | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-websocket/pom.xml) |



除了应用 starters 之外,下面的starters 还可以用于添加到 *生产环境的*  特性:



**Table 13.2. Spring Boot production starters**

| Name                           | Description                                                  | Pom                                                          |
| ------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `spring-boot-starter-actuator` | Starter for using Spring Boot’s Actuator which provides production ready features to help you monitor and manage your application | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-actuator/pom.xml) |



最后,Spring Boot 同样提供了以下 starters ,它们可以用在当你想要排除或者替换某些指定技术的时候:

**Table 13.3. Spring Boot technical starters**

| Name                                | Description                                                  | Pom                                                          |
| ----------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `spring-boot-starter-jetty`         | Starter for using Jetty as the embedded servlet container. An alternative to [`spring-boot-starter-tomcat`](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#spring-boot-starter-tomcat) | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-jetty/pom.xml) |
| `spring-boot-starter-log4j2`        | Starter for using Log4j2 for logging. An alternative to [`spring-boot-starter-logging`](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#spring-boot-starter-logging) | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-log4j2/pom.xml) |
| `spring-boot-starter-logging`       | Starter for logging using Logback. Default logging starter   | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-logging/pom.xml) |
| `spring-boot-starter-reactor-netty` | Starter for using Reactor Netty as the embedded reactive HTTP server. | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-reactor-netty/pom.xml) |
| `spring-boot-starter-tomcat`        | Starter for using Tomcat as the embedded servlet container. Default servlet container starter used by [`spring-boot-starter-web`](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#spring-boot-starter-web) | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-tomcat/pom.xml) |
| `spring-boot-starter-undertow`      | Starter for using Undertow as the embedded servlet container. An alternative to [`spring-boot-starter-tomcat`](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#spring-boot-starter-tomcat) | [Pom](https://github.com/spring-projects/spring-boot/tree/v2.1.6.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-undertow/pom.xml) |



> 如果想要找那些其他组织贡献的 starters, 查阅 GitHub 中 `spring-boot-starters` 模块中的 [README file](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-project/spring-boot-starters/README.adoc)  