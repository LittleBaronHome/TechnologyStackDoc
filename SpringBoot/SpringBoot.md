# SpringBoot

将Spring繁琐的配置注解化，并内置了大量的常用组件

>  推荐使用Intellj IDEA创建SpringBoot项目，IDEA继承了SpringBoot官网，不同版本IDEA包含的springBoot版本也一样。
>
> File -> New -> Project -> Spring Initializr
> 
> 在依赖选择界面，可选择
>
> * Developer -> Spring Boot DevTools 实现热部署
> * Web -> Spring Web 自动导入Web项目依赖

## 依赖管理

**pom.xml**

```xml
<!-- 继承父依赖进行依赖版本管理，springBoot的核心依赖在父工程中 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.16.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

<!-- 项目信息 -->
<groupId>com.springboot</groupId>
<artifactId>demo</artifactId>
<version>0.0.1-SNAPSHOT</version>
<name>springboot-demo</name>
<description>Demo project for Spring Boot</description>

<!-- 项目属性 -->
<properties>
    <java.version>1.8</java.version>
</properties>

<!-- 依赖管理，版本号由父依赖管理 -->
<dependencies>
    <!-- spring-boot-starter-web 继承了Web项目所需的依赖，包括RsetFul、SpringMVC、Tomcat等，是Web项目必备依赖 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    
    <!-- spring-boot-starter-test 是SpringBoot项目测试依赖包，默认test作用域 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>

<!-- maven打包插件 -->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

SpringBoot为项目所需依赖提供了一站式解决方案，即starter

下表列出常用的springboot提供的starter

> 详细文档参照springboot官网中对应版本的 *Reference Doc - > Using Sping Boot -> Starters*

| Name                           | Description                                                  |
| :----------------------------- | :----------------------------------------------------------- |
| `spring-boot-starter`          | Core starter, including auto-configuration support, logging and YAML |
| `spring-boot-starter-activemq` | Starter for JMS messaging using Apache ActiveMQ              |
| `spring-boot-starter-amqp`     | Starter for using Spring AMQP and Rabbit MQ                  |
| `spring-boot-starter-aop`      | Starter for aspect-oriented programming with Spring AOP and AspectJ |
| `spring-boot-starter-data-jdbc` | Starter for using Spring Data JDBC               |
| `spring-boot-starter-data-jpa`  | Starter for using Spring Data JPA with Hibernate |
| `spring-boot-starter-data-redis` | Starter for using Redis key-value data store with Spring Data Redis and the Lettuce client |
| `spring-boot-starter-jdbc`       | Starter for using JDBC with the HikariCP connection pool     |
| `spring-boot-starter-quartz`   | Starter for using the Quartz scheduler |
| `spring-boot-starter-security` | Starter for using Spring Security      |
| `spring-boot-starter-thymeleaf`    | Starter for building MVC web applications using Thymeleaf views |
| `spring-boot-starter-validation`   | Starter for using Java Bean Validation with Hibernate Validator |
| `spring-boot-starter-web`          | Starter for building web, including RESTful, applications using Spring MVC. Uses Tomcat as the default embedded container |
| `spring-boot-starter-web-services` | Starter for using Spring Web Services                        |
| `spring-boot-starter-actuator` | Starter for using Spring Boot’s Actuator which provides production ready features to help you monitor and manage your application |
| `spring-boot-starter-logging`  | Starter for logging using Logback. Default logging starter   |

## 自动装配原理

### 原理

![启动原理图](D:\Typora\SpringBoot\images\启动原理图.png)

### 源码

**主程序**

```java
// 项目配置、启动类
@SpringBootApplication
public class SpringbootDemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootDemoApplication.class, args);
    }
}
```

**核心注解@SpringBootApplication**

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration // 说明此类为配置类（此注解下钻后只有@Configuration注解）
@EnableAutoConfiguration // 自动配置
// 扫描当前类同级包
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {}
```

