# Spring框架入门

### 作用简介

​	纵览Spring ， 读者会发现Spring 可以做非常多的事情。 但归根结底， 支撑Spring的仅仅是少许的基本理念， 所有的理念都可以追溯到Spring最根本的使命上： 简化Java开发。

​	Spring的目标是致力于全方位的简化Java开发。 这势必引出更多的解释， Spring是如何简化Java开发的？

​	为了降低Java开发的复杂性， Spring采取了以下4种关键策略：

- 基于POJO的轻量级和最小侵入性编程；
- 通过依赖注入和面向接口实现松耦合；
- 基于切面和惯例进行声明式编程；
- 通过切面和模板减少样板式代码。

​	几乎Spring所做的任何事情都可以追溯到上述的一条或多条策略。

​	我将通过具体的案例进一步阐述这些理念， 以此来证明Spring是如何完美兑现它的承诺的， 也就是简化Java开发。 让我们先从基于POJO的最小侵入性编程开始。

### 最小侵入编程

​	如果你从事Java编程有一段时间了， 那么你或许会发现（可能你也实际使用过） 很多框架通过强迫应用继承它们的类或实现它们的接口从而导致应用与框架绑死。这种侵入式的编程方式在早期版本的Struts以及无数其他的Java规范和框架中都能看到。

​	Spring竭力避免因自身的API而弄乱你的应用代码。 Spring不会强迫你实现Spring规范的接口或继承Spring规范的类， 相反， 在基于Spring构建的应用中， 它的类通常没有任何痕迹表明你使用了Spring。 最坏的场景是， 一个类或许会使用Spring注解， 但它依旧是POJO。

​	举个例子， 请参考下面的HelloWorldBean类：

```java
package com.chi.spring;

public class HelloWorldBean {
  public String sayHello() {
    return "Hello World";
  }
}
```

​	可以看到， 这是一个简单普通的Java类——POJO。 没有任何地方表明它是一个Spring组件。 Spring的非侵入编程模型意味着这个类在Spring应用和非Spring应用中都可以发挥同样的作用。

​	**Spring的非入侵式就是不强制类要实现Spring的任何接口或类，没有任何地方表明它是一个Spring组件。 意味着这个类在Spring应用和非Spring应用中都可以发挥同样的作用。**

​	尽管形式看起来很简单， 但POJO一样可以具有魔力。 Spring赋予POJO魔力的方式之一就是通过DI来装配它们。 让我们看看DI是如何帮助应用对象彼此之间保持松散耦合的。

### 依赖注入

​	任何一个有实际意义的应用（肯定比Hello World示例更复杂） 都会由两个或者更多的类组成， 这些类相互之间进行协作来完成特定的业务逻辑。 按照传统的做法， 每个对象负责管理与自己相互协作的对象（即它所依赖的对象） 的引用， 这将会导致高度耦合和难以测试的代码。

​	举个例子， 考虑下程序清单1.2所展现的Knight类：

```java
package sia.knights;

public class DamselRescuingKnight implements Knight {

  private RescueDamselQuest quest;

  public DamselRescuingKnight() {
    this.quest = new RescueDamselQuest();
  }

  public void embarkOnQuest() {
    quest.embark();
  }

}
```

​	DamselRescuingKnight只能执行RescueDamselQuest探险任务。

​	可以看到， DamselRescuingKnight在它的构造函数中自行创建了Rescue DamselQuest。 这使得DamselRescuingKnight紧密地和RescueDamselQuest耦合到了一起， 因此极大地限制了这个骑士执行探险的能力。 如果一个少女需要救援， 这个骑士能够召之即来。 但是如果一条恶龙需要杀掉， 那么这个骑士就爱莫能助了。

​	更糟糕的是， 为这个DamselRescuingKnight编写单元测试将出奇地困难。 在这样的一个测试中， 你必须保证当骑士的embarkOnQuest()方法被调用的时候， 探险的embark()方法也要被调用。 但是没有一个简单明了的方式能够实现这一点。 很遗憾， DamselRescuingKnight将无法进行测试。

​	**耦合具有两面性（two-headed beast） 。 一方面， 紧密耦合的代码难以测试、 难以复用、 难以理解， 并且典型地表现出“打地鼠”式的bug特性（修复一个bug， 将会出现一个或者更多新的bug） 。 另一方面， 一定程度的耦合又是必须的——完全没有耦合的代码什么也做不了。 为了完成有实际意义的功能， 不同的类必须以适当的方式进行交互。 总而言之， 耦合是必须的， 但应当被小心谨慎地管理。**

​	**通过DI**， **<font color='red'>对象的依赖关系</font>将由系统中负责协调各对象的<font color='red'>第三方组件</font>在<font color='red'>创建对象的时候进行设定</font>**。 对象无需自行创建或管理它们的依赖关系。

​	DI的另外一种介绍：DI是组装应用对象的一种方式，借助这种方式，对象无需知道依赖来自何处或者依赖的实现方式。不同于自己获得依赖对象，对象会在运行期间被容器赋予它们所依赖的对象。依赖对象通常会通过接口来被注入，这样就可以保证低耦合。

​	依赖注入会将所依赖的关系自动交给目标对象， 而不是让对象自己去获取依赖。为了展示这一点， 让我们看一看程序清单1.3中的BraveKnight， 这个骑士不仅勇敢， 而且能挑战任何形式的探险。

```java
package sia.knights;
  
public class BraveKnight implements Knight {

  private Quest quest;

  public BraveKnight(Quest quest) {
    this.quest = quest;
  }

  public void embarkOnQuest() {
    quest.embark();
  }

}
```

​	我们可以看到， 不同于之前的DamselRescuingKnight， BraveKnight没有自行创建探险任务， 而是在构造的时候把探险任务作为构造器参数传入。 这是依赖注入的方式之一， 即**构造器注入（constructor injection）** 。

​	更重要的是， 传入的探险类型是Quest， 也就是所有探险任务都必须实现的一个接口。 所以， BraveKnight能够响应RescueDamselQuest、 SlayDragonQuest、 MakeRound TableRounderQuest等任意的Quest实现。

​	这里的要点是BraveKnight没有与任何特定的Quest实现发生耦合。 对它来说， 被要求挑战的探险任务只要实现了Quest接口， 那么具体是哪种类型的探险就无关紧要了。 **这就是DI所带来的最大收益——松耦合**。

​	**如果一个对象只通过接口（而不是具体实现或初始化过程） 来表明依赖关系， 那么这种依赖就能够在对象本身毫不知情的情况下， 用不同的具体实现进行替换。**

​	现在BraveKnight类可以接受你传递给它的任意一种Quest的实现， 但该怎样把特定的Query实现传给它呢？ 假设， 希望BraveKnight所要进行探险任务是杀死一只怪龙， 那么程序清单1.5中的SlayDragonQuest也许是挺合适的。

```java
package sia.knights;

import java.io.PrintStream;

public class SlayDragonQuest implements Quest {

  private PrintStream stream;

  public SlayDragonQuest(PrintStream stream) {
    this.stream = stream;
  }

  public void embark() {
    stream.println("Embarking on quest to slay the dragon!");
  }

}
```

​	SlayDragonQuest是要注入到BraveKnight中的Quest实现

​	我们可以看到， SlayDragonQuest实现了Quest接口， 这样它就适合注入到BraveKnight中去了。 与其他的Java入门样例有所不同， SlayDragonQuest没有使用System.out.println()， 而是在构造方法中请求一个更为通用的PrintStream。 这里最大的问题在于:

- 我们该如何将SlayDragonQuest交给BraveKnight呢？
- 又如何将PrintStream交给SlayDragonQuest呢？

​	创建应用组件之间协作的行为通常称为装配（wiring） 。 Spring有多种装配bean的方式， 采用XML是很常见的一种装配方式。 程序清单1.6展现了一个简单的Spring配置文件： knights.xml， 该配置文件将BraveKnight、 SlayDragonQuesth和PrintStream装配到了一起。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="..." ...>

  <bean id="knight" class="sia.knights.BraveKnight">
    <constructor-arg ref="quest" />
  </bean>

  <bean id="quest" class="sia.knights.SlayDragonQuest">
    <constructor-arg value="#{T(System).out}" />
  </bean>

</beans>
```

​	**装配的作用就是创建类的实例，同时将类的构造函数或者setter函数参数设置好，这是为了配置对象和对象之间的关系。**

​	在这里， BraveKnight和SlayDragonQuest被声明为Spring中的bean。 就BraveKnight bean来讲， 它在构造时传入了对SlayDragonQuest bean的引用， 将其作为构造器参数。 同时， SlayDragonQuest bean的声明使用了Spring表达式语言（Spring Expression Language） ， 将System.out（这是一个PrintStream） 传入到了SlayDragonQuest的构造器中。

​	Spring还支持使用Java来描述配置。 比如， 程序清单1.7展现了基于Java的配置， 它的功能与程序清单1.6相同。

```java
package sia.knights.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import sia.knights.BraveKnight;
import sia.knights.Knight;
import sia.knights.Quest;
import sia.knights.SlayDragonQuest;

@Configuration
public class KnightConfig {

  @Bean
  public Knight knight() {
    return new BraveKnight(quest());
  }
  
  @Bean
  public Quest quest() {
    return new SlayDragonQuest(System.out);
  }

}
```

​	尽管BraveKnight依赖于Quest， 但是它并不知道传递给它的是什么类型的Quest， 也不知道这个Quest来自哪里。 与之类似， SlayDragonQuest依赖于PrintStream， 但是在编码时它并不需要知道这个PrintStream是什么样子的。 **总之，尽管Bean之间存在种种依赖关系，但在编码时它们并不知道具体依赖谁，只有Spring通过它的配置， 能够了解这些组成部分是如何装配起来的。 这样的话， 就可以在不改变所依赖的类的情况下， 修改依赖关系**。

### ApplicationContext 自动装配

​	Spring通过应用上下文（Application Context） 装载bean的定义并把它们组装起来。 Spring应用上下文全权负责对象的创建和组装。 Spring自带了多种应用上下文的实现， 它们之间主要的区别仅仅在于如何加载配置。

​	因为knights.xml中的bean是使用XML文件进行配置的， 所以选择ClassPathXmlApplicationContext作为应用上下文相对是比较合适的。 该类加载位于应用程序类路径下的一个或多个XML配置文件。 程序清单1.8中的main()方法调用ClassPathXmlApplicationContext加载knights.xml， 并获得Knight对象的引用。

```java
package sia.knights;

import org.springframework.context.support.
                   ClassPathXmlApplicationContext;

