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



