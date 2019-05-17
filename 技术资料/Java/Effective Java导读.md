# 知识结构

Java本身基础知识

- java创建对象的几种方式，不通过构造函数也可以创建对象吗？

  ```
  可以的
  比如Clone();
  重写clone()类一定要重写equals()类
  重写equals()类 如果要用到HashMap或者HashSet等数据结构 一定要重写hashcode()方法
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

### Java 字符串函数

##### substring()

```
substring(int beginIndex){
}//beginIndex 就是以新的beginIndex为开头，字符串也是从0开始的数组，并且包含beginIndex所指向字符
substring(int beginIndex,int endIndex){
}//beginIndex 就是以新的beginIndex开头，包含beginIndex所指向的字符，如果把字符串看成一个数组，然后不包含endIndex的字符，因此endIndex可以为s.length()
```

### 覆盖equals()方法要注意的

- 自反性 x.equals(x)
- 一致性 x.equals(x)每次返回的结果应该一致
- 传递性 x.equals(b),b.equals(c),x应该 euqals(c)
- 对称性 x.equals(b), b.equals(x)结果应该一致
- 

### 谨慎的覆盖clone()方法

- 1.Cloneable 是一个空的接口，决定了clone()的实现方式,如果一个类实现了Cloneable，那么Object的clone()方法就会返回该对象的逐域拷贝，否则会抛出异常
- 2.如果实现了Cloneable，就表示该类和所偶超类都要遵循一个机制：无需构造器就可以创建对象
- 3.Object的clone()在拷贝的的时候是拷贝引用和数值，所以原对象和clone对象中域的引用是指向相同的对象，因此如果域引用了可变的对象，那么就会造成问题。
- 4.为了解决3的问题，要对一个对象实施clone()的时候，需要根据类的域的类型来判断是否要对域进行clone()，而在对域进行clone()的时候同样也会有3的问题。
- 5.所以为了克隆复杂的对象，最后的方法就是先调用Object的clone(),然后将所有域置为空白，最后调用类的api来重新产生对象状态。
- 6.另一个实现对象拷贝的办法就是提供一个对象拷贝构造器或者拷贝工厂。

为什么要谨慎覆盖clone()方法，主要是复制了基本类型的域，但是clone出来的新的对象可能还指向引用类型的域

现在了解Object类的clone()方法：

protected native Object clone() throws CloneNotSupportedException;

clone()方法是一个受保护的本地方法。

clone()方法的通用约束如下：

x.clone() != x        应该返回true

x.clone().getClass() == x.getClass()        应该返回true

x.clone().equals(x)           应该返回true

覆盖Clone记得覆盖equals()方法，覆盖equals记得覆盖hashcode()方法，



<https://www.cnblogs.com/wangliyue/p/4453658.html>

简而言之，所有实现了Cloneable接口的类都应该有一个公有的方法覆盖从Object类继承而来的clone()方法，此公有方法先调用super.clone()，

得到一个“浅拷贝”的对象，然后根据原对象的实例域的情况，修正任何需要修正的域。一般情况下，这意味这要拷贝任何包含内部“深层结构”

的可变对象。并将新对象的引用代替原来指向这些对象的引用。虽然，这些内部拷贝操作往往可以通过层层递进的调用clone()来完成（要求图中的Apple类和Dog类也必须正确的提供公有的clone()方法），但这通常不是最佳的方法。如果该类只包含基本类型的域，和不可变类型的引用域，

那么多半情况下是没有域需要修正的。但是如果引用域只有不可变类型的引用域...

- Object中的clone()默认是浅copy，调用super.clone()方法 引用类型域需要自己操作进行变更

### 始终覆盖toString()方法

### 深copy，浅copy

shen

##### 深copy 

引用类型域和基本类型域都会被copy



##### 浅copy

调用父类的super.clone()就是浅copy的代表方法