**自动配置注解@EnableAutoConfiguration**

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage // 自动配置包
@Import(AutoConfigurationImportSelector.class) // 导入选择器
public @interface EnableAutoConfiguration {}
```

**自动配置包注解@AutoConfigurationPackage**

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import(AutoConfigurationPackages.Registrar.class) // 导入了AutoConfigurationPackages.Registrar类
public @interface AutoConfigurationPackage {}
```

**AutoConfigurationPackages.Registrar类**

```java
/**
 * {@link ImportBeanDefinitionRegistrar} to store the base package from the importing
 * configuration.
 */
static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports {

    @Override
    public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
        register(registry, new PackageImport(metadata).getPackageName());
    }

    @Override
    public Set<Object> determineImports(AnnotationMetadata metadata) {
        return Collections.singleton(new PackageImport(metadata));
    }

}
```

**AutoConfigurationImportSelector选择器类**

```java
public class AutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware,
		ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {
	/**
	 * Return the {@link AutoConfigurationEntry} based on the {@link AnnotationMetadata}
	 * of the importing {@link Configuration @Configuration} class.
	 * @param autoConfigurationMetadata the auto-configuration metadata
	 * @param annotationMetadata the annotation metadata of the configuration class
	 * @return the auto-configurations that should be imported
	 */
    // 获取自动配置的实体类
	protected AutoConfigurationEntry getAutoConfigurationEntry(AutoConfigurationMetadata autoConfigurationMetadata,
			AnnotationMetadata annotationMetadata) {
		if (!isEnabled(annotationMetadata)) {
			return EMPTY_ENTRY;
		}
		AnnotationAttributes attributes = getAttributes(annotationMetadata);
        // 获取候补配置集合
		List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
		configurations = removeDuplicates(configurations);
		Set<String> exclusions = getExclusions(annotationMetadata, attributes);
		checkExcludedClasses(configurations, exclusions);
		configurations.removeAll(exclusions);
		configurations = filter(configurations, autoConfigurationMetadata);
		fireAutoConfigurationImportEvents(configurations, exclusions);
		return new AutoConfigurationEntry(configurations, exclusions);
	}            
}
```

**获取候补配置集合方法getCandidateConfigurations**

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    // 获取候补配置
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(
        getSpringFactoriesLoaderFactoryClass(), // EnableAutoConfiguration注解类
        getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
                    + "are using a custom packaging, make sure that file is correct.");
    return configurations;
}

protected Class<?> getSpringFactoriesLoaderFactoryClass() {
    return EnableAutoConfiguration.class;
}
```
```java
public final class SpringFactoriesLoader {
    public static final String FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories";
    
    public static List<String> loadFactoryNames(Class<?> factoryClass, @Nullable ClassLoader classLoader) {
        String factoryClassName = factoryClass.getName();
        // 先加载 META-INF/spring.factories 文件（在spring-boot-autoconfig包下）
        // 在筛选出文件中 EnableAutoConfiguration 下的配置
        return loadSpringFactories(classLoader).getOrDefault(factoryClassName, Collections.emptyList());
    }
   
    // 加载 META-INF/spring.factories 文件
    private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {}
   
}
```

**META-INF/spring.factories**

```properties
# Initializers
org.springframework.context.ApplicationContextInitializer=\
org.springframework.boot.autoconfigure.SharedMetadataReaderFactoryContextInitializer,\
org.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener

# Application Listeners
org.springframework.context.ApplicationListener=\
org.springframework.boot.autoconfigure.BackgroundPreinitializer

# Auto Configuration Import Listeners
org.springframework.boot.autoconfigure.AutoConfigurationImportListener=\
org.springframework.boot.autoconfigure.condition.ConditionEvaluationReportAutoConfigurationImportListener

# Auto Configuration Import Filters
org.springframework.boot.autoconfigure.AutoConfigurationImportFilter=\
org.springframework.boot.autoconfigure.condition.OnBeanCondition,\
org.springframework.boot.autoconfigure.condition.OnClassCondition,\
org.springframework.boot.autoconfigure.condition.OnWebApplicationCondition

# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
```

> 借助注解@ConditionalOn*，springboot巧妙的实现了引入对应的starter包，生效对应的配置文件

**案例：AopAutoConfiguration**

```java
@Configuration // 这是一个配置类
// 当导入了EnableAspectJAutoProxy.class, Aspect.class, Advice.class, AnnotatedElement.class这几个类，此配置类才生效
@ConditionalOnClass({ EnableAspectJAutoProxy.class, Aspect.class, Advice.class, AnnotatedElement.class })
// 当满足prefix = "spring.aop", name = "auto", havingValue = "true", matchIfMissing = true这几个值，此配置类才生效
@ConditionalOnProperty(prefix = "spring.aop", name = "auto", havingValue = "true", matchIfMissing = true)
public class AopAutoConfiguration {

	@Configuration
	@EnableAspectJAutoProxy(proxyTargetClass = false)
	@ConditionalOnProperty(prefix = "spring.aop", name = "proxy-target-class", havingValue = "false",
			matchIfMissing = false)
	public static class JdkDynamicAutoProxyConfiguration {

	}

	@Configuration
	@EnableAspectJAutoProxy(proxyTargetClass = true)
	@ConditionalOnProperty(prefix = "spring.aop", name = "proxy-target-class", havingValue = "true",
			matchIfMissing = true)
	public static class CglibAutoProxyConfiguration {

	}

}
```

### 总结

>  **springboot所有自动配置都是在启动的时候扫描并加载`spring.factories`所有自动配置类，判断条件是否成立，只要导入的相应的starter，就会加载对应的配置**

1. 主启动类上的核心注解`@SpringBootApplication`继承了两个注解`@SpringBootConfiguration`和`@EnableAutoConfiguration`

2. `@SpringBootConfiguration`只继承了`@Configuration`代表了这是一个配置类

3. `@EnableAutoConfiguration`继承了两个注解`@AutoConfigurationPackage`和`@Import(AutoConfigurationImportSelector.class)`

4. `@AutoConfigurationPackage`中引入了一个选择器`@Import(AutoConfigurationPackages.Registrar.class)`

5. `AutoConfigurationImportSelector.class`自动导入选择器中通过方法`getAutoConfigurationEntry()`加载所有候选的配置文件

   * `getAutoConfigurationEntry()`中调用`getCandidateConfigurations()`，
* 在`getCandidateConfigurations()`中通过传入`EnableAutoConfiguration.class`调用`SpringFactoriesLoader.loadFactoryNames()`
   * `SpringFactoriesLoader.loadFactoryNames()`加载位于`spring-boot-autoconfigure.jar!META-INF/spring.factories`下的所有配置文件，过滤分类为EnableAutoConfiguration的Config文件集合
* Config文件中通过`@ConditionalOn*`注解决定了该配置文件是否加载

## 主启动类

```java
@SpringBootApplication
public class SpringbootDemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootDemoApplication.class, args);
    }
}

// 构造器
@SuppressWarnings({ "unchecked", "rawtypes" })
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
    this.resourceLoader = resourceLoader;
    Assert.notNull(primarySources, "PrimarySources must not be null");
    this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources));
    this.webApplicationType = WebApplicationType.deduceFromClasspath();
    setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class));
    setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
    this.mainApplicationClass = deduceMainApplicationClass();
}
```

1. 推断应用类型是普通的项目还是Web项目
2. 查找并加载所有可用初始化器，设置到initializers中
3. 找出所有应用程序监听器，设置到listeners中
4. 推断设置main方法的定义类，找到主类

### 主启动类启动过程  

后续再完善

## 配置文件

> springboot支持.properties和.yml两种配置文件类型
>
> 推荐使用yml
>
> * yml支持数组、对象等多种复杂数据类型的配置，properties只能配置键值对
>
> * yml可使用占位符、el表达式等高级用法
> 
> * 不同文件类型加载顺序 yml -> yaml -> properties (properties会最终生效)
>
> **如果在业务中专门编写了一个bean与配置项对应，可直接使用@configurationProperties注解绑定，支持JSR303检验**
>
> **如果只是获取配置文件的某个值，则使用@Value绑定**

### 路径

**配置文件可以放置的位置有(加载顺序：4 -3 - 2 - 1   1位置的文件最后加载，覆盖前边的文件)：**

1. file:/config/
2. file:/
3. classpath:/config
4. classpath:/

### 配置项

在自动装配原理中我们知道了springboot的所有自动装配类都在`spring-boot-autoconfigure.jar!META-INF/spring.factories`中定义了`*AutoConfiguration`的集合，在`*AutoConfiguration`类中会通过`@EnableConfigurationProperties`的注解引入一个`*.Properties`类，在该类中就定义了该组件可以配置的所有配置项，可能声明在大类或内部类上。

> **再配置文件中配置`debug: true`就可以再项目启动时看到当前项目自动配置了哪些组件**

```java
// 可以声明在大类上
@Configuration
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
@AutoConfigureAfter({ DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class,
		ValidationAutoConfiguration.class })
