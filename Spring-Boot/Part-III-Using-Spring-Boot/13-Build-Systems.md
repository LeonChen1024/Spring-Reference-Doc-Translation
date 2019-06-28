# 13. 构建系统

我们强烈推荐你选择一个支持 [*依赖管理*](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#using-boot-dependency-management) 的构建系统并且可以使用 “Maven Central” 中的仓库. 我们推荐你选择 Maven 或者 Gradle. 使用其他的构建系统 (比如Ant)也是可以运行 Spring Boot 的,但是他们没有得到很好的支持.

## 13.1 依赖管理

每个 Spring Boot 的正式版本都会提供一个它支持的依赖列表. 在使用的时候, 你不需要给构建配置中的这些依赖提供版本号,因为 Spring Boot 会替你做这些管理. 当你升级了 Spring Boot ,这些依赖会以统一的方式一起升级.

>  如果你需要,你仍然可以指定一个版本并覆盖 Spring Boot 的推荐版本.