# Java位运算

### 原码

### 反码

### 补码

### 位运算符





# 面向对象

### 封装

- 概念

  ​	将类的某些信息隐藏在类内部，不允许外部程序直接访问，而是通过该类提供的方法来实现对隐藏信息的操作和访问。

- 优点

  - 只能通过规定的方法访问数据。
  - 隐藏类的实例细节，方便修改和实现。

- 额外知识点

  1. 访问修饰符

     ![](../assets/访问修饰符.png)

     此处默认情况下，在包外的子类也可以访问父类的该属性。

### 继承

- 概念

  ​	继承是类与类的一种关系，是一种“is a”的关系。比如“狗”继承“动物”，这里动物类是狗类的父类或者基类，狗类是动物类的子类或者派生类。

  ​	注：java中的继承是**单继承**，即**一个类只有一个父类。**

- 优点

- 额外知识点

  1. 方法的重写Override

     子类如果对继承的父类的方法不满意（不适合），可以自己编写继承的方法，这种方式就称为方法的重写。当调用方法时会优先调用子类的方法。

  2. 方法的重载Overload

     在同一个类中处理不同数据的多个相同方法名的多态手段。

  3. 继承的初始化顺序

     - 初始化父类再初始化子类
     - 先执行初始化对象中属性，再执行构造方法中的初始化

     基于上面两点，我们就知道实例化一个子类，java程序的执行顺序是：

     　**父类对象属性初始化---->父类对象构造方法---->子类对象属性初始化--->子类对象构造方法**

  4. super关键字

     在对象的内部使用，可以代表父类对象。

     - 访问父类的属性：super.age

     - 访问父类的方法：super.eat()

     　super的应用：

     　首先我们知道子类的构造的过程当中必须调用父类的构造方法。其实这个过程已经隐式地使用了我们的super关键字。

     　这是因为如果子类的构造方法中没有显示调用父类的构造方法，则系统默认调用父类无参的构造方法。

     　那么如果自己用super关键字在子类里调用父类的构造方法，则必须在子类的构造方法中的**第一行**。

     　**要注意的是：如果子类构造方法中既没有显示调用父类的构造方法，而父类没有无参的构造方法，则编译出错。**

     （补充说明，虽然没有显示声明父类的无参的构造方法，系统会自动默认生成一个无参构造方法，但是，如果你声明了一个有参的构造方法，而没有声明无参的构造方法，这时系统不会动默认生成一个无参构造方法，此时称为父类有没有无参的构造方法。）

### 多态

##### 概念

​	多态是面向对象编程语言的重要特性，它允许基类的指针或引用指向派生类的对象，而在具体访问时实现方法的动态绑定。

​	java里的多态主要表现在两个方面：

1. 引用多态

   父类的引用可以指向本类的对象；

   父类的引用可以指向子类的对象；

2. 方法多态

   ​	根据上述创建的两个对象：本类对象和子类对象，同样都是父类的引用，当我们指向不同的对象时，它们调用的方法也是多态的。

   ​	创建本类对象时，调用的方法为本类方法；

   ​	创建子类对象时，调用的方法为子类重写的方法或者继承的方法；

   ​	使用多态的时候要注意：**如果我们在子类中编写一个独有的方法（没有继承父类的方法），此时就不能通过父类的引用创建的子类对象来调用该方法！！！**

   ​	**注意： 继承是多态的基础。**

##### 作用

- 应用程序不必为每一个派生类编写功能调用，只需要对抽象基类进行处理即可。大大提高程序的可复用性(继承保证)
- 派生类的功能可以被基类的方法或引用变量所调用，这叫向后兼容，可以提高可扩充性和可维护性。 

##### 引用类型转换

- 向上类型转换(隐式/自动类型转换)，是小类型转换到大类型。

  ​	就以上述的父类Animal和一个子类Dog来说明，当父类的引用可以指向子类的对象时，就是向上类型转换。如：

  ```java
  Dog dog = new Dog();
  Animal animal = dog;	//自动类型提升，向上类型转换
  ```

- 向下类型转换（强制类型转换），是大类型转换到小类型，有风险，可能会出现数据溢出。

  ​	将上述代码再加上一行，我们再次将父类转换为子类引用，那么会出现错误，编译器不允许我们直接这么做**，**虽然我们知道这个父类引用指向的就是子类对象，但是编译器认为这种转换是存在风险的**。**如：

  ```java
  Dog dog = new Dog();
  Animal animal = dog;	//自动类型提升，向上类型转换
  
  //Dog dog2 = animal;		//编译器不通过
  Dog dog2 = (Dog)dog2;	//强制类型转换
  ```

  ​	**但是如果父类引用没有指向该子类的对象，则不能向下类型转换**，虽然编译器不会报错，但是运行的时候程序会出错，如：

  ```java
  Animal animal = new Animal();
  Dog dog = (Dog)animal;			//编译器不会报错，但运行时会报错
  ```

  ​	其实这就是上面所说的子类的引用指向父类的对象，而强制转换类型也不能转换！！

  ​	还有一种情况是父类的引用指向**其他子类的对象**，则不能通过强制转为**该子类的对象**。如：

  ```java
  Dog dog = new Dog();
  Animal animal = dog;	//自动类型提升，向上类型转换
  
  Cat cat = (Cat)animal();
  ```

  ​	这是因为我们在编译的时候进行了强制类型转换，编译时的类型是我们强制转换的类型，所以编译器不会报错，而当我们运行的时候，程序给animal开辟的是Dog类型的内存空间，这与Cat类型内存空间不匹配，所以无法正常转换。这两种情况出错的本质是一样的，所以我们在使用强制类型转换的时候要特别注意这两种错误！下面有个更安全的方式来实现向下类型转换。

- Instanceof运算符**，**来解决引用对象的类型，避免类型转换的安全性问题。

  ​	instanceof是Java的一个二元操作符，和==，>，<是同一类东东。由于它是由字母组成的，所以也是Java的保留关键字。**它的作用是测试它左边的对象是否是它右边的类的实例**，返回boolean类型的数据。

  　　我们来使用instanceof运算符来规避上面的错误，代码修改如下：

  ```java
  Dog dog = new Dog();
  Animal animal = dog;	//自动类型提升，向上类型转换
  
  if(animal instanceof Cat)
  	Cat cat = (Cat)animal();
  ```

  ​	利用if语句和instanceof运算符来判断两个对象的类型是否一致。

  　　**补充说明：**在比较一个对象是否和另一个对象属于同一个类实例的时候，我们通常可以采用instanceof和getClass两种方法通过两者是否相等来判断，但是两者在判断上面是有差别的。Instanceof进行类型检查规则是:**你属于该类吗？或者你属于该类的派生类吗？**而通过getClass获得类型信息采用==来进行检查是否相等的操作是**严格的判断**,**不会存在继承方面的考虑**；

##### JVM中多态的引用调用过程

​	实例如下：

```java
public class A{
    public String name = "父类name";
    public void move(){
      System.out.println("父类move");
    }
}

public class B extends A{
    public String name = "子类name";
    @Override
    public void move() {
        // TODO Auto-generated method stub
        System.out.println("子类move");
    }  
}

public class Test {
    public static void main(String[] args) {
        A a = new B();
        a.move();
    }
}
```

​	**类B是类A的子类， A a = new B() 编译时变量和运行时变量不一样，所以多态发生了。**