public class WebMvcAutoConfiguration {
    
    @Configuration
	@Import(EnableWebMvcConfiguration.class)
    // 也可以声明在内部类
	@EnableConfigurationProperties({ WebMvcProperties.class, ResourceProperties.class })
	@Order(0)
	public static class WebMvcAutoConfigurationAdapter implements WebMvcConfigurer {}
}
```

`WebMvcProperties.class`,` ResourceProperties.class`就是两个配置项类

```java
@ConfigurationProperties(prefix = "spring.mvc")
public class WebMvcProperties {
    /**
	 * Formatting strategy for message codes. For instance, `PREFIX_ERROR_CODE`.
	 */
	private DefaultMessageCodesResolver.Format messageCodesResolverFormat;

	/**
	 * Locale to use. By default, this locale is overridden by the "Accept-Language"
	 * header.
	 */
	private Locale locale;

	/**
	 * Define how the locale should be resolved.
	 */
	private LocaleResolver localeResolver = LocaleResolver.ACCEPT_HEADER;

	/**
	 * Date format to use. For instance, `dd/MM/yyyy`.
	 */
	private String dateFormat;

	/**
	 * Whether to dispatch TRACE requests to the FrameworkServlet doService method.
	 */
	private boolean dispatchTraceRequest = false;

	/**
	 * Whether to dispatch OPTIONS requests to the FrameworkServlet doService method.
	 */
	private boolean dispatchOptionsRequest = true;
}
```

`*.Properties`类中定义的成员变量就是在配置文件application.yml中可以配中的内容。

### 多Profile

**方式一：将不同环境的配置文件分开编写，以application-*.yml的方式命名，在application.yml文件中通过`spring.profiles.active`属性配置**

application.yml

application-dev.yml

application-uat.yml

**方式二：用`---`分隔多部分内容**

```yml
spring:
  profiles:
    active: dev
# 默认配置
server:
  port: 8080
  
---
spring:
  profiles: dev
server:
  port: 8081
  
---
spring:
  profiles: uat
server:
  port: 8082
  
```

## 静态资源

在**自动装配原理**章节提到所有的自动配置都会在`*.Configuration`类中定义，静态资源的加载就在`WebMvcAutoConfiguration`类中有一个`addResourceHandlers()`方法用来处理静态资源的加载

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    // 若配置中关闭了静态资源加载，则直接返回
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
        return;
    }
    // webjars方式不常用，暂不探究
    Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
    CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
    if (!registry.hasMappingForPattern("/webjars/**")) {
        customizeResourceHandlerRegistration(registry.addResourceHandler("/webjars/**")
                                             .addResourceLocations("classpath:/META-INF/resources/webjars/")
                                             .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
    }
    // 加载静态资源
    String staticPathPattern = this.mvcProperties.getStaticPathPattern();
    if (!registry.hasMappingForPattern(staticPathPattern)) {
        customizeResourceHandlerRegistration(registry
                                // 加载WebMvcProperties中定义的staticPathPattern路径 /**
                                .addResourceHandler(staticPathPattern)
                                // 加载ResourceProperties中定义的CLASSPATH_RESOURCE_LOCATIONS路径
                                // "classpath:/META-INF/resources/", "classpath:/resources/", 
                                // "classpath:/static/", "classpath:/public/"
                                .addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations()))
                                .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl)
        );
    }
}
```

