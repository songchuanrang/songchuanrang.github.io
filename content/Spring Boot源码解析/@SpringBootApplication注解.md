---
title: SpringBootApplication注解
linktitle: SpringBootApplication注解
date: 2023-11-08 10:31:25.262 +0800
type: book
weight: 100
---

## @SpringBootApplication 类定义

@SpringBootApplication 标注在某个类上，说明这个类是 SpringBoot 的主配置类，SpringBoot 就应该运行这个类的 main 方法来启动 SpringBoot 应用。
注解定义如下：

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
        @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
        @Filter(type = FilterType.CUSTOM,
                classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {

    /**
     * Exclude specific auto-configuration classes such that they will never be applied.
     * @return the classes to exclude
     */
    @AliasFor(annotation = EnableAutoConfiguration.class, attribute = "exclude")
    Class<?>[] exclude() default {};

    /**
     * Exclude specific auto-configuration class names such that they will never be
     * applied.
     * @return the class names to exclude
     * @since 1.3.0
     */
    @AliasFor(annotation = EnableAutoConfiguration.class, attribute = "excludeName")
    String[] excludeName() default {};

    /**
     * Base packages to scan for annotated components. Use {@link #scanBasePackageClasses}
     * for a type-safe alternative to String-based package names.
     * @return base packages to scan
     * @since 1.3.0
     */
    @AliasFor(annotation = ComponentScan.class, attribute = "basePackages")
    String[] scanBasePackages() default {};

    /**
     * Type-safe alternative to {@link #scanBasePackages} for specifying the packages to
     * scan for annotated components. The package of each class specified will be scanned.
     * <p>
     * Consider creating a special no-op marker class or interface in each package that
     * serves no purpose other than being referenced by this attribute.
     * @return base packages to scan
     * @since 1.3.0
     */
    @AliasFor(annotation = ComponentScan.class, attribute = "basePackageClasses")
    Class<?>[] scanBasePackageClasses() default {};

}
```

## @SpringBootApplication 定义解析

### @Target

### @Retention