- A a 作为一个引用类型数据，存储在JVM栈的**本地变量表**中。 
- new B()作为**实例对象数据**存储在堆中，Ｂ的对象实例数据（接口、方法、field、对象类型等）的地址也存储在堆中，Ｂ的对象的类型数据（对象实例数据的地址所执行的数据）存储在方法区中。
- java虚拟机规范中并未对引用类型访问具体对象的方式做规定，目前主流的实现方式主要有两种：

1. 通过句柄访问：

   在这种方式中，JVM堆中会专门有一块区域用来作为句柄池，存储相关句柄所执行的实例数据地址（包括在堆中地址和在方法区中的地址）。这种实现方法由于用句柄表示地址，因此十分**稳定**。

   结构图如下：

   ![](/Users/mrjiao/Documents/typora/java/assets/通过句柄访问具体对象.png)

2. 通过直接指针访问

   通过直接指针访问的方式中，reference中存储的就是对象在堆中的实际地址，在堆中存储的对象信息中包含了在方法区中的相应类型数据。**这种方法最大的优势是速度快，在HotSpot虚拟机中用的就是这种方式**。

   ![](/Users/mrjiao/Documents/typora/java/assets/通过直接指针访问对象.png)

# [自动装箱与拆箱](http://www.cnblogs.com/liuling/archive/2013/05/05/intAndInteger.html)

### 简介

​	Java为每种基本数据类型都提供了对应的包装器类型，至于为什么会为每种基本数据类型提供包装器类型在此不进行阐述，有兴趣的朋友可以查阅相关资料。在Java SE5之前，如果要生成一个数值为10的Integer对象，必须这样进行：

```java
Integer i = new Integer(10);
```

​	而在从Java SE5开始就提供了自动装箱的特性，如果要生成一个数值为10的Integer对象，只需要这样就可以了：

```java
Integer i = 10;
```

​	这个过程中会自动根据数值创建对应的 Integer对象，这就是装箱，即将基本数据类型自动转换为对应的对象。

　　那什么是拆箱呢？顾名思义，跟装箱对应，就是自动将包装器类型转换为基本数据类型：

```java
Integer i = 10;  //装箱
int n = i;   //拆箱
```

​	简单一点说，装箱就是  自动将基本数据类型转换为包装器类型；拆箱就是  自动将包装器类型转换为基本数据类型。

　　下表是基本数据类型对应的包装器类型：

| int（4字节）    | Integer   |
| --------------- | --------- |
| byte（1字节）   | Byte      |
| short（2字节）  | Short     |
| long（8字节）   | Long      |
| float（4字节）  | Float     |
| double（8字节） | Double    |
| char（2字节）   | Character |
| boolean（未定） | Boolean   |

<hr>

### 自动装箱拆箱的实现

​	上一小节了解装箱的基本概念之后，这一小节来了解一下装箱和拆箱是如何实现的。

　　我们就以Interger类为例，下面看一段代码：

```java
public class Main {
    public static void main(String[] args) {
         
        Integer i = 10;
        int n = i;
    }
}
```

​	反编译class文件之后得到如下内容：

![](/Users/mrjiao/Documents/typora/java/assets/自动装箱与拆箱.png)

​	从反编译得到的字节码内容可以看出，在装箱的时候自动调用的是Integer的valueOf(int)方法。而在拆箱的时候自动调用的是Integer的intValue方法，即Integer i = Integer.valueOf(10);

　　其他的也类似，比如Double、Character，不相信的朋友可以自己手动尝试一下。

　　因此可以用一句话总结装箱和拆箱的实现过程：

　　装箱过程是通过调用包装器的valueOf方法实现的，而拆箱过程是通过调用包装器的 xxxValue方法实现的。（xxx代表对应的基本数据类型）。

### 额外注意点

##### Integer自动装箱

观察如下代码，输出是什么？

```java
public class Main {
    public static void main(String[] args) {
         
        Integer i1 = 100;
        Integer i2 = 100;
        Integer i3 = 200;
        Integer i4 = 200;
         
        System.out.println(i1==i2);
        System.out.println(i3==i4);
    }
}
```

```java
//输出
true
false
```

​	输出结果表明 i1 和 i2 指向的是同一个对象，而 i3 和 i4 指向的是不同的对象。此时只需一看源码便知究竟，下面这段代码是Integer的valueOf方法的具体实现：

```java
public static Integer valueOf(int i) {
        if(i >= -128 && i <= IntegerCache.high)
            return IntegerCache.cache[i + 128];
        else
            return new Integer(i);
    }
```

​	而其中IntegerCache类的实现为：

```java
private static class IntegerCache {
        static final int high;
        static final Integer cache[];

        static {
            final int low = -128;

            // high value may be configured by property
            int h = 127;
            if (integerCacheHighPropValue != null) {
                // Use Long.decode here to avoid invoking methods that
                // require Integer's autoboxing cache to be initialized
                int i = Long.decode(integerCacheHighPropValue).intValue();
                i = Math.max(i, 127);
                // Maximum array size is Integer.MAX_VALUE
                h = Math.min(i, Integer.MAX_VALUE - -low);
            }
            high = h;

            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);
        }

        private IntegerCache() {}
    }
```

​	从这2段代码可以看出，在通过valueOf方法创建Integer对象的时候，如果数值在[-128,127]之间，便返回指向IntegerCache.cache中已经存在的对象的引用；否则创建一个新的Integer对象。上面的代码中i1和i2的数值为100，因此会直接从cache中取已经存在的对象，所以i1和i2指向的是同一个对象，而i3和i4则是分别指向不同的对象。

​	额外补充：Integer i = new Integer(xxx)和Integer i =xxx;这两种方式的区别。当然，这个题目属于比较宽泛类型的。但是要点一定要答上，我总结一下主要有以下这两点区别：

　　1）第一种方式不会触发自动装箱的过程；而第二种方式会触发；

　　2）在执行效率和资源占用上的区别。第二种方式的执行效率和资源占用在一般性情况下要优于第一种情况（注意这并不是绝对的）。

##### Double/Float中自动装箱

观察如下代码，输出是什么？

```java
public class Main {
    public static void main(String[] args) {
         
        Double i1 = 100.0;
        Double i2 = 100.0;
        Double i3 = 200.0;
        Double i4 = 200.0;
         
        System.out.println(i1==i2);
        System.out.println(i3==i4);
    }
}
```

```java
//输出
false
false
```

查看Double的valueOf代码：

```java
public static Double valueOf(double d) {
    return new Double(d);
}
```

​	在这里只解释一下为什么Double类的valueOf方法会采用与Integer类的valueOf方法不同的实现。很简单：在某个范围内的整型数值的个数是有限的，而浮点数却不是。

##### Boolean 的自动装箱

观察代码：

```java
public class Main {
    public static void main(String[] args) {
         
        Boolean i1 = false;
        Boolean i2 = false;
        Boolean i3 = true;
        Boolean i4 = true;
         
        System.out.println(i1==i2);
        System.out.println(i3==i4);
    }
}
```

```java
//输出结果是：
true
true
```

至于为什么是这个结果，同样地，看了Boolean类的源码也会一目了然。下面是Boolean的valueOf方法的具体实现：

```java
public static Boolean valueOf(boolean b) {
        return (b ? TRUE : FALSE);
    }
```

而其中的 TRUE 和FALSE又是什么呢？在Boolean中定义了2个静态成员属性：