**总结**

springboot项目可以识别(若重名，加载顺序：4 - 3 - 2 - 1  1中的文件会覆盖前面的文件)

1. resources/META-INF/resources
2. resources/resources
3. resources/static
4. resources/public

> **classpath:下通常还会有一个templates文件夹，该文件夹下的文件只能通过Controller跳转**

### 首页

`WebMvcAutoConfiguration`类中有一个`welcomePageHandlerMapping()`方法用来加载首页

```java
// 加载首页映射
@Bean
public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext) {
    WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(
        new TemplateAvailabilityProviders(applicationContext), applicationContext, getWelcomePage(),
        this.mvcProperties.getStaticPathPattern());
    welcomePageHandlerMapping.setInterceptors(getInterceptors());
    welcomePageHandlerMapping.setCorsConfigurations(getCorsConfigurations());
    return welcomePageHandlerMapping;
}

private Optional<Resource> getWelcomePage() {
    String[] locations = getResourceLocations(this.resourceProperties.getStaticLocations());
    return Arrays.stream(locations).map(this::getIndexHtml).filter(this::isReadable).findFirst();
}

private Resource getIndexHtml(String location) {
    // 获取静态资源路径下的index.html文件
    return this.resourceLoader.getResource(location + "index.html");
}
```

**在静态资源路径下添加index.html文件，就可用通过ip:port直接访问到**

### 模板引擎Thymeleaf

1. 导入Thymeleaf的starter`spring-boot-starter-thymeleaf`
2. 将html文件放在templates目录下
3. 在html文件中加入约束 `xmlns:th="http://www.thymeleaf.org"`

> Thymeleaf语法不在这里探究

## 整合SpringMVC

