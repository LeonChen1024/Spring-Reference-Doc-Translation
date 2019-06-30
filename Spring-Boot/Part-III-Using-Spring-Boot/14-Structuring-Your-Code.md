# 14. 结构化你的代码





[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/13-Build-Systems.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/15-Configuration-Classes.md)     





Spring Boot 的运行并没有要求特殊的代码布局.尽管如此, 这里也有一些最好的方式可以帮助到你.

## 14.1 使用 “默认” 包

当一个类没有使用`package`声明的时候,我们认为它是在 “默认包中”. 通常情况下我们不推荐使用 “默认包” 并且鼓励尽量避免使用它. 它会导致 Spring Boot 应用在你使用 `@ComponentScan`, `@EntityScan`的时候出现特殊的问题, 或者是使用 `@SpringBootApplication` 注解的时候, 因为它会读取每一个 jar 中的每一个类.



> 我们推荐你遵循 Java 的推荐包命名规则并且使用一个域名的倒序(比如,com.example.project)



## 14.2 定位 Main 应用类

我们通常推荐你将你的 main 应用类放在根的包路径下.  [`@SpringBootApplication` 注解](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-using-springbootapplication-annotation.html) 通常会放在你的 main 类中,它隐式定义了一个基准的 “查找包”.比如, 如果你想要写一个 JPA 应用 , `@SpringBootApplication` 注解的类的包将会用来查找 `@Entity` 项目. 使用一个根目录同时允许你的 component 扫描只会在你的项目上进行.

> 如果你不想使用 `@SpringBootApplication`,  `@EnableAutoConfiguration` 和 `@ComponentScan` 注解导入了同样的行为的定义,你也可以使用它们来作为替代.

下面的清单展示了一个典型的布局:

```xml-dtd
com
 +- example
     +- myapplication
         +- Application.java
         |
         +- customer
         |   +- Customer.java
         |   +- CustomerController.java
         |   +- CustomerService.java
         |   +- CustomerRepository.java
         |
         +- order
             +- Order.java
             +- OrderController.java
             +- OrderService.java
             +- OrderRepository.java
```



 `Application.java` 文件会定义 `main` 方法, 通过使用基础的 `@SpringBootApplication`, 如下:

```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```









[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/13-Build-Systems.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/15-Configuration-Classes.md)     