```java
 public static final Boolean TRUE = new Boolean(true);

    /** 
     * The <code>Boolean</code> object corresponding to the primitive 
     * value <code>false</code>. 
     */
    public static final Boolean FALSE = new Boolean(false);
```

##### 知识点补充

1. **当 "=="运算符的两个操作数都是 包装器类型的引用，则是比较指向的是否是同一个对象，而如果其中有一个操作数是表达式（即包含算术运算）或基本类型，则比较的是数值（即会触发自动拆箱的过程）**

2. **Integer、Short、Byte、Character、Long这几个类的valueOf方法的实现是类似的。Double、Float的valueOf方法的实现是类似的。**

3. **无论如何，Integer与new Integer不会相等。不会经历拆箱过程，new Integer的引用指向堆，而通过IntegerCache获得的Integer引用指向专门存放他的内存（常量池），他们的内存地址不一样，所以为false**

4. **对于包装器类型，equals方法并不会进行类型转换，并且比较类型为八大类型时，会直接比较值（equals已经被重写）**，见Integer类下equals()的重写：

   ```java
   public boolean equals(Object obj) {
       if (obj instanceof Integer) {
           return value == ((Integer)obj).intValue();
       }
       return false;
   }
   ```

##### [测试](https://www.cnblogs.com/dolphin0520/p/3780005.html)

下面代码的输出是什么？

```java
public class Main {
    public static void main(String[] args) {
        
        int i1 = 1;
        Integer i2 = new Integer(1);		//i2引用指向堆
        Integer a = 1;						//a 引用指向常量池
        Integer b = 2;
        Integer c = 3;
        Integer d = 3;
        Integer e = 321;
        Integer f = 321;
        Long g = 3L;
        Long h = 2L;
        
        System.out.println(i1==a);			//true，自动拆箱
        System.out.println(i2==a);			//false，引用位置不一致，分别为堆和常量池
        System.out.println(c==d);			//true，引用位置一致，都在常量池
        System.out.println(e==f);			//false，引用位置不一致，在堆里的两个对象
        System.out.println(c==(a+b));		//true，有表达式，自动触发拆箱
        System.out.println(c.equals(a+b));	//true，有表达式，自动触发拆箱，再通过Integer中重写											//的equals()进行比较，两者类型一样，直接比较两者值
        System.out.println(g==(a+b));		//true，表达式触发拆箱，直接比较基本类型int和long的											//值是否相等
        System.out.println(g.equals(a+b));	//false，会调用Long中重写的equals()，在判断类型时直											   //接return false
        System.out.println(g.equals(a+h));	//true，自动转型
    }
}
```

# 关键字

### Final 关键字

使用final关键字做标识有“最终的”含义。

- final 修饰类，则该类不允许被继承。

- final 修饰方法，则该方法不允许被覆盖(重写)。这样做的原因是方法已经实现好，不希望被子类重写覆盖。

- final 修饰属性，则该类的该属性不会进行隐式的初始化，所以 该final 属性的**初始化属性必须有值**，或在构造方法中赋值(但只能选其一，且必须选其一，因为没有默认值！)，且初始化之后就不能改了，只能赋值一次。

- final 修饰变量，则该变量的值只能赋一次值，在声明变量的时候才能赋值，即变为常量。

- final修饰的引用变量，引用不可更改，但引用的对象中的内容可以更改

  ```java
  final Student stu = new Student();
  stu = new Student();	//error，引用不可更改，不可指向新的对象
  stu.name = "xiaoming";	//correct，引用的对象中的具体数据可以更改
  ```

测试

```java
String a = "xiaoming2";			//位于方法区、常量池中
final String b = "xiaoming";	//位于方法区、常量池中
String c = "xiaoming"			//位于方法区、常量池中

String d = b+2;					//位于方法区、常量池中
String e = c+2;					//位于堆中

sout(a==d);		//true
sout(a==e);		//false
```

变量 d 是 b+2 得到的，由于b是一个常量，所以在使用b的时候相当于使用b的原始值来进行计算，生成的d也是一个常量。

c 是指向常量池中的xiaoming，由于c不是final修饰，也就是说在使用d的时候不会提前知道c的值是什么，所以在计算e的时候就不一样了，e是由于使用 c 的引用计算，最后会在堆上生成一个 "xiaoming2"，故不相等。

# 内部类

内部类（ Inner Class ）就是定义在另外一个类里面的类。内部类的主要作用如下：

- 内部类提供了**更好的封装**，可以把内部类隐藏在外部类之内，**不允许**同一个包中的其他类访问该类。
- 内部类的方法可以**直接访问外部类的所有数据**，包括**私有的数据**。
- 内部类所实现的功能使用外部类同样可以实现，只是有时使用内部类更方便。

