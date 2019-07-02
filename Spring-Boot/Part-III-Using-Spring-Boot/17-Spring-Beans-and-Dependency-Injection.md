# 17. Spring Beans 和依赖注入





[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/16-Auto-configuration.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/18-Using-the-SpringBootApplication-Annotation.md)     





你可以自由的使用任意的标准 Spring 框架的技术来定义你的 bean 和他们注入的依赖. 简单来说,我们发现通常使用 `@ComponentScan` (来找到你的 bean) 并使用 `@Autowired` (来进行构造器注入) 是很好用的.

如果你是按照前面推荐的方法来结构化你的代码 (将你的 application 类放在包的根目录下), 你可以添加 `@ComponentScan` 而不需要任何的参数. 你的应用中所有的组件 (`@Component`, `@Service`, `@Repository`, `@Controller` 等等.) 都会自动以 Spring Beans 的形式注册.

下面的例子展示了一个 `@Service` Bean 使用了构造器注入来获取一个 `RiskAssessor`:

```java
package com.example.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class DatabaseAccountService implements AccountService {

	private final RiskAssessor riskAssessor;

	@Autowired
	public DatabaseAccountService(RiskAssessor riskAssessor) {
		this.riskAssessor = riskAssessor;
	}

	// ...

}
```

如果一个 bean 只有一个构造器, 你可以忽略 `@Autowired` 注解, 如下所示:

```java
@Service
public class DatabaseAccountService implements AccountService {

	private final RiskAssessor riskAssessor;

	public DatabaseAccountService(RiskAssessor riskAssessor) {
		this.riskAssessor = riskAssessor;
	}

	// ...

}
```

> 注意我们使用构造器注入的时候让  `riskAssessor`  字段标记为  `final`,  指明了它不能被改变.





[上一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/16-Auto-configuration.md)                                											[主页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/tree/master/Spring-Boot)																				[下一页](https://github.com/LeonChen1024/Spring-Reference-Doc-Translation/blob/master/Spring-Boot/Part-III-Using-Spring-Boot/18-Using-the-SpringBootApplication-Annotation.md)     

