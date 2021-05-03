# Lombok工具类插件使用

## 引言

- 还记得刚刚接触Java，学习面向对象时因为手写`get`，`set`，`hashcode`，`equals`等方法的痛苦吗？
- 还记得后来上手了`Eclipse`或者`ldea`这样工具可以快速生成`get`，`set`等相关方法的兴奋吗？好强大
- 后来工作时间长了，这种兴奋渐渐就变成了的苦恼？

> **产生的苦恼原因**

很重要一个原因就是因为随着项目业务模块功能不断扩展，项目中涉及库表越来越多，导致项目中实体类（Entity）对象越来越多，每次写Entity对象最头疼就是生成`Get`，`SET`等相关方法，极大影响开发效率，如何更好的解决呢？lombok出现仿佛让我们回到曾经的那种喜悦！

## 1\. 什么是Lombok

- ### [官网解释](https://projectlombok.org/)

> Project Lombok is a **java library** that **automatically plugs** into your editor and build tools, **spicing up your java**.  Never write another **getter or equals method** again, with one **annotation** your class has a fully featured builder, Automate your logging variables, and much more.

- **谷歌翻译**

> Lombok项目是一个**Java库**，它会**自动**插入您的编辑器和构建工具中，从而使您的Java更**加生动有趣**。 永远不要再写另一个getter或equals方法，带有一个注释的您的类有一个功能全面的生成器，自动化您的日志记录变量等等。

- **百度百科**

Lombok项目是一个Java库，它会自动插入编辑器和构建工具中，Lombok提供了一组有用的注释（注解），用来消除Java类中的大量样板代码。仅五个字符（@Data）就可以替换数百行代码从而产生干净，简洁且易于维护的Java类。

- **通俗解释**

Lombok快速开发工具，提供了一组`java`相关注解，通过注解用来更快速生成`java`对象中我们想要的相关方法（`get`，`set`，`toString`.…）等一系列方法

## 2.使用Lombok

### 2.1安装lombok依赖