[内部类](http://www.cnblogs.com/hysum/p/7101974.html)可分为如下几种：

- 成员内部类
- 静态内部类
- 方法内部类
- 匿名内部类

具体介绍如下：

### 成员内部类

​	内部类中最常见的就是成员内部类，也称为普通内部类。我们来看如下代码：

### 静态内部类

​	静态类只有在使用的时候才会被加载。

### 方法内部类



### 匿名内部类

# [实例化对象过程](https://www.cnblogs.com/study-everyday/p/6752715.html#autoid-2-0-0)

​	实例化一个对象主要由类的加载过程和对象的创建过程两部分组成。

### 类的加载过程

​	首先，Jvm在执行时，遇到一个新的类时，会到内存中的方法区去找class的信息，如果找到就直接拿来用，如果没有找到，就会去将类文件加载到方法区。在类加载时，静态成员变量加载到方法区的静态区域，非静态成员变量加载到方法区的非静态区域。

​	静态代码块是在类加载时自动执行的代码，非静态代码块是在创建对象时自动执行的代码，不创建对象不执行该类的非静态代码块。

其具体过程如下：

1. JVM会先去方法区中找有没有相应类的.class存在。如果有，就直接使用；如果没有，则把相关类的.clss加载到方法区。

2. 在.class加载到方法区时，先加载父类再加载子类；先加载静态内容，再加载非静态内容（如果父类和子类都有静态内容和非静态内容，架子啊顺序为：加载父类静态内容 -> 子类静态内容 -> 父类非静态内容 -> 子类非静态内容）

3. 加载静态内容：

- 把.class中的所有静态内容加载到方法区下的静态区域内
- 静态内容加载完成之后，对所有的静态变量进行默认初始化
- 所有的静态变量默认初始化完成之后，再进行显式初始化
- 当静态区域下的所有静态变量显式初始化完后，执行静态代码块

4. 加载非静态内容：把.class中的所有非静态变量及非静态代码块加载到方法区下的非静态区域内。至此，整个类的加载过程完毕。

​	额外注意：对于静态方法和非静态方法都是被动调用，即系统不会自动调用执行，所以用户没有调用时都不执行，主要区别在于静态方法可以直接用类名直接调用（实例化对象也可以），而非静态方法只能先实例化对象后才能调用。

### 对象的创建过程

1. 在堆中为新生对象分配内存；

2. 调用构造函数初始化对象，构造函数分为隐式三步和显式一步：
   1. 执行super()语句（隐式三步）
   2. 对开辟空间下的所有非静态成员变量进行显示初始化（隐式三步）
   3. 执行构造代码块（隐式三步）
   4. 执行构造函数（显式一步）

3. 初始化完毕后，将空间分配的地址赋给引用对象。

注：  执行构造函数可能出现以下三种情况:

1. 构造方法体的第一行是this()语句，则不会执行隐式三步，而是调用this()语句所对应的的构造方法，最终肯定会有第一行不是this语句的构造方法。

   ```java
   public class Student {
       private String name;
       private String age;
       Student() {}
       Student(String name) {
           // 会执行隐式三步
           this.name = name;
       }
       Student(String name, String age) {
           // 不会执行隐式三步
           this(name);
           this.age = age;
       }
   ```

   

2. 构造方法体的第一行是super()语句，则调用相应的父类的构造方法， 

3. **构造方法体的第一行既不是this()语句也不是super()语句，则隐式调用super()，即其父类的默认构造方法，这也是为什么一个父类通常要提供默认构造方法的原因。**

### 案例

```java
 1 // 父类
 2 
 3 public class Parent {
 4     int a = 10;
 5     static int b = 11;
 6     // 静态代码块
 7     static {
 8         System.out.println("Parent静态代码块：b=" + b);
 9         b++;
10     }
11     // 代码块
12     {
13         System.out.println("Parent代码块： a=" + a);
14         System.out.println("Parent代码块： b=" + b);
15         b++;
16         a++;
17     }
18 
19     // 无参构造函数
20     Parent() {
21         System.out.println("Parent无参构造函数： a=" + a);
22         System.out.println("Parent无参构造函数： b=" + b);
23     }
24 
25     // 有参构造函数
26     Parent(int a) {
27         System.out.println("Parent有参构造函数： a=" + a);
28         System.out.println("Parent有参构造函数： b=" + b);
29     }
30 
31     // 方法
32     void function() {
33         System.out.println("Parent function run ……");
34     }
35 
36 }
```

```java
 1 // 子类
 2 
 3 public class Child extends Parent {
 4     int x = 10;
 5     static int y = 11;
 6     // 静态代码块
 7     static {
 8         System.out.println("Child静态代码块：y=" + y);
 9         y++;
10     }
11     // 代码块
12     {
13         System.out.println("Child代码块： x=" + x);
14         System.out.println("Child代码块： y=" + y);
15         y++;
16         x++;
17     }
18 
19     // 构造函数
20     Child() {
21         System.out.println("Child构造函数： x=" + x);
22         System.out.println("Child构造函数： y=" + y);
23     }
24 
25     // 方法
26     void function() {
27         System.out.println("Child function run ……");
28     }
29 
30 }
```

```java
 1 // 测试
 2 
 3 public class Test {
 4     public static void main(String[] args) {
 5         Child demo = new Child();
 6         demo.function();
 7         System.out.println("…………………………………………………………………………………………………………………………");
 8         Child child = new Child();
 9         child.function();
10     }
11 }
```

```java
// 输出
Parent静态代码块：b=11
Child静态代码块：y=11
Parent代码块： a=10
Parent代码块： b=12
Parent无参构造函数： a=11
Parent无参构造函数： b=13
Child代码块： x=10
Child代码块： y=12
Child构造函数： x=11
Child构造函数： y=13
Child function run ……
…………………………………………………………………………………………………………………………
Parent代码块： a=10
Parent代码块： b=13
Parent无参构造函数： a=11
Parent无参构造函数： b=14
Child代码块： x=10
Child代码块： y=13
Child构造函数： x=11
Child构造函数： y=14
Child function run ……　
```

# 抽象类与接口

### 抽象类

- 概念

  - 若类前使用abstract关键字修饰，则该类为抽象类。

  - 抽象类是约束子类必须有什么方法，而并不关注子类如何实现这些方法。

  - 子类必须实现抽象类中的抽象方法。

  - 抽象类不能直接创建，可以定义引用变量来指向子类对象。

- 应用场景

  - 在某些情况下，某个父类只是知道其子类应该包含怎样的方法，但无法准确知道这些子类如何实现这些方法(可实现动态多态)。
  - 从多个具有相同特征的类中抽象出一个抽象类，以这个抽象类作为子类的模板，从而避免子类设计的随意性。

- 抽象类中的方法

  - 抽象类定义抽象方法，只有声明，不需要实现。抽象方法没有方法体以分号结束，抽象方法必须用abstract关键字来修饰。

  - 包含抽象方法的类是抽象类。抽象类中可以包含普通的方法，也可以没有抽象方法。如下所示：

    ```java
    public abstract class Phone(){
        public abstract void call();	//抽象方法，只有声明，没有实现
        
        public void message(){			//抽象类中的普通方法
            sout("我是普通方法");
        }
    }
    ```

  - 子类必须实现抽象类中的抽象方法。

### 接口

- 概念

  ​	接口可以理解为一种特殊的类，由**全局常量**和公共的**抽象方法**所组成。也可理解为一个特殊的抽象类，因为它含有抽象方法。

  ​	如果说类是一种具体实现体，而接口定义了某一批类所需要遵守的规范，接口不关心这些类的内部数据，也不关心这些类里方法的实现细节，它只规定这些类里必须提供的某些方法。

  ​	接口是抽象方法和全局常量的集合。如果一个类实现了某个接口，那么它就继承了这个接口的抽象方法。这就像契约模式，如果实现了这个接口，那么就必须确保使用这些方法。接口只是一种形式，接口自身不能做任何事情。

- 接口中的全局常量与抽象方法

  ​	在声明接口中的方法和成员变量时，我们不需要去指定修饰符，所有的方法都被默认加上了“**public abstract**” 修饰符，所有的成员变量都被加上了“**public static final**”修饰符，因此成员变量在声明时必须初始化。

### 抽象类与接口的区别

- 区别对比

| **参数**           | **抽象类**                                                   | **接口**                                                     |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 默认的方法实现     | 它可以有默认的方法实现                                       | 接口完全是抽象的。它根本不存在方法的实现                     |
| 实现               | 子类使用**extends**关键字来继承抽象类。如果子类不是抽象类的话，它需要提供抽象类中所有声明的方法的实现。 | 子类使用关键字**implements**来实现接口。它需要提供接口中所有声明的方法的实现 |
| 构造器             | 抽象类可以有构造器                                           | 接口不能有构造器                                             |
| 与正常Java类的区别 | 除了你不能实例化抽象类之外，它和普通Java类没有任何区别       | 接口是完全不同的类型                                         |
| 访问修饰符         | 抽象方法可以有**public**、**protected**和**default**这些修饰符 | 接口方法默认修饰符是**public**。你不可以使用其它修饰符。     |
| main方法           | 抽象方法可以有main方法并且我们可以运行它                     | 接口没有main方法，因此我们不能运行它。                       |
| 多继承             | 抽象方法可以继承一个类和实现多个接口                         | 接口只可以继承一个或多个其它接口                             |
| 速度               | 它比接口速度要快                                             | 接口是稍微有点慢的，因为它需要时间去寻找在类中实现的方法。   |
| 添加新方法         | 如果你往抽象类中添加新的方法，你可以给它提供默认的实现。因此你不需要改变你现在的代码。 | 如果你往接口中添加方法，那么你必须改变实现该接口的类。       |

- 什么时候使用接口、抽象类
  - 如果你拥有一些方法并且想让它们中的一些有默认实现，那么使用抽象类吧。
  - 如果你想实现多重继承，那么你必须使用接口。由于**Java不支持多继承**，子类不能够继承多个类，但可以实现多个接口。因此你就可以使用接口来解决它。
  - 如果基本功能在不断改变，那么就需要使用抽象类。如果不断改变基本功能并且使用接口，那么就需要改变所有实现了该接口的类。



# Object源码解析

Object类下的八个方法：

### Object.toString()

### Object.getClass()

### Object.clone()

### Object.hashCode()

​	参照：容器 -> 补充 -> hashcode和equals 部分

### Object.equals()

​	参照：容器 -> 补充 -> hashcode和equals 部分

### Object.wait()

​	见：并发 -> 线程常用方法补充 -> sleep()和wait() 部分。

### Object.notify()

​	见：并发 -> 线程常用方法补充 -> notify()和notifyAll() 部分。

### Object.notifyAll()

​	见：并发 -> 线程常用方法补充 -> notify()和notifyAll() 部分。

# String/StringBuilder/StringBuffer

​	三者都是用来对字符串进行操作，这三个类之间的区别主要是在两个方面，即运行速度和线程安全这两方面。

### 运行速度

​	首先说运行速度，或者说是执行速度，**在这方面运行速度快慢为：StringBuilder > StringBuffer > String**。String最慢的原因：**String为字符串常量，而StringBuilder和StringBuffer均为字符串变量，即String对象一旦创建之后该对象是不可更改的，但后两者的对象是变量，是可以更改的。**以下面一段代码为例：

```java
String str="abc";
System.out.println(str);
str=str+"de";
System.out.println(str);
```

​	如果运行这段代码会发现先输出“abc”，然后又输出“abcde”，好像是str这个对象被更改了，其实，这只是一种假象罢了，JVM对于这几行代码是这样处理的，首先创建一个String对象str，并把“abc”赋值给str，然后在第三行中，其实JVM又创建了一个新的对象也名为str，然后再把原来的str的值和“de”加起来再赋值给新的str，而原来的str就会被JVM的垃圾回收机制（GC）给回收掉了，所以，str实际上并没有被更改，也就是前面说的String对象一旦创建之后就不可更改了。所以，Java中对String对象进行的操作实际上是一个不断创建新的对象并且将旧的对象回收的一个过程，所以执行速度很慢。

​	而StringBuilder和StringBuffer的对象是变量，对变量进行操作就是直接对该对象进行更改，而不进行创建和回收的操作，所以速度要比String快很多。另外，有时候我们会这样对字符串进行赋值:

```java
1 String str="abc"+"de";
2 StringBuilder stringBuilder=new StringBuilder().append("abc").append("de");
3 System.out.println(str);
4 System.out.println(stringBuilder.toString());
```

​	这样输出结果也是“abcde”和“abcde”，但是String的速度却比StringBuilder的反应速度要快很多，这是因为第1行中的操作和

```java
String str="abcde";
```

　　是完全一样的，所以会很快，而如果写成下面这种形式:

```java
1 String str1="abc";
2 String str2="de";
3 String str=str1+str2;
```

​	那么JVM就会像上面说的那样，不断的创建、回收对象来进行这个操作了。速度就会很慢。

### 线程安全

​	**在线程安全上，StringBuilder是线程不安全的，而StringBuffer是线程安全的**

　　如果一个StringBuffer对象在字符串缓冲区被多个线程使用时，StringBuffer中很多方法可以带有synchronized关键字，所以可以保证线程是安全的，但StringBuilder的方法则没有该关键字，所以不能保证线程安全，有可能会出现一些错误的操作。所以如果要进行的操作是多线程的，那么就要使用StringBuffer，但是在单线程的情况下，还是建议使用速度比较快的StringBuilder。

### 总结

　　**String：适用于少量的字符串操作的情况**

　　**StringBuilder：适用于单线程下在字符缓冲区进行大量操作的情况**

　　**StringBuffer：适用多线程下在字符缓冲区进行大量操作的情况**

# lambda 表达式



# 反射

### 简介

​	JAVA反射机制是指在运行过程中，对java中的任何一个类，都能知道这个类的所有属性和方法，对于任意一个对象，都能够调用它的任意方法和属性。这种动态获取信息以及动态调用对象方法的功能称为java语言反射机制。

### 反射机制

- 什么是反射

  使用反射机制，

  Java反射机制是在运行状态中，对于任意一个类都能够知道这个类的所有属性和构造方法

  对于任意一个对象，都能够调用它的任意方法和属性；这种动态获取信息以及动态调用对象的方法的功能称为java 语言的反射机制

- 学习一个新的类，自然先从其api看起，反射涉及的类除了Class类之外，基本都在java.lang.reflect包里Constructor，Field，Method类等

- 类，反射就是获取类的全部信息嘛

  在类加载的时候，jvm会创建一个class对象，class对象可以说是反射中最常用的，获取class对象的方式只要有三种

  1. 根据类名：类名.class

     当类被加载成.class文件时，这时候Person类变成了.class，在获取该字节码文件对象，也就是获取自己，该类处于**字节码阶段**

  2. 根据对象：对象.getClass()

     通过类的实例获得该文件的字节码对象，该类处于创建对象阶段。

  3. 根据全限定类名：Class.forName(全限定类名)如 Class.forName(“com.test.User");

     通过Class类中的静态方法，拿到一个类的字节码文件对象，这时候类还是**源文件阶段**，没有变成字节码文件

- 构造函数是java创建对象的必经之路，所以通过反射拿到一个类的构造函数后，再去创建这个类的对象自然是易如反掌

  ```
  //取消安全性检查后，才可以使用private修饰的构造函数，也可以单独对某个构造函数进行设置
  
  public void constructorTest() throws Exception {
      // 获取全部的构造函数
      Constructor<?>[] constructors = userClass.getConstructors();
      // 取消安全性检查,设置后才可以使用private修饰的构造函数，也可以单独对某个构造函数进行设置
      // Constructor.setAccessible(constructors, true);
      for (int i = 0; i < constructors.length; i++) {
        Class<?> parameterTypesClass[] = constructors[i].getParameterTypes();
        System.out.print("第" + i + "个构造函数:\t (");
        for (int j = 0; j < parameterTypesClass.length; j++) {
          System.out.print(parameterTypesClass[j].getName() + (j == parameterTypesClass.length - 1 ? "" : "\t"));
        }
        logger.info(")");
      }
      // 调用构造函数，实例化对象
      logger.info("实例化，调用无参构造:\t" + constructors[0].newInstance());
      logger.info("实例化，调用有参构造:\t" + constructors[1].newInstance("韦德", 35));
    }
  ```

- 属性，spring ioc 基本是基于属性的？对未提供set方法对private属性值依旧可以注入，应该是运用的反射机制

  ```
  //Field其实就是属性的一个抽象类
  @Test
    public void fieldTest() throws Exception {
      User user = userClass.newInstance();
      // 获取当前类所有属性
      Field fields[] = userClass.getDeclaredFields();
      // 获取公有属性(包括父类)
      // Field fields[] = cl.getFields();
      // 取消安全性检查,设置后才可以获取或者修改private修饰的属性，也可以单独对某个属性进行设置
      Field.setAccessible(fields, true);
      for (Field field : fields) {
        // 获取属性名 属性值 属性类型
        logger.info("属性名:" + field.getName() + "\t属性值:" + field.get(user) + "  \t属性类型:" + field.getType());
      }
      Field fieldUserName = userClass.getDeclaredField("name");
      // 取消安全性检查,设置后才可以获取或者修改private修饰的属性，也可以批量对所有属性进行设置
      fieldUserName.setAccessible(true);
      fieldUserName.set(user, "韦德");
      // 可以直接对 private 的属性赋值
      logger.info("修改属性后对象:\t" + user);
    }
  ```

  

- 方法，对javabean不陌生，在用框架操作javabean时，大多都是通过反射机制调用get，set方法对java bean进行操作这里的抽象方法类 变成了Method

- Java反射中的构造函数，属性，方法有着诸多相似之处，不仅仅是因为它们有着共同的父类AccessibleObject，基本上所有的api都有相似之处。学习的过程中死记api是最愚蠢的，找方法，理解反射的设计思路。去尝试感悟设计思想，才是王道。

- java以面向对象和封装性著称，但是反射在java中堪称作弊器，可以从源文件直接获取类，方法，属性等，在运行阶段可以重新加载类，干编译阶段的事，简直就是作弊。



### API解析

##### getMethod()/getMethods()

​	public Method getMethod(String name, Class<?>... parameterTypes)，方法在Class类下。

​	public Method[] getMethod()，方法在Class类下。

​	**返回某个类的所有公用（public）方法包括其继承类的公用方法，当然也包括它所实现接口的方法。**

##### getDeclaredMethod()/getDeclaredMethods()

​	public Method getDeclaredMethod(String name, Class<?>...parameterTypes)，方法在Class类下。

​	public Method[] getDeclaredMethods()，方法在Class类下。

​	**该方法可以获取类下声明的所有方法，包括公共、保护、默认（包）访问和私有方法，但不包括继承的方法。当然也包括它所实现接口的方法。**

测试代码如下：

```java
public interface ReflectInterface {
    void interfaceImpTest();
}
```

```java
public class ReflectInterfaceImpl implements ReflectInterface {
    @Override
    public void interfaceImpTest() {
        System.out.println("implement test");
    }

    public void publicTest(){
        System.out.println("public test");
    }

    private void privateTest(){
        System.out.println("private test");
    }
}

```

```java
public class Test {

    @org.junit.Test
    public void reflectTest() throws IllegalAccessException, InstantiationException {
        Class clazz = ReflectInterfaceImpl.class;
        Object reflectInterfaceImpObj = clazz.newInstance();
        Method[] methods = clazz.getMethods();
        Method[] declaredMethods = clazz.getDeclaredMethods();
        System.out.println("以下为getMethods：");
        for(Method method : methods)
            System.out.println(method);
        System.out.println("以下为getDeclaredMethods：");
        for(Method method : declaredMethods)
            System.out.println(method);
    }
}
```

```java
// output
以下为getMethods：
public void reflect.ReflectInterfaceImpl.interfaceImpTest()
public void reflect.ReflectInterfaceImpl.publicTest()
public final void java.lang.Object.wait(long,int) throws java.lang.InterruptedException
public final native void java.lang.Object.wait(long) throws java.lang.InterruptedException
public final void java.lang.Object.wait() throws java.lang.InterruptedException
public boolean java.lang.Object.equals(java.lang.Object)
public java.lang.String java.lang.Object.toString()
public native int java.lang.Object.hashCode()
public final native java.lang.Class java.lang.Object.getClass()
public final native void java.lang.Object.notify()
public final native void java.lang.Object.notifyAll()
    
以下为getDeclaredMethods：
public void reflect.ReflectInterfaceImpl.interfaceImpTest()
public void reflect.ReflectInterfaceImpl.publicTest()
private void reflect.ReflectInterfaceImpl.privateTest()

Process finished with exit code 0
```

##### getField()/getFields()

​	public Field getField(String name)，方法在Class类下。

​	public Field[] getField()，方法在Class类下。

getDeclaredField()/getDeclaredFields()

​	public Field getField(String name)，方法在Class类下。

​	public Field[] getField()，方法在Class类下。

​	与上述getMethod同理，getMethod是获取类下所有公开变量，包括父类中的变量。getDeclaredFields 是获取类下所有声明的变量，包括共有、私有、保护变量，但不包括父类中的变量。

# IO

### 预备知识

##### 字符与字节

​	在Java中有输入、输出两种IO流，每种输入、输出流又分为字节流和字符流两大类。关于字节，我们在学习8大基本数据类型中都有了解，每个字节(byte)有8bit组成，每种数据类型又几个字节组成等。关于字符，我们可能知道代表一个汉字或者英文字母。

​	**但是字节与字符之间的关系是怎样的？**

​	Java采用unicode编码，通讲，2个字节来表示一个字符。在0~127整数之间的字符映射，unicode向下兼容ASCII，也就是1个字节表示一个字符。一个中文或英文字符的unicode编码都占2个字节。

| 编码方式     | 英文字符 | 中文字符 |
| ------------ | -------- | -------- |
| GB 2312、GBK | 1        | 2        |
| UTF-8        | 1        | 3-4      |
| UTF-16       | 2        | 3-4      |
| UTF-32       | 4        | 4        |

##### File类

​	File类是java.io包下代表与平台无关的文件和目录，也就是说，如果希望在程序中操作**文件和目录**，都可以通过File类来完成。

1. 构造函数如下：

   ```java
   //构造函数File(String pathname)
   File f1 =new File("c:\\abc\\1.txt");
   //File(String parent,String child)
   File f2 =new File("c:\\abc","2.txt");
   //File(File parent,String child)
   File f3 =new File("c:"+File.separator+"abc");//separator 跨平台分隔符
   File f4 =new File(f3,"3.txt");
   System.out.println(f1);//c:\abc\1.txt
   ```

   注意点：**路径分隔符：**

   - windows： "/" "\\\\" 都可以；
   - linux/unix： "/"；
   - 所以推荐都使用"/"，也可以直接使用代码`File.separator`，表示跨平台分隔符。

2. 创建与删除方法

   ```java
   //如果文件存在返回false，否则返回true并且创建文件 
   boolean createNewFile();
   //创建一个File对象所对应的目录，成功返回true，否则false。且File对象必须为路径而不是文件。只会创建最后一级目录，如果上级目录不存在就抛异常。
   boolean mkdir();
   //创建一个File对象所对应的目录，成功返回true，否则false。且File对象必须为路径而不是文件。创建多级目录，创建路径中所有不存在的目录
   boolean mkdirs()    ;
   //如果文件存在返回true并且删除文件，否则返回false
   boolean delete();
   //在虚拟机终止时，删除File对象所表示的文件或目录。
   void deleteOnExit();
   ```

3. 判断方法

   ```java
   boolean canExecute()    ;//判断文件是否可执行
   boolean canRead();//判断文件是否可读
   boolean canWrite();//判断文件是否可写
   boolean exists();//判断文件是否存在
   boolean isDirectory();//判断是否是目录
   boolean isFile();//判断是否是文件
   boolean isHidden();//判断是否是隐藏文件或隐藏目录
   boolean isAbsolute();//判断是否是绝对路径 文件不存在也能判断
   ```

4. 获取方法

   ```java
   String getName();//返回文件或者是目录的名称
   String getPath();//返回路径
   String getAbsolutePath();//返回绝对路径
   String getParent();//返回父目录，如果没有父目录则返回null
   long lastModified();//返回最后一次修改的时间
   long length();//返回文件的长度
   File[] listRoots();// 列出所有的根目录（Window中就是所有系统的盘符）
   String[] list() ;//返回一个字符串数组，给定路径下的文件或目录名称字符串
   String[] list(FilenameFilter filter);//返回满足过滤器要求的一个字符串数组
   File[]  listFiles();//返回一个文件对象数组，给定路径下文件或目录
   File[] listFiles(FilenameFilter filter);//返回满足过滤器要求的一个文件对象数组
   ```

   其中包含了一个重要的接口**FileNameFilter**，该接口是个文件过滤器，包含了一个`accept(File dir,String name)`方法，该方法依次对指定File的所有子目录或者文件进行迭代，按照指定条件，进行过滤，过滤出满足条件的所有文件。

   ```java
   // 文件过滤
   File[] files = file.listFiles(new FilenameFilter() {
       @Override
       public boolean accept(File file, String filename) {
       	return filename.endsWith(".mp3");
       }
   });
   ```

   file目录下的所有子文件如果满足后缀是.mp3的条件的文件都会被过滤出来。

### IO 流的概念

​	Java的IO流是实现输入/输出的基础，它可以方便地实现数据的输入/输出操作，在Java中把不同的输入/输出源抽象表述为"流"。流是一组有顺序的，有起点和终点的字节集合，是对数据传输的总称或抽象。即数据在两设备间的传输称为流，流的本质是数据传输，根据数据传输特性将流抽象为各种类，方便更直观的进行数据操作。
 	**流有输入和输出，输入时是流从数据源流向程序。输出时是流从程序传向数据源，而数据源可以是内存，文件，网络或程序等。**

### IO 流的分类

​	io 流可以按照如下三种标准分类：

##### 输入流和输出流

​	根据数据流向不同分为：输入流和输出流：

- 输入流:只能从中读取数据，而不能向其写入数据。
- 输出流：只能向其写入数据，而不能从中读取数据。

​	如下如所示：对程序而言，向右的箭头，表示输入，向左的箭头，表示输出：

![](/Users/mrjiao/Documents/typora/java/assets/io 流输入流输出流.png)

##### 字节流和字符流

​	字节流和字符流和用法几乎完全一样，区别在于字节流和字符流所操作的数据单元不同。
​	字符流的由来： 因为数据编码的不同，而有了对字符进行高效操作的流对象。本质其实就是基于字节流读取时，去查了指定的码表。字节流和字符流的区别：

- 读写单位不同：字节流以字节（8bit）为单位，字符流以字符为单位，根据码表映射字符，一次可能读多个字节。

- 处理对象不同：字节流能处理所有类型的数据（如图片、avi等），而字符流只能处理字符类型的数据。

  只要是处理纯文本数据，就优先考虑使用字符流。 除此之外都使用字节流。

##### 节点流和处理流

​	按照流的角色来分，可以分为节点流和处理流。

- **可以从/向一个特定的IO设备（如磁盘、网络）读/写数据的流，称为节点流，节点流也被成为低级流。**
- **处理流是对一个已存在的流进行连接或封装，通过封装后的流来实现数据读/写功能，处理流也被称为高级流。**

```java
//节点流，直接传入的参数是IO设备
FileInputStream fis = new FileInputStream("test.txt");
//处理流，直接传入的参数是流对象
BufferedInputStream bis = new BufferedInputStream(fis);
```

​	当使用处理流进行输入/输出时，程序并不会直接连接到实际的数据源，没有和实际的输入/输出节点连接。使用处理流的一个明显好处是，只要使用相同的处理流，程序就可以采用完全相同的输入/输出代码来访问不同的数据源，随着处理流所包装节点流的变化，程序实际所访问的数据源也相应地发生变化。
​	实际上，Java使用处理流来包装节点流是一种典型的装饰器设计模式，通过使用处理流来包装不同的节点流，既可以消除不同节点流的实现差异，也可以提供更方便的方法来完成输入/输出功能。

### IO 流的四大基类

​	根据流的流向以及操作的数据单元不同，将流分为了四种类型，每种类型对应一种抽象基类。这四种抽象基类分别为：InputStream,Reader,OutputStream以及Writer。四种基类下，对应不同的实现类，具有不同的特性。在这些实现类中，又可以分为节点流和处理流。下面就是整个由着四大基类支撑下的整个IO流的框架图：

![](/Users/mrjiao/Documents/typora/java/assets/io 流四大基类.png)

##### InputStream

​	InputStream 是所有的输入字节流的父类，它是一个抽象类，主要包含三个方法：

```java
//读取一个字节并以整数的形式返回(0~255),如果返回-1已到输入流的末尾。 
int read() ； 
//读取一系列字节并存储到一个数组buffer，返回实际读取的字节数，如果读取前已到输入流的末尾返回-1。 
int read(byte[] buffer) ； 
//读取length个字节并存储到一个字节数组buffer，从off位置开始存,最多len， 返回实际读取的字节数，如果读取前以到输入流的末尾返回-1。 
int read(byte[] buffer, int off, int len) ； 
```

##### OutputStream

​	OutputStream 是所有的输出字节流的父类，它是一个抽象类，主要包含如下四个方法：

```java
//向输出流中写入一个字节数据,该字节数据为参数b的低8位。 
void write(int b) ; 
//将一个字节类型的数组中的数据写入输出流。 
void write(byte[] b); 
//将一个字节类型的数组中的从指定位置（off）开始的,len个字节写入到输出流。 
void write(byte[] b, int off, int len); 
//将输出流中缓冲的数据全部写出到目的地。 
void flush();
```

##### Reader

​	Reader 是所有的输入字符流的父类，它是一个抽象类，主要包含三个方法：

```java
//读取一个字符并以整数的形式返回(0~255),如果返回-1已到输入流的末尾。 
int read() ； 
//读取一系列字符并存储到一个数组buffer，返回实际读取的字符数，如果读取前已到输入流的末尾返回-1。 
int read(char[] cbuf) ； 
//读取length个字符,并存储到一个数组buffer，从off位置开始存,最多读取len，返回实际读取的字符数，如果读取前以到输入流的末尾返回-1。 
int read(char[] cbuf, int off, int len) 
```

​	对比InputStream和Reader所提供的方法，就不难发现两个基类的功能基本一样的，只不过读取的数据单元不同。

​	**在执行完流操作后，要调用close()方法来关系输入流，因为程序里打开的IO资源不属于内存资源，垃圾回收机制无法回收该资源，所以应该显式关闭文件IO资源。**

​	除此之外，InputStream和Reader还支持如下方法来移动流中的指针位置：

```java
//在此输入流中标记当前的位置
//readlimit - 在标记位置失效前可以读取字节的最大限制。
void mark(int readlimit)
// 测试此输入流是否支持 mark 方法
boolean markSupported()
// 跳过和丢弃此输入流中数据的 n 个字节/字符
long skip(long n)
//将此流重新定位到最后一次对此输入流调用 mark 方法时的位置
void reset()
```

##### Writer

​	Writer 是所有的输出字符流的父类，它是一个抽象类,主要包含如下六个方法：

```java
//向输出流中写入一个字符数据,该字节数据为参数b的低16位。 
void write(int c); 
//将一个字符类型的数组中的数据写入输出流， 
void write(char[] cbuf) 
//将一个字符类型的数组中的从指定位置（offset）开始的,length个字符写入到输出流。 
void write(char[] cbuf, int offset, int length); 
//将一个字符串中的字符写入到输出流。 
void write(String string); 
//将一个字符串从offset开始的length个字符写入到输出流。 
void write(String string, int offset, int length); 
//将输出流中缓冲的数据全部写出到目的地。 
void flush() 
```

​	可以看出，Writer比OutputStream多出两个方法，主要是支持写入字符和字符串类型的数据。

​	**使用Java的IO流执行输出时，不要忘记关闭输出流，关闭输出流除了可以保证流的物理资源被回收之外，还能将输出流缓冲区的数据flush到物理节点里（因为在执行close()方法之前，自动执行输出流的flush()方法）**

### RandomAccessFile

##### 简介

​	RandomAccessFile，该类是IO流体系中功能最丰富的文件内容访问类，既可以读取文件内容，也可以向文件输出数据。

​	RandomAccessFile支持“随机访问”的方式，程序快可以直接跳转到文件的任意地方来读写数据。由于RandomAccessFile可以自由访问文件的任意位置，**所以如果需要访问文件的部分内容，而不是把文件从头读到尾，使用RandomAccessFile将是更好的选择。**

​	与OutputStream、Writer等输出流不同的是，RandomAccessFile允许自由定义文件记录指针，RandomAccessFile可以不从开始的地方开始输出，因此RandomAccessFile可以向已存在的文件后追加内容。**如果程序需要向已存在的文件后追加内容，则应该使用RandomAccessFile。**

​	RandomAccessFile的方法虽然多，但它有一个最大的局限，就是只能读写文件，不能读写其他IO节点。

​	**RandomAccessFile的一个重要使用场景就是网络请求中的多线程下载及断点续传。**

##### RandomAccess中的方法

1. 构造函数

   ​	RandomAccessFile类有两个构造函数，其实这两个构造函数基本相同，只不过是指定文件的形式不同——一个需要使用String参数来指定文件名，一个使用File参数来指定文件本身。除此之外，创建RandomAccessFile对象时还需要指定一个mode参数，该参数指定RandomAccessFile的访问模式，一共有4种模式。

   - **"r" : **   以只读方式打开。调用结果对象的任何 write 方法都将导致抛出 IOException。
   -  **"rw":**    打开以便读取和写入。
   -  **"rws":**  打开以便读取和写入。相对于 "rw"，"rws" 还要求对“文件的内容”或“元数据”的每个更新都同步写入到基础存储设备。
   -  **"rwd" :**  打开以便读取和写入，相对于 "rw"，"rwd" 还要求对“文件的内容”的每个更新都同步写入到基础存储设备。

   ```java
   public RandomAccessFile(String name, String mode) throws FileNotFoundException{};
   public RandomAccessFile(File file, String mode) throws FileNotFoundException{};
   ```

2. RandomAccessFile的重要方法

   ​	RandomAccessFile既可以读文件，也可以写文件，所以类似于InputStream的read()方法，以及类似于OutputStream的write()方法，RandomAccessFile都具备。除此之外，RandomAccessFile具备两个特有的方法，来支持其随机访问的特性。

   ​	RandomAccessFile对象包含了一个记录指针，用以标识当前读写处的位置，当程序新创建一个RandomAccessFile对象时，该对象的文件指针记录位于文件头（也就是0处），当读/写了n个字节后，文件记录指针将会后移n个字节。除此之外，RandomAccessFile还可以自由移动该记录指针。下面就是RandomAccessFile具有的两个特殊方法，来操作记录指针，实现随机访问：

   ```java
   long getFilePointer( );		//返回文件记录指针的当前位置
   void seek(long pos );		//将文件指针定位到pos位置
   ```

3. RandomAccessFile的使用

   ​	利用RandomAccessFile实现文件的多线程下载，即多线程下载一个文件时，将文件分成几块，每块用不同的线程进行下载。下面是一个利用多线程在写文件时的例子，其中预先分配文件所需要的空间，然后在所分配的空间中进行分块，然后写入：

   ```java
   /** 
    * 测试利用多线程进行文件的写操作 
    */  
   public class Test {  
     
       public static void main(String[] args) throws Exception {  
           // 预分配文件所占的磁盘空间，磁盘中会创建一个指定大小的文件  
           RandomAccessFile raf = new RandomAccessFile("D://abc.txt", "rw");  
           raf.setLength(1024*1024); // 预分配 1M 的文件空间  
           raf.close();  
             
           // 所要写入的文件内容  
           String s1 = "第一个字符串";  
           String s2 = "第二个字符串";  
           String s3 = "第三个字符串";  
           String s4 = "第四个字符串";  
           String s5 = "第五个字符串";  
             
           // 利用多线程同时写入一个文件  
           // 从文件的1024字节之后开始写入数据 
           new FileWriteThread(1024*1,s1.getBytes()).start();  
           // 从文件的2048字节之后开始写入数据 
           new FileWriteThread(1024*2,s2.getBytes()).start();  
           // 从文件的3072字节之后开始写入数据  
           new FileWriteThread(1024*3,s3.getBytes()).start(); 
           // 从文件的4096字节之后开始写入数据  
           new FileWriteThread(1024*4,s4.getBytes()).start(); 
           // 从文件的5120字节之后开始写入数据  
           new FileWriteThread(1024*5,s5.getBytes()).start(); 
       }  
         
       // 利用线程在文件的指定位置写入指定数据  
       static class FileWriteThread extends Thread{  
           private int skip;  
           private byte[] content;  
             
           public FileWriteThread(int skip,byte[] content){  
               this.skip = skip;  
               this.content = content;  
           }  
             
           public void run(){  
               RandomAccessFile raf = null;  
               try {  
                   raf = new RandomAccessFile("D://abc.txt", "rw");  
                   raf.seek(skip);  
                   raf.write(content);  
               } catch (FileNotFoundException e) {  
                   e.printStackTrace();  
               } catch (IOException e) {  
                   // TODO Auto-generated catch block  
                   e.printStackTrace();  
               } finally {  
                   try {  
                       raf.close();  
                   } catch (Exception e) {  
                   }  
               }  
           }  
       }  
     
   }  
   ```

   **当RandomAccessFile向指定文件中插入内容时，将会覆盖掉原有内容。如果不想覆盖掉，则需要将原有内容先读取出来，然后先把插入内容插入后再把原有内容追加到插入内容后。**

### 对象序列化与反序列化

​	什么是序列化和反序列化呢？这是针对对象来说的，因为我们在写入文件的时候，常常因为要保存的是一个对象，也就是一个obj，但是里面的变量又很多，我们不可能挨个申明，一个个写入，这时候，我们就可以使用对象的序列化与反序列化。

​	**序列化就是对象到保存文件的过程。反序列化就是从保存的文件，转换为对象的过程。**我们使用ObjectOutputStream和ObjectInputStream 实现对象的序列化和反序列化。

```java
File file = new File("test.txt");

MyService ms = new MyService();
try {
    OutputStream os = new FileOutputStream(file);
    //创建时，需要给予一个outputStream，这个很好理解，
    //因为对象操作肯定是字节操作，不能使用字符操作
    ObjectOutputStream oos = new ObjectOutputStream(os);
    oos.writeObject(ms);
    oos.close();
    os.close();
} catch (FileNotFoundException e) {
    e.printStackTrace();
} catch (IOException e) {
    e.printStackTrace();
}


try {
    InputStream is = new FileInputStream(file);
    ObjectInputStream ios = new ObjectInputStream(is);
    MyService object = (MyService)ios.readObject();
    System.out.println(object.name);
    is.close();
    ios.close();    
} catch (FileNotFoundException e) {
    e.printStackTrace();
} catch (IOException e) {
    e.printStackTrace();
} catch (ClassNotFoundException e) {
    e.printStackTrace();
}
```

# 额外补充

### [值传递与引用传递](https://baijiahao.baidu.com/s?id=1614769568984126810&wfr=spider&for=pc)