public class KnightMain {

  public static void main(String[] args) throws Exception {
    ClassPathXmlApplicationContext context = 
        new ClassPathXmlApplicationContext(
            "META-INF/spring/knight.xml");
    
    Knight knight = context.getBean(Knight.class);
    knight.embarkOnQuest();
    context.close();
  }

}
```

​	这里的main()方法基于knights.xml文件创建了Spring应用上下文。 随后它调用该应用上下文获取一个ID为knight的bean。 得到Knight对象的引用后， 只需简单调用embarkOnQuest()方法就可以执行所赋予的探险任务了。 **注意这个类完全不知道我们的英雄骑士接受哪种探险任务， 而且完全没有意识到这是由BraveKnight来执行的。 只有knights.xml文件知道哪个骑士执行哪种探险任务。**

### 面向切面编程

​	**DI能够让相互协作的软件组件保持松散耦合， 而面向切面编程（aspect-oriented programming， AOP） 允许你把遍布应用各处的功能分离出来形成可重用的组件。**

​	面向切面编程往往被定义为促使软件系统实现关注点的分离一项技术。 系统由许多不同的组件组成， 每一个组件各负责一块特定功能。 除了实现自身核心的功能之外， 这些组件还经常承担着额外的职责。 诸如日志、 事务管理和安全这样的系统服务经常融入到自身具有核心业务逻辑的组件中去， 这些系统服务通常被称为横切关注点， 因为它们会跨越系统的多个组件。

​	如果将这些关注点分散到多个组件中去， 你的代码将会带来双重的复杂性。

- 实现系统关注点功能的代码将会重复出现在多个组件中。 这意味着如果你要改变这些关注点的逻辑， 必须修改各个模块中的相关实现。
- 即使你把这些关注点抽象为一个独立的模块， 其他模块只是调用它的方法， 但方法的调用还是会重复出现在各个模块中。
- 组件会因为那些与自身核心业务无关的代码而变得混乱。 一个向地址簿增加地址条目的方法应该只关注如何添加地址， 而不应该关注它是不是安全的或者是否需要支持事务

​	下图展示了这种复杂性。 左边的业务对象与系统级服务结合得过于紧密。 每个对象不但要知道它需要记日志、 进行安全控制和参与事务， 还要亲自执行这些服务。

![](/Users/mrjiao/Documents/typora/java/assets/业务对象与系统级服务结合过于紧密.png)

​	在整个系统内， 关注点（例如日志和安全）的调用经常散布到各个模块中， 而这些关注点并不是模块的核心业务。

​	AOP能够使这些服务模块化， 并以声明的方式将它们应用到它们需要影响的组件中去。 所造成的结果就是这些组件会具有更高的内聚性并且会更加关注自身的业务， 完全不需要了解涉及系统服务所带来复杂性。 总之， AOP能够确保POJO的简单性。

​	如下图所示， 我们可以把切面想象为覆盖在很多组件之上的一个外壳。 应用是由那些实现各自业务功能的模块组成的。 借助AOP， 可以使用各种功能层去包裹核心业务层。 这些层以声明的方式灵活地应用到系统中， 你的核心应用甚至根本不知道它们的存在。 这是一个非常强大的理念， 可以将安全、 事务和日志关注点与核心业务逻辑相分离。

![](/Users/mrjiao/Documents/typora/java/assets/业务与系统级业务相分离.png)

​	利用AOP， 系统范围内的关注点覆盖在它们所影响组件之上

​	为了示范在Spring中如何应用切面， 让我们重新回到骑士的例子， 并为它添加一个切面。

​	每一个人都熟知骑士所做的任何事情， 这是因为吟游诗人用诗歌记载了骑士的事迹并将其进行传唱。 假设我们需要使用吟游诗人这个服务类来记载骑士的所有事迹。 程序清单1.9展示了我们会使用的Minstrel类。

```java
package sia.knights;

import java.io.PrintStream;

public class Minstrel {

  private PrintStream stream;
  
  public Minstrel(PrintStream stream) {
    this.stream = stream;
  }

  public void singBeforeQuest() {
    stream.println("Fa la la, the knight is so brave!");
  }

  public void singAfterQuest() {
    stream.println("Tee hee hee, the brave knight " +
    		"did embark on a quest!");
  }

}
```

​	正如你所看到的那样， Minstrel是只有两个方法的简单类。 在骑士执行每一个探险任务之前， singBeforeQuest()方法会被调用； 在骑士完成探险任务之后， singAfterQuest()方法会被调用。 在这两种情况下， Minstrel都会通过一个PrintStream类来歌颂骑士的事迹， 这个类是通过构造器注入进来的。

​	把Minstrel加入你的代码中并使其运行起来， 这对你来说是小事一桩。 我们适当做一下调整从而让BraveKnight可以使用Minstrel。 程序清单1.10展示了将BraveKnight和Minstrel组合起来的第一次尝试。

```java
package sia.knights;
  
public class BraveKnight implements Knight {

  private Quest quest;
  private Minstrel minstrel;

  public BraveKnight(Quest quest, Minstrel minstrel) {
    this.quest = quest;
		this.minstrel = minstrel;
  }

  public void embarkOnQuest() {
		minstrel.singBeforeQuest();
    quest.embark();
		minstrel.singAfterQuest();
  }

}
```

​	这应该可以达到预期效果。 现在， 你所需要做的就是回到Spring配置中， 声明Minstrel bean并将其注入到BraveKnight的构造器之中。

​	但是， 请稍等……

​	我们似乎感觉有些东西不太对。 管理他的吟游诗人真的是骑士职责范围内的工作吗？ 在我看来， 吟游诗人应该做他份内的事， 根本不需要骑士命令他这么做。 毕竟， 用诗歌记载骑士的探险事迹， 这是吟游诗人的职责。 为什么骑士还需要提醒吟游诗人去做他份内的事情呢？此外， 因为骑士需要知道吟游诗人， 所以就必须把吟游诗人注入到BarveKnight类中。 这不仅使BraveKnight的代码复杂化了， 而且还让我疑惑是否还需要一个不需要吟游诗人的骑士呢？ 如果Minstrel为null会发生什么呢？ 我是否应该引入一个空值校验逻辑来覆盖该场景？

​	简单的BraveKnight类开始变得复杂， 如果你还需要应对没有吟游诗人时的场景， 那代码会变得更复杂。 但利用AOP， 你可以声明吟游诗人必须歌颂骑士的探险事迹， 而骑士本身并不用直接访问Minstrel的方法。

​	要将Minstrel抽象为一个切面， 你所需要做的事情就是在一个Spring配置文件中声明它。 程序清单1.11是更新后的knights.xml文件， Minstrel被声明为一个切面。

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="..." ...>

  <bean id="knight" class="sia.knights.BraveKnight">
    <constructor-arg ref="quest" />
  </bean>

  <bean id="quest" class="sia.knights.SlayDragonQuest">
    <constructor-arg ref="fakePrintStream" />
  </bean>

  <bean id="minstrel" class="sia.knights.Minstrel">
    <constructor-arg ref="fakePrintStream" />
  </bean>

  <bean id="fakePrintStream" class="sia.knights.FakePrintStream" />

  <aop:config>
    <aop:aspect ref="minstrel">
      <aop:pointcut id="embark"
          expression="execution(* *.embarkOnQuest(..))"/>
        
      <aop:before pointcut-ref="embark" 
          method="singBeforeQuest"/>

      <aop:after pointcut-ref="embark" 
          method="singAfterQuest"/>
    </aop:aspect>
  </aop:config>

</beans>
```

​	这里使用了Spring的aop配置命名空间把Minstrel bean声明为一个切面。 首先， 需要把Minstrel声明为一个bean， 然后在元素中引用该bean。 为了进一步定义切面， 声明（使用） 在embarkOnQuest()方法执行前调用Minstrel的singBeforeQuest()方法。 这种方式被称为前置通知（before advice） 。 同时声明（使用）在embarkOnQuest()方法执行后调用singAfter Quest()方法。 这种方式被称为后置通知（after advice） 。

​	首先， Minstrel仍然是一个POJO， 没有任何代码表明它要被作为一个切面使用。 当我们按照上面那样进行配置后， 在Spring的上下文中， Minstrel实际上已经变成一个切面了。

​	其次， 也是最重要的， Minstrel可以被应用到BraveKnight中， 而BraveKnight不需要显式地调用它。 实际上， BraveKnight完全不知道Minstrel的存在。

​	必须还要指出的是， 尽管我们使用Spring把Minstrel转变为一个切面， 但首先要把它声明为一个Spring bean。 能够为其他Spring bean做到的事情都可以同样应用到Spring切面中， 例如为它们注入依赖。

### 使用模板消除样板式代码

​	你是否写过这样的代码， 当编写的时候总会感觉以前曾经这么写过？ 我的朋友， 这不是似曾相识。 这是样板式的代码（boilerplate code） 。 通常为了实现通用的和简单的任务， 你不得不一遍遍地重复编写这样的代码。

​	遗憾的是， 它们中的很多是因为使用Java API而导致的样板式代码。 样板式代码的一个常见范例是使用JDBC访问数据库查询数据。 举个例子， 如果你曾经用过JDBC， 那么你或许会写出类似下面的代码。

```java
// jdbc复习

```

​	正如你所看到的， 这段JDBC代码查询数据库获得员工姓名和薪水。 我打赌你很难把上面的代码逐行看完， 这是因为少量查询员工的代码淹没在一堆JDBC的样板式代码中。 首先你需要创建一个数据库连接， 然后再创建一个语句对象， 最后你才能进行查询。

​	为了平息JDBC可能会出现的怒火， 你必须捕捉SQLException， 这是一个检查型异常， 即使它抛出后你也做不了太多事情。

​	最后， 毕竟该说的也说了， 该做的也做了， 你不得不清理战场， 关闭数据库连接、 语句和结果集。 同样为了平息JDBC可能会出现的怒火， 你依然要捕SQLException。

​	上面查询代码和你实现其他JDBC操作时所写的代码几乎是相同的。 只有少量的代码与查询员工逻辑有关系， 其他的代码都是JDBC的样板代码。

​	JDBC不是产生样板式代码的唯一场景。 在许多编程场景中往往都会导致类似的样板式代码， JMS、 JNDI和使用REST服务通常也涉及大量的重复代码。

​	Spring旨在通过模板封装来消除样板式代码。 Spring的JdbcTemplate使得执行数据库操作时， 避免传统的JDBC样板代码成为了可能。

