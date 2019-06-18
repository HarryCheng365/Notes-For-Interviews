# 类加载机制

### 有这么三个问题

```
虚拟机什么时候才会加载class文件，并初始化类呢？类加载和初始化的时机
虚拟机如何加载一个class文件？ Java类加载的方式的问题，具体的话就使类加载器，双亲委派机制（为什么，有什么好处）。
虚拟机加载一个class文件，要经历哪些具体步骤的问题。（类加载过程和步骤的问题）
```



### 双亲委派模型

### 为什么使用双亲委派模型

双亲委派模型很好地解决了类加载器的统一加载问题：越基础的类由越上层的加载器进行加载，进而保证Java类型体系中最基础的行为，防止应用程序变得混乱，如果可以任意方向执行类加载器，比如依赖类没有加载反倒先加载了子类，并且还可以防止重复加载的问题。
使用双亲委派模型的原因：

java类随着其类加载器一起具备了一种带有优先级的层级关系
比如，java.lang.Object 类总是由启动类加载器进行加载，因此Object类在程序的各种类加载器环境中都是同一个类型（是否是同一类型由类加载器与类本身共同决定）。

使用双亲委派模型原因:java类随着其类加载器一起具备了一种带有优先级的层次关系.例如 java.lang.Object,无论哪一个类加载器要加载该类,最终都是委托给处于顶端的启动类加载器,因此object在程序的各种类加载器环境中都是同一个类.相反如果没有使用双亲委派模型,那么假如用户自定义了一个称为java.lang.Object的类,并放在classPath中,那么系统将会出现多个不同的Object类,



