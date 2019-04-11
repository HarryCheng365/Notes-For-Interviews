

## 知识结构

Java本身基础知识

- java创建对象的几种方式，不通过构造函数也可以创建对象吗？

  ```
  可以的
  比如Clone();方式
  ```

  



**Java 内部类 集合类 以及他们之间的接口实现与类继承关系**

- HashMap 和 Hashtable的区别 

  ```
  他们都属于Map类接口，实现了将唯一值映射到特定值上
  HashMap 没有分类或者排序，它允许一个null键和多个null值
  Hashtable类似于HashMap 但是不允许null键和null值。它也比HashMap慢，因为它是同步的
  ```

- Java泛型

  ```
  java泛型包括 泛型方法 泛型类 
  类型通配符 T和？的区别？
  ```

- Java Scanner类 接受 控制台的输入

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

  

  