​	举个例子， 使用Spring的JdbcTemplate（利用了 Java 5特性的JdbcTemplate实现） 重写的getEmployeeById()方法仅仅关注于获取员工数据的核心逻辑， 而不需要迎合JDBC API的需求。 程序清单1.13展示了修订后的getEmployeeById()方法。

```java
// spring jdbc template

```

​	正如你所看到的， 新版本的getEmployeeById()简单多了， 而且仅仅关注于从数据库中查询员工。 模板的queryForObject()方法需要一个SQL查询语句， 一个RowMapper对象（把数据映射为一个域对象） ， 零个或多个查询参数。 GetEmp loyeeById()方法再也看不到以前的JDBC样板式代码了， 它们全部被封装到了模板中。

​	我已经向你展示了Spring通过面向POJO编程、 DI、 切面和模板技术来简化Java开发中的复杂性。 在这个过程中， 我展示了在基于XML的配置文件中如何配置bean和切面， 但这些文件是如何加载的呢？ 它们被加载到哪里去了？ 让我们再了解下Spring容器， 这是应用中的所有bean所驻留的地方。

# Spring结构

Spring结构图如下：

![](/Users/mrjiao/Documents/typora/java/assets/Spring%E7%BB%93%E6%9E%84%E5%9B%BE.png)

各模块解析如下：

- Spring核心容器
- Spring的AOP模块
- 数据访问与集成
- web与远程调用
- Instrument
- 测试

# 装配Bean

### 简介

​	类似于拍一部电影需要很多人合作，实现一个应用程序也需要各个对象的紧密合作。在传统java开发中，一般都是通过构造器或者查找来关联对象之间的关系，这样通常会导致结构复杂的代码，难以复用也难以单元测试。如果情况不严重，这些对象只是做了它们自己分外的事，情况严重，则代码难以复用和进行单元测试。

​	在Spring中，对象无需自己查找或创建与其所关联的其他对象，相反，容器负责把需要相互协作的对象引用赋予给各个对象。**创建应用对象之间的协作关系的行为通常称为装配(wiring)，这也是依赖注入的本质**。

### Spring配置的可选方案

​	Spring提供了如下几种Bean的装配方案：

- 自动化装配Bean
- 通过Java代码装配Bean
- 通过XML装配Bean

### 自动化装配Bean

​	Spring 自动化装配Bean是最便利的装配Bean的方式，其从两个角度来实现自动化装配：

- 组件扫描(Component Scanning)：Spring会自动发现应用上下文中创建的Bean；
- 自动装配(Autowiring)：Spring自动满足Bean之间的依赖。

### 通过JavaConfig装配Bean



### 通过XML装配Bean



### Bean的作用域

​	Spring定义了多种作用域，可以基于这些作用域创建Bean，包括：

- 单例(Singleton)：在整个应用中，只创建Bean的一个实例，为默认作用域；
- 原型(Prototype)：每次注入或通过Spring应用上下文获取的时候，都创建一个Bean；
- 会话(Session)：在Web应用中，为每个会话创建一个Bean实例；
- 请求(Request)：在Web应用中，为每个请求创建一个Bean实例。

# Bean容器

### 简介

​	在基于Spring的应用中，应用对象存在于Spring容器中，Spring容器负责创建对象，装配它们，配置它们并管理它们的整个生命周期，从出生到死亡。如下图所示：

![](/Users/mrjiao/Documents/typora/java/assets/Spring容器.png)

​	容器是Spring框架的核心，Spring容器使用DI管理构成应用的组件，它会创建相互协作的组件之间的关联。这样会使对象更加简单干净，更加易于测试。

​	Spring容器并不是只有一个，Spring自带了多个容器实现，可以归结为两种不同的类型。

- Bean工厂容器(由BeanFactory接口定义)，该容器是最简单的容器，提供基本的DI支持；
- 应用上下文(由ApplicationContext接口定义)，该容器是基于BeanFactory创建，并提供框架级别的服务，例如从属性文件解析文本信息以及发布应用实践给感兴趣的事件监听者。



由于BeanFactory对大多数应用来说往往太低级，因此，ApplicationContext要比BeanFactory更受欢迎，此处着重讨论ApplicationContext。

### ApplicationContext

ApplicationContext的整体结构图如下：

![](/Users/mrjiao/Documents/typora/java/assets/SpringApplicationContext结构.png)

由图可以看出，Spring自带了多种类型的应用上下文。如：

- AnnotationConfigApplicationContext：从一个或多个基于Java的配置类中加载Spring应用上下文；
- AnnotationConfigWebApplicationContext：从一个或多个基于Java的配置类中加载Spring Web应用上下文；
- ClassPathXmlApplicationContext：从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类资源；
- FileSystemApplicationContext：从文件系统下的一个或多个XML配置文件中加载上下文定义；
- XmlWebApplicationContext：从Web应用下的一个或者多个XML配置文件中加载上下文定义。

无论是从文件系统中装载应用上下文还是从类路径下装载应用上下文，将bean加载到bean工厂的过程都是相似的。如以下代码所示：

```java
//从文件路径上加载
ApplicationContext context = new FileSystemXmlApplicationContext("/usr/test.xml");
//从应用的类路径下加载
ApplicationContext context = new ClassPathXmlApplicationContext("tet.xml");
//从配置类中加载
ApplicationContext context = new AnnotationConfigApplicationContext(MainConfig.class);
```

### BeanFactory

### 基于注解向容器中注入bean



# [Bean的生命周期](https://www.cnblogs.com/zrtqsk/p/3735273.html)

### 简介

​	在传统的JAVA应用中，bean的生命周期很简单，使用 new 关键字进行实例化，然后bean就可以使用了。一旦不使用，bean就会被垃圾回收机制自动回收。

​	但Spring容器中，bean的生命周期要复杂很多，ApplicationContext中一个bean的生命周期如下：

![](/Users/mrjiao/Documents/typora/java/assets/bean 生命周期1.png)

### 接口方法分类

Bean的完整生命周期经历了各种方法调用，这些方法可以划分为以下几类：

1. Bean自身的方法：这个包括了Bean本身调用的方法和通过配置文件中<bean>的init-method和destroy-method指定的方法

2. Bean级生命周期接口方法：这个包括了BeanNameAware、BeanFactoryAware、InitializingBean和DiposableBean这些接口的方法

3. 容器级生命周期接口方法：这个包括了InstantiationAwareBeanPostProcessor 和 BeanPostProcessor 这两个接口实现，一般称它们的实现类为“后处理器”。

4. 工厂后处理器接口方法：这个包括了AspectJWeavingEnabler, ConfigurationClassPostProcessor, CustomAutowireConfigurer等等非常有用的工厂后处理器接口的方法。工厂后处理器也是容器级的。在应用上下文装配配置文件之后立即调用。

在函数调用中的具体流程如下，其中，**红色为容器级生命周期接口方法，绿色为Bean级生命周期接口方法**：

![](/Users/mrjiao/Documents/typora/java/assets/bean%E5%A3%B0%E6%98%8E%E5%91%A8%E6%9C%9F2.png)

### 完整生命周期测试

1. 首先是一个简单的Spring Bean，调用Bean自身的方法和Bean级生命周期接口方法，为了方便演示，它实现了BeanNameAware、BeanFactoryAware、InitializingBean和DiposableBean这4个接口，同时有2个方法，对应配置文件中<bean>的init-method和destroy-method。如下：

   ```java
   public class Person implements BeanFactoryAware, BeanNameAware,
           InitializingBean, DisposableBean {
   
       private String name;
       private String address;
       private int phone;
   
       private BeanFactory beanFactory;
       private String beanName;
   
       public Person() {
           System.out.println("【构造器】调用Person的构造器实例化");
       }
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           System.out.println("【注入属性】注入属性name");
           this.name = name;
       }
   
       public String getAddress() {
           return address;
       }
   
       public void setAddress(String address) {
           System.out.println("【注入属性】注入属性address");
           this.address = address;
       }
   
       public int getPhone() {
           return phone;
       }
   
       public void setPhone(int phone) {
           System.out.println("【注入属性】注入属性phone");
           this.phone = phone;
       }
   
       @Override
       public String toString() {
           return "Person [address=" + address + ", name=" + name + ", phone="
                   + phone + "]";
       }
   
       // 这是BeanFactoryAware接口方法
       @Override
       public void setBeanFactory(BeanFactory arg0) throws BeansException {
           System.out
                   .println("【BeanFactoryAware接口】调用BeanFactoryAware.setBeanFactory()");
           this.beanFactory = arg0;
       }
   
       // 这是BeanNameAware接口方法
       @Override
       public void setBeanName(String arg0) {
           System.out.println("【BeanNameAware接口】调用BeanNameAware.setBeanName()");
           this.beanName = arg0;
       }
   
       // 这是InitializingBean接口方法
       @Override
       public void afterPropertiesSet() throws Exception {
           System.out
                   .println("【InitializingBean接口】调用InitializingBean.afterPropertiesSet()");
       }
   
       // 这是DiposibleBean接口方法
       @Override
       public void destroy() throws Exception {
           System.out.println("【DiposibleBean接口】调用DiposibleBean.destory()");
       }
   
       // 通过<bean>的init-method属性指定的初始化方法
       public void myInit() {
           System.out.println("【init-method】调用<bean>的init-method属性指定的初始化方法");
       }
   
       // 通过<bean>的destroy-method属性指定的初始化方法
       public void myDestory() {
           System.out.println("【destroy-method】调用<bean>的destroy-method属性指定的初始化方法");
       }
   }
   ```

2. 接下来是演示BeanPostProcessor接口的方法，如下：

   ```java
   public class MyBeanPostProcessor implements BeanPostProcessor {
   
       public MyBeanPostProcessor() {
           super();
           System.out.println("这是BeanPostProcessor实现类构造器！！");
           // TODO Auto-generated constructor stub
       }
   
       @Override
       public Object postProcessAfterInitialization(Object arg0, String arg1)
               throws BeansException {
           System.out
           .println("BeanPostProcessor接口方法postProcessAfterInitialization对属性进行更改！");
           return arg0;
       }
   
       @Override
       public Object postProcessBeforeInitialization(Object arg0, String arg1)
               throws BeansException {
           System.out
           .println("BeanPostProcessor接口方法postProcessBeforeInitialization对属性进行更改！");
           return arg0;
       }
   }
   ```

   ​	如上，BeanPostProcessor接口包括2个方法postProcessAfterInitialization和postProcessBeforeInitialization，这两个方法的第一个参数都是要处理的Bean对象，第二个参数都是Bean的name。返回值也都是要处理的Bean对象。这里要注意。

