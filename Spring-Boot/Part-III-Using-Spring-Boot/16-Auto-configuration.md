# 16. 自动配置



[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/14-Structuring-Your-Code.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/17-Spring-Beans-and-Dependency-Injection.md)     



Spring Boot  自动配置是为了根据你添加的 jar 文件依赖自动的配置你的 Spring 应用. 比如, 如果你的 classpath 中有 `HSQLDB`,并且你没有手动的配置任何的数据库连接 bean, 那么 Spring Boot 自动配置一个内存数据库用于使用.

> 无论何时你应该只添加一个 `@SpringBootApplication` 或者 `@EnableAutoConfiguration`  注解.我们通常推荐你添加其中一个到你的主  `@Configuration`  类.



## 16.1 逐步替换自动配置



自动配置是非侵入式的.在任何时候你都可以开始定义你自己的配置来取代自动配置中的某些部分. 比如, 如果你添加了你自己的 `DataSource` bean,那么默认的数据库支持将会无效.

如果你想要搞明白当前设置了什么自动配置,还有为什么要设置,可以以 `--debug` 模式启动你的应用.这么做会启用调试日志并将各种情况的日志打印在控制台.



## 16.2 禁用指定的 Auto-configuration 类

如果你发现 auto-configuration 类在你不想要应用的地方生效了,你可以使用 `@EnableAutoConfiguration` 中的 exclude 属性来禁用他们,如下所示:

```java
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;
import org.springframework.context.annotation.*;

@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class MyConfiguration {
}
```

如果这个类不在  classpath上, 你可以使用这个注解的 `excludeName` 属性来指定一个全路径吗名称. 最后,你可以通过  `spring.autoconfigure.exclude` 属性控制 auto-configuration 类的列表排除项.

> 你可以通过 注解级别或者属性级别来定义排除项.







[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/14-Structuring-Your-Code.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/17-Spring-Beans-and-Dependency-Injection.md)     

