

# 知识结构

Java本身基础知识

- java创建对象的几种方式，不通过构造函数也可以创建对象吗？

  ```
  可以的
  比如Clone();方式
  ```

  



**Java 内部类 集合类 以及他们之间的接口实现与类继承关系**

HashMap 和 Hashtable的区别 

```
他们都属于Map类接口，实现了将唯一值映射到特定值上
HashMap 没有分类或者排序，它允许一个null键和多个null值
Hashtable类似于HashMap 但是不允许null键和null值。它也比HashMap慢，因为它是同步的
```



### Java泛型

```
java泛型包括 泛型方法 泛型类 
类型通配符 T和？的区别？
```



### Java Scanner类 

```java
System.out.print()||println()||或者单独一个print()都可以控制台输出
输入需要Scanner类的帮忙 
		Scanner scan = new Scanner(System.in);
        // 从键盘接收数据

        // nextLine方式接收字符串
        System.out.println("nextLine方式接收：");
        // 判断是否还有输入
        if (scan.hasNextLine()) {
            String str2 = scan.nextLine();
            System.out.println("输入的数据为：" + str2);
        }
        scan.close();
next();与nextLine();区别很小，就是 next是遇到有效字符+空格才可以，并且会忽略掉有效字符前的空格
接受多行输入的时候，就需要我们自己约定结束符号了
		StringBuilder stringbuilder = new StringBuilder();
		Scanner scan = new Scanner(System.in);
		while(true){
            String text = scanner.nextLine().trim();
                if("".equals(text))
                    break;
    		stringbuilder.append(text);
		}
```

- ![ç±"ççå½å¨æ-11.2kB](http://static.zybuluo.com/Rico123/5l9j3f8eenb2v9dudcqby2v3/%E7%B1%BB%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F)

### **常用的几个Math函数**

##### Math.random()

```
调用Math.Random()函数能够返回带正号的double值
取值范围是[0.0,1.0),在该范围内（近似）均匀分布。因为返回值是double类型的,小数点后面可以保留15位小数，所以产生相同的可能性非常小,在这一定程度上是随机数。double8字节 两个数字一个字节
Random() 使用一个和当前系统时间对应的 与时间有关的数字作为种子数
Random(long seed) 直接传入一个种子数
种子，就是产生随机数的第一次使用值，机制是通过一个函数，将这个种子的值转化为随机数空间中的某一个点上，并且产生的随机数均匀的散布在空间中。以后产生的随机数都与前一个随机数有关，但是仅仅是有关，只是随机算法的起源数字，和生成的随机数区间没有关系

用算法进行模拟 或许，对函数进行模拟也是一样的
概率论：点估计和区间估计
区间估计：对于某个区间而言，会与真实值相合，区间估计，也就出现了置信区间

(Math.random()*100)+1;


```



##### Math.min()

其实就是两者相互比较，保留小者，因此min可以设置一个很大的数 比如Integer.MAX_VALUE;

##### Math.max()

其实就是两者相互比较，保留大者，因此max可以设置一个很小的值比如Integer.MIN_VALUE;

##### Math.sqrt(), 

##### Math.cbrt(), 

##### Math.pow(a,b),

#####  Math.abs() 取绝对值

```
Math.sqrt() 计算平方根
Math.sqrt(16);
Math.cbrt() 计算立方根
Math.cbrt(8);
Math.pow( , ) 计算n的m次幂
Math.pow(2,3)；
Math.abs() 计算绝对值
Math.abs(-5); 某个数的绝对值
```



### Java反射机制

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
  取消安全性检查后，才可以使用private修饰的构造函数，也可以单独对某个构造函数进行设置
  
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
  Field其实就是属性的一个抽象类
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



### Java 字符串函数

##### substring()

```
substring(int beginIndex){
}//beginIndex 就是以新的beginIndex为开头，字符串也是从0开始的数组，并且包含beginIndex所指向字符
substring(int beginIndex,int endIndex){
}//beginIndex 就是以新的beginIndex开头，包含beginIndex所指向的字符，如果把字符串看成一个数组，然后不包含endIndex的字符，因此endIndex可以为s.length()
```