3. InstantiationAwareBeanPostProcessor 接口本质是BeanPostProcessor的子接口，一般我们继承Spring为其提供的适配器类InstantiationAwareBeanPostProcessor Adapter来使用它，如下：

   ```java
   package springBeanTest;
   
   import java.beans.PropertyDescriptor;
   
   import org.springframework.beans.BeansException;
   import org.springframework.beans.PropertyValues;
   import org.springframework.beans.factory.config.InstantiationAwareBeanPostProcessorAdapter;
   
   public class MyInstantiationAwareBeanPostProcessor extends
           InstantiationAwareBeanPostProcessorAdapter {
   
       public MyInstantiationAwareBeanPostProcessor() {
           super();
           System.out
                   .println("这是InstantiationAwareBeanPostProcessorAdapter实现类构造器！！");
       }
   
       // 接口方法、实例化Bean之前调用
       @Override
       public Object postProcessBeforeInstantiation(Class beanClass,
               String beanName) throws BeansException {
           System.out
                   .println("InstantiationAwareBeanPostProcessor调用postProcessBeforeInstantiation方法");
           return null;
       }
   
       // 接口方法、实例化Bean之后调用
       @Override
       public Object postProcessAfterInitialization(Object bean, String beanName)
               throws BeansException {
           System.out
                   .println("InstantiationAwareBeanPostProcessor调用postProcessAfterInitialization方法");
           return bean;
       }
   
       // 接口方法、设置某个属性时调用
       @Override
       public PropertyValues postProcessPropertyValues(PropertyValues pvs,
               PropertyDescriptor[] pds, Object bean, String beanName)
               throws BeansException {
           System.out
                   .println("InstantiationAwareBeanPostProcessor调用postProcessPropertyValues方法");
           return pvs;
       }
   }
   ```

   这个有3个方法，其中第二个方法postProcessAfterInitialization就是重写了BeanPostProcessor的方法。第三个方法postProcessPropertyValues用来操作属性，返回值也应该是PropertyValues对象。

4. 演示工厂后处理器接口方法，如下：

   ```java
   package springBeanTest;
   
   import org.springframework.beans.BeansException;
   import org.springframework.beans.factory.config.BeanDefinition;
   import org.springframework.beans.factory.config.BeanFactoryPostProcessor;
   import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
   
   public class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor {
   
       public MyBeanFactoryPostProcessor() {
           super();
           System.out.println("这是BeanFactoryPostProcessor实现类构造器！！");
       }
   
       @Override
       public void postProcessBeanFactory(ConfigurableListableBeanFactory arg0)
               throws BeansException {
           System.out
                   .println("BeanFactoryPostProcessor调用postProcessBeanFactory方法");
           BeanDefinition bd = arg0.getBeanDefinition("person");
           bd.getPropertyValues().addPropertyValue("phone", "110");
       }
   
   }
   ```

5. 配置xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
       xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="
               http://www.springframework.org/schema/beans 
               http://www.springframework.org/schema/beans/spring-beans-3.2.xsd">
   
       <bean id="beanPostProcessor" class="springBeanTest.MyBeanPostProcessor">
       </bean>
   
       <bean id="instantiationAwareBeanPostProcessor" class="springBeanTest.MyInstantiationAwareBeanPostProcessor">
       </bean>
   
       <bean id="beanFactoryPostProcessor" class="springBeanTest.MyBeanFactoryPostProcessor">
       </bean>
       
       <bean id="person" class="springBeanTest.Person" init-method="myInit"
           destroy-method="myDestory" scope="singleton" p:name="张三" p:address="广州"
           p:phone="15900000000" />
   
   </beans>
   ```

6. 测试

   ```java
   package springBeanTest;
   
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   
   public class BeanLifeCycle {
   
       public static void main(String[] args) {
   
           System.out.println("现在开始初始化容器");
           
           ApplicationContext factory = new ClassPathXmlApplicationContext("springBeanTest/beans.xml");
           System.out.println("容器初始化成功");    
           //得到Preson，并使用
           Person person = factory.getBean("person",Person.class);
           System.out.println(person);
           
           System.out.println("现在开始关闭容器！");
           ((ClassPathXmlApplicationContext)factory).registerShutdownHook();
       }
   }
   ```

   关闭容器使用的是实际是AbstractApplicationContext的钩子方法。

   我们来看一下结果：

   ```java
   现在开始初始化容器
   
   loadBeanDefinitions
   
   这是BeanFactoryPostProcessor实现类构造器！！
   
   BeanFactoryPostProcessor调用postProcessBeanFactory方法
   
   这是BeanPostProcessor实现类构造器！！
   
   这是InstantiationAwareBeanPostProcessorAdapter实现类构造器！！
   
   InstantiationAwareBeanPostProcessor调用postProcessBeforeInstantiation方法
   
   【构造器】调用Person的构造器实例化
   
   InstantiationAwareBeanPostProcessor调用postProcessPropertyValues方法
   
   【注入属性】注入属性address
   【注入属性】注入属性name
   【注入属性】注入属性phone
   
   【BeanNameAware接口】调用BeanNameAware.setBeanName()
     
   【BeanFactoryAware接口】调用BeanFactoryAware.setBeanFactory()
     
   BeanPostProcessor接口方法postProcessBeforeInitialization对属性进行更改！
   
   【InitializingBean接口】调用InitializingBean.afterPropertiesSet()
     
   【init-method】调用<bean>的init-method属性指定的初始化方法
   
   BeanPostProcessor接口方法postProcessAfterInitialization对属性进行更改！
   
   InstantiationAwareBeanPostProcessor调用postProcessAfterInitialization方法
   
   容器初始化成功
   
   Person [address=广州, name=张三, phone=110]
   
   现在开始关闭容器！
   
   【DiposibleBean接口】调用DiposibleBean.destory()
     
   【destroy-method】调用<bean>的destroy-method属性指定的初始化方法
   ```

### bean的前置/后置处理

​	上图中可以看到bean在被容器创建的过程中会执行一些前置操作和后置操作，具体如何指定？有以下四种绑定方式：

##### 声明并绑定init/destroy方法：

​	该方式属于Bean自身的方法。

​	基础的bean类定义如下，可以看到已经声明了init和destroy方法，注意此处方法名并不一定是init和destroy，只要正确绑定就可以。

```java
//基础的bean类
public class Car{
		public void init(){sout("Car init");}
  
    public void destroy(){}
  
    public Car(){sout("Car construct");}
  
    public void doSomethingElse(){}
}
```

1. 基于xml的方式：

```xml
<bean id="car" class="Car" init-method="init" destroy-method="destroy"></bean>
```

2. 基于注解的方式：

```java
//配置类
@Configuration	//使用该注解表明该类是一个配置类
public class Cfg(){
  
    @Bean(initMethod="init", destryMethod="destroy")
    public Car getCar(){
        return new Car();
    }
}
```

```java
//容器启动
public class Test{
    
    @Test
    public void test(){
        ApplicationContext context = new AnnotationConfigApplicationContext(Cfg.class);
    		sout("容器创建完成");
        context.close();
    }  
}
```

```java
//输出
car construct
car init
容器创建完成
car destroy
```

​	由此引入init和destroy的作用，很多情况下需要对bean进行一些预处理和后置操作，比如说连接数据源、关闭连接等，这些操作都可以放在init和destroy中完成。

​	上面的测试用例是建立在单实例的情况下，**在单实例情况下，bean会被自动创建，会自动调用其init和destroy方法，而在多实例情况下，bean只有在获取时才会被创建，只有在创建时init才会被调用，创建完成后销毁容器也不会调用destroy！**

​	如下例所示：

```java
//基础的bean类
public class Car{
		public void init(){sout("Car init");}
  
    public void destroy(){}
  
    public Car(){sout("Car construct");}
  
    public void doSomethingElse(){}
}
```

```java
//配置类
@Configuration	//使用该注解表明该类是一个配置类
public class Cfg(){
  
    @Scope("prototype")	//改变作用域，改成多实例
    @Bean(initMethod="init", destryMethod="destroy")
    public Car getCar(){
        return new Car();
    }
}
```

```java
//容器启动
public class Test{
    
    @Test
    public void test(){
        ApplicationContext context = new AnnotationConfigApplicationContext(Cfg.class);
    		sout("容器创建完成");
        context.close();
    }  
}
```

```java
//输出
容器创建完成
```

可以看到，bean的构造函数、init函数和destroy函数都没有被执行。我们再将代码进行改动：

```java
//基础的bean类
public class Car{
		public void init(){sout("Car init");}
  
    public void destroy(){}
  
    public Car(){sout("Car construct");}
  
    public void doSomethingElse(){}
}
```

```java
//配置类
@Configuration	//使用该注解表明该类是一个配置类
public class Cfg(){
  
    @Scope("prototype")	//改变作用域，改成多实例
    @Bean(initMethod="init", destryMethod="destroy")
    public Car getCar(){
        return new Car();
    }
}
```

```java
//容器启动
public class Test{
    
    @Test
    public void test(){
        ApplicationContext context = new AnnotationConfigApplicationContext(Cfg.class);
    		sout("容器创建完成");
        Car car = (Car)context.getBean(Car.class);
        context.close();
    }  
}
```

```java
//输出
car construct
car init
容器创建完成
```

可以看到，bean的构造函数和init函数都被调用，但销毁函数并没有调用。**多实例情况下destroy函数并不会随ioc容器的关闭而自动调用，需要手动调用！**

##### InitializingBean.afterPropertiesSet()

​	[该方式属于Bean级生命周期的方法](https://www.bilibili.com/video/av20967380/?p=13)。



# 面向切面的Spring

### 简介

​	软件中的一些功能(如日志、安全、事务管理等)会用到应用程序的多个地方，若在每个点都调用它们，代码会很繁杂，如下图所示：

![](/Users/mrjiao/Documents/typora/java/assets/业务对象与系统级服务结合过于紧密.png)

​	可否让业务对象只关注自身的业务，而将其它方面的问题交由其他应用对象来处理呢？如下图所示：

![](/Users/mrjiao/Documents/typora/java/assets/业务与系统级业务相分离.png)

​	可以看到，所有的业务对象都被集中到了一个面上，在这个面的下方有事务管理切面、日志管理切面、安全管理切面。我们每次只关心业务切面上的逻辑，而将别的切面上的逻辑交由其它应用对象来处理。这就是面向切面编程的思想(AOP，Aspect-Oriented Programming)。

# IOC代码解析

### [简介](https://www.jianshu.com/p/83693d3d0a65)

​	**IOC 总体来说有两处地方最重要，一个是创建 Bean 容器，一个是初始化 Bean**，如果读者觉得一次性看完本文压力有点大，那么可以按这个思路分两次消化。读者不一定对 Spring 容器的源码感兴趣，也许附录部分介绍的知识对读者有些许作用。

​	希望通过本文可以让读者不惧怕阅读 Spring 源码，也希望大家能反馈表述错误或不合理的地方。

### 引言

##### 启动Spring容器

先看下最基本的启动 Spring 容器的例子：

```java
public static void main(String[] args) {
    ApplicationContext context = new ClassPathXmlApplicationContext("classpath:applicationfile.xml");
}
```

以上代码就可以利用配置文件来启动一个 Spring 容器了，请使用 maven 的小伙伴直接在 dependencies 中加上以下依赖即可，个人比较反对那些不知道要添加什么依赖，然后把 Spring 的所有相关的东西都加进来的方式。

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>4.3.11.RELEASE</version>
</dependency>
```