[官网使用maven安装依赖地址 ](https://projectlombok.org/setup/maven)

[Maven Repository安装依赖地址](https://mvnrepository.com/artifact/org.projectlombok/lombok/1.18.12)

比较多人使用的版本还是`1.18.12`

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.12</version>
    <scope>provided</scope>
</dependency>
```

### 2.2IDEA安装Lombok插件

默认在Idea(2020版本及以下)或者Eclipse中是不支持lombok语法，写上之后没有任何效果，需要在这两个IDE中额外安装插件才可以继续使用，否则会报红色的提示错误。

File-->Settings-->Plugins（插件）-->Marketplace-->搜索 Lombok-->Install-->Apply-->**重启 IDEA**

![](https://gitee.com/codeluojay/TyproaImage/raw/master/images/2021011121120718.png)

### 2.3编写实体类和使用`@Data`

```java
@Data
public class User {
    private String id;
    private String name;
    private Integer age;
    private Date birthday;
}
```

使用Lombok注解自动生成的（`get`，`set`，`toString`.…）等一系列方法，可以在`Idea`中`Structure`查看

![image-20210503182650200](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\image-20210503182650200.png)

或者利用Idea自带的反编译功能将编译过后的`class`文件夹下字节码文件打开，查看是否存在这些方法

![image-20210503183009615](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210503183009615.png)

### 2.4Lombok的好处

- 对比编译后的字节码文件，未编译前的Lombok文件更加干净简洁
- 新版2021版本的Idea中默认集成Lombok插件，可见Lombok插件已经是潮流趋势

## 3. Lombok 原理

### 3.1Lombok是插件不是框架

通过查看Lombok的注解`@Data`的源码，可以知道这个注解表明它的执行在Java源代码时期。

```java
@Target({ElementType.TYPE})
//	这个注解表明它的执行在Java源代码时期
@Retention(RetentionPolicy.SOURCE)
public @interface Data {
    String staticConstructor() default "";
}
```

而打开我们熟悉的SpringBoot的注解查看，会发觉它的执行在运行时时期。

![image-20210503185241063](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210503185241063.png)

所以Lombok的运行原理和Spring以及SpringBoot框架的注解运行是不同，Lombok只能算是一个插件，不是一个框架。

### 3.2Lombok工作原理

在Java 规中的JSR 269（Java Specification Requests）提供了 插件化注解处理 API（Pluggable Annotation Processing API），Lombok利用了这个JSR269和JDK6 提供的特性，在 Javac**编译期**（SOURCE 标识）利用注解，在字节码文件中生成 get、set 等方法。

Java代码被执行的过程，需要经过`.java源文件`编译成`.class字节码文件`最终被`JVM`执行

Java代码被执行的过程再细化一点就是，`.java源文件`需要经过`java编译器`的语法树对代码进行词法和语法分析等一些操作才能`.class字节码文件`最终被`JVM`执行。

![image-20210503190754404](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210503190754404.png)

Java代码被执行的过程中间再细化一点就如图所示

![](https://img-blog.csdnimg.cn/20210111193915248.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODM3ODAzNA==,size_16,color_FFFFFF,t_70)

所以Lombok的运行再源码时期的注解原理：将Java源文件利用自己的注解抽象成语法树（简单理解为修改语法树的内容）去给源文件的属性字段加上`get`和`set`，然后编译成字节码文件，给`JVM`执行。

![image-20210503191343820](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210503191343820.png)

所以Lombok是再语法树和字节码文件中才会生效也是基于上面原理，同时我们明白这个原理后，应该要优化Lombok插件的依赖，在开发环境中使用即可，不需要用在生产环境中,也不需要随着项目启动而启动。

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <!--定义作用域是在开发环境中使用，生产环境中不使用-->
    <scope>provided</scope>
</dependency>
```

## 4\. Lombok 注解

### 4.1 @Getter/@Setter注解源码

```java
// @Getter注解
//	FIELD：可以在字段上使用 TYPE：可以在类上使用
@Target({ElementType.FIELD, ElementType.TYPE})
//	在源码时候使用的
@Retention(RetentionPolicy.SOURCE)
public @interface Getter {
    AccessLevel value() default AccessLevel.PUBLIC;
    Getter.AnyAnnotation[] onMethod() default {};
    boolean lazy() default false;
}

// @Setter注解
@Target({ElementType.FIELD, ElementType.TYPE})
@Retention(RetentionPolicy.SOURCE)
public @interface Setter {
    AccessLevel value() default AccessLevel.PUBLIC;
}
```

1. `@Target({ElementType.FIELD, ElementType.TYPE})` 表明可以在字段和类上使用这个注解
2. ` AccessLevel value() default AccessLevel.PUBLIC;`表明可以设置访问权限（默认 public）
3. `boolean lazy() default false`表明使用懒加载机制来节省资源，（默认false不使用懒加载机制）

#### 4.1.1@Getter和@Setter使用注意事项

在单个字段上使用，只会对该字段生成`getter`方法。

![image-20210503200710382](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210503200710382.png)在类上使用注解，在这种情况下，这个注解对该类中的所有非静态字段起作用

![image-20210503201217973](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210503201217973.png)。

3\. 无法为 static 设置 get/set 方法，只为 final 类型设置 get 方法，static是存放在类中的共享变量，final代表不可更改，所以无法设置set方法。

![image-20210503202349792](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210503202349792.png)

4.访问权限的控制

除非显式地指定 AccessLevel，否则生成的 getter/setter 方法将是`public`级别,可以在单个字段上面设置访问权限

![image-20210503204024222](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210503204024222.png)

###  4.2 @ToString

```java
//	只能在类上使用
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.SOURCE)
public @interface ToString {
    boolean includeFieldNames() default true;
    //	exclude 排除ToString中不输出的多个字段
    String[] exclude() default {};
    // of 指定ToString必须包含哪些字段
    String[] of() default {};
    //	是否调用父类的字段输出到ToString中
    boolean callSuper() default false;
    //	不使用getter方法获取值来输出，直接通过获取字段值来获取值
    boolean doNotUseGetters() default false;
    //	对设置明确包含的字段设置为true后，只会对有明确声明的字段才会有效，其余没有设置一概不管
    boolean onlyExplicitlyIncluded() default false;
    @Target({ElementType.FIELD})
    @Retention(RetentionPolicy.SOURCE)
    public @interface Exclude {
    }
    @Target({ElementType.FIELD, ElementType.METHOD})
    @Retention(RetentionPolicy.SOURCE)
    public @interface Include {
        //	可以给字段设置权重，优先输出
        int rank() default 0;
        //	可以更改输出的名字
        String name() default "";
    }
}
```

@ToString的使用注意事项

`@ToString` 只能加在类上，自动生成 `ToString` 方法，使用 `exclude` 排除多个字段，`of` 必须包含哪些字段。其次是ToString内部定义的`@Include`和`@Exclude`可以用在各个字段上

```java
@ToString(exclude = {"排除字段1","排除字段2"} ,of = {"包含字段1","包含字段2"})
```

![image-20210503223123530](C:\Users\Admin\Desktop\Lombok使用介绍.assets\image-20210503223123530.png)

`@ToString.Include`和@`ToString.Exclude`的使用注意事项

![image-20210503223719280](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210503223719280.png)

细心观察，你会发现两个案例的效果是一样的，使用`@ToString.Include`和@`ToString.Exclude`和在类上的`@ToString`使用 `exclude` 和`of` 

同时使用`@ToString.Include`中的`rank=1`对`name`字段设置高权重优先于`age`输出，对`birthday`设置设置别名输出`生日`

### 4.3 @EqualsAndHashCode

```java
//	类上使用
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.SOURCE)
public @interface EqualsAndHashCode {
    //	排除
    String[] exclude() default {};
    //	包含
    String[] of() default {};
    //	是否调用父类的字段
    boolean callSuper() default false;
    //	使用get方法获取值
    boolean doNotUseGetters() default false;
    // 对设置明确包含的字段设置为true后，只会对有明确声明的字段才会有效，其余没有设置一概不管
    boolean onlyExplicitlyIncluded() default false;
    @Target({ElementType.FIELD})
    @Retention(RetentionPolicy.SOURCE)
    public @interface Exclude {
    }
    @Target({ElementType.FIELD, ElementType.METHOD})
    @Retention(RetentionPolicy.SOURCE)
    public @interface Include {
        String replaces() default "";
    }
}
```

@EqualsAndHashCode使用注意事项

生成 equals 方法、canEqual（判断一个类是否属于特定类）、hashCode 方法。也可以进行相等比较的排除，指定。其实只要把之前`@Getter`的注解中的字段吃透了，`@EqualsAndHashCode`的字段大体上都是一致的理解

```java
@EqualsAndHashCode(exclude = {"排除字段1","排除字段2"})
```

![image-20210503233456712](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210503233456712.png)

### 4.4 @NonNull

可以加在成员变量前，也可以加在方法参数前。用来指定某个字段不能为空

### 4.5 构造器注解

>     @NoArgsConstructor：无参构造器
>
>     @AllArgsConstructor:所有参数构造器

@NoArgsConstructor生成无参构造函数

![image-20210503234932922](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210503234932922.png)

@AllArgsConstructor生成所有参数的构造函数

![image-20210503235140829](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210503235140829.png)

注意：@NoArgsConstructor最好和@AllArgsConstructor一起使用，避免防止动态代理和其他运行时的类默认要调用无参构造方法时，找不到无参构造方法报其他错误。

### 4.6 @Data

集成@Getter、@Setter、@ToString、@EqualsAndHashCode、@RequiredArgsConstructor等方法

### 4.7 @Accessors（chain = true）

开启set方法的链式调用

```java
@Data
// 加上这个注解可以开启链式调用
@Accessors(chain = true)
public class User {
    private String id;
    private String name;
    private Integer age;
    private Date birthday;
}
```

在测试类的中的用法

![image-20210504001941232](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210504001941232.png)

### 4.8 @Log

支持 Log4j、Slf4j日志功能，本质上就是Lombok给我们自动生成，在IDEA中可以通过提示看到

```java
private static final org.slf4j.Logger log = org.slf4j.LoggerFactory.getLogger(UserController.class)
```

![image-20210504010038597](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210504010038597.png)

比如我在SpringBoot中的UserController中加入打印日志的语句，同时请求的这个方法，就会输出对应的日志记录，

```java

@Slf4j
@Controller
@RequestMapping("/user")
public class UserController {

    // easyexcel导出Excel到指定位置
    @GetMapping("/export2File")
    @ResponseBody
    public String export2File(@RequestParam String excelName,
                              @RequestParam String sheetName) {
        log.info("excelName:[{}],sheetName:[{}]",excelName,sheetName);
        ExcelUtils.export2File(path, excelName, sheetName, 
                               User.class, userService.getAll());
        return "导出成功";
    }
}
```

输出信息

> 2021-05-04 00:47:38.142  INFO 17492 --- [nio-8080-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
> 2021-05-04 00:47:38.142  INFO 17492 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
> 2021-05-04 00:47:38.150  INFO 17492 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 8 ms
> **2021-05-04 00:47:44.325  INFO 17492 --- [nio-8080-exec-1] c.s.ssm.controller.UserController        : excelName:[UserExcel],sheetName:[UserSheet]**

