---
layout:     post
title:      "Java 注解深入理解"
subtitle:   "\"Java注解 \""
date:       2016-09-23 20:00:00
author:     "Devin"
header-img: "img/leetcode-algorithm.jpg"
tags:
    - Java
    - 注解
---

## 内容概要

* Annotation的概念

* Annotation的作用

* Annotation的分类

* 系统内置注解

* 元注解

* 自定义注解

* 解析注解信息

* JDK8注解新特性

附：[项目源码地址](https://github.com/zhuifengshen/AnnotationDemo)

## 一、Annotation的概念

Annotation(注解)是插入代码中的元数据，在JDK5.0及以后版本引入。它可以在编译期使用预编译工具进行处理, 也可以在运行期使用 Java 反射机制进行处理，用于创建文档，跟踪代码中的依赖性，甚至执行基本编译时检查。因为本质上，Annotion是一种特殊的接口，程序可以通过反射来获取指定程序元素的Annotion对象，然后通过Annotion对象来获取注解里面的元数据。（元数据从metadata一词译来，就是“关于数据的数据”的意思）

## 二、Annotation的作用

**Annotation的作用大致可分为三类：**

* 编写文档：通过代码里标识的元数据生成文档；
 
* 代码分析：通过代码里标识的元数据对代码进行分析；

* 编译检查：通过代码里标识的元数据让编译器能实现基本的编译检查；

综上所述可知，Annotation主要用于提升软件的质量和提高软件的生产效率。

## 三、Annotation的分类

* **根据成员个数分类**

1.标记注解：没有定义成员的Annotation类型，自身代表某类信息，如：@Override

2.单成员注解：只定义了一个成员，比如@SuppressWarnings 定义了一个成员String[] value，使用value={...}大括号来声明数组值，一般也可以省略“value=”

3.多成员注解：定义了多个成员，使用时以name=value对分别提供数据

* **根据注解使用的功能和用途分类**

1.系统内置注解：系统自带的注解类型，如@Override

2.元注解：注解的注解，负责注解其他注解，如@Target

3.自定义注解：用户根据自己的需求自定义的注解类型


## 四、系统内置注解

* **JavaSE中内置三个标准注解，定义在java.lang中**

1.@Override：用于修饰此方法覆盖了父类的方法；

2.@Deprecated：用于修饰已经过时的方法；

3.@SuppressWarnnings:用于通知java编译器禁止特定的编译警告；

* **@Override 限定重写父类方法**

@Override 是一个标记注解类型，它被用作标注方法。它说明了被标注的方法重写了父类的方法，起到了断言的作用。如果我们使用了这种Annotation在一个没有覆盖父类方法的方法时，java编译器将以一个编译错误来警示。

下面的代码是一个使用@Override修饰一个企图重写父类的displayName()方法，而又存在拼写错误成displayname()，这时编译器就会提示错误：

```java
public class Fruit{
    public void displayName(){
        System.out.println("水果的名字是：*****");
    }
}

class Orange extends Fruit{
    @Override
    public void displayName(){
        System.out.println("水果的名字是：桔子");
    }
}

class Peach extends Fruit{
    @Override
    public void displayname(){
        System.out.println("水果的名字是：桃子");
    }
}
```
Orange 类编译不会有任何问题，Peach 类在编译的时候会提示相应的错误；@Override注解只能用于方法，不能用于其他程序元素。

* **@Deprecated 用于标记已过时**

Deprecated也是一个标记注解。当一个类型或者类型成员使用@Deprecated修饰的话，编译器将不鼓励使用这个被标注的程序元素。而且这种修饰具有一定的 “延续性”：如果我们在代码中通过继承或者覆盖的方式使用了这个过时的类型或者成员，虽然继承或者覆盖后的类型或者成员并不是被声明为 @Deprecated，但编译器仍然要报警。

注意，@Deprecated这个annotation类型和javadoc中的 @deprecated这个tag是有区别的：前者是java编译器识别的，而后者是被javadoc工具所识别用来生成文档。

下面一段程序中使用了@Deprecated注解标示方法过期，同时在方法注释中用@deprecated tag 标示该方法已经过时，代码如下：

```java
public class AppleService {
    public void displayName(){
        System.out.println("水果的名字是：苹果");
    }

    /**
     * @deprecated 该方法已经过期，不推荐使用
     */
    @Deprecated
    public void showTaste(){
        System.out.println("水果的苹果的口感是：脆甜");
    }

    public void showTaste(int typeId){
        if(typeId==1){
            System.out.println("水果的苹果的口感是：酸涩");
        }
        else if(typeId==2){
            System.out.println("水果的苹果的口感是：绵甜");
        }
        else{
            System.out.println("水果的苹果的口感是：脆甜");
        }
    }
}

public class AppleConsumer {
    //@SuppressWarnings({"deprecation"})
    public static void main(String[] args) {
        AppleService appleService=new AppleService();
        appleService.showTaste();
        appleService.showTaste(2);
    }
}

```
AppleService类的showTaste() 方法被@Deprecated标注为过时方法，在AppleConsumer类中使用的时候，编译器会给出该方法已过期，不推荐使用的提示。

* **@SuppressWarnnings 抑制编译器警告**

@SuppressWarnings 其注解目标为类、字段、函数、函数入参、构造函数和函数的局部变量。在java5.0，sun提供的javac编译器为我们提供了-Xlint选项来使编译器对合法的程序代码提出警告，此种警告从某种程度上代表了程序错误。例如当我们使用一个generic collection类而又没有提供它的类型时，编译器将提示出"unchecked warning"的警告。通常当这种情况发生时，我们就需要查找引起警告的代码。如果它真的表示错误，我们就需要纠正它。例如如果警告信息表明我们代码中的switch语句没有覆盖所有可能的case，那么我们就应增加一个默认的case来避免这种警告。

有时我们无法避免这种警告，例如，我们使用必须和非generic的旧代码交互的generic collection类时，我们不能避免这个unchecked warning。此时@SuppressWarning就要派上用场了，在调用的方法前增加@SuppressWarnings修饰，告诉编译器停止对此方法的警告。

SuppressWarning不是一个标记注解。它有一个类型为String[]的成员，这个成员的值为被禁止的警告名。使用示例如下：

```java
public class SuppressWarningTest {
    @SuppressWarnings("unchecked")
    public void addItems2(String item){
        @SuppressWarnings("unused")
        List list = new ArrayList();
        List items = new ArrayList();
        items.add(item);
    }

    @SuppressWarnings({"unchecked","unused"})
    public void addItems1(String item){
        List list = new ArrayList();
        list.add(item);
    }

    @SuppressWarnings("all")
    public void addItems(String item){
        List list = new ArrayList();
        list.add(item);
    }
}
```

@SuppressWarnings注解的**常见参数值：**

1.deprecation：使用了不赞成使用的类或方法时的警告；

2.unchecked：执行了未检查的转换时的警告，例如当使用集合时没有用泛型 (Generics) 来指定集合保存的类型;

3.fallthrough：当 Switch 程序块直接通往下一种情况而没有 Break 时的警告;

4.path：在类路径、源文件路径等中有不存在的路径时的警告;

5.serial：当在可序列化的类上缺少 serialVersionUID 定义时的警告;

6.finally：任何 finally 子句不能正常完成时的警告;

7.unused:代码中的变量或方法没有被使用产生的警告;

8.rawtypes:使用泛型时没有指定类型的警告；

9.all：关于以上所有情况的警告。

10.[更多关键字](http://www.cnblogs.com/fsjohnhuang/p/4040785.html)


## 五、元注解

元注解的作用就是负责注解其他注解。Java5.0定义了4个标准的meta-annotation类型，它们被用来提供对其它 annotation类型作说明。Java5.0定义的元注解：

1.@Target

2.@Retention

3.@Documented

4.@Inherited

* **@Target**

作用：描述该注解修饰的范围，可被用于 packages、types（类、接口、枚举、Annotation类型）、类型成员（方法、构造方法、成员变量、枚举值）、方法参数和本地变量（如循环变量、catch参数）。

**取值**(ElementType)：

1.CONSTRUCTOR:用于描述构造器

2.FIELD:用于描述域

3.LOCAL_VARIABLE:用于描述局部变量

4.METHOD:用于描述方法

5.PACKAGE:用于描述包

6.PARAMETER:用于描述参数

7.TYPE:用于描述类、接口(包括注解类型) 或enum声明

* **@Retention**

作用：描述该注解的生命周期，表示在什么编译级别上保存该注解的信息。Annotation被保留的时间有长短：某些Annotation仅出现在源代码中，而被编译器丢弃；而另一些却被编译在class文件中；编译在class文件中的Annotation可能会被虚拟机忽略，而另一些在class被装载时将被读取（请注意并不影响class的执行，因为Annotation与class在使用上是被分离的）。

**取值**（RetentionPoicy）：

1.SOURCE:在源文件中有效（即源文件保留）

2.CLASS:在class文件中有效（即class保留）
　　　　
3.RUNTIME:在运行时有效（即运行时保留）

* **@Documented**

@Documented Annotation的作用是在生成javadoc文档的时候将该Annotation也写入到文档中。

* **@Inherited**

作用：@Inherited 元注解是一个标记注解，@Inherited阐述了某个被标注的类型是被继承的。如果一个使用了@Inherited修饰的annotation类型被用于一个class，则这个annotation将被用于该class的子类。

## 六、自定义注解

使用@interface自定义注解，自动继承了java.lang.annotation.Annotation接口，由编译程序自动完成其他细节。在定义注解时，不能继承其他的注解或接口。@interface用来声明一个注解，其中的每一个方法实际上是声明了一个配置参数。方法的名称就是参数的名称，返回值类型就是参数的类型（返回值类型只能是基本类型、Class、String、enum）。可以通过default来声明参数的默认值。

* **定义注解格式：**
public @interface 注解名 {定义体}

* **注解参数的可支持数据类型：**

1.所有基本数据类型（int,float,boolean,byte,double,char,long,short)

2.String类型

3.Class类型

4.enum类型

5.Annotation类型

6.以上所有类型的数组

* **参数定义要点**

1.只能用public或默认(default)这两个访问权修饰；

2.参数成员只能用基本类型byte,short,char,int,long,float,double,boolean八种基本数据类型和 String,Enum,Class,annotations等数据类型,以及这一些类型的数组；

3.如果只有一个参数成员，建议参数名称设为value()；
 
4.注解元素必须有确定的值，要么在定义注解的默认值中指定，要么在使用注解时指定，非基本类型的注解元素的值不可为null。因此, 使用空字符串或负数作为默认值是一种常用的做法。

* **简单的自定义注解实例：**

```java
/**
 *自定义注解MyAnnotation
 */
@Target(ElementType.TYPE) //目标对象是类型
@Retention(RetentionPolicy.RUNTIME) //保存至运行时
@Documented //生成javadoc文档时，该注解内容一起生成文档
@Inherited //该注解被子类继承
public @interface MyAnnotation {
    public String value() default ""; //当只有一个元素时，建议元素名定义为value(),这样使用时赋值可以省略"value="
    String name() default "devin"; //String
    int age() default 18; //int
    boolean isStudent() default true; //boolean
    String[] alias(); //数组
    enum Color {GREEN, BLUE, RED,} //枚举类型
    Color favoriteColor() default Color.GREEN; //枚举值
}


@MyAnnotation(
        value = "info",
        name = "myname",
        age = 99,
        isStudent = false,
        alias = {"name1", "name2"},
        favoriteColor = MyAnnotation.Color.RED
)
public class MyClass {
    //使用MyAnnotation注解，该类生成的javadoc文档包含注解信息如下：
    /*
    @MyAnnotation(value = "info", name = "myname", age = 99, isStudent = false, alias = {"name1","name2"}, favoriteColor = Color.RED)
    public class MyClass
    extends Object
     */
}


public class MySubClass extends MyClass{
	//子类MySubClass继承了父类MyClass的注解
}
```


## 七、解析注解信息

Java使用**Annotation接口**来代表程序元素前面的注解，该接口是所有Annotation类型的父接口。相应地，Java在java.lang.reflect 包下新增了**AnnotatedElement接口**，该接口代表程序中可以接受注解的程序元素。

实际上，java.lang.reflect 包所有提供的反射API扩充了读取运行时Annotation信息的能力。当一个Annotation类型被定义为运行时的Annotation后，该注解才能是运行时可见，当class文件被装载时被保存在class文件中的Annotation才会被虚拟机读取。

**AnnotatedElement接口**是所有程序元素（Field、Method、Package、Class和Constructor）的父接口，所以程序通过反射获取了某个类的AnnotatedElement对象之后，程序就可以调用该对象的如下七个方法来访问Annotation信息：

1.<T extends Annotation> T getAnnotation(Class<T> annotationClass) ：返回该程序元素上存在的、指定类型的注解，如果该类型注解不存在，则返回null；

2.Annotation[] getDeclaredAnnotation(Class<T>)：返回该程序元素上存在的、指定类型的注解，如果该类型注解不存在，则返回null；与此接口中的其他方法不同，该方法将忽略继承的注解；

3.Annotation[] getAnnotations()：返回该程序元素上存在的所有注解；

4.Annotation[] getDeclaredAnnotations()：返回直接存在于此元素上的所有注释。与此接口中的其他方法不同，该方法将忽略继承的注解；

5.Annotation[] getAnnotationsByType(Class<T>)：返回直接存在于此元素上指定注解类型的所有注解；

6.Annotation[] getDeclaredAnnotationsByType(Class<T>)：返回直接存在于此元素上指定注解类型的所有注解。与此接口中的其他方法不同，该方法将忽略继承的注解；

7.boolean isAnnotationPresent(Class<?extends Annotation> annotationClass)：判断该程序元素上是否包含指定类型的注解，存在则返回true，否则返回false；

```java
/***********注解声明***************/
/**
 * 水果名称注解
 */
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface FruitName {
    String value() default " ";
}


/**
 * 水果颜色注解
 */
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface FruitColor {
    /**
     * 颜色枚举
     */
    public enum Color{BLUE, RED, GREEN};

    /**
     * 颜色属性
     * @return
     */
    Color fruitColor() default Color.GREEN;
}


/**
 * 水果供应商注解
 */
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface FruitProvider {
    /**
     * 供应商编号
     * @return
     */
    public int id() default -1;

    /**
     * 供应商名称
     * @return
     */
    public String name() default " ";

    /**
     * 供应商地址
     * @return
     */
    public String address() default " ";
}
/***********注解使用***************/
public class Apple {
    @FruitName("Apple")
    private String appleName;
    @FruitColor(fruitColor = FruitColor.Color.RED)
    private String appleColor;
    @FruitProvider(id = 1, name = "陕西红富士集团", address = "陕西红富士大厦")
    private String appleProvider;

    public String getAppleProvider() {
        return appleProvider;
    }

    public void setAppleProvider(String appleProvider) {
        this.appleProvider = appleProvider;
    }

    public String getAppleName() {
        return appleName;
    }

    public void setAppleName(String appleName) {
        this.appleName = appleName;
    }

    public String getAppleColor() {
        return appleColor;
    }

    public void setAppleColor(String appleColor) {
        this.appleColor = appleColor;
    }

    public void displayName(){
        System.out.println(getAppleName());
    }
}

/***********注解信息获取***************/
public class AnnotationParser {
    public static void main(String[] args) {
        Field[] fields = Apple.class.getDeclaredFields();
        for (Field field : fields) {
            //System.out.println(field.getName().toString());
            if (field.isAnnotationPresent(FruitName.class)){
                FruitName fruitName = field.getAnnotation(FruitName.class);
                System.out.println("水果的名称：" + fruitName.value());
            }else if (field.isAnnotationPresent(FruitColor.class)){
                FruitColor fruitColor = field.getAnnotation(FruitColor.class);
                System.out.println("水果的颜色："+fruitColor.fruitColor());
            }else if (field.isAnnotationPresent(FruitProvider.class)){
                FruitProvider fruitProvider = field.getAnnotation(FruitProvider.class);
                System.out.println("水果供应商编号:" + fruitProvider.id() + " 名称:" + fruitProvider.name() + " 地址:" + fruitProvider.address());
            }
        }
    }
}

/***********输出结果***************/
水果的名称：Apple
水果的颜色：RED
水果供应商编号:1 名称:陕西红富士集团 地址:陕西红富士大厦

```

## 八、JDK8注解新特性

JDK 8 主要有两点改进：类型注解和重复注解

* **类型注解**

类型注解在@Target中增加了两个ElementType参数：

1.ElementType.TYPE_PARAMETER 表示该注解能写在类型变量的声明语句中；

2.ElementType.TYPE_USE 表示该注解能写在使用类型的任何语句中（例如声明语句、泛型和强制转换语句中的类型）;

从而扩展了注解使用的范围，可以使用在创建类实例、类型映射、implements语句、throw exception声明中的类型前面。例如：

1.**创建类实例**

new @Interned MyObject();

2.**类型映射**

myString = (@NonNull String) str;

3.**implements 语句中**

class UnmodifiableList<T> implements @Readonly List<@Readonly T> { ... }

4.**throw exception声明**

 void monitorTemperature() throws @Critical TemperatureException { ... }

**简单示例：**

```java 
@Target({ElementType.TYPE_PARAMETER, ElementType.TYPE_USE})
public @interface Encrypted {
}


public class MyTypeAnnotation {
    @Encrypted String data;
    List<@Encrypted String> strings;
}
```

**类型注解的作用：**

首先，局域变量声明中的类型注解也可以保留在类文件中，完整泛型被保留，并且在运行期可以访问，从而有助于我们获取更多的代码信息；其次，类型注解可以支持在的程序中做强类型检查。配合第三方工具check framework，可以在编译的时候检测出runtime error，以提高代码质量；最后，代码中包含的注解清楚表明了编写者的意图，使代码更具有表达意义，有助于阅读者理解程序，毕竟代码才是“最根本”的文档、“最基本”的注释。

* **重复注解**

重复注释就是运行在同一元素上多次使用同一注解，使用@Repeatable注解。

之前也有重复使用注解的解决方案，但可读性不是很好，例如：

```java
public @interface Authority {
     String role();
}

public @interface Authorities {
    Authority[] value();
}

public class RepeatAnnotationUseOldVersion {    
    @Authorities({@Authority(role="Admin"),@Authority(role="Manager")})
    public void doSomeThing(){
    }
}
```

**而现在的实现如下：**

```java
@Repeatable(Authorities.class)
public @interface Authority {
     String role();
}

public @interface Authorities {
    Authority[] value();
}

public class RepeatAnnotationUseNewVersion {
    @Authority(role="Admin")
    @Authority(role="Manager")
    public void doSomeThing(){ }
}
```

不同的地方是，创建重复注解Authority时，加上@Repeatable,指向存储注解Authorities，在使用时候，直接可以重复使用Authority注解。从上面例子看出，java 8里面做法更适合常规的思维，可读性强一点。

## 总结

本篇主要从Annotation的概念、作用、分类进行了大概的介绍，然后通过对系统内置注解、元注解、自定义注解、解析注解信息等四个方面逐步以代码实例的方式展开对注解认识和使用，最后讲了JDK8中新添加的类型注解和重复注解，从而对Java 注解有了更系统化的认识。从jdk5引入注解以来，我们看到了注解在javadoc文档、JUnit单元测试、Spring依赖配置等各方面蓬勃发展，而现在jdk8更是大大拓展了注解的使用范围，为新的设计和工具带来了更多的机遇，让我们拭目以待。希望本篇对刚入门或想对注解有个全面回顾的小伙伴有帮助，更多关于Java的精彩内容，敬请关注[DevinBlog](http://zhangchuzhao.site)

参考文章：

[Java Annotations](http://tutorials.jenkov.com/java/annotations.html)

[深入理解Java注解](http://www.cnblogs.com/peida/archive/2013/04/23/3036035.html)

[Java8新特性探究](https://my.oschina.net/benhaile/blog/180932)