**注：spring-context 会自动将 spring-core、spring-beans、spring-aop、spring-expression 这几个 jar 包带进来。**

​	多说一句，很多开发者入门就直接接触的 SpringMVC，对 Spring 其实不是很了解，Spring 是渐进式的工具，并不具有很强的侵入性，它的模块也划分得很合理，即使你的应用不是 web 应用，或者之前完全没有使用到 Spring，而你就想用 Spring 的依赖注入这个功能，其实完全是可以的，它的引入不会对其他的组件产生冲突。

<hr>

##### ApplicationContext 结构

​	废话说完，我们继续。`ApplicationContext context = new ClassPathXmlApplicationContext(...)` 其实很好理解，从名字上就可以猜出一二，就是在 ClassPath 中寻找 xml 配置文件，根据 xml 文件内容来构建 ApplicationContext。当然，除了 ClassPathXmlApplicationContext 以外，我们也还有其他构建 ApplicationContext 的方案可供选择，我们先来看看大体的继承结构是怎么样的：

![](/Users/mrjiao/Documents/typora/java/assets/SpringApplicationContext结构.png)

​	我们可以看到，ClassPathXmlApplicationContext 兜兜转转了好久才到 ApplicationContext 接口，同样的，我们也可以使用绿颜色的 **FileSystemXmlApplicationContext** 和 **AnnotationConfigApplicationContext** 这两个类。

- **FileSystemXmlApplicationContext** 的构造函数需要一个 xml 配置文件在系统中的路径，其他和 ClassPathXmlApplicationContext 基本上一样。

- **AnnotationConfigApplicationContext** 是基于注解来使用的，它不需要配置文件，采用 java 配置类和各种注解来配置，是比较简单的方式，也是大势所趋吧。

<hr>

##### 实例化ApplicationContext

​	不过本文旨在帮助大家理解整个构建流程，所以决定使用 ClassPathXmlApplicationContext 进行分析。我们先来一个简单的例子来看看怎么实例化 ApplicationContext。

​	首先，定义一个接口：

```java
public interface MessageService {
    String getMessage();
}
```

​	定义接口实现类：

```java
public class MessageServiceImpl implements MessageService {

    public String getMessage() {
        return "hello world";
    }
}
```

接下来，我们在 **resources** 目录新建一个配置文件，文件名随意，通常叫 application.xml 或 application-xxx.xml 就可以了：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns="http://www.springframework.org/schema/beans"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd" default-autowire="byName">

    <bean id="messageService" class="com.javadoop.example.MessageServiceImpl"/>
</beans>
```

这样，我们就可以跑起来了：

```java
public class App {
    public static void main(String[] args) {
        // 用我们的配置文件来启动一个 ApplicationContext
        ApplicationContext context = new ClassPathXmlApplicationContext("classpath:application.xml");

        System.out.println("context 启动成功");

        // 从 context 中取出我们的 Bean，而不是用 new MessageServiceImpl() 这种方式
        MessageService messageService = context.getBean(MessageService.class);
        // 这句将输出: hello world
        System.out.println(messageService.getMessage());
    }
}
```

以上例子很简单，不过也够引出本文的主题了，就是怎么样通过配置文件来启动 Spring 的 ApplicationContext？也就是我们今天要分析的 IOC 的核心了。ApplicationContext 启动过程中，会负责创建实例 Bean，往各个 Bean 中注入依赖等。

### BeanFactory

##### 简介

​	BeanFactory，从名字上也很好理解，生产 bean 的工厂，它负责生产和管理各个 bean 实例。初学者可别以为我之前说那么多和 BeanFactory 无关，前面说的 ApplicationContext 其实就是一个 BeanFactory。我们来看下和 BeanFactory 接口相关的主要的继承结构：

##### BeanFactory 继承结构图

![](/Users/mrjiao/Documents/typora/java/assets/BeanFactory 继承结构图.png)

我想，大家看完这个图以后，可能就不是很开心了。ApplicationContext 往下的继承结构前面一张图说过了，这里就不重复了。这张图呢，背下来肯定是不需要的，有几个重点和大家说明下就好：

1. **ApplicationContext 继承了 ListableBeanFactory，这个 Listable 的意思就是，通过这个接口，我们可以获取多个 Bean，大家看源码会发现，最顶层 BeanFactory 接口的方法都是获取单个 Bean 的**。
2. **ApplicationContext 继承了 HierarchicalBeanFactory，Hierarchical 单词本身已经能说明问题了，也就是说我们可以在应用中起多个 BeanFactory，然后可以将各个 BeanFactory 设置为父子关系**。
3. AutowireCapableBeanFactory 这个名字中的 Autowire 大家都非常熟悉，它就是用来自动装配 Bean 用的，但是仔细看上图，ApplicationContext 并没有继承它，不过不用担心，不使用继承，不代表不可以使用组合，如果你看到 ApplicationContext 接口定义中的最后一个方法 getAutowireCapableBeanFactory() 就知道了。
4. ConfigurableListableBeanFactory 也是一个特殊的接口，看图，特殊之处在于它继承了第二层所有的三个接口，而 ApplicationContext 没有。这点之后会用到。
5. 请先不用花时间在其他的接口和类上，先理解我说的这几点就可以了。

然后，请读者打开编辑器，翻一下 BeanFactory、ListableBeanFactory、HierarchicalBeanFactory、AutowireCapableBeanFactory、ApplicationContext 这几个接口的代码，大概看一下各个接口中的方法，大家心里要有底，限于篇幅，我就不贴代码介绍了。

<hr>
### IOC代码总体分析

先看ApplicationContext总体结构图：

![](/Users/mrjiao/Documents/typora/java/assets/SpringApplicationContext结构.png)

1. 最外层main方法，传入配置类，创建ioc容器

   ```java
   public class Main {
       public static void main(String[] args) {
           ApplicationContext context = new AnnotationConfigApplicationContext(XXX.class);
       }
   }
   ```

2. 在ApplicationContext中注册配置类，调用**AbstractApplicationContext的refresh()方法**刷新容器

   ```java
   public class ClassPathXmlApplicationContext extends AbstractXmlApplicationContext {
   
     public ClassPathXmlApplicationContext(String[] configLocations, boolean refresh, ApplicationContext parent) throws BeansException {
   		//...
       if (refresh) {
         refresh(); // 核心方法
       }
     }
       ...
   }
   ```

   以下为`refresh()`内部解析，**refresh()方法定义在AbstractApplicationContext类中**：

   1. 准备工作，记录下容器的启动时间，标记"已启动状态"等

      ```java
       this.prepareRefresh();
      ```

   2. 获得Bean工厂，具体为`ConfigurableListableBeanFactory`，这步完成后，bean就会解析成一个个Bean定义，注册到BeanFactory中。当然，此时的Bean还没有初始化，只是配置信息都提取出来了。注册实际上也只是将这些信息保存到了注册中心(说到底核心是一个 beanName-> beanDefinition 的 map)。

      ```java
      ConfigurableListableBeanFactory beanFactory = this.obtainFreshBeanFactory();
      ```

      以下为`obtainFreshBeanFactory()`的内部解析

      

   3. 设置BeanFactory的类加载器，添加几个BeanPostProcessor，手动注册几个特殊的Bean。

      ```
      this.prepareBeanFactory(beanFactory);
      ```

   4. 这里需要知道`BeanFactoryPostProcessor`知识点，[具体请点击](https://www.cnblogs.com/sishang/p/6588542.html)。

      这里是提供给子类的扩展点，到这里的时候，所有的 Bean 都加载、注册完成了，但是都还没有初始化，具体的子类可以在这步的时候添加一些特殊的 BeanFactoryPostProcessor 的实现类或做点什么事。

      ```java
      this.postProcessBeanFactory(beanFactory);
      ```

   5. 调用 BeanFactoryPostProcessor 各个实现类的 postProcessBeanFactory(factory) 方法

      ```
      this.invokeBeanPostProcessors(beanFactory);
      ```

   6. 注册BeanPostProcessor的实现类，BeanPostProcessor属于容器级生命周期控制接口，其中定义了两个方法，分别是`postProcessBeforeInitialization`和`postProcessAfterInitialization`，在容器初始化以后，Spring 会负责调用里面的 postProcessBeanFactory 方法。 两个方法分别在 Bean 初始化之前和初始化之后得到执行。注意，到这里 Bean 还没初始化

      ```
      this.registerBeanPostProcessors(beanFactory);
      ```

   7. 以下几个操作不是重点，不展开说：

      ```java
      //初始化当前 ApplicationContext 的 MessageSource
      this.initMessageSource();
      
      //初始化当前 ApplicationContext 的事件广播器
      this.initApplicationEventMulticaster();
      ```

   8. 从方法名就可以知道，典型的模板方法(钩子方法)，具体的子类可以在这里初始化一些特殊的 Bean（在初始化 singleton beans 之前）

      ```
      this.onRefresh();
      ```

   9. 注册事件监听器，监听器需要实现 ApplicationListener 接口。这也不是我们的重点，过

      ```
      this.registerListeners();
      ```

   10. 重点，初始化所有的Singleton Bean，lazy-init的除外

       ```
       this.finishBeanFactoryInitialization(beanFactory);
       ```

   11. 最后，广播事件，ApplicationContext 初始化完成

       ```
       this.finishRefresh();
       ```

       

```java
1）传入配置类，创建ioc容器
2）注册配置类，调用refresh（）刷新容器；
3）registerBeanPostProcessors(beanFactory);注册bean的后置处理器来方便拦截bean的创建；
 			1）先获取ioc容器已经定义了的需要创建对象的所有BeanPostProcessor
 * 			2）给容器中加别的BeanPostProcessor
 * 			3）优先注册实现了PriorityOrdered接口的BeanPostProcessor；
 * 			4）再给容器中注册实现了Ordered接口的BeanPostProcessor；
 * 			5）注册没实现优先级接口的BeanPostProcessor；
 * 			6）注册BeanPostProcessor，实际上就是创建BeanPostProcessor对象，保存在容器中；
 * 				创建internalAutoProxyCreator的BeanPostProcessor【AnnotationAwareAspectJAutoProxyCreator】
 * 				1）、创建Bean的实例
 * 				2）、populateBean；给bean的各种属性赋值
 * 				3）、initializeBean：初始化bean；
 * 						1）、invokeAwareMethods()：处理Aware接口的方法回调
 * 						2）、applyBeanPostProcessorsBeforeInitialization()：应用后置处理器的postProcessBeforeInitialization（）
 * 						3）、invokeInitMethods()；执行自定义的初始化方法
 * 						4）、applyBeanPostProcessorsAfterInitialization()；执行后置处理器的postProcessAfterInitialization（）；
 * 				4）、BeanPostProcessor(AnnotationAwareAspectJAutoProxyCreator)创建成功；--》aspectJAdvisorsBuilder
 * 			7）、把BeanPostProcessor注册到BeanFactory中；
 * 				beanFactory.addBeanPostProcessor(postProcessor);
