# SpringBoot常用注解

## 1、@SpringBootApplication

​     查看源码可发现：@SpringBootApplication是一个复合注解，包含了@SpringBootConfiguration，@EnableAutoConfiguration，@ComponentScan这三个注解。

##     @SpringBootConfiguration：

​         标注当前类是配置类，这个注解继承自@Configuration。并会将当前类内声明的一个或多个以@Bean注解标记的方法的实例纳入到spring容器中，并且实例名及时方法名。

##      @EnableAutoCofiguraion:

​        自动配置的注解，这个注解会根据我们添加



