# 15. Configuration Classes







[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/14-Structuring-Your-Code.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/16-Auto-configuration.md)     



Spring Boot 偏向于使用基于 Java 的配置. 尽管可以使用 XML 来配置 `SpringApplication`, 我们通常推荐你使用 单个加了 `@Configuration` 注解的类当做控制源. 通常情况下 `main` 方法是一个主 `@Configuration` 类的很好的候选项.



> 许多网上的 Spring 的配置的例子使用了 XML 配置.如果可能的话,请尝试使用有同等效果的Java 的配置方式.查找 `Enable*` 注解会是一个很好的开始.

 

## 15.1 导入额外的配置类

你不需要将你所有的 `@Configuration`添加到一个类中.  `@Import` 注解可以用来导入额外的配置类. 或者, 你可以使用 `@ComponentScan` 来自动提取所有的 Spring 模块, 包括 `@Configuration` 类.



## 15.2 导入 XML 配置

如果你必须使用 XML 配置, 我们推荐你仍然以一个 `@Configuration` 类 作为开始.你可以使用一个 `@ImportResource` 注解来加载 XML 配置文件.







[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/14-Structuring-Your-Code.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/16-Auto-configuration.md)     