![èªå®ä¹ç±"å è½½å¨å è½½ç±"çè¿ç¨-54.2kB](http://static.zybuluo.com/Rico123/z3hgytno2ijb152rdy2bd4pv/%E8%87%AA%E5%AE%9A%E4%B9%89%E7%B1%BB%E5%8A%A0%E8%BD%BD%E5%99%A8%E5%8A%A0%E8%BD%BD%E7%B1%BB%E7%9A%84%E8%BF%87%E7%A8%8B)



自底向上检查，查看是否已经加载，如果已经载入，就返回该类的实例的引用

然后自顶向下开始加载，如果一直到CustomClassLoader还没有 加载成功就会抛出异常

BootstrapClassLoader

ExtClassLoader

AppClassLoader

CustomClassLoader

ClassLoader是天然线程安全的



### 双亲委派模型描述

```
自底向上加载，子类需要用到某个class的时候，自底向上查找，看父类类加载器是否加载，查到了则返回相应类实例的引用，没有查到就bootstrapClassLoader就会抛出异常并且 自顶而下尝试加载，
```



### 补充：关于自定义类加载器

```
加密：java代码可以轻易的被反编译，如果你需要对你的代码进行加密以防止反编译，可以先将编译后的代码用加密算法加密，类加密后就不能再使用自带的类加载器了，编译->加载(类加载器，将一个类从二进制字节流读入JVM内部，并存储在运行时内存区的方法区里->然后将其转换为一个与目标类型对应的java.lang.class对象实例
```

从上面对于java.lang.ClassLoader的loadClass(String name, boolean resolve)方法的解析来看，可以得出以下2个结论：

1、如果不想打破双亲委派模型，那么只需要重写findClass方法即可

父加载器开始尝试加载.class文件，加载成功就返回一个java.lang.Class，加载不成功就抛出一个ClassNotFoundException，给子加载器去加载，//返回一个Class对象，就是加载过程，已经有了class对象了

其实向上检查的时候，如果父类已经加载过，就会返回一个引用

2、如果想打破双亲委派模型，那么就重写整个loadClass方法

当然，我们自定义的ClassLoader不想打破双亲委派模型，所以自定义的ClassLoader继承自java.lang.ClassLoader并且只重写findClass方法。

loadClass，界定了双亲委派模型的方法



### 简介

​	所有的Class都是由ClassLoader进行加载的，ClassLoader负责将Class文件里的二进制数据流**加载**进系统，然后交给java虚拟机进行**连接**、**初始化**等操作。

### 类的生命周期

​	**加载，验证，准备，解析，初始化，使用和卸载。其中验证，准备，解析3个部分统称为连接**。这7个阶段发生顺序如下图：

![](/Users/Haoyu/Documents/Notes-For-Interviews/%E6%8A%80%E6%9C%AF%E8%B5%84%E6%96%99/assets/%E7%B1%BB%E5%8A%A0%E8%BD%BD%E7%9A%84%E4%B8%83%E4%B8%AA%E9%98%B6%E6%AE%B5.png)

​	加载，验证，准备，初始化，卸载这5个阶段的顺序是确定的，而解析阶段则不一定：它在某些情况下可以在初始化完成后在开始，这是为了支持Java语言的运行时绑定。

​	**其中加载，验证，准备，解析及初始化是属于类加载机制中的步骤。注意此处的加载不等同于类加载。**

### 类加载的具体过程

1. 加载

   (1) 通过一个类的全限定名来获取定义此类的二进制流；

   (2) 将这个字节流所代表的静态存储结构转换为方法区的运行时数据结构；

   (3) 在内存中生成一个代表这个类的java.lang.Class对象，作为方法区这个类的各种数据的访问入口。

2. 验证

   **是连接阶段的第一步，目的是为了确保Class文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。**

   包含四个阶段的校验动作：

   - 文件格式验证：验证字节流是否符合Class文件格式的规范，并且能被当前版本的虚拟机处理。
   - 元数据验证：对类的元数据信息进行语义校验，是否不存在不符合Java语言规范的元数据信息。
   - 字节码验证：最复杂的一个阶段，主要目的是通过数据流和控制流分析，确定程序语义是合法的，符合逻辑的。对类的方法体进行校验分析，保证被校验类的方法在运行时不会做出危害虚拟机安全的事件。
   - 符号引用验证：最后一个阶段的校验发生在虚拟机将符号引用转换为直接引用的时候，这个转换动作将在连接的第三个阶段——解析阶段中发生。符号验证的目的是确保解析动作能正常进行。

3. 准备

   **准备阶段是正式为static修饰的类变量分配内存并设置类变量初始值的阶段**。这些变量所使用的内存都将在方法区中分配。只包括类变量。初始值“通常情况”下是数据类型的零值。“特殊情况”下，如果类字段的字段属性表中存在Constant Value属性，那么在准备阶段变量的值就会被初始化为Constant Value属性所指定的值。

   分配内存并且初始化值了，在准备阶段是对静态成员变量赋于默认初始值，注意赋予默认初始值不是初始化

   初始化在下面，是对静态成员变量的赋值，这里只是加载静态函数，静态成员变量，分配空间和默认值

   初始化在下面初始化阶段，通过直接赋值或者静态代码块的环节，进行赋值

   另外

   

4. 解析

   **虚拟机将常量池内的符号引用替换为直接引用的过程。**“动态解析”的含义就是必须等到程序实际运行到这条指令的时候，解析动作才能进行。相对的，其余可触发解析的指令都是“静态”的，可以在刚刚完成加载阶段，还没有开始执行代码时就进行解析。

   关于解析阶段，符号引用转为直接引用的说明：

   - 符号引用（Symbolic References）：符号引用以一组符号来描述所引用的目标，符号可以是任何形式的字面量，只要使用时能够无歧义的定位到目标即可。例如，在Class文件中它以CONSTANT_Class_info、CONSTANT_Fieldref_info、CONSTANT_Methodref_info等类型的常量出现。

     符号引用与虚拟机的内存布局无关，引用的目标并不一定加载到内存中。在Java中，一个java类将会编译成一个class文件。在编译时，java类并不知道所引用的类的实际地址，因此只能使用符号引用来代替。比如org.simple.People类引用了org.simple.Language类，在编译时People类并不知道Language类的实际内存地址，因此只能使用符号org.simple.Language（假设是这个，当然实际中是由类似于CONSTANT_Class_info的常量来表示的）来表示Language类的地址。各种虚拟机实现的内存布局可能有所不同，但是它们能接受的符号引用都是一致的，因为符号引用的字面量形式明确定义在Java虚拟机规范的Class文件格式中。

   - 直接引用：直接引用可以是

     - 直接指向目标的指针（比如，指向“类型”【Class对象】、类变量、类方法的直接引用可能是指向方法区的指针），该方法为hotSpot中的实现。

       具体如下图所示：

       ![](/Users/Haoyu/Documents/Notes-For-Interviews/%E6%8A%80%E6%9C%AF%E8%B5%84%E6%96%99/assets/%E9%80%9A%E8%BF%87%E7%9B%B4%E6%8E%A5%E6%8C%87%E9%92%88%E8%AE%BF%E9%97%AE%E5%AF%B9%E8%B1%A1.png)

     - 相对偏移量（比如，指向实例变量、实例方法的直接引用都是偏移量）

     - 一个能间接定位到目标的句柄

       具体如下图所示：

       ![](/Users/Haoyu/Documents/Notes-For-Interviews/%E6%8A%80%E6%9C%AF%E8%B5%84%E6%96%99/assets/%E9%80%9A%E8%BF%87%E5%8F%A5%E6%9F%84%E8%AE%BF%E9%97%AE%E5%85%B7%E4%BD%93%E5%AF%B9%E8%B1%A1.png)

     直接引用是和虚拟机的布局相关的，同一个符号引用在不同的虚拟机实例上翻译出来的直接引用一般不会相同。如果有了直接引用，那引用的目标必定已经被加载入内存中了。

程序的装入也分静态装入，编译时动态装入和运行时动态装入

然后链接上也分静态链接 和动态链接

这里

1. 初始化

   初始化结点，是执行静态代码块，前面都是对类的装载，和bean 通过bean名拿到bean的配置一样

   类加载过程中的最后一步。**简单地说，初始化就是对静态类变量进行赋值及执行静态代码块。**

   初始化阶段是执行类构造器<clinit>()方法的过程。

   <clinit>()方法是由编译器自动收集类中的**所有类变量的赋值动作和静态语句块中的语句合并产生的**。

   <clinit>()与类的构造函数不同，它不需要显示地调用父类构造器，虚拟机会保证在子类的<clinit>()方法执行之前，父类的<clinit>()方法已经执行完毕。

父类静态域或着静态代码块，然后是子类静态域或者子类静态代码块（静态代码块先被加载，然后再是静态属性）

##### 注意一点：

​	类在加载阶段，方法区里就有类对象了，会创建该class的对象，作为方法区访问这个类数据的入口

​	在类准备阶段，就会对类变量分配内存并且赋给初始值了，并且初始化的时候 从静态代码块开始执行对类变量进行赋值，所以准备阶段已经分配过内存了，对象类型数据，而对象实例数据会在堆中分配，比如该类的某属性是什么类型的在方法区中就要记录一下



子父类 静态代码块只在类加载的时候

执行一次准备的时候 加载子父函数的静态属性，然后 静态代码块，非静态代码块，是在初始化的时候执行

静态代码块只执行一次，然后非静态代码块，每get一个实例就执行一次

main 函数

开始使用 实例化

父非静态代码块

父构造函数

子非静态代码块

子构造函数

构造函数

### 静态代码块和非静态代码块的区别

```java
//例子
class StaticDemo{

//静态代码块
static {
System.out.println("父类静态代码块被执行");
}

//非静态代码块
{
System.out.println("父类非静态代码块被执行");
}
//构造方法
StaticDemo(){

System.out.println("父类构造方法被执行");
}
}
public class StaticTest extends StaticDemo {

/**
* 静态代码块的特点：
* 随着类的加载而执行，而且只执行一次
* 静态代码块额执行顺序优先于main函数
*/
static {

System.out.println("子类静态代码块被执行");
}
//非静态代码块
{
System.out.println("子类非静态代码块被执行");
}
StaticTest(){
System.out.println("子类构造方法被执行");
}

public static void main(String[] hq){
System.out.println("main函数");
new StaticTest();
}
  
相同点：都是在JVM加载类时且在构造方法执行之前执行，在类中都可以定义多个，

　　　　一般在代码块中对一些static变量进行赋值。

不同点：静态代码块在非静态代码块之前执行(静态代码块—>非静态代码块—>构造方法)。

　　　　静态代码块只在第一次new执行一次，之后不再执行，而非静态代码块在每new

　　　　一次就执行一次。非静态代码块可在普通方法中定义(不过作用不大)；而静态代码块不行。
```



### 触发类加载的条件

1. **遇到new,getstatic,putstatic或invokestatic这4条字节码指令时**，如果类没有进行过初始化，则需要先触发初始化。生成这4条指令的最常见的Java代码场景是：
   - 使用new关键字实例化对象的时候；
   - 读取或设置一个类的静态字段的时候（被final修饰，已在编译期把结果放入常量池的静态字段除外）；
   - 调用一个类的静态方法的时候。
2. 使用java.lang.reflect包的方法对类进行反射调用的时候。
3. 当初始化一个类的时候，发现其父类还没有进行过初始化，则需要先出发父类的初始化。
4. 当虚拟机启动时，用户需要指定一个要执行的主类（包含main()方法的那个类），虚拟机会先初始化这个主类。
5. 当使用JDK1.7的动态语言支持时，如果一个java.lang.invoke.MethodHandle实例最后的解析结果REF_getStatic,REF_putStatic,REF_invokeStatic的方法句柄，并且这个方法句柄所对应的类没有进行初始化，则需要先出发初始化。

### 类加载器

​	通过上述的了解，我们已经知道了类加载机制的大概流程及各个部分的功能。其中加载部分的功能是将类的class文件读入内存，并为之创建一个java.lang.Class对象。这部分功能就是由类加载器来实现的。

##### 类加载器分类

不同的类加载器负责加载不同的类。主要分为两类：

- **启动类加载器（Bootstrap ClassLoader）：**由C++语言实现（针对HotSpot）,负责将存放在<JAVA_HOME>\lib目录或-Xbootclasspath参数指定的路径中的类库加载到内存中，即负责加载Java的核心类，即核心库java.*。
- **其他类加载器：**由Java语言实现，继承自抽象类ClassLoader。如：
  - **扩展类加载器（Extension ClassLoader）：**负责加载<JAVA_HOME>\lib\ext目录或java.ext.dirs系统变量指定的路径中的所有类库，即负责加载扩展库javax.*。
  - **应用程序类加载器（Application ClassLoader）：**负责加载用户类路径（classpath）上的指定类库，我们可以直接使用这个类加载器，通过ClassLoader.getSystemClassLoader()方法直接获取。一般情况，如果我们没有自定义类加载器默认就是用这个加载器。

​	以上2大类，3小类类加载器基本上负责了所有Java类的加载。下面我们来具体了解上述几个类加载器实现类加载过程时相互配合协作的流程。

##### 双亲委派模型

1. 工作流程

   **双亲委派模型的工作流程是：如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把请求委托给父加载器去完成，依次向上，因此，所有的类加载请求最终都应该被传递到顶层的启动类加载器中，只有当父加载器在它的搜索范围中没有找到所需的类时，即无法完成该加载，子加载器才会尝试自己去加载该类。**

   ![](/Users/Haoyu/Documents/Notes-For-Interviews/%E6%8A%80%E6%9C%AF%E8%B5%84%E6%96%99/assets/%E5%8F%8C%E4%BA%B2%E5%A7%94%E6%B4%BE%E6%A8%A1%E5%9E%8B.png)

   ​	具体对应的加载内容如下：

   ![](/Users/Haoyu/Documents/Notes-For-Interviews/%E6%8A%80%E6%9C%AF%E8%B5%84%E6%96%99/assets/%E7%B1%BB%E5%8A%A0%E8%BD%BD%E5%99%A8%E5%8F%8C%E4%BA%B2%E5%A7%94%E6%B4%BE%E6%A8%A1%E5%9E%8B.png)

   ​	这样的**好处**是不同层次的类加载器具有不同优先级，比如所有Java对象的超级父类java.lang.Object，位于rt.jar，无论哪个类加载器加载该类，最终都是由启动类加载器进行加载，保证安全。即使用户自己编写一个java.lang.Object类并放入程序中，虽能正常编译，但不会被加载运行，保证不会出现混乱。

   双亲委派模型

2. 代码实现

   ClassLoader中loadClass方法实现了双亲委派模型：

   ```java
   protected Class<?> loadClass(String name, boolean resolve)
       throws ClassNotFoundException
   {
       synchronized (getClassLoadingLock(name)) {
           //检查该类是否已经加载过
           Class c = findLoadedClass(name);
           if (c == null) {
               //如果该类没有加载，则进入该分支
               long t0 = System.nanoTime();
               try {
                   if (parent != null) {
                       //当父类的加载器不为空，则通过父类的loadClass来加载该类
                       c = parent.loadClass(name, false);
                   } else {
                       //当父类的加载器为空，则调用启动类加载器来加载该类
                       c = findBootstrapClassOrNull(name);
                   }
               } catch (ClassNotFoundException e) {
                   //非空父类的类加载器无法找到相应的类，则抛出异常
               }
   
               if (c == null) {
               //向上寻找是否被加载
               //向下逐层加载
                   //当父类加载器无法加载时，则调用findClass方法来加载该类
                   long t1 = System.nanoTime();
                   c = findClass(name); //用户可通过覆写该方法，来自定义类加载器
   
                   //用于统计类加载器相关的信息
                   sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                   sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                   sun.misc.PerfCounter.getFindClasses().increment();
               }
           }
           if (resolve) {
               //对类进行link操作
               resolveClass(c);
           }
           return c;
       }
   }
   ```

   代码对应的流程如下：

   - 首先，检查一下指定名称的类是否已经加载过，如果加载过了，就不需要再加载，直接返回。
   - 如果此类没有加载过，那么，再判断一下是否有父加载器；如果有父加载器，则由父加载器加载（即调用parent.loadClass(name, false);）.或者是调用bootstrap类加载器来加载。
   - 如果父加载器及bootstrap类加载器都没有找到指定的类，那么调用当前类加载器的findClass方法来完成类加载。

3. 破坏双亲委派模型

   双亲委任模型不是一个强制性的约束模型，而是一个建议型的类加载器实现方式。在Java的世界中大部分的类加载器都遵循者模型，但也有例外，到目前为止，双亲委派模型有过3次大规模的“被破坏”的情况。 

   - **第一次**：在双亲委派模型出现之前—–即JDK1.2发布之前；

   - **第二次**：是这个模型自身的缺陷导致的。我们说，双亲委派模型很好的解决了各个类加载器的基础类的统一问题（越基础的类由越上层的加载器进行加载），基础类之所以称为“基础”，是因为它们总是作为被用户代码调用的API， 但没有绝对，**如果基础类调用会用户的代码**怎么办呢？

     ​	这不是没有可能的。一个典型的例子就是JNDI服务，JNDI现在已经是Java的标准服务，它的代码由启动类加载器去加载（在JDK1.3时就放进去的rt.jar）,但它需要调用由独立厂商实现并部署在应用程序的ClassPath下的JNDI接口提供者（SPI， Service Provider Interface）的代码，但启动类加载器不可能“认识“这些代码啊。因为这些类不在rt.jar中，但是启动类加载器又需要加载。怎么办呢？

     ​	为了解决这个问题，Java设计团队只好引入了一个不太优雅的设计：线程上下文类加载器（Thread Context ClassLoader）。这个类加载器可以通过java.lang.Thread类的setContextClassLoader方法进行设置。如果创建线程时还未设置，它将会从父线程中继承一个，如果在应用程序的全局范围内都没有设置过多的话，那这个类加载器默认即使应用程序类加载器。

     ​	有了线程上下文加载器，JNDI服务使用这个线程上下文加载器去加载所需要的SPI代码，也就是父类加载器请求子类加载器去完成类加载的动作，这种行为实际上就是打通了双亲委派模型的层次结构来逆向使用类加载器，实际上已经违背了双亲委派模型的一般性原则。但这无可奈何，Java中所有涉及SPI的加载动作基本胜都采用这种方式。例如JNDI，JDBC，JCE，JAXB，JBI等。

   - **第三次**：为了实现热插拔，热部署，模块化，意思是添加一个功能或减去一个功能不用重启，只需要把这模块连同类加载器一起换掉就实现了代码的热替换（tomcat中的jsp改动就是一个很典型的例子）。

<hr>

##### jdbc 破坏双亲委派模型

​	首先，理解一下为什么JDBC需要破坏双亲委派模式，原因是原生的JDBC中Driver驱动本身只是一个接口，并没有具体的实现，具体的实现是由不同数据库类型去实现的。例如，其中一种实现就是MySQL的mysql-connector-.jar中的Driver类来具体实现。

​	原生的JDBC中的类是放在rt.jar包的，是由**启动类加载器**进行类加载的，在JDBC中的Driver类中需要动态去加载不同数据库类型的Driver类，而mysql-connector-.jar中的Driver类是用户自己写的代码，那启动类加载器肯定是不能进行加载的，既然是自己编写的代码，那就需要由**应用程序类加载器**去进行类加载。于是乎，这个时候就引入线程上下文件类加载器(Thread Context ClassLoader)。**有了这个东西之后，程序就可以把原本需要由启动类加载器进行加载的类，由应用程序类加载器去进行加载了。**

​	下面看看JDBC中是怎么去应用：

```java
private static Connection getConnection(
        String url, java.util.Properties info, Class<?> caller) throws SQLException {
        /*
         * When callerCl is null, we should check the application's
         * (which is invoking this class indirectly)
         * classloader, so that the JDBC driver class outside rt.jar
         * can be loaded from here.
         */
        //callerCL为空的时候，其实说明这个ClassLoader是启动类加载器，但是这个启动类加载并不能识别rt.jar之外的类，这个时候就把callerCL赋值为Thread.currentThread().getContextClassLoader();也就是应用程序启动类
        ClassLoader callerCL = caller != null ? caller.getClassLoader() : null;
        synchronized(DriverManager.class) {
            // synchronize loading of the correct classloader.
            if (callerCL == null) {
                callerCL = Thread.currentThread().getContextClassLoader();
            }
        }

        if(url == null) {
            throw new SQLException("The url cannot be null", "08001");
        }

        println("DriverManager.getConnection(\"" + url + "\")");

        // Walk through the loaded registeredDrivers attempting to make a connection.
        // Remember the first exception that gets raised so we can reraise it.
        SQLException reason = null;

        for(DriverInfo aDriver : registeredDrivers) {
            // If the caller does not have permission to load the driver then
            // skip it.
            //继续看这里 
            if(isDriverAllowed(aDriver.driver, callerCL)) {
                try {
                    println("    trying " + aDriver.driver.getClass().getName());
                    Connection con = aDriver.driver.connect(url, info);
                    if (con != null) {
                        // Success!
                        println("getConnection returning " + aDriver.driver.getClass().getName());
                        return (con);
                    }
                } catch (SQLException ex) {
                    if (reason == null) {
                        reason = ex;
                    }
                }

            } else {
                println("    skipping: " + aDriver.getClass().getName());
            }

        }

        // if we got here nobody could connect.
        if (reason != null)    {
            println("getConnection failed: " + reason);
            throw reason;
        }

        println("getConnection: no suitable driver found for "+ url);
        throw new SQLException("No suitable driver found for "+ url, "08001");
    }

    private static boolean isDriverAllowed(Driver driver, ClassLoader classLoader) {
        boolean result = false;
        if(driver != null) {
            Class<?> aClass = null;
            try {
                //这一步会对类进行初始化的动作，而初始化之前自然也要进行的类的加载工作
                aClass =  Class.forName(driver.getClass().getName(), true, classLoader);
            } catch (Exception ex) {
                result = false;
            }

             result = ( aClass == driver.getClass() ) ? true : false;
        }

        return result;
    }
```

##### Tomcat破坏双亲委派模型

1. **Tomcat 如果使用默认的类加载机制行不行？**

   我们思考一下：Tomcat是个web容器， 那么它要解决什么问题： 

   - 一个web容器可能需要部署两个应用程序，不同的应用程序可能会依赖同一个第三方类库的不同版本，不能要求同一个类库在同一个服务器只有一份，因此要保证每个应用程序的类库都是独立的，保证相互隔离。 
   - 部署在同一个web容器中相同的类库相同的版本可以共享。否则，如果服务器有10个应用程序，那么要有10份相同的类库加载进虚拟机，这是扯淡的。 
   - web容器也有自己依赖的类库，不能于应用程序的类库混淆。基于安全考虑，应该让容器的类库和程序的类库隔离开来。 
   - web容器要支持jsp的修改，我们知道，jsp 文件最终也是要编译成class文件才能在虚拟机中运行，但程序运行后修改jsp已经是司空见惯的事情，否则要你何用？ 所以，web容器需要支持 jsp 修改后不用重启。

   再看看我们的问题：Tomcat 如果使用默认的类加载机制行不行？ 

   答案是不行的。为什么？我们看，第一个问题，如果使用默认的类加载器机制，那么是无法加载两个相同类库的不同版本的，默认的类加载器是不管你是什么版本的，只在乎你的全限定类名，并且只有一份。第二个问题，默认的类加载器是能够实现的，因为他的职责就是保证唯一性。第三个问题和第一个问题一样。我们再看第四个问题，我们想我们要怎么实现jsp文件的热修改（楼主起的名字），jsp 文件其实也就是class文件，那么如果修改了，但类名还是一样，类加载器会直接取方法区中已经存在的，修改后的jsp是不会重新加载的。那么怎么办呢？我们可以直接卸载掉这jsp文件的类加载器，所以你应该想到了，每个jsp文件对应一个唯一的类加载器，当一个jsp文件修改了，就直接卸载这个jsp类加载器。重新创建类加载器，重新加载jsp文件。

2. **Tomcat 如何实现自己独特的类加载机制？**

   所以，Tomcat 是怎么实现的呢？牛逼的Tomcat团队已经设计好了。我们看看他们的设计图：

   ![](/Users/mrjiao/Documents/typora/java/assets/tomcat%E7%B1%BB%E5%8A%A0%E8%BD%BD%E5%99%A8%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

   我们看到，前面3个类加载和默认的一致，CommonClassLoader、CatalinaClassLoader、SharedClassLoader和WebappClassLoader则是Tomcat自己定义的类加载器，它们分别加载/common/、/server/、/shared/（在tomcat 6之后已经合并到根目录下的lib目录下）和/WebApp/WEB-INF/中的Java类库。

   其中WebApp类加载器和Jsp类加载器通常会存在多个实例，**每一个Web应用程序对应一个WebApp类加载器，每一个JSP文件对应一个Jsp类加载器**。

   - commonLoader：Tomcat最基本的类加载器，加载路径中的class可以被Tomcat容器本身以及各个Webapp访问；
   - catalinaLoader：Tomcat容器私有的类加载器，加载路径中的class对于Webapp不可见
   - sharedLoader：各个Webapp共享的类加载器，加载路径中的class对于所有Webapp可见，但是对于Tomcat容器不可见；
   - WebappClassLoader：各个Webapp私有的类加载器，加载路径中的class只对当前Webapp可见；

   从图中的委派关系中可以看出：

   - CommonClassLoader能加载的类都可以被Catalina ClassLoader和SharedClassLoader使用，从而实现了公有类库的共用，而CatalinaClassLoader和Shared ClassLoader自己能加载的类则与对方相互隔离。
   - WebAppClassLoader可以使用SharedClassLoader加载到的类，但各个WebAppClassLoader实例之间相互隔离。
   - JasperLoader的加载范围仅仅是这个JSP文件所编译出来的那一个.Class文件，它出现的目的就是为了被丢弃：当Web容器检测到JSP文件被修改时，会替换掉目前的JasperLoader的实例，并通过再建立一个新的Jsp类加载器来实现JSP文件的HotSwap功能。

   好了，至此，我们已经知道了tomcat为什么要这么设计，以及是如何设计的，那么，tomcat 违背了java 推荐的双亲委派模型了吗？答案是：违背了。 我们前面说过：

   **双亲委派模型要求除了顶层的启动类加载器之外，其余的类加载器都应当由自己的父类加载器加载。**

   很显然，tomcat 不是这样实现，tomcat 为了实现隔离性，没有遵守这个约定，每个webappClassLoader加载自己的目录下的class文件，不会传递给父类加载器。

3. **如果tomcat 的 Common ClassLoader 想加载 WebApp ClassLoader 中的类，该怎么办？**

   看了前面的关于破坏双亲委派模型的内容，我们心里有数了，我们可以使用线程上下文类加载器实现，使用线程上下文加载器，可以让父类加载器请求子类加载器去完成类加载的动作。