> **springboot官网中对应版本的 *Reference Doc - > Sping Boot Feature-> Developing Web Applications -> Spring MVC Auto-configuration*中描述**
>
> The auto-configuration adds the following features on top of Spring’s defaults:
>
> - Inclusion of `ContentNegotiatingViewResolver` and `BeanNameViewResolver` beans.
> - Support for serving static resources, including support for WebJars (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.4.0-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content))).
> - Automatic registration of `Converter`, `GenericConverter`, and `Formatter` beans.
> - Support for `HttpMessageConverters` (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.4.0-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-spring-mvc-message-converters)).
> - Automatic registration of `MessageCodesResolver` (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.4.0-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-spring-message-codes)).
> - Static `index.html` support.
> - Custom `Favicon` support (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.4.0-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-spring-mvc-favicon)).
> - Automatic use of a `ConfigurableWebBindingInitializer` bean (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.4.0-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-spring-mvc-web-binding-initializer)).
>
> If you want to keep those Spring Boot MVC customizations and make more [MVC customizations](https://docs.spring.io/spring/docs/5.2.9.RELEASE/spring-framework-reference/web.html#mvc) (interceptors, formatters, view controllers, and other features), you can add your own `@Configuration` class of type `WebMvcConfigurer` but **without** `@EnableWebMvc`.

Spring boot MVC自动配置包括以下：

* 支持视图解析器

* 支持静态资源

* 支持自动转换（如，前端字段与后端pojo字段的对应）

* 支持HTTP消息转换

* 支持Message Code映射

* 支持首页定制

* 支持图标定制

* 支持初始化绑定

如果你想保留这些自动配置，并添加更多的自定义配置，可以添加一个实现了接口`WebMvcConfigurer`的配置类`@Configuration`，通过重写方法实现，不要使用`@EnableWebMvc`

  ## 整合Spring Data JPA

### 导入JPA的starter

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

###  配置数据库

```yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull&allowMultiQueries=true&useSSL=false
    username: root
    password: mysql123
  jpa:
    database: MySQL
    database-platform: org.hibernate.dialect.MySQL5InnoDBDialect
    show-sql: true
    hibernate:
      ddl-auto: update
```

> **ddl-auto**
>
> `create`：每次运行程序时，都会重新创建表，故而数据会丢失
>
> `create-drop`：每次运行程序时会先创建表结构，然后待程序结束时清空表
>
> `upadte`：每次运行程序，没有表时会创建表，如果对象发生改变会更新表结构，原有数据不会清空，只会更新（推荐使用）
>
> `validate`：运行程序会校验数据与数据库的字段类型是否相同，字段不同会报错
>
> `none`: 禁用DDL处理

### 实体类

> 每个注解的详细参数可下钻到注解内部查看

```java
import javax.persistence.*;

@Entity
@Table(schema = "test", name = "user")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;
    
    @Column(name = "name", length = 10, nullable = false)
    private String name;
    
    @Column(name = "create_time")
    private Date createTime;

}
```

>@GeneratedValue提供了主键的生成策略，有两个属性,分别是strategy和generator
>
>**strategy属性：**提供四种值:
>
>* AUTO主键由程序控制, 是默认选项 ,不设置就是这个
>
>* IDENTITY 主键由数据库生成, 采用数据库自增长, Oracle不支持这种方式
>
>* SEQUENCE 通过数据库的序列产生主键, MYSQL  不支持
>
>* Table 提供特定的数据库产生主键, 该方式更有利于数据库的移植
>
>@GenericGenerator 是Hibernate提供的主键生成策略注解，搭配@GeneratedValue使用
>
>```java
>@Id
>@GenericGenerator(name = "idGenerator", strategy = "uuid")
>@GeneratedValue(generator = "idGenerator")
>private String id;
>```

### DAO

```JAVA
@Repository
public interface UserRepository extends JpaRepository<User, Integer> {
}
```

### Service

```java
@Autowired
private UserRepository userRepository;

public void add() {
    userRepository.save(new User("qiao", null));
}
```

> **内置方法**通过下钻父类`JpaRepository<User, Integer>`查看(三层)
>
> `public interface JpaRepository<T, ID> extends PagingAndSortingRepository<T, ID>`
>
> `public interface PagingAndSortingRepository<T, ID> extends CrudRepository<T, ID>`
>
> `public interface CrudRepository<T, ID>`

### 自定义查询

Spring Data Jpa通过解析方法名创建查询，框架在进行方法名解析时，会先把方法名多余的前缀find…By, read…By, query…By, count…By以及get…By截取掉，然后对剩下部分进行解析，第一个By会被用作分隔符来指示实际查询条件的开始。 我们可以在实体属性上定义条件，并将它们与And和Or连接起来，从而创建大量查询

```java
User findByUsername(String username);

List<User> findByUsernameLike(String username);

User findByUsernameAndPassword(String username, String password);

List<User> findByIdIn(List<String> ids);

List<User> findByIdInOrderByUsername(List<String> ids);

void deleteByIdIn(List<String> ids);

Long countByUsernameLike(String username);
```

#### 支持的关键字

| Keyword           | Sample                                                  | JPQL snippet                                                 |
| :---------------- | :------------------------------------------------------ | :----------------------------------------------------------- |
| And               | findByLastnameAndFirstname                              | … where x.lastname = ?1 and x.firstname = ?2                 |
| Or                | findByLastnameOrFirstname                               | … where x.lastname = ?1 or x.firstname = ?2                  |
| Is,Equals         | findByFirstname,findByFirstnameIs,findByFirstnameEquals | … where x.firstname = ?1                                     |
| Between           | findByStartDateBetween                                  | … where x.startDate between ?1 and ?2                        |
| LessThan          | findByAgeLessThan                                       | … where x.age < ?1                                           |
| LessThanEqual     | findByAgeLessThanEqual                                  | … where x.age <= ?1                                          |
| GreaterThan       | findByAgeGreaterThan                                    | … where x.age > ?1                                           |
| GreaterThanEqual  | findByAgeGreaterThanEqual                               | … where x.age >= ?1                                          |
| After             | findByStartDateAfter                                    | … where x.startDate > ?1                                     |
| Before            | findByStartDateBefore                                   | … where x.startDate < ?1                                     |
| IsNull            | findByAgeIsNull                                         | … where x.age is null                                        |
| IsNotNull,NotNull | findByAge(Is)NotNull                                    | … where x.age not null                                       |
| Like              | findByFirstnameLike                                     | … where x.firstname like ?1                                  |
| NotLike           | findByFirstnameNotLike                                  | ... findByFirstnameNotLike                                   |
| StartingWith      | findByFirstnameStartingWith                             | … where x.firstname like ?1 (parameter bound with appended %) |
| EndingWith        | findByFirstnameEndingWith                               | … where x.firstname like ?1 (parameter bound with prepended %) |
| Containing        | findByFirstnameContaining                               | … where x.firstname like ?1 (parameter bound wrapped in %)   |
| OrderBy           | findByAgeOrderByLastnameDesc                            | … where x.age = ?1 order by x.lastname desc                  |
| Not               | findByLastnameNot                                       | … where x.lastname <> ?1                                     |
| In                | findByAgeIn(Collection<Age> ages)                       | … where x.age in ?1                                          |
| NotIn             | findByAgeNotIn(Collection<Age> ages)                    | … where x.age not in ?1                                      |
| True              | findByActiveTrue()                                      | … where x.active = true                                      |
| False             | findByActiveFalse()                                     | … where x.active = false                                     |
| IgnoreCase        | findByFirstnameIgnoreCase                               | … where UPPER(x.firstame) = UPPER(?1)                        |

#### @Query

```java
@Query("select u from User u where u.email = ?1")
User getByEmail(String eamil);

@Query("select u from User u where u.username = ?1 and u.password = ?2")
User getByUsernameAndPassword(String username, String password);

@Query("select u from User u where u.username like %?1%")
List<User> getByUsernameLike(String username);
```

#### 使用命名参数

```java
@Query("select u from User u where u.id = :id")
User getById(@Param("id") String userId);

@Query("select u from User u where u.username = :username or u.email = :email")
User getByUsernameOrEmail(@Param("username") String username, @Param("email") String email);
```

#### 限制查询结果

> Spring Data Jpa支持使用`first`、`top`以及`Distinct` 关键字来限制查询结果

```java
User findFirstByUsernameOrderByUsernameAsc(String username);

List<User> findTop10ByUsername(String username, Sort sort);
    
List<User> findTop10ByUsername(String username, Pageable pageable);
```

## 整合Swagger3

> Swagger2（基于openApi3）已经在17年停止维护了，取而代之的是 sagger3（基于openApi3），
>
> 而国内几乎没有 sagger3使用的文档，百度搜出来的都是swagger2的使用
>
> 

**1. pom.xml**

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>${springfox.version}</version>
</dependency>
```

**2. 在主启动类上添加`@EnableOpenApi`注解启动swagger**

此时，启动项目，访问http://127.0.0.1:8080/swagger-ui/就可以直接访问到swagger页面

**3. 自定义配置**

```java
@Configuration
public class SwaggerConfig {

    // 分组实现
    @Bean
    public Docket allDocket(Environment environment) {
        return getDefaultDocket(environment, "A所有接口", "/**");
    }

    @Bean
    public Docket demoDocket(Environment environment) {
        return getDefaultDocket(environment, "Demo接口", "/demo/**");
    }

    private Docket getDefaultDocket(Environment environment, String groupName, String path){
        return new Docket(DocumentationType.OAS_30)
                // 文档信息
                .apiInfo(
                        new ApiInfoBuilder()
                                .title("JPI微服务接口文档")
                                .description("JPI微服务接口文档")
                                .version("1.0.1")
                                .contact(new Contact("", "", ""))
                                .build()
                )
                // 配置展示接口文档的环境
                .enable(environment.acceptsProfiles(Profiles.of("dev", "preview", "stage")))
                .groupName(groupName)
                .select()
                // 配置扫描包
                .apis(RequestHandlerSelectors.basePackage("com.gds.jpi"))
                .paths(PathSelectors.ant(path))
                .build();
    }
}
```



