# Spring5

## IOC



## 动态代理

[java代理模式简述](https://github.com/LittleBaronHome/learnNote/blob/master/Java代理模式.md)

## AOP

> 面向切面编程，通过预编译的方式和运行期动态代理实现程序功能的统一维护的技术

### 名词解释

* 横切关注点：与业务无关。但我们需要关注的部分，如日志

* 切面（Aspect）：横切关注点被模块化后的对象，即一个类，如Log.class

* 通知（Advice）：切面要完成工作，即类中的方法

* 目标（Target）：被通知的对象

* 代理（Proxy）：向目标对象通知之后创建的对象

* 切入点（PointCut）：切面同通知执行的地点

* 连接点（JointPoint）：与切入点匹配的执行点

### 通知类型

| 通知类型 | 连接点               | 实现接口                                        |
| -------- | -------------------- | ----------------------------------------------- |
| 前置通知 | 方法前               | org.springframework.aop.MethodBeforeAdvice      |
| 后置通知 | 方法后               | org.springframework.aop.AfterReturningAdvice    |
| 环绕通知 | 方法前后             | org.aopalliance.intercept.MethodInterceptor     |
| 异常通知 | 方法抛出异常         | org.springframework.aop.ThrowsAdvice            |
| 引进通知 | 类中增加新的方法属性 | org.springframework.aop.IntroductionInterceptor |

### 代码实战

**1. 引包 ** 

```xml
<!-- 引入包 --!>
<dependency>
	<groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```
**2. 定义业务类 **

```java
// User.java
public interface User {
    void add();
    void update();
    void remove();
}

// UserImpl.java
public class UserImpl implements User {
    @Override
    void add() {
        Sytem.out.println("add");
    }
    
    @Override
    void update() {
        Sytem.out.println("update");
    }
    
    @Override
    void remove() {
        Sytem.out.println("remove");
    }
}
```

**3. 定义切面 （两种方式）**

**3.1. 实现官方接口定义切面**

```java
public class BeforeLog implements MethodBeforeAdvice {
    /**
     *
     * @param method 要执行的目标方法
     * @param args 目标方法参数
     * @param target 目标对象
     * @throws Throwable
     */
    @Override
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(String.format("即将执行: %s类的%s方法！", target.getClass().getName(), method.getName()));
    }
}
```

**3.2. 自定义切面**

```java
public class Log {
    public void before() {
        System.out.println("方法执行前");
    }

    public void after() {
        System.out.println("方法执行后");
    }
}
```
**4. XML配置**

```xml
<!-- 引入约束 -->
<beans xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/aop
                           https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 注册bean -->
    <bean id="beforeLog" class="com.BeforeLog" />
    <bean id="log" class="com.log" />
    
    <!-- 配置aop -->
    <aop:config>
        
        <!-- 实现官方接口定义切面 -->
        <!-- 切入点 execution(返回值 包名.类名.方法名(参数)) ..标识任何参数-->
    	<aop:pointcut id="pointcut1" expression="execution(* com.UserImpl.*(..))" />
        <!-- 在定义的切入点执行注册的bean -->
        <aop:advisor advice-ref="beforeLog" pointcut-ref="pointcut1" />
        
        
         <!-- 自定义切面 -->
        <aop:aspect ref="diy">
            <!-- 切入点 execution(返回值 包名.类名.方法名(参数)) ..标识任何参数-->
            <aop:pointcut id="pointcut2" expression="execution(* com.UserImpl.*(..))" />
            <!-- 在定义的切入点执行注册的bean -->
            <aop:before method="before" pointcut-ref="pointcut2" />
            <aop:after method="after" pointcut-ref="pointcut2" />
        </aop:aspect>
    </aop:config>
</beans>
```

**5.  注解配置**

**5.1. 自动加载**

```java
@Component // 注册bean
@Aspect // 标记切面配置类
public class Log {

    @Before("execution(* com.springboot.demo.Aop.User.*(..))")
    public void before() {
        System.out.println("方法执行前");
    }

    @After("execution(* com.springboot.demo.Aop.User.*(..))")
    public void after() {
        System.out.println("方法执行后");
    }

    @Around("execution(* com.springboot.demo.Aop.User.*(..))")
    public void round(ProceedingJoinPoint pj) throws Throwable {
        System.out.println("环绕前");

        pj.proceed();

        System.out.println("环绕后");
    }
}
```

**5.2.手动加载**

```java
// 先定义一个自定义注解类
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface LogAspect {
}

// 切面
@Component
@Aspect
public class Log {

    // 创建一个方法，方法名随意，重点是加载自定义的注解
    @Pointcut("@annotation(com.springboot.demo.Aop.LogAspect)")
    public void init(){}

    @Before("init() && @annotation(logAspect)")
    public void before(LogAspect logAspect) {
        System.out.println("方法执行前");
    }
}

// 在要记录日志的方法实现类上添加 @LogAspect注解，也可以在LogAspect中加入参数，在通知中获取
```

> 执行顺序：环绕前 - 方法执行前 - 【执行方法】- 环绕后 - 返回结果（@AfterReturning） - 方法执行后

**6. 测试**

```java
// 测试
public static void main(String[] args) {
	ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    // 动态代理代理的是接口
    User user = (User) context.getBean("user");
    
    user.add();
}
```

