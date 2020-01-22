# 24. 外部化配置





[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/23-SpringApplication.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-IV-Spring-Boot-features/24-Externalized-Configuration.md)     



Spring Boot 允许你使用外部配置这样你可以在不同的环境里使用相同的应用.你可以使用属性文件,YAML 文件,环境变量,还有命令行参数来外部化配置. 属性值可以直接通过 `@Value` 注解注入到 bean 中, 通过 Spring 的 `Environment` 提取, 或者通过 `@ConfigurationProperties`  [结构化对象](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-external-config.html#boot-features-external-config-typesafe-configuration-properties) .

Spring Boot 使用了一个非常特殊的 `PropertySource` 顺序,它的设计允许对值进行重写.属性的顺序如下:

1. [Devtools 全局设置属性 ](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-devtools.html#using-boot-devtools-globalsettings) 在你的home文件夹 (当启用  devtools 的时候是`~/.spring-boot-devtools.properties` ).
2. tests 中的 [`@TestPropertySource`](https://docs.spring.io/spring/docs/5.1.8.RELEASE/javadoc-api/org/springframework/test/context/TestPropertySource.html) .
3. test 中的 `properties` 属性  . 在 [`@SpringBootTest`](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/api/org/springframework/boot/test/context/SpringBootTest.html) 还有 [用来测试一个指定的应用切片的 test 注解](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-testing.html#boot-features-testing-spring-boot-applications-testing-autoconfigured-tests).
4. 命令行参数.
5.  `SPRING_APPLICATION_JSON` 中的属性 (环境变量或者系统属性中的单行 JSON).
6. `ServletConfig` 初始化参数.
7. `ServletContext` 初始化参数.
8.  `java:comp/env` 中的JNDI 属性.
9. Java 系统属性 (`System.getProperties()`).
10. OS 环境变量.
11.  `random.*` 中的 `RandomValuePropertySource` .
12. 在你打包的jar外的 [Profile-specific 应用属性](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-external-config.html#boot-features-external-config-profile-specific-properties)  (`application-{profile}.properties` 和 YAML 变体).
13. 打包在你的jar内的 [Profile-specific 应用属性](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-external-config.html#boot-features-external-config-profile-specific-properties)  (`application-{profile}.properties` 和 YAML 变体).
14. 在你打包的 jar 外的应用属性 (`application.properties` 和 YAML 变体).
15. 打包在你的jar 内的应用属性 (`application.properties` 和 YAML 变体).
16. 在你的 `@Configuration` 类中的 [`@PropertySource`](https://docs.spring.io/spring/docs/5.1.8.RELEASE/javadoc-api/org/springframework/context/annotation/PropertySource.html) 注解.
17. 默认属性 (指定的 `SpringApplication.setDefaultProperties` 设置).

 

为了提供一个具体的例子,假设你开发一个 `@Component` 使用了 `name` 属性, 如下所示:

```java
import org.springframework.stereotype.*;
import org.springframework.beans.factory.annotation.*;

@Component
public class MyBean {

    @Value("${name}")
    private String name;

    // ...

}
```

在你的应用 classpath (比如,在你的jar内) 你可以使用一个 `application.properties` 文件来提供一个默认属性值给 `name`. 当你在一个新的环境运行的时候,一个 `application.properties` 文件可以提供到jar的外部来重写 `name`. 对于一次性测试,你可以通过一个指定的命令行开关来启动(比如,java -jar app.jar --name="Spring"`).

>  `SPRING_APPLICATION_JSON` 属性可以通过环境变量在命令行中使用.比如,你可以在 UN*X shell 中使用如下命令行:
>
> ```shell
> $ SPRING_APPLICATION_JSON='{"acme":{"name":"test"}}' java -jar myapp.jar
> ```
>
> 在前面的例子中,你以 `acme.name=test` 结束了 Spring `Environment`. 你还可以在系统属性中支持 JSON 使用 `spring.application.json` 格式, 如下所示:
>
> ```shell
> $ java -Dspring.application.json='{"name":"test"}' -jar myapp.jar
> ```
>
> 你可以通过命令行参数来支持 JSON,如下所示:
>
> ```shell
> $ java -jar myapp.jar --spring.application.json='{"name":"test"}'
> ```
>
> 你可以支持 JSON 作为一个 JNDI 变量,如下:
>
>  `java:comp/env/spring.application.json`.



## 24.1 配置随机值

 `RandomValuePropertySource` 在注入随机值的时候很有用(比如,加密或者测试用例). 它可以产生 integers, longs, uuids, 或者 strings,如下所示:

```properties
my.secret=${random.value}
my.number=${random.int}
my.bignumber=${random.long}
my.uuid=${random.uuid}
my.number.less.than.ten=${random.int(10)}
my.number.in.range=${random.int[1024,65536]}
```

 `random.int*` 语法是 `OPEN value (,max) CLOSE` 其中 `OPEN,CLOSE` 是任意的字符并且 `value,max` 是整数. 如果提供了 `max` , 那么 `value` 是最小值并且 `max` 是最大值 (不包含).

## 24.2 访问命令行属性

默认情况下, `SpringApplication` 会转化任意的命令行额外参数(也就是以 `--` 开头的参数, 比如 `--server.port=9000`) 成一个 `property` 并将他们添加到 Spring `Environment` 中. 如同前面提到的,命令行属性总是比其他的属性源优先级高.

如果你不想要命令行属性被添加到 `Environment`, 你可以通过使用 `SpringApplication.setAddCommandLineProperties(false)` 来禁用他们.

## 24.3 应用属性文件

`SpringApplication` 从以下位置的  `application.properties` 文件加载属性并将他们添加到 Spring `Environment`:

1.  在当前文件夹下的 `/config` 子文件夹
2. 当前文件夹
3.  classpath 的 `/config` 包
4.  classpath 根路径

这个列表的排序是根据优先级来排的 (列表中更高的位置会重写更低位置的属性). 

> 你还可以 [使用 YAML ('.yml') 文件](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-external-config.html#boot-features-external-config-yaml) 作为一个可选的 '.properties'.

如果你不想使用 `application.properties` 做为配置的文件名,你可以通过指定  `spring.config.name` 环境属性来使用另一个文件名.你还可以通过使用 `spring.config.location`  环境属性来指定一个位置 (是一个通过逗号来分隔的文件夹位置或文件位置列表). 下面的例子展示了如何指定一个不同的文件名:

``` shell
$ java -jar myproject.jar --spring.config.name=myproject
```

下面的例子展示了如何指定两个位置:

```shell
$ java -jar myproject.jar --spring.config.location=classpath:/default.properties,classpath:/override.properties
```



> `spring.config.name` 和 `spring.config.location` 会在非常早的时候使用,用来指明哪些文件需要被加载,所以他们必须被定义成一个环境属性(通常作为一个 OS 环境变量,一个系统属性,或者一个命令行参数).

如果 `spring.config.location` 包含了文件夹,它们应该以 `/` 结尾(并且, 在运行时,在加载之前添加  `spring.config.name`  生成的名字,包括 profile 指定的文件名).  `spring.config.location` 指定的文件按照它本身使用的方法使用,不支持 profile 指定的变体,并且会被其他任意的 profile 指定的属性覆盖.

配置位置是以倒序的方式查找.默认情况下,配置的位置是 `classpath:/,classpath:/config/,file:./,file:./config/`. 那么最后查找的顺序如下:

1. `file:./config/`
2. `file:./`
3. `classpath:/config/`
4. `classpath:/`

当通过使用 `spring.config.location` 来自定义配置的位置的时候, 它们会替换掉默认的位置.比如,如果 `spring.config.location` 配置的值是 `classpath:/custom-config/,file:./custom-config/`, 查找的顺序如下:

1. `file:./custom-config/`
2. `classpath:custom-config/`

另一种情况,当自定义配置位置使用 `spring.config.additional-location` 来设置, 它们是作为默认位置的额外位置.额外的位置会在默认位置之前搜索.比如,如果额外位置有 `classpath:/custom-config/,file:./custom-config/` , 搜索顺序变为如下:

1. `file:./custom-config/`
2. `classpath:custom-config/`
3. `file:./config/`
4. `file:./`
5. `classpath:/config/`
6. `classpath:/`

这个查找顺序允许你在一个配置文件中指定一个默认值并且可以有选择的在其他配置文件中进行覆盖.你可以为你的应用在 `application.properties` 中提供默认值(或者其他的在 `spring.config.name` 中指定的名字) 在某一个默认位置. 这些默认值在运行时可以被其他自定义位置的值覆盖.  

> 如果你使用环境变量而不是系统属性,大多数操作系统不允许句号分割键名,但是你可以使用下划线来替代 (比如, `SPRING_CONFIG_NAME` 可以用来替换 `spring.config.name`).

> 如果你的应用运行在容器中,那么 JNDI 属性 (位于 `java:comp/env`) 或者 servlet context 初始化参数可以用来替换,或者也可以使用环境变量或者系统属性.



## 24.4 Profile指定属性

除了 `application.properties` 文件, profile指定的属性也可以通过以下命名规则来定义: `application-{profile}.properties`.  `Environment` 有一些默认的 profiles (通常是, `[default]`) 如果设置激活的 profiles. 换句话说,如果没有显式激活 profiles,那么会加载 `application-default.properties` .

Profile指定的属性与标准的 `application.properties` 从相同的地方加载,  profile指定的文件会覆盖没有指定的, profile指定的文件可以在打包的jar内部也可以在外部.

如果指定了一些 profiles, 会使用最后者胜利的策略来应用这些属性.比如,profiles 指定了 `spring.profiles.active` 属性是在 `SpringApplication` API 之后添加的那么会比它拥有更高的优先级.

> 如果你在 `spring.config.location` 指定了一些文件, 这些文件的 profile指定的变体是不会考虑的. 如果你想要同时使用  profile指定的属性, 使用 `spring.config.location` 中的文件.



## 24.5 属性中的占位符

 `application.properties` 中的值是通过现有的 `Environment` 来过滤的, 所以你可以引用前面定义的值 (比如,从系统属性里).

```properties
app.name=MyApp
app.description=${app.name} is a Spring Boot application
```

> 你还可以使用这个技术来创建 "简短" 的已存在的 Spring Boot 属性. 查看 [*77.4章, “使用 ‘简短’ 命令行参数”*](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)  的怎么做来获取详情.

## 24.6 加密属性

Spring Boot 没有提供任何的内置加密属性值,然而,它提供了必要的钩子节点来修改包含在 Spring `Environment` 中的值.  `EnvironmentPostProcessor` 接口允许你在应用启动之前操作 `Environment` . 查看 [Section 76.3, “Customize the Environment or ApplicationContext Before It Starts”](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/howto-spring-boot-application.html#howto-customize-the-environment-or-application-context) 获取详情.

如果你在查找一个安全的方式来存储证书还有密码,  [Spring Cloud Vault](https://cloud.spring.io/spring-cloud-vault/) 项目提供了存储外部配置在 [HashiCorp Vault](https://www.vaultproject.io/) 的支持.

## 24.7 使用 YAML 来替换属性

[YAML](https://yaml.org/) 是一个 JSON 的超集,是一个针对特殊的继承式配置数据的便捷格式. `SpringApplication` 类自动支持 YAML 作为一个可选的属性不论你是否有 [SnakeYAML](https://bitbucket.org/asomov/snakeyaml) 库在你的 classpath 中.

> 如果你使用了 “Starters”, SnakeYAML 在 `spring-boot-starter` 中自动有提供.

### 24.7.1 加载 YAML

Spring 框架提供了两种便捷的类用来加载 YAML 文件. `YamlPropertiesFactoryBean` 加载 YAML 作为 `Properties` 还有 `YamlMapFactoryBean` 加载 YAML 作为一个 `Map`.

比如,考虑如下的 YAML 文档:

```yaml
environments:
	dev:
		url: https://dev.example.com
		name: Developer Setup
	prod:
		url: https://another.example.com
		name: My Cool App
```

上面的例子可以被转换为如下属性:

```properties
environments.dev.url=https://dev.example.com
environments.dev.name=Developer Setup
environments.prod.url=https://another.example.com
environments.prod.name=My Cool App
```

YAML 列表代表这属性的键 `[index]` . 比如,参考以下 YAML:

```yaml
my:
servers:
	- dev.example.com
	- another.example.com
```

上面的例子可以被转换为如下属性:

```properties
my.servers[0]=dev.example.com
my.servers[1]=another.example.com
```

要像这样绑定到属性上可以使用 Spring Boot 的 `Binder` 工具 (也就是 `@ConfigurationProperties` 的功能), 你需要有一个目标类型 `java.util.List` (or `Set`) 的bean ,并且你需要提供一个 setter 或者通过一个可变值来初始化它. 比如, 下面的例子绑定了前面的内容到属性中:

```java
@ConfigurationProperties(prefix="my")
public class Config {

	private List<String> servers = new ArrayList<String>();

	public List<String> getServers() {
		return this.servers;
	}
}
```

### 24.7.2 在  Spring 环境中使 YAML 作为属性

 `YamlPropertySourceLoader` 类可以用来暴露 YAML 作为一个 `PropertySource` 在 Spring `Environment`. 这么做允许你使用 `@Value` 注解并带着占位符来访问 YAML 属性.

### 24.7.3 Multi-profile YAML 文档

你可以指定多个 profile-specific YAML 文档在一个文件中,通过使用 `spring.profiles` 键来指明什么时候应用这个文档,如下所示:

```yaml
server:
	address: 192.168.1.100
---
spring:
	profiles: development
server:
	address: 127.0.0.1
---
spring:
	profiles: production & eu-central
server:
	address: 192.168.1.120
```

在前面的例子中,如果 `development` profile 被激活,  `server.address` 属性是 `127.0.0.1`. 类似的,如果 `production` **和**  `eu-central` profiles 处于激活状态,  `server.address` 属性是 `192.168.1.120`. 如果 `development`, `production` 和 `eu-central` profiles 是没启用的, 那么属性的值为 `192.168.1.100`.

> `spring.profiles` 可以包含一个简单的 profile 名称 (比如 `production`) 或这一个 profile 表达式. 一个 profile 表达式允许表达一个更复杂的profile 逻辑, 比如 `production & (eu-central | eu-west)`. 查阅 [reference guide](https://docs.spring.io/spring/docs/5.1.8.RELEASE/spring-framework-reference/core.html#beans-definition-profiles-java) 获取更多细节.

当应用context 启动的时候如果没有显式的激活项,默认的profile将会被激活.所以,在下面的 YAML 中, 我们为 `spring.security.user.password` 设置了一个值 **只有** 在 "默认" profile中才可用:

```yaml
server:
  port: 8000
---
spring:
  profiles: default
  security:
    user:
      password: weak
```

然而,在下面的例子中,密码总是会被设置,因为它没有依附与任何profile ,而且它在其他所有profile需要的情况下会被重置:

```yaml
server:
  port: 8000
spring:
  security:
    user:
      password: weak
```

Spring profiles 通过使用 `spring.profiles` 元素来指定,也可能通过使用 `!` 字符来否定. 如果一个文档指定否定和非否定的 profiles , 至少一个非否定的 profile 必须匹配,并且没有否定的profiles被匹配.

### 24.7.4 YAML 缺点

YAML 文件不能通过使用 `@PropertySource` 注解加载. 所以, 在你需要通过这种方式加载数值的时候,你需要使用一个 properties .

使用多 YAML 文档格式在 profile-指定的 YAML 文件中会导致非预期的行为. 比如, 考虑以下名为 `application-dev.yml` 的配置文件, 同时 `dev` profile 被激活:

```yaml
server:
  port: 8000
---
spring:
  profiles: !test
  security:
    user:
      password: weak
```

在上面的例子中,profile 否定和 profile 表达式将不会像预期的那样表现. 我们推荐你不要绑定 profile 到YAML 文件还有多YAML文档的格式并且坚持使用他们中的一个.

## 24.8 类型安全 的配置属性

使用 `@Value("${property}")`  注解来注入配置属性有时候可能会显得很笨重,特别是如果你正在使用多个属性或者你的数据是自然继承的.Spring Boot 提供了一个可选的方法使得属性拥有强类型挂历并且校验你应用中的配置,如下所示:

```java
package com.example;

import java.net.InetAddress;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties("acme")
public class AcmeProperties {

	private boolean enabled;

	private InetAddress remoteAddress;

	private final Security security = new Security();

	public boolean isEnabled() { ... }

	public void setEnabled(boolean enabled) { ... }

	public InetAddress getRemoteAddress() { ... }

	public void setRemoteAddress(InetAddress remoteAddress) { ... }

	public Security getSecurity() { ... }

	public static class Security {

		private String username;

		private String password;

		private List<String> roles = new ArrayList<>(Collections.singleton("USER"));

		public String getUsername() { ... }

		public void setUsername(String username) { ... }

		public String getPassword() { ... }

		public void setPassword(String password) { ... }

		public List<String> getRoles() { ... }

		public void setRoles(List<String> roles) { ... }

	}
}
```



前面的 POJO 定义了如下属性:

- `acme.enabled`, 默认值是 `false` .
- `acme.remote-address`, 一个可以携带 `String` 的类型.
- `acme.security.username`, 一个嵌套的 "security" 对象,名称是由属性名称来定义的. 特别的是,返回类型完全没有使用可能变成 `SecurityProperties`.
- `acme.security.password`.
- `acme.security.roles`, 一个 `String` 的集合.



> Getters 和 setters 通常是强制要求的,因为绑定是通过标准Java bean 属性来做描述器的,就像在 Spring MVC 中一样,一个setter 可能在以下情况被忽略:
>
> - Maps, 只要他们初始化了,就需要一个 getter 但是不一定需要一个 setter, 因为他们可以通过binder 来改变.
> - 集合和数组可以通过索引 (特别是在 YAML 中) 或者使用一个逗号分隔值(属性)来访问.在后面的例子中,一个setter 是必须的.我们推荐总是对这些类型中添加一个 setter. 如果你初始化了一个集合,确保这是不可变的 (如同前面的例子).
> - 如果嵌套 POJO 属性已经初始化了 (就像上面例子中的 `Security` 字段),一个 setter 是非必须的. 如果你想要 binder 使用默认构造器来创建实例,你需要一个setter.
>
> 一些人使用 Lombok 项目来自动添加 getters 和 setters. 确保 Lombok 不会对这个类型生成任何的特殊的构造器, 因为容器会自动使用它来初始化对象.
>
> 最后,只有标准的Java Bean 属性会被绑定并且不支持静态属性.

> 在 [differences between `@Value` and `@ConfigurationProperties`](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-external-config.html#boot-features-external-config-vs-value) 中也可能看到相关内容.

你还需要在 `@EnableConfigurationProperties` 注解中列出注册的属性类,如下所示:

```java
@Configuration
@EnableConfigurationProperties(AcmeProperties.class)
public class MyConfiguration {
}
```



> 当注解 `@ConfigurationProperties` bean 以这种方式注册,这个 bean 有一个常规的名字: `<prefix>-<fqn>`, 其中 `<prefix>`  是在 `@ConfigurationProperties`  中指定的环境键前缀并且 `<fqn>`  是bean的一个全路径名称. 如果注解并没有提供任何前缀,那么bean只能使用全路径名称.
>
> 上面的例子的 bean 的名称是 `acme-com.example.AcmeProperties`.

前面的配置创建了一个常规的bean给  `AcmeProperties`. 我们推荐使用 `@ConfigurationProperties` 只处理环境和特殊情况下不从 context 注入其他的bean . 要记得, `@EnableConfigurationProperties` 注解还是自动提供到你的项目里的所以任何已存在的被注解了 `@ConfigurationProperties` 的bean 都会被 `Environment` 配置. 你可以使用  `AcmeProperties` 作为一个bean ,来取代注解 `MyConfiguration` 到 `@EnableConfigurationProperties(AcmeProperties.class)`, 如下所示:

```java
@Component
@ConfigurationProperties(prefix="acme")
public class AcmeProperties {

	// ... see the preceding example

}
```

这种类型的配置可以在 `SpringApplication` 使用外部 YAML 配置的时候良好工作,如下所示:

```yaml
# application.yml

acme:
	remote-address: 192.168.1.1
	security:
		username: admin
		roles:
		  - USER
		  - ADMIN

# additional configuration as required
```

想要使用 `@ConfigurationProperties` beans, 你可以像使用其他 bean 那样的方式注入他们,如下所示:

```java
@Service
public class MyService {

	private final AcmeProperties properties;

	@Autowired
	public MyService(AcmeProperties properties) {
	    this.properties = properties;
	}

 	//...

	@PostConstruct
	public void openConnection() {
		Server server = new Server(this.properties.getRemoteAddress());
		// ...
	}

}
```



> 使用 `@ConfigurationProperties` 同时允许你生成  metadata 文件,它们可以被 IDEs 用来提供对你的键的自动补全. 查阅 [Appendix B, *Configuration Metadata*](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/configuration-metadata.html) 来获得更多细节.

### 24.8.1 第三方配置

除了使用 `@ConfigurationProperties` 来注解一个类,你还可以使用它在 public `@Bean` 方法上. 这么做在你想要绑定属性到在你控制之外的第三方模块的时候是非常有用的.

为了要从 `Environment` 属性中配置一个bean, 添加 `@ConfigurationProperties` 到这个bean的注册中,如下所示: 



```java
@ConfigurationProperties(prefix = "another")
@Bean
public AnotherComponent anotherComponent() {
	...
}
```

所有定义了 `another` 前缀的属性都以和前面  `AcmeProperties`  相似的方式映射到这个 `AnotherComponent` bean .

### 24.8.2 松绑定

Spring Boot 使用了一些松绑定的规则来绑定 `Environment` 属性到 `@ConfigurationProperties` beans, 所以不需要保持 `Environment` 属性名和 bean 属性名保持完全一致. 通常的例子中包含 - 的属性是很有用的 (比如,  `context-path` 绑定到 `contextPath`), 还有大写环境属性 ( 比如, `PORT` 绑定到 `port`).

比如,参考如下 `@ConfigurationProperties` 类:

```java
@ConfigurationProperties(prefix="acme.my-project.person")
public class OwnerProperties {

	private String firstName;

	public String getFirstName() {
		return this.firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

}
```

在上面的例子中,可以使用如下属性名:

| 属性                                | 注意                                                         |
| ----------------------------------- | ------------------------------------------------------------ |
| `acme.my-project.person.first-name` | Kebab 风格,  `.properties` 和 `.yml`  文件中推荐的格式.      |
| `acme.myProject.person.firstName`   | Standard camel case syntax.                                  |
| `acme.my_project.person.first_name` | Underscore notation, which is an alternative format for use in `.properties` and `.yml` files. |
| `ACME_MYPROJECT_PERSON_FIRSTNAME`   | Upper case format, which is recommended when using system environment variables. |



> 注解的 `prefix` 值只能使用 kebab 风格 (小写并且用 `-` 分隔,比如 `acme.my-project.person`

**Table 24.2. 每种属性源的松绑定原则**

| 属性源    | Simple                                             | List                                                         |
| --------- | -------------------------------------------------- | ------------------------------------------------------------ |
| 属性文件  | 驼峰风格, 烤串风格,或者下划线符号风格              | 标准列表语法使用 `[ ]` 或者逗号分隔值                        |
| YAML 文件 | 驼峰风格, 烤串风格,或者下划线符号风格              | 标准 YAML 列表语法或者逗号分隔值                             |
| 环境变量  | 大写格式并使用下划线分隔. `_` 不应该在属性名中使用 | 用下划线环绕在数字值两边,比如 `MY_ACME_1_OTHER = my.acme[1].other` |
| 系统属性  | 驼峰风格, 烤串风格,或者下划线符号风格              | 标准列表语法使用 `[ ]` 或者逗号分隔值                        |



> 我们推荐在可以的时候,属性使用小写烤串风格,比如 `my.property-name=acme`.

当绑定到 `Map` 属性的时候, 如果 `key` 包含了任何其他不是小写字母或者 `-`, 你需要使用中括号来使得这个原始值被保留.如果键没有被 `[]` 包围, 任何不是小写字母或者 `-` 的字符都会被删除. 比如,考虑绑定如下属性到 `Map`:

```properties
acme:
  map:
    "[/key1]": value1
    "[/key2]": value2
    /key3: value3
```

上面的属性值将会绑定到一个 `Map` 带有 `/key1`, `/key2` 和 `key3` 作为键的 map.

### 24.8.3 合并复杂类型

当列表在多个地方进行了配置,覆盖功能会对整个列表生效.

比如,假设一个 `MyPojo` 对象带有一个 `name` 和 `description` 默认值为 `null` 的属性. 下面的例子从 `AcmeProperties`暴露了一个 `MyPojo` 对象的列表:

```java
@ConfigurationProperties("acme")
public class AcmeProperties {

	private final List<MyPojo> list = new ArrayList<>();

	public List<MyPojo> getList() {
		return this.list;
	}

}
```

考虑如下配置:

```yaml
acme:
  list:
    - name: my name
      description: my description
---
spring:
  profiles: dev
acme:
  list:
    - name: my another name
```

如果 `dev` 配置没有激活, `AcmeProperties.list` 包含一个 `MyPojo` 条目, 如同前面定义的. 如果 `dev` 配置被启用了,  `list` *仍然* 包含只有一个条目 (带有一个 `my another name` 的名字和一个 `null` 的描述). 这个配置不会添加第二个 `MyPojo` 实例到这个列表, 并且他不会合并条目.

当一个 `List` 被定义到多个配置文件中,会使用其中一个拥有最高优先级的条目(只会使用那一个).思考如下例子:

```yaml
acme:
  list:
    - name: my name
      description: my description
    - name: another name
      description: another description
---
spring:
  profiles: dev
acme:
  list:
    - name: my another name
```

在前面的例子中,如果 `dev` 配置被激活, `AcmeProperties.list` 包含一个 `MyPojo` 条目 (带有一个 `my another name` 的名字和一个 `null` 的描述).对于 YAML 来说,逗号分隔的列表和 YAML 列表可以被用来完全覆盖列表的内容.

对于 `Map` 属性, 你可以从多个源来绑定属性值.然而,对于多个源中的同一个属性,会使用优先级最高的值.下面的例子从 `AcmeProperties` 中暴露了一个 Map<String, MyPojo>` :

```java
@ConfigurationProperties("acme")
public class AcmeProperties {

	private final Map<String, MyPojo> map = new HashMap<>();

	public Map<String, MyPojo> getMap() {
		return this.map;
	}

}
```

思考如下配置

```yaml
acme:
  map:
    key1:
      name: my name 1
      description: my description 1
---
spring:
  profiles: dev
acme:
  map:
    key1:
      name: dev name 1
    key2:
      name: dev name 2
      description: dev description 2
```

如果 `dev` 配置没有激活, `AcmeProperties.map` 包含了一个键为 `key1` (名字为 `my name 1` 并且描述为 `my description 1`). 如果 `dev` 配置被启用, `map` 包含了两条键为 `key1` 的条目(名字为 `dev name 1` 并且描述为 `my description 1`) 和 `key2` (名字为 `dev name 2` 并且描述为 `dev description 2`).

> 前面的合并规则可以从所有的属性源添加而不仅仅是YAML 文件.



### 24.8.4 属性转换