```

### IOC代码细节分析

##### BeanFactory启动过程分析

**理解该部分前，首先要脑海中要有ApplicationContext系列的实现继承结构图和BeanFactory的继承实现结构图。**

第一步，我们肯定要从 ClassPathXmlApplicationContext 的构造方法说起。

```java
public class ClassPathXmlApplicationContext extends AbstractXmlApplicationContext {
  private Resource[] configResources;

  // 如果已经有 ApplicationContext 并需要配置成父子关系，那么调用这个构造方法
  public ClassPathXmlApplicationContext(ApplicationContext parent) {
    super(parent);
  }
  ...
  public ClassPathXmlApplicationContext(String[] configLocations, boolean refresh, ApplicationContext parent) throws BeansException {

    super(parent);
    // 根据提供的路径，处理成配置文件数组(以分号、逗号、空格、tab、换行符分割)
    setConfigLocations(configLocations);
    if (refresh) {
      refresh(); // 核心方法
    }
  }
    ...
}
```

接下来，就是 refresh()，这里简单说下为什么是 refresh()，而不是 init() 这种名字的方法。因为 ApplicationContext 建立起来以后，其实我们是可以通过调用 refresh() 这个方法重建的，refresh() 会将原来的 ApplicationContext 销毁，然后再重新执行一次初始化操作。

往下看，refresh() 方法里面调用了那么多方法，就知道肯定不简单了，请读者先看个大概，细节之后会详细说。

```java
@Override
public void refresh() throws BeansException, IllegalStateException {
   // 来个锁，不然 refresh() 还没结束，你又来个启动或销毁容器的操作，那不就乱套了嘛
   synchronized (this.startupShutdownMonitor) {

      // 准备工作，记录下容器的启动时间、标记“已启动”状态、处理配置文件中的占位符
      prepareRefresh();

      // 这步比较关键，这步完成后，配置文件就会解析成一个个 Bean 定义，注册到 BeanFactory 中，
      // 当然，这里说的 Bean 还没有初始化，只是配置信息都提取出来了，
      // 注册也只是将这些信息都保存到了注册中心(说到底核心是一个 beanName-> beanDefinition的 map)
      ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

      // 设置 BeanFactory 的类加载器，添加几个 BeanPostProcessor，手动注册几个特殊的 bean
      // 这块待会会展开说
      prepareBeanFactory(beanFactory);

      try {
         // 【这里需要知道 BeanFactoryPostProcessor 这个知识点，Bean 如果实现了此接口，
         // 那么在容器初始化以后，Spring 会负责调用里面的 postProcessBeanFactory 方法。】

         // 这里是提供给子类的扩展点，到这里的时候，所有的Bean 都加载、注册完成，但是都还没有初始化
         // 具体的子类可以在这步的时候添加一些特殊的BeanFactoryPostProcessor 的实现类或做点什么事
         postProcessBeanFactory(beanFactory);
         // 调用 BeanFactoryPostProcessor 各个实现类的 postProcessBeanFactory(factory) 方法
         invokeBeanFactoryPostProcessors(beanFactory);

         // 注册 BeanPostProcessor 的实现类，注意看和 BeanFactoryPostProcessor 的区别
         // 此接口两个方法: postProcessBeforeInitialization 和 postProcessAfterInitialization
         // 两个方法分别在 Bean 初始化之前和初始化之后得到执行。注意，到这里 Bean 还没初始化
         registerBeanPostProcessors(beanFactory);

         // 初始化当前 ApplicationContext 的 MessageSource，国际化这里就不展开说了
         initMessageSource();

         // 初始化当前 ApplicationContext 的事件广播器，这里也不展开了
         initApplicationEventMulticaster();

         // 从方法名就可以知道，典型的模板方法(钩子方法)，
         // 具体的子类可以在这里初始化一些特殊的 Bean（在初始化 singleton beans 之前）
         onRefresh();

         // 注册事件监听器，监听器需要实现 ApplicationListener 接口。这也不是我们的重点，过
         registerListeners();

         // 重点，重点，重点
         // 初始化所有的 singleton beans
         //（lazy-init 的除外）
         finishBeanFactoryInitialization(beanFactory);

         // 最后，广播事件，ApplicationContext 初始化完成
         finishRefresh();
      }

      catch (BeansException ex) {
         if (logger.isWarnEnabled()) {
            logger.warn("Exception encountered during context initialization - " +
                  "cancelling refresh attempt: " + ex);
         }

         // Destroy already created singletons to avoid dangling resources.
         // 销毁已经初始化的 singleton 的 Beans，以免有些 bean 会一直占用资源
         destroyBeans();

         // Reset 'active' flag.
         cancelRefresh(ex);

         // 把异常往外抛
         throw ex;
      }

      finally {
         // Reset common introspection caches in Spring's core, since we
         // might not ever need metadata for singleton beans anymore...
         resetCommonCaches();
      }
   }
}
```

下面，我们开始一步步来肢解这个 refresh() 方法。

##### 创建 Bean 容器前的准备工作

这个比较简单，直接看代码中的几个注释即可。

```java
// 准备工作，记录下容器的启动时间、标记“已启动”状态、处理配置文件中的占位符
protected void prepareRefresh() {
   // 记录启动时间，
   // 将 active 属性设置为 true，closed 属性设置为 false，它们都是 AtomicBoolean 类型
   this.startupDate = System.currentTimeMillis();
   this.closed.set(false);
   this.active.set(true);

   if (logger.isInfoEnabled()) {
      logger.info("Refreshing " + this);
   }

   // Initialize any placeholder property sources in the context environment
   initPropertySources();

   // 校验 xml 配置文件
   getEnvironment().validateRequiredProperties();

   this.earlyApplicationEvents = new LinkedHashSet<ApplicationEvent>();
}
```

##### 创建Bean容器，加载注册Bean

​	我们回到 refresh() 方法中的下一行 obtainFreshBeanFactory()。注意，这个方法是全文最重要的部分之一，这里将会初始化 BeanFactory、加载 Bean、注册 Bean 等等。当然，这步结束后，Bean 并没有完成初始化。这里指的是 Bean 实例并未在这一步生成。

```java
// AbstractApplicationContext.java
// 这步比较关键，这步完成后，配置文件就会解析成一个个 Bean 定义，注册到 BeanFactory 中，
// 当然，这里说的 Bean 还没有初始化，只是配置信息都提取出来了，
// 注册也只是将这些信息都保存到了注册中心(说到底核心是一个 beanName-> beanDefinition的 map)
protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {
   // 关闭旧的 BeanFactory (如果有)，创建新的 BeanFactory，加载 Bean 定义、注册 Bean 等等
   refreshBeanFactory();

   // 返回刚刚创建的 BeanFactory
   ConfigurableListableBeanFactory beanFactory = getBeanFactory();
   if (logger.isDebugEnabled()) {
      logger.debug("Bean factory for " + getDisplayName() + ": " + beanFactory);
   }
   return beanFactory;
}
```

```java
// AbstractRefreshableApplicationContext.java 120
// 关闭旧的 BeanFactory (如果有)，创建新的 BeanFactory，加载 Bean 定义、注册 Bean 等等
@Override
protected final void refreshBeanFactory() throws BeansException {
   // 如果 ApplicationContext 中已经加载过 BeanFactory 了，销毁所有 Bean，关闭 BeanFactory
   // 注意，应用中 BeanFactory 本来就是可以多个的，这里可不是说应用全局是否有 BeanFactory，而是当前
   // ApplicationContext 是否有 BeanFactory
   if (hasBeanFactory()) {
      destroyBeans();
      closeBeanFactory();
   }
   try {
      // 初始化一个 DefaultListableBeanFactory，为什么用这个，我们马上说。
      DefaultListableBeanFactory beanFactory = createBeanFactory();
      // 用于 BeanFactory 的序列化，我想不部分人应该都用不到
      beanFactory.setSerializationId(getId());

      // 下面这两个方法很重要，别跟丢了，具体细节之后说
      // 设置 BeanFactory 的两个配置属性：是否允许 Bean 覆盖、是否允许循环引用
      customizeBeanFactory(beanFactory);

      // 加载 Bean 到 BeanFactory 中
      loadBeanDefinitions(beanFactory);
      synchronized (this.beanFactoryMonitor) {
         this.beanFactory = beanFactory;
      }
   }
   catch (IOException ex) {
      throw new ApplicationContextException("I/O error parsing bean definition source for " + getDisplayName(), ex);
   }
}
```

​	看到这里的时候，我觉得读者就应该站在高处看 ApplicationContext 了，ApplicationContext 继承自 BeanFactory，但是它不应该被理解为 BeanFactory 的实现类，而是说其内部持有一个实例化的 BeanFactory（DefaultListableBeanFactory）。以后所有的 BeanFactory 相关的操作其实是委托给这个实例来处理的。

​	我们说说为什么选择实例化 **DefaultListableBeanFactory** ？前面我们说了有个很重要的接口 ConfigurableListableBeanFactory，它实现了 BeanFactory 下面一层的所有三个接口，我把之前的继承图再拿过来大家再仔细看一下：

![](/Users/mrjiao/Documents/typora/java/assets/BeanFactory 继承结构图.png)

​	我们可以看到 ConfigurableListableBeanFactory 只有一个实现类 DefaultListableBeanFactory，而且实现类 DefaultListableBeanFactory 还通过实现右边的 AbstractAutowireCapableBeanFactory 通吃了右路。所以结论就是，最底下这个家伙 DefaultListableBeanFactory 基本上是最牛的 BeanFactory 了，这也是为什么这边会使用这个类来实例化的原因。

​	如果你想要在程序运行的时候动态往 Spring IOC 容器注册新的 bean，就会使用到这个类。那我们怎么在运行时获得这个实例呢？之前我们说过 ApplicationContext 接口能获取到 AutowireCapableBeanFactory，就是最右上角那个，然后它向下转型就能得到 DefaultListableBeanFactory 了。

​	在继续往下之前，我们需要先了解 BeanDefinition。**我们说 BeanFactory 是 Bean 容器，那么 Bean 又是什么呢？**这里的 BeanDefinition 就是我们所说的 Spring 的 Bean，我们自己定义的各个 Bean 其实会转换成一个个 BeanDefinition 存在于 Spring 的 BeanFactory 中。所以，如果有人问你 Bean 是什么的时候，你要知道 Bean 在代码层面上可以认为是 BeanDefinition 的实例。

​	**BeanDefinition 中保存了我们的 Bean 信息，比如这个 Bean 指向的是哪个类、是否是单例的、是否懒加载、这个 Bean 依赖了哪些 Bean 等等。**

##### BeanDefinition 接口定义

我们来看下 BeanDefinition 的接口定义：

```java
public interface BeanDefinition extends AttributeAccessor, BeanMetadataElement {

