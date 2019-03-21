# Spring 注解

## 声明 Bean
1. @Component，使用用于修饰类，表明是一个 Bean
2. @Service，在业务逻辑层使用(Service层)
3. @Repository，数据访问层(dao 层)
4. @Controller，在展示层使用，表明是一个控制器

## 注入 Bean
1. @Autowired：Spring 提供
2. @Inject：由 JSR-330 提供
3. @Resource: 由 JSR-250 提供

## Java 配置类注解
1. @Configuration，声明当前类为配置类，相当于 Spring 的 xml 配置文件；
2. @Bean 注解在方法，声明当前方法的返回值为一个 Bean，替代 xml 中的方式；
3. @ComponentScan，扫描 Bean，相当于 xml 配置文件中的 <component-scan>.