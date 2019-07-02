# 18. 使用 @SpringBootApplication 注解





[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/17-Spring-Beans-and-Dependency-Injection.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/19-Running-Your-Application.md)     



许多 Spring Boot 开发者喜欢在他们的 app 中使用 auto-configuration, 组件扫描还有在他们的 "application class"上定义额外的配置. 一个`@SpringBootApplication` 注解可以用来开启以下三个功能:

- `@EnableAutoConfiguration`: 启用 [Spring Boot’s 自动配置机制](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-auto-configuration.html)
- `@ComponentScan`: 启用 `@Component` 扫描在 application 放置的包中 (查阅 [the best practices](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-structuring-your-code.html))
- `@Configuration`: 允许在上下文中注册额外的 bean 或者导入额外的配置类

 `@SpringBootApplication` 注册等同于使用了 `@Configuration`, `@EnableAutoConfiguration`, 还有 `@ComponentScan` 的默认属性, 如下所示:

```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication // same as @Configuration @EnableAutoConfiguration @ComponentScan
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```

> `@SpringBootApplication` 同样提供了别名来自定义  `@EnableAutoConfiguration` 和 `@ComponentScan` 的属性.

> 这些功能都不是强制的,你可以选择使用任意的特性来替换这个注解.比如,你可能不想使用组件扫描: 
>
> ```java
> package com.example.myapplication;
> 
> import org.springframework.boot.SpringApplication;
> import org.springframework.context.annotation.ComponentScan
> import org.springframework.context.annotation.Configuration;
> import org.springframework.context.annotation.Import;
> 
> @Configuration
> @EnableAutoConfiguration
> @Import({ MyConfig.class, MyAnotherConfig.class })
> public class Application {
> 
> 	public static void main(String[] args) {
> 			SpringApplication.run(Application.class, args);
> 	}
> 
> }
> ```
>
> 在这个例子中, `Application`  如同其他的 Spring Boot 应用一样,除了  `@Component` 注解类并不会自动检测到并且用户定义的 bean 要显式的导入(使用  `@Import` )



[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/17-Spring-Beans-and-Dependency-Injection.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/19-Running-Your-Application.md)     