   // 我们可以看到，默认只提供 sington 和 prototype 两种，
   // 很多读者可能知道还有 request, session, globalSession, application, websocket 这几种，
   // 不过，它们属于基于 web 的扩展。
   String SCOPE_SINGLETON = ConfigurableBeanFactory.SCOPE_SINGLETON;
   String SCOPE_PROTOTYPE = ConfigurableBeanFactory.SCOPE_PROTOTYPE;

   // 比较不重要，直接跳过吧
   int ROLE_APPLICATION = 0;
   int ROLE_SUPPORT = 1;
   int ROLE_INFRASTRUCTURE = 2;

   // 设置父 Bean，这里涉及到 bean 继承，不是 java 继承。请参见附录的详细介绍
   // 一句话就是：继承父 Bean 的配置信息而已
   void setParentName(String parentName);

   // 获取父 Bean
   String getParentName();

   // 设置 Bean 的类名称，将来是要通过反射来生成实例的
   void setBeanClassName(String beanClassName);

   // 获取 Bean 的类名称
   String getBeanClassName();


   // 设置 bean 的 scope
   void setScope(String scope);

   String getScope();

   // 设置是否懒加载
   void setLazyInit(boolean lazyInit);

   boolean isLazyInit();

   // 设置该 Bean 依赖的所有的 Bean，注意，这里的依赖不是指属性依赖(如 @Autowire 标记的)，
   // 是 depends-on="" 属性设置的值。
   void setDependsOn(String... dependsOn);

   // 返回该 Bean 的所有依赖
   String[] getDependsOn();

   // 设置该 Bean 是否可以注入到其他 Bean 中，只对根据类型注入有效，
   // 如果根据名称注入，即使这边设置了 false，也是可以的
   void setAutowireCandidate(boolean autowireCandidate);

   // 该 Bean 是否可以注入到其他 Bean 中
   boolean isAutowireCandidate();

   // 主要的。同一接口的多个实现，如果不指定名字的话，Spring 会优先选择设置 primary 为 true 的 bean
   void setPrimary(boolean primary);

   // 是否是 primary 的
   boolean isPrimary();

   // 如果该 Bean 采用工厂方法生成，指定工厂名称。对工厂不熟悉的读者，请参加附录
   // 一句话就是：有些实例不是用反射生成的，而是用工厂模式生成的
   void setFactoryBeanName(String factoryBeanName);
   // 获取工厂名称
   String getFactoryBeanName();
   // 指定工厂类中的 工厂方法名称
   void setFactoryMethodName(String factoryMethodName);
   // 获取工厂类中的 工厂方法名称
   String getFactoryMethodName();

   // 构造器参数
   ConstructorArgumentValues getConstructorArgumentValues();

   // Bean 中的属性值，后面给 bean 注入属性值的时候会说到
   MutablePropertyValues getPropertyValues();

   // 是否 singleton
   boolean isSingleton();

   // 是否 prototype
   boolean isPrototype();

   // 如果这个 Bean 是被设置为 abstract，那么不能实例化，
   // 常用于作为 父bean 用于继承，其实也很少用......
   boolean isAbstract();

   int getRole();
   String getDescription();
   String getResourceDescription();
   BeanDefinition getOriginatingBeanDefinition();
}
```

​	这个 BeanDefinition 其实已经包含很多的信息了，暂时不清楚所有的方法对应什么东西没关系，希望看完本文后读者可以彻底搞清楚里面的所有东西。

​	这里接口虽然那么多，但是没有类似 getInstance() 这种方法来获取我们定义的类的实例，真正的我们定义的类生成的实例到哪里去了呢？别着急，这个要很后面才能讲到。

​	有了 BeanDefinition 的概念以后，我们再往下看 refreshBeanFactory() 方法中的剩余部分：

```java
customizeBeanFactory(beanFactory);
loadBeanDefinitions(beanFactory);
```

##### customizeBeanFactory

customizeBeanFactory(beanFactory) 比较简单，就是配置是否允许 BeanDefinition 覆盖、是否允许循环引用。

```java
protected void customizeBeanFactory(DefaultListableBeanFactory beanFactory) {
   if (this.allowBeanDefinitionOverriding != null) {
      // 是否允许 Bean 定义覆盖
      beanFactory.setAllowBeanDefinitionOverriding(this.allowBeanDefinitionOverriding);
   }
   if (this.allowCircularReferences != null) {
      // 是否允许 Bean 间的循环依赖
      beanFactory.setAllowCircularReferences(this.allowCircularReferences);
   }
}
```

​	BeanDefinition 的覆盖问题可能会有开发者碰到这个坑，就是在配置文件中定义 bean 时使用了相同的 id 或 name，默认情况下，allowBeanDefinitionOverriding 属性为 null，如果在同一配置文件中重复了，会抛错，但是如果不是同一配置文件中，会发生覆盖。

​	循环引用也很好理解：A 依赖 B，而 B 依赖 A。或 A 依赖 B，B 依赖 C，而 C 依赖 A。

​	默认情况下，Spring 允许循环依赖，当然如果你在 A 的构造方法中依赖 B，在 B 的构造方法中依赖 A 是不行的。至于这两个属性怎么配置？我在附录中进行了介绍，尤其对于覆盖问题，很多人都希望禁止出现 Bean 覆盖，可是 Spring 默认是不同文件的时候可以覆盖的。

​	之后的源码中还会出现这两个属性，读者有个印象就可以了。

##### 加载 Bean: loadBeanDefinitions

​	接下来是最重要的 loadBeanDefinitions(beanFactory) 方法了，这个方法将根据配置，加载各个 Bean，然后放到 BeanFactory 中。读取配置的操作在 XmlBeanDefinitionReader 中，其负责加载配置、解析。

```java
// AbstractXmlApplicationContext.java 80
// 加载 Bean 到 BeanFactory 中
/** 我们可以看到，此方法将通过一个 XmlBeanDefinitionReader 实例来加载各个 Bean。*/
@Override
protected void loadBeanDefinitions(DefaultListableBeanFactory beanFactory) throws BeansException, IOException {
   // 给这个 BeanFactory 实例化一个 XmlBeanDefinitionReader
   XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(beanFactory);

   // Configure the bean definition reader with this context's
   // resource loading environment.
   beanDefinitionReader.setEnvironment(this.getEnvironment());
   beanDefinitionReader.setResourceLoader(this);
   beanDefinitionReader.setEntityResolver(new ResourceEntityResolver(this));

   // 初始化 BeanDefinitionReader，其实这个是提供给子类覆写的，
   // 我看了一下，没有类覆写这个方法，我们姑且当做不重要吧
   initBeanDefinitionReader(beanDefinitionReader);
   // 重点来了，继续往下
   loadBeanDefinitions(beanDefinitionReader);
}
```

现在还在这个类中，接下来用刚刚初始化的 Reader 开始来加载 xml 配置，这块代码读者可以选择性跳过，不是很重要。也就是说，下面这个代码块，读者可以很轻松地略过。

```java
//AbstractXmlApplicationContext.java 120
protected void loadBeanDefinitions(XmlBeanDefinitionReader reader) throws BeansException, IOException {
   Resource[] configResources = getConfigResources();
   if (configResources != null) {
      // 往下看
      reader.loadBeanDefinitions(configResources);
   }
   String[] configLocations = getConfigLocations();
   if (configLocations != null) {
      // 2
      reader.loadBeanDefinitions(configLocations);
   }
}

// 上面虽然有两个分支，不过第二个分支很快通过解析路径转换为 Resource 以后也会进到这里
@Override
public int loadBeanDefinitions(Resource... resources) throws BeanDefinitionStoreException {
   Assert.notNull(resources, "Resource array must not be null");
   int counter = 0;
   // 注意这里是个 for 循环，也就是每个文件是一个 resource
   for (Resource resource : resources) {
      // 继续往下看
      counter += loadBeanDefinitions(resource);
   }
   // 最后返回 counter，表示总共加载了多少的 BeanDefinition
   return counter;
}

// XmlBeanDefinitionReader 303
@Override
public int loadBeanDefinitions(Resource resource) throws BeanDefinitionStoreException {
   return loadBeanDefinitions(new EncodedResource(resource));
}

// XmlBeanDefinitionReader 314
public int loadBeanDefinitions(EncodedResource encodedResource) throws BeanDefinitionStoreException {
   Assert.notNull(encodedResource, "EncodedResource must not be null");
   if (logger.isInfoEnabled()) {
      logger.info("Loading XML bean definitions from " + encodedResource.getResource());
   }
   // 用一个 ThreadLocal 来存放配置文件资源
   Set<EncodedResource> currentResources = this.resourcesCurrentlyBeingLoaded.get();
   if (currentResources == null) {
      currentResources = new HashSet<EncodedResource>(4);
      this.resourcesCurrentlyBeingLoaded.set(currentResources);
   }
   if (!currentResources.add(encodedResource)) {
      throw new BeanDefinitionStoreException(
            "Detected cyclic loading of " + encodedResource + " - check your import definitions!");
   }
   try {
      InputStream inputStream = encodedResource.getResource().getInputStream();
      try {
         InputSource inputSource = new InputSource(inputStream);
         if (encodedResource.getEncoding() != null) {
            inputSource.setEncoding(encodedResource.getEncoding());
         }
         // 核心部分是这里，往下面看
         return doLoadBeanDefinitions(inputSource, encodedResource.getResource());
      }
      finally {
         inputStream.close();
      }
   }
   catch (IOException ex) {
      throw new BeanDefinitionStoreException(
            "IOException parsing XML document from " + encodedResource.getResource(), ex);
   }
   finally {
      currentResources.remove(encodedResource);
      if (currentResources.isEmpty()) {
         this.resourcesCurrentlyBeingLoaded.remove();
      }
   }
}

// 还在这个文件中，第 388 行
protected int doLoadBeanDefinitions(InputSource inputSource, Resource resource)
      throws BeanDefinitionStoreException {
   try {
      // 这里就不看了，将 xml 文件转换为 Document 对象
      Document doc = doLoadDocument(inputSource, resource);
      // 继续
      return registerBeanDefinitions(doc, resource);
   }
   catch (...
}
// 还在这个文件中，第 505 行
// 返回值：返回从当前配置文件加载了多少数量的 Bean
public int registerBeanDefinitions(Document doc, Resource resource) throws BeanDefinitionStoreException {
   BeanDefinitionDocumentReader documentReader = createBeanDefinitionDocumentReader();
   int countBefore = getRegistry().getBeanDefinitionCount();
   // 这里
   documentReader.registerBeanDefinitions(doc, createReaderContext(resource));
   return getRegistry().getBeanDefinitionCount() - countBefore;
}
// DefaultBeanDefinitionDocumentReader 90
@Override
public void registerBeanDefinitions(Document doc, XmlReaderContext readerContext) {
   this.readerContext = readerContext;
   logger.debug("Loading bean definitions");
   Element root = doc.getDocumentElement();
   // 从 xml 根节点开始解析文件
   doRegisterBeanDefinitions(root);
} 
```

​	**经过漫长的链路，一个配置文件终于转换为一颗 DOM 树了，注意，这里指的是其中一个配置文件，不是所有的**，读者可以看到上面有个 for 循环的。下面开始从根节点开始解析：





# AOP源码解析

### 简介

​	AOP，即Aspect-Oriented Programming，面向切面编程。要实现的是在我们原来写的代码的基础上，进行一定的包装，如在方法执行前、方法返回后、方法抛出异常后等地方进行一定的拦截处理或者叫增强处理。

​	Spring AOP的特点如下：

- 它基于动态代理来实现。默认地，如果使用接口的，用 JDK 提供的动态代理实现，如果没有接口，使用 CGLIB 实现。大家一定要明白背后的意思，包括什么时候会不用 JDK 提供的动态代理，而用 CGLIB 实现。
- Spring 3.2 以后，spring-core 直接就把 CGLIB 和 ASM 的源码包括进来了，这也是为什么我们不需要显式引入这两个依赖
- Spring 的 IOC 容器和 AOP 都很重要，Spring AOP 需要依赖于 IOC 容器来管理。
- 如果你是 web 开发者，有些时候，你可能需要的是一个 Filter 或一个 Interceptor，而不一定是 AOP。
- Spring AOP 只能作用于 Spring 容器中的 Bean，它是使用纯粹的 Java 代码实现的，只能作用于 bean 的方法。

### 实例演示

1. 业务逻辑类（需要为Bean）：

   定义一个业务逻辑类（MathCalculator），在业务逻辑运行的时候将日志进行打印（方法之前、方法运行结束、方法出现异常，xxx），代码如下：

   ```java
   public class MathCalculator {
   	
   	  public int div(int i,int j){
   		    System.out.println("MathCalculator...div...");
   		    return i/j;	
   	  }
   }
   ```

2. 日志切面类：

   定义一个日志切面类（LogAspects）：切面类里面的方法需要动态感知MathCalculator.div运行到哪里然后执行：

   通知方法：

   - 前置通知(@Before)：logStart：在目标方法(div)运行之前运行

   - 后置通知(@After)：logEnd：在目标方法(div)运行结束之后运行（无论方法正常结束还是异常结束）

   - 返回通知(@AfterReturning)：logReturn：在目标方法(div)正常返回之后运行

   - 异常通知(@AfterThrowing)：logException：在目标方法(div)出现异常以后运行

   - 环绕通知(@Around)：动态代理，手动推进目标方法运行（joinPoint.procced()）

   代码如下：

   ```java
   import org.aspectj.lang.JoinPoint;
   import org.aspectj.lang.annotation.*;
   /**
    * 切面类
    * @Aspect： 告诉Spring当前类是一个切面类
    */
   @Aspect
   public class LogAspects {
   	
   	//抽取公共的切入点表达式
   	@Pointcut("execution(public int com.atguigu.aop.MathCalculator.*(..))")
   	public void pointCut(){};
   	
   	//@Before在目标方法之前切入；切入点表达式（指定在哪个方法切入）
   	@Before("pointCut()")
   	public void logStart(JoinPoint joinPoint){
   		Object[] args = joinPoint.getArgs();
   		System.out.println(""+joinPoint.getSignature().getName()+"运行。。。@Before:参数列表是：{"+Arrays.asList(args)+"}");
   	}
   	
   	@After("com.atguigu.aop.LogAspects.pointCut()")
   	public void logEnd(JoinPoint joinPoint){
   		System.out.println(""+joinPoint.getSignature().getName()+"结束。。。@After");
   	}
   	
   	//JoinPoint一定要出现在参数表的第一位
   	@AfterReturning(value="pointCut()",returning="result")
   	public void logReturn(JoinPoint joinPoint,Object result){
   		System.out.println(""+joinPoint.getSignature().getName()+"正常返回。。。@AfterReturning:运行结果：{"+result+"}");
   	}
   	
   	@AfterThrowing(value="pointCut()",throwing="exception")
   	public void logException(JoinPoint joinPoint,Exception exception){
   		System.out.println(""+joinPoint.getSignature().getName()+"异常。。异常信息：{"+exception+"}");
   	}
   }
   ```

   以上代码是将切入点提到一个公共切入点表达式中，不需要有实现，即：

   ```java
   //抽取公共的切入点表达式
   	@Pointcut("execution(public int com.atguigu.aop.MathCalculator.*(..))")
   	public void pointCut(){};
   
   //@Before在目标方法之前切入；切入点表达式（指定在哪个方法切入）
   	@Before("pointCut()")
   
   //...
   ```

   实际上，可以为每个切入点，指定一个单独的切入点表达式，如：

   ```java
   //@Before在目标方法之前切入；切入点表达式（指定在哪个方法切入）
   	@Before("public int com.atguigu.aop.MathCalculator.div(int, int)")
   ```

   如果想切入`MathCalculator`中的所有方法，但参数必须为(int, int)，则使用如下规则：

   ```java
   //@Before在目标方法之前切入；切入点表达式（指定在哪个方法切入）
   	@Before("public int com.atguigu.aop.MathCalculator.*(int, int)")
   ```

   如果想切入`MathCalculator`中的所有方法，不限制参数类型和数量，使用如下规则：

   ```java
   //@Before在目标方法之前切入；切入点表达式（指定在哪个方法切入）
   	@Before("public int com.atguigu.aop.MathCalculator.*(..)")
   ```

   此外，还有一些额外注意点：

   - 在切面中如何获得方法名信息和参数信息？通过`JoinPoint`实现；

   - 在切面中如何获得返回值？通过指定`@AfterReturning(value="pointCut()", returning="result")`实现；

   - 在切面中如何捕获异常信息？通过指定`@AfterThrowing(value="pointCut()", throwing="exception")`实现；

     以上的具体实现，见上面代码。

3. 配置类：

   在配置类中开启基于注解的aop模式：`@EnableAspectJAutoProxy`，并且将业务逻辑组件和切面类都加入到容器中，告诉Spring哪个是切面类（@Aspect），哪个是业务逻辑组件（@Bean）。

   代码如下：

   ```java
   @EnableAspectJAutoProxy
   @Configuration
   public class MainConfigOfAOP {
   	 
   	//业务逻辑类加入容器中
   	@Bean
   	public MathCalculator calculator(){
   		return new MathCalculator();
   	}
   
   	//切面类加入到容器中
   	@Bean
   	public LogAspects logAspects(){
   		return new LogAspects();
   	}
   }
   ```

4. 启动测试：

   启动三步流程：

   - 业务逻辑组件和切面类都加入到容器中，告诉Spring哪个是切面类（@Aspect），哪个是业务逻辑组件（@Bean）；

   - 在切面类上的每一个通知方法上标注通知注解，告诉Spring何时何地运行（切入点表达式）；

   - 开启基于注解的aop模式：@EnableAspectJAutoProxy。

   代码如下：

   ```java
   public class Main {
       public static void main(String[] args) {
           ApplicationContext context = new AnnotationConfigApplicationContext(MainConfigOfAOP.class);
           MathCalculator calculator = context.getBean(MathCalculator.class);
         
           calculator.div(2, 1);
           calculator.div(2, 0);
       }
   }
   ```

   输出如下：

   ```java
   div运行。。。@Before:参数列表是：{[2, 1]}
   MathCalculator...div...
   div结束。。。@After
   div正常返回。。。@AfterReturning:运行结果：{2}
   
   -------手动分割线--error信息已经省去--------
   
   div运行。。。@Before:参数列表是：{[2, 0]}
   MathCalculator...div...
   div结束。。。@After
   div异常。。。异常信息：{java.lang.ArithmeticException: / by zero}
   ```

5. 整体流程总结：

   - 导入aop模块；Spring AOP：(spring-aspects)

   - 定义一个业务逻辑类（MathCalculator）；在业务逻辑运行的时候将日志进行打印（方法之前、方法运行结束、方法出现异常，xxx）

   - 定义一个日志切面类（LogAspects）：如本例子中，切面类里面的方法需要动态感知MathCalculator.div运行到哪里然后执行；

   - 给切面类的目标方法标注何时何地运行（通知注解）；

   - 将切面类和业务逻辑类（目标方法所在类）都加入到容器中;

   - 必须告诉Spring哪个类是切面类(给切面类上加一个注解：@Aspect)

   - 给配置类中加 @EnableAspectJAutoProxy 【开启基于注解的aop模式】

   



