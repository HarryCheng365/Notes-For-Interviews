个人认为 JVM的理解

其实

一部分是Java类的编译，加载，执行机制。

另一部分就是Java虚拟机内存模型和GC机制。

# Java虚拟机内存模型

### 前言

jvm主要由class loader、runtime data area、excution engine和native interface四部分组成，其结构图如下：

![](../assets/jvm%20%E7%BB%93%E6%9E%84.png)

​	其中：

- class loader 用于将class文件加载到内存
- runtime data area 是jvm内存空间结构模型
- excution engine 用于对java字节码文件进行解析
- native interface 融合不同开发语言的原生库为java所用

Java虚拟机再执行Java程序的过程中会把它所管理的内存划分为若干个不同的数据区域
这些数据区域都有各自的用途，以及创建和销毁的时间，并且他们可以分为两种类型：
1.线程共享的数据区包括方法区(运行时的常量值）和堆
2.线程私有的数据区包括虚拟机、本地方法栈和程序计数器。

![JVMåå­æ¨¡å.png-56.5kB](http://static.zybuluo.com/Rico123/wozzd000rzpwwpz4eqi0xf1j/JVM%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B.png)



##### 自身理解：和计算机虚拟内存模型要区分开

虚拟内存模型是针对进程的

虚拟内存模型是为了解决内存空间有限，将数据化为一个个内存页，这些页面不全部都在内存，二是按需调用，另外页表地址由地址前缀和页偏移

不一样哈，一个是进程模型，一个是虚拟内存模型，注意了

常见的linux内存模型

进程模型：程序文件，已初始化的数据，未初始化的数据，运行时堆（这是进程虚拟存储器）



### 程序计数器

- 程序计数器是什么，里面记录什么？

  记录线程执行到哪了，计数器记录的是正在执行的字节码指令的地址✅

- 为什么需要程序计数器？

  多线程抢夺计算资源，所以要时间片轮询实现并发。
  因此，为了线程切换后能够恢复到正确的执行位置，每条线程都需要一个独立的程序计数器去记录其正在执行的字节码指令地址。记录执行到哪了。

- 程序计数器的实现？

  有点像线程的TLAB线程私有缓冲区，都是一小块内存空间

  程序计数器是线程私有的一块较小的内存空间，其可以看做是当前线程所执行的字节码的行号指示器。


- 程序计数器是唯一一个没有规定任何OutOfMemorryError的区域。

  如果线程正在执行的是一个 Java 方法，计数器记录的是正在执行的字节码指令的地址；
  如果正在执行的是 Native 方法，则计数器的值为空。

  //native方法的意思就是该方法 由非java语言实现，非java代码的接口，不是java方法，java类，就不会有字节码文件，也就没有字节码地址，这时候程序计数器就为空了，并且

### 虚拟机栈 

- 虚拟机栈是什么？

  每个方法在执行的时候都会创建一个栈帧，用于存储局部变量表、操作数栈、动态链接、方法出口等信息，而且 每个方法从调用直至完成的过程，对应一个栈帧在虚拟机栈中入栈到出栈的过程。
  每个方法再执行的时候都会创建一个栈帧，局部变量表主要存放一些基本类型的变量和对象句柄，他们可以是方法参数，也可以是方法的局部变量。

- 虚拟机栈的实现？

- 虚拟机栈的异常情况？

  虚拟机栈有两种异常情况
  1.StackOverflowError和OutOfMemorryError。我们知道，一个线程拥有一个自己的栈，这个栈的大小决定了方法调用的可达深度（比如递归多少层，或嵌套调用多少层其他方法）若线程请求的栈深度大于虚拟机允许的深度，则抛出StackOverFlowError的异常。
  此外栈的大小是固定的，也可以是动态扩展的，若虚拟机栈可以动态扩展，但扩展时无法申请到足够的内存
  （比如没有足够的内存为一个新创建，则抛出OutofMemorryError异常

java方法执行的内存模型，线程私有✅

```
虚拟机栈描述的是Java方法执行的内存模型，是线程私有的。



```

局部变量表中存放了编译期，可知的各种基本数据类型和对象引用。

### 本地方法栈 ✅

虚拟机栈，栈帧结构：局部变量表，动态链接方法，操作数栈，返回地址。

方法执行的时候的返回地址，其实就是调用关系，一个方法call了一个方法，方法执行完怎么回去，就是这个返回地址。

```
本地方法栈和Java虚拟栈十分相似，也是线程私有的，区别是虚拟机栈为虚拟机执行Java方法服务，而本地方法栈为虚拟机执行Native方法。Native Method Stack。
与虚拟机栈一致，也会抛出上述两种异常。
```

### 线程共享数据区 ✅

```
线程共享的数据区 具体包括 Java堆 和 方法区 两个区域
```

### Java堆

所有的对象实例都在此分配空间，所以Java堆一定是线程共享的，并且对内存消耗较大，因此不一定非要是物理连续， 另外Java堆在实现的时候，一定要是可扩展的，否则就会抛出异常，因为所有对象实例都在此分配空间，所以也是GC的重灾区，也叫GC堆

### ![](../assets/堆.png)

```
1.Java堆的唯一目的就是存放对象实例，几乎所有的对象实例（和数组）都在这里分配内存。

2.Java堆是线程共享的（这一块内存区域是共享的，不是说类的对象是共享的），类的对象从中分配空间，这些对象通过new、newarray、 anewarray 和 multianewarray 等指令建立，它们不需要程序代码来显式的释放。

由于Java堆唯一目的就是用来存放对象实例，因此其也是垃圾收集器管理的主要区域，故也称为称为 GC堆。从内存回收的角度看，由于现在的垃圾收集器基本都 采用分代收集算法，所以为了方便垃圾回收Java堆还可以分为 新生代 和 老年代 。

3.Java堆可以物理上不连续，只要逻辑上连续即可。


4.而且，Java堆在实现时，既可以是固定大小的，也可以是可拓展的，并且主流虚拟机都是按可扩展来实现的（通过-Xmx(最大堆容量) 和 -Xms(最小堆容量)控制）。如果在堆中没有内存完成实例分配，并且堆也无法再拓展时，将会抛出 OutOfMemoryError 异常。 

```

##### TLAB Sun Hotspot JVM

```
Thread Local Allocation Buffer
Sun Hotspot JVM 为了提升对象内存分配的效率，对于所创建的线程都会分配一块独立的空间 TLAB，其大小由JVM根据运行的情况计算而得。
在TLAB上分配对象时不需要加锁(相对于CAS配上失败重试方式 )，因此JVM在给线程的对象分配内存时会尽量的在TLAB上分配，在这种情况下JVM中分配对象内存的性能和C基本是一样高效的，但如果对象过大的话则仍然是直接使用堆空间分配。

这也是共享区的问题，Java堆的线程安全问题
虚拟机为新生对象分配内存时，需要考虑修改指针 (该指针用于划分内存使用空间和空闲空间) 时的线程安全问题，因为存在可能出现正在给对象A分配内存，指针还未修改，对象B又同时使用原来的指针分配内存的情况。

在分配内存时候的线程安全问题，需要对指针进行加锁。

TLAB 的存在就是为了解决这个问题：每个线程在Java堆中预先分配一小块内存 TLAB，哪个线程需要分配内存就在自己的TLAB上进行分配，若TLAB用完并分配新的TLAB时，再加同步锁定，这样就大大提升了对象内存分配的效率。
CAS是在里面怎么用的，
```



### 方法区

方法区：静态域，运行常量池，可以GC，但是归属于MajorGC的一部分，并且有严苛的GC条件

```
方法区与java堆一样，也是线程共享的，并且不需要物理上连续的内存，其用于存储被虚拟机加载的类信息，常量，静态变量，即时编译器编译后的代码等数据。 而java堆，是动态分配的，所以 在类加载器的时候，类信息首先被放在方法区内。
方法区通常和永久区（Perm）关联在一起，但永久代和方法区不是一个概念，只是有的虚拟机用永久代来实现方法去，这样就可以用永久代GC来管理方法区，

```

##### 方法区—运行时常量池

```
运行时常量池（Runtime Constant Pool）是方法区的一部分

常量池在编译期间就将一部分数据存放于该区域，包含基本数据类型如int、long等以final声明的常量值，和String字符串、特别注意的是对于方法运行期位于栈中的局部变量String常量的值可以通过 String.intern()方法将该值置入到常量池中。

用于存放编译期生成的各种 字面量 和 符号引用。其中，字面量比较接近Java语言层次的常量概念，如文本字符串、被声明为final的常量值等。因此String是被声明为final的，所以改变的时候都是申请一个新的空间再copy内容过去。

```

##### 方法区-静态域

位于方法区的一块内存。存放类中以static生命的静态成员变量。



##### 补充: 为什么要使用native方法

```
java使用起来很方便，但是java保证灵活性的同时，保持多态的同时，也是牺牲运行时间和内存空间，因此有些代码，java实现起来不容易，比如更底层一点的图像处理啊 ，涉及到cpu gpu加速的问题，还是native方法比较好，
其实native方法也是一种面向对象的应用，我提供一个native接管，管你怎么和底层交互的，这些繁琐的我都不需要知道。
```



##### 补充：JVM启动的时候 

mx指定了Java虚拟机（JVM）的最大内存分配池，同时Xms指定了初始内存分配池。就是JVM默认堆的大小。

这意味着您的JVM将以Xms大量内存启动，并且将能够使用最Xmx大量的内存。例如，像下面这样启动JVM将以256 MB的内存启动它，并允许进程使用高达2048 MB的内存：

-Xmn2g：设置年轻代大小为2G。整个JVM内存大小=年轻代大小 + 年老代大小 + 持久代大小。持久代一般固定大小为64m，所以增大年轻代后，将会减小年老代大小。此值对系统性能影响较大，Sun官方推荐配置为整个堆的3/8。
-Xss128k：设置每个线程的堆栈大小。JDK5.0以后每个线程堆栈大小为1M，以前每个线程堆栈大小为256K。更具应用的线程所需内存大小进行调整。在相同物理内存下，减小这个值能生成更多的线程。但是操作系统对一个进程内的线程数还是有限制的，不能无限生成，经验值在3000~5000左右。

堆设置
-Xms:初始堆大小
-Xmx:最大堆大小 可以以X
-XX:NewSize=n:设置年轻代大小
-XX:NewRatio=n:设置年轻代和年老代的比值。如:为3，表示年轻代与年老代比值为1：3，年轻代占整个年轻代年老代和的1/4
-XX:SurvivorRatio=n:年轻代中Eden区与两个Survivor区的比值。注意Survivor区有两个。如：3，表示Eden：Survivor=3：2，一个Survivor区占整个年轻代的1/5
-XX:MaxPermSize=n:设置持久代大小
收集器设置
-XX:+UseSerialGC:设置串行收集器
-XX:+UseParallelGC:设置并行收集器
-XX:+UseParalledlOldGC:设置并行年老代收集器
-XX:+UseConcMarkSweepGC:设置并发收集器
垃圾回收统计信息
-XX:+PrintGC
-XX:+PrintGCDetails
-XX:+PrintGCTimeStamps
-Xloggc:filename
并行收集器设置
-XX:ParallelGCThreads=n:设置并行收集器收集时使用的CPU数。并行收集线程数。
-XX:MaxGCPauseMillis=n:设置并行收集最大暂停时间
-XX:GCTimeRatio=n:设置垃圾回收时间占程序运行时间的百分比。公式为1/(1+n)
并发收集器设置
-XX:+CMSIncrementalMode:设置为增量模式。适用于单CPU情况。
-XX:ParallelGCThreads=n:设置并发收集器年轻代收集方式为并行收集时，使用的CPU数。并行收集线程数。





# JavaGC机制



### 垃圾回收机制的意义 ✅

```
Java技术体系中所提倡的 自动内存管理 最终可以归结为自动化地解决了两个问题：给对象分配内存 以及回收分配给对象的内存，而且这两个问题针对的内存区域就是Java内存模型中的堆区
Java垃圾回收机制要考虑的问题十分复杂
1.哪些内存需要回收（对象是否可以被回收的两种经典算法：引用计数法 和 可达性分析算法）
2.什么时候回收（堆的新生代，老生代，永久代的垃圾回收机制，MinorGC和FullGC）
3.如何回收？（三种经典垃圾回收算法（标记清除法，复制算法，标记整理算法）以及分代收集器

Java使用一系列软指针来跟踪对象的各个引用，并用一个对象表将这些软指针映射为对象的引用。之所以称为软指针，是因为这些指针并不直接指向对象，而是指向对象的引用。
使用软指针，Java的GC能够以单独的线程运行在后台，并依次检查每个对象，通过更改

```

### 垃圾回收综述 ✅

```
JVM由于要执行GC而停止了应用程序的执行称之为Stop-the-World，该情形会在任何一种GC算法中发生。当Stop-the-world发生时，除了GC所需的线程以外，所有线程都处于等待状态直到GC任务完成。事实上，GC优化很多时候就是指减少Stop-the-world发生的时间，从而使系统具有 高吞吐 、低停顿 的特点。
```

### 哪些内存需要回收？

程序计数器，栈，堆，方法区四部分组成

其中程序计数器，栈(虚拟机栈，本地方法栈)区域随线程而生，随线程而灭，不需要我们考虑。

而堆和方法区中的内存分配都是动态的，只有在程序运行期间才能知道需要创建哪些对象，垃圾收集齐所关注的是这部分内存。

##### 可达性算法：判断对象的引用链是否可达

```
可达性算法是通过判断对象的引用链是否可达来决定对象是否可以被回收。
可达性分析算法是从离散数学中的图论引入的，程序把所有的引用关系看作一张图，通过一系列的名为 “GC Roots” 的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径称为引用链（Reference Chain）

当一个对象到 GC Roots 没有任何引用链相连（用图论的话来说就是从 GC Roots 到这个对象不可达）时，则证明此对象是不可用的。
可以作为GCRoot的对象包括
1.虚拟机栈
2.本地方法栈中Native方法引用对象
3.方法区中类静态属性引用的对象
4.方法区中常量引用对象
```

可以作为GCRoots的

方法区中永久区对象，以及Thread中的虚拟机栈和native方法

##### 引用计数法

```
引用计数法是通过判断对象的引用数量来决定对象是否可以被回收。
引用计数算法是垃圾收集器中的早期策略。在这种方法中，堆中的每个对象实例都有一个引用计数。
有新的引用变量引用这个对象的时候 引用计数器+1
但当一个对象实例的某个引用超过了生命周期或者被设置为一个新值时，对象实例的引用计数减 1。特别地，当一个对象实例被垃圾收集时，它引用的任何对象实例的引用计数器均减 1。
可以被收集的标准：任何引用计数器为0的对象

但是引用计数法也有问题，就是循环引用的问题，如果两个对象循环引用，同时没有其他外部对象引用它俩
其实这两个对象是不可达的，如果用可达性算法分析的话
```

##### 引用

​	符号引用和直接引用这是程序链接时候的概念

​	强引用 就是实例，只要还在用 就不会被收回

​	软引用 还有用，但非必须 只能存活到溢出的时候，会被强制回收掉

​	弱引用 比如ThreadLocal变量

​	虚引用 无法通过虚引用获得一个对象实例，为一个对象设置虚引用关联的唯一目的就是在这个对象被垃圾

​	收集器回收之前收到一个通知



​	再谈引用，在JDK 1.2之后，Java 对引用的概念进行了扩充，**将引用分为强引用( Strong Reference )、软引用(Soft Reference)、弱引用( Weak Reference)、虚引用( Phantom Reference )**四种，这四种引用强度依次逐渐减弱。



- 强引用，强引用就是在程序代码中普遍存在的，类似“Object obj = new Object()”这类的引用，只要强引用还在，垃圾收集器永远不会回收掉被引用的对象
- 软引用，软引用是用来描述一些还有用但非必须的对象。**对于软引用关联着的对象，在系统将要发生内存溢出异常之前，将会把这些对象列进回收范围之中进行第二次回收**。如果这次回收还没有足够的内存，才会抛出内存溢出异常。
- 弱引用，弱引用也是用来描述非必须对象的，但它的强度比软引用更弱一些，**被弱引用关联的对象只能存活到下一次垃圾收集发生之前**。当垃圾收集器工作时，无论当前内存是否足够，都会回收掉只被弱引用关联的对象。
- 虚引用，虚引用也称为幽灵引用或幻影引用，它是最弱的一种引用关系。一个对象是否有虚引用的存在，完全不会对其生存时间产生影响，也无法通过虚引用来获得一个对象实例。为一个对象设置虚引用关联的唯一目的就是能在这个对象被收集器回收时收到一个系统通知。

##### 生存还是死亡

​	即使在可达性算法中不可达的对象，也并非是非死不可。要宣告一个对象真正死亡，至少要经历过两个标记过程，具体过程如下：

- 如果对象在进行可达性分析后发现没有与GC Roots相连接的引用链，那它将会被第一次标记并且进行一次筛选，筛选的条件是此对象是否有必要执行**finalize()**方法。<font color="red">????</font>



### 

### 垃圾回收算法：

##### 标记清除法：

```
标记-清除算法分为标记和清除两个阶段。
该算法首先从根集合进行扫描，沿着引用逐个扫描 对存活的对象进行标记，标记完毕后，再扫描整个空间中的未被标记的对象进行回收

但是标记清除法也有不足
1.效率问题，标记和清除两个过程的效率都不高，至少遍历一遍，是n的复杂度
2.空间问题，标记-清除算法不需要进行对象的移动，并且仅对不存活的对象进行处理，因此会有大量不连续的内存碎片产生，这样如果大的对象需要内存分配的话，不得不提前触发另一次GC
```

##### 复制算法

其实有点像操作系统的分段内存管理

```
复制算法将可用内存按容量划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就将还存活着的对象复制到另外一块上面，然后再把已使用过的内存空间一次清理掉。

这种算法适用于对象存活率低的场景，比如新生代。这样使得每次都是对整个半区进行内存回收，内存分配时也就不用考虑内存碎片等复杂情况，只要移动堆顶指针，按顺序分配内存即可，实现简单，运行高效。该算法示意图如下所示：
```

##### 标记整理法

复制收集算法在对象存活率较高时就要进行较多的复制操作，效率将会变低。

更关键的是，如果不想浪费50%的空间，就需要有额外的空间进行分配担保，以应对被使用的内存中所有对象都100%存活的极端情况，所以在老年代一般不能直接选用这种算法。

标记整理算法的标记过程类似标记清除算法，但后续步骤不是直接对可回收对象进行清理，而是让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存，类似于磁盘整理的过程，该垃圾回收算法适用于对象存活率高的场景（老年代）,让所有存货的都往一端移动，因此，存活的对象越多，移动的距离越短

![标记整理法](../assets/标记整理法.png)

标记整理算法与标记清除算法最显著的区别是：标记清除算法不进行对象的移动，并且仅对不存活的对象进行处理；

而标记整理算法会将所有的存活对象移动到一端，并对不存活对象进行处理，因此其不会产生内存碎片。标记整理算法的作用示意图，因此标记整理法不会产生内存碎片如下：



### 分代收集算法

对于一个大型的系统，当创建的对象和方法变量比较多时，堆内存中的对象也会比较多，如果逐一分析对象是否该回收，那么势必造成效率低下

分代收集算法是基于这样一个事实：

不同的对象的生命周期(存活情况)是不一样的，而不同生命周期的对象位于堆中不同的区域，因此对堆内存不同区域采用不同的策略进行回收可以提高 JVM 的执行效率。

当代商用虚拟机使用的都是分代收集算法：

- 新生代对象存活率低，就采用复制算法；

- 老年代存活率高，就用标记清除算法或者标记整理算法。

  Java堆内存一般可以分为新生代、老年代和永久代三个模块，如下图所示：

  ![](../assets/堆.png)

### 是什么时候回收？

##### 新生代（Young Generation）

　　新生代的目标就是尽可能快速的收集掉那些生命周期短的对象，一般情况下，所有新生成的对象首先都是放在新生代的。新生代内存按照 8:1:1 的比例分为一个eden区和两个survivor(survivor0，survivor1)区，大部分对象在Eden区中生成。在进行垃圾回收时，先将eden区存活对象复制到survivor0区，然后清空eden区，当这个survivor0区也满了时，则将eden区和survivor0区存活对象复制到survivor1区，然后清空eden和这个survivor0区，此时survivor0区是空的，然后交换survivor0区和survivor1区的角色（即下次垃圾回收时会扫描Eden区和survivor1区），即保持survivor0区为空，如此往复。特别地，当survivor1区也不足以存放eden区和survivor0区的存活对象时，就将存活对象直接存放到老年代。如果老年代也满了，就会触发一次FullGC，也就是新生代、老年代都进行回收。注意，新生代发生的GC也叫做MinorGC，MinorGC发生频率比较高，不一定等 Eden区满了才触发。

Full GC 新生代 老年代 都回收

只有当S1区满了的时候，才会存到老年代，

老年代满的时候会出发MajorGC

永久代对垃圾回收没有影响，但方法区，满足三个条件的class文件或者常量可以被回收

##### 老年代（Old Generation）

　　老年代存放的都是一些生命周期较长的对象，就像上面所叙述的那样，在新生代中经历了N次垃圾回收后仍然存活的对象就会被放到老年代中。此外，老年代的内存也比新生代大很多(大概比例是1:2)，当老年代满时会触发Major GC(Full GC)，老年代对象存活时间比较长，因此FullGC发生的频率比较低。

##### 永久代（Permanent Generation）

　　永久代主要用于存放静态文件，如Java类、方法等。永久代对垃圾回收没有显著影响，但是有些应用可能动态生成或者调用一些class，例如使用反射、动态代理、CGLib等bytecode框架时，在这种时候需要设置一个比较大的永久代空间来存放这些运行过程中新增的类。

### 内存分配与回收策略

- 程序员不可以强制GC发生，但是可以建议系统GC 调用System.gc()方法

##### 对象优先在Eden区分配

​	当Eden区域内存不足时，将会发起一次minor GC，Eden区域与Suvivor区域的默认比例是8：1。

##### 大对象直接进入老年代

这样会提前触发full gc 造成长时间的stop the world

##### 长期存活对象直接进入老年代

​	虚拟机给每个对象定义了一个对象年龄计数器，如果对象在 Eden 出生并经历过第一次MinorGC后任仍然存活，并且能被Survivor容纳的话，就会被移动到Survivor空间。对象在Survivor空间中每经历过一次MinorGC，年龄就加一，当它年龄增加到一定程度（默认15岁），就会晋升到老年代。可以通过 -XX：MaxTenuringThreshold参数来修改默认年龄。

##### 动态对象年龄判定

​	如果在Survivor区域中相同年龄所有对象大小总和大于Survivor空间的一半，年龄大于或等于该年龄的对象就可以直接进入老年代，无须等到MaxTenuringThreshold中要求的年龄。

##### Minor GC/ Major GC/ Full GC

​	按照JVM 内置的通用垃圾回收原则。堆内存划分为 Eden、Survivor 和 Tenured/Old 空间，

![](../assets/%E5%A0%86.png)

1. Minor GC

   ​	从年轻代空间（包括 Eden 和 Survivor 区域）回收内存被称为 Minor GC。这一定义既清晰又易于理解。但是，当发生Minor GC事件的时候，有一些有趣的地方需要注意到：

   - 当 JVM 无法为一个新的对象分配空间时会触发 Minor GC，比如当 Eden 区满了。所以分配率越高，越频繁执行 Minor GC。//什么时候回收
   - 内存池被填满的时候，其中的内容全部会被复制，指针会从0开始跟踪空闲内存。Eden 和 Survivor 区进行了标记和复制操作，取代了经典的标记、扫描、压缩、清理操作。所以 Eden 和 Survivor 区不存在内存碎片。写指针总是停留在所使用内存池的顶部。
   - 执行 Minor GC 操作时，不会影响到永久代。从永久代到年轻代的引用被当成 GC roots，从年轻代到永久代的引用在标记阶段被直接忽略掉。
   - **质疑常规的认知，所有的 Minor GC 都会**触发**“全世界的暂停（stop-the-world）”**，停止应用程序的线程。对于大部分应用程序，停顿导致的延迟都是可以忽略不计的。其中的真相就是，大部分 Eden 区中的对象都能被认为是垃圾，永远也不会被复制到 Survivor 区或者老年代空间。如果正好相反，Eden 区大部分新生对象不符合 GC 条件，Minor GC 执行时暂停的时间将会长很多。

   所以 Minor GC 的情况就相当清楚了——**每次 Minor GC 会清理年轻代的内存**。

2. Major GC & Full GC

   - **Major GC** 是清理老年代。
   - **Full GC** 是清理整个堆空间—包括年轻代和老年代。

   很不幸，实际上它还有点复杂且令人困惑。首先，许多 Major GC 是由 Minor GC 触发的，所以很多情况下将这两种 GC 分离是不太可能的。另一方面，许多现代垃圾收集机制会清理部分永久代空间，所以使用“cleaning”一词只是部分正确。//

##### 补充：关于方法区的GC

静态变量是属于类的不是属于实例对象的；所以这个类存在静态变量就会一直存在；静态变量存储在方法区；用于保存类常量以及字符串常量。注意，这个区域不是用于存储那些从老年代存活下来的对象，按照分代属于持久代；这个区域也可能发生GC。发生在这个区域的GC事件也被算为 Major GC 。只不过在这个区域发生GC的条件非常严苛，必须符合以下三种条件才会被回收：
 1、所有实例被回收

2、加载该类的ClassLoader 被回收

3、Class 对象无法通过任何途径访问（包括反射）

所以一般来说 静态变量 常量和文件很少会被GC回收的；



### CMS收集器

并发标记清楚收集器

​	CMS( Concurrent Mark Sweep )收集器是一种以获取最短回收停顿时间为目标的收集器。该收集器主要用于希望系统停顿时间短、频繁与用户交互的应用，如BS系统的服务端。

**CMS收集器只能回收Old对象，需要一个回收年轻代的收集器配合使用**

​	CMS是基于“标记——清除”算法实现。主要步骤如下：

- 初始标记

  需要“stop the world”，该步骤只是标记一下GC Roots能直接关联到的对象，速度很快。

- 并发标记

  进行GC Roots Tracing的过程。

- 重新标记

  需要“stop the world”，该阶段为了修正并发标记期间因为用户程序继续运作而导致标记产生变动的那一部分对象的标记记录。停顿时间比初始标记长一点，但远比并发标记时间短。

  简记：为了修正并发标记期间变动的标记记录。

- 并发清除

  使用垃圾收集算法进行垃圾收集。

​	由于整个过程中耗时最长的并发标记和并发清除过程都可以与用户进程一起工作，所以，从总体上来说，CMS收集器的内存回收过程是和用户线程一起并发执行的。

MixedGC 也是并发清楚的一个过程 对于老年代来说

​	整体流程如下：

![](../assets/CMS.png)

其缺点如下：

- 对CPU资源非常敏感。在并发阶段，其虽然不会导致用户线程停顿，但会因为占用了一部分线程（或者说是CPU 资源）而导致程序变慢，总吞吐量会降低；
- CMS收集器无法收集浮动垃圾（Floating Garbage）可能会出现“Concurrent Mode Failure”失败而导致另一次Full GC的产生。由于CMS并发清理阶段用户线程还在执行，伴随程序运行自然就还会有新的垃圾不断产生，这一部分垃圾出现在标记过程之后，CMS无法在当次收集中处理掉它们，只好等待下一次GC再收集它们，这一部分垃圾就称为“浮动垃圾”；
- CMS是基于标记——清除算法，每次收集结束后会有大量空间碎片产生，会给大对象分配带来很大麻烦。往往会出现老年代还有很大空间剩余，但是无法找到足够大的连续空间来分配当前对象，不得不提前触发一次Full GC。

##### 补充：自己的体悟：CMS是一个老年代的收集器

### CMS和G1的区别：

##### 分代收集

G1可以回收年轻代老年代，CMS只适合回收老年代

但是单论老年代回收算法，其实两者过程都差不多的，都是要经过起始标记，并发标记，最终标记，垃圾清除这四个步骤

##### 如何处理跨代引用

年轻代引用老年代没什么问题，老年代引用年轻代在收集的时候就会影响young gc 处理方法是 用一个叫card的数据结构，有点像我们内存中的分页，来记录这些引用，并在标记收集的时候避开拥有记录的

G1更是拓展用了一个remeber set来做

##### 如何处理并发过程的对象化

##### Write Barrier

##### FullGC



### G1收集器

1. 引入

​	为解决CMS算法产生空间碎片和其它一系列的问题缺陷，HotSpot提供了另外一种垃圾回收策略，G1（Garbage First）算法。G1垃圾收集算法主要应用在多CPU大内存的服务中，在满足高吞吐量的同时，竟可能的满足垃圾回收时的暂停时间，该设计主要针对如下应用场景：

- 垃圾收集线程和应用线程并发执行，和CMS一样
- 空闲内存压缩时避免冗长的暂停时间
- 应用需要更多可预测的GC暂停时间
- 不希望牺牲太多的吞吐性能
- 不需要很大的Java堆

<hr>

2. 堆内存结构

   以往的垃圾回收算法，如CMS，使用的堆内存结构如下：

   ![](../assets/%E5%A0%86.png)

- 新生代：eden space + 2个survivor
- 老年代：old space
- 持久代：1.8之前的perm space
- 元空间：1.8之后的metaspace

​	这些space必须是地址连续的空间。

**在G1算法中，采用了另外一种完全不同的方式组织内存，堆内存被划分成多个大小相等的内存块，所以不同的分代从CMS的连续内存空间，到这里成为了逻辑上的分代**

​	在G1算法中，采用了另外一种完全不同的方式组织堆内存，堆内存被划分为多个大小相等的内存块（Region），每个Region是逻辑连续的一段内存，结构如下：

​	![](../assets/Region.png)

​	每个Region被标记了E、S、O和H，说明每个Region在运行时都充当了一种角色，其中H是以往算法中没有的，它代表Humongous，这表示这些Region存储的是巨型对象（humongous object，H-obj），当新建对象大小超过Region大小一半时，直接在新的一个或多个连续Region中分配，并标记为H。

<hr>

3. Region

   堆内存中一个Region的大小可以通过`-XX:G1HeapRegionSize`参数指定，大小区间只能是1M、2M、4M、8M、16M和32M，总之是2的幂次方，如果G1HeapRegionSize为默认值，则在堆初始化时计算Region的实践大小，具体实现如下：

   ![](../assets/region2.png)

   默认把堆内存按照2048份均分，最后得到一个合理的大小。

<hr>

4. GC 模式

   G1中提供了三种模式垃圾回收模式，young gc、mixed gc 和 full gc，在不同的条件下被触发。

- young gc

  ​	发生在年轻代的GC算法，一般对象（除了巨型对象）都是在eden region中分配内存，当所有eden region被耗尽无法申请内存时，就会触发一次young gc，这种触发机制和之前的young gc差不多，执行完一次young gc，活跃对象会被拷贝到survivor region或者晋升到old region中，空闲的region会被放入空闲列表中，等待下次被使用。

  | 参数                    | 含义                                |
  | ----------------------- | ----------------------------------- |
  | -XX:MaxGCPauseMillis    | 设置G1收集过程目标时间，默认值200ms |
  | -XX:G1NewSizePercent    | 新生代最小值，默认值5%              |
  | -XX:G1MaxNewSizePercent | 新生代最大值，默认值60%             |

- mixed gc

  ​	当越来越多的对象晋升到老年代old region时，为了避免堆内存被耗尽，虚拟机会触发一个混合的垃圾收集器，即mixed gc，该算法并不是一个old gc，除了回收整个young region，还会回收一部分的old region，这里需要注意：是一部分老年代，而不是全部老年代，可以选择哪些old region进行收集，从而可以对垃圾回收的耗时时间进行控制。

  ​	那么mixed gc什么时候被触发？

  ​	先回顾一下cms的触发机制，如果添加了以下参数：

  ```java
  -XX:CMSInitiatingOccupancyFraction=80 
  -XX:+UseCMSInitiatingOccupancyOnly
  ```

  当老年代的使用率达到80%时，就会触发一次cms gc。相对的，mixed gc中也有一个阈值参数 `-XX:InitiatingHeapOccupancyPercent`，当老年代大小占整个堆大小百分比达到该阈值时，会触发一次mixed gc.

  mixed gc的执行过程有点类似cms，主要分为以下几个步骤：

  **标记的详细过程：**

  1. initial mark: 初始标记过程，整个过程Stop The World，标记了从GC Root可达的对象
  2. concurrent marking: 并发标记过程，整个过程gc collector线程与应用线程可以并行执行，标记出GC Root可达对象衍生出去的存活对象，并收集各个Region的存活对象信息
  3. remark: 最终标记过程，整个过程Stop The World，标记出那些在并发标记过程中遗漏的，或者内部引用发生变化的对象
  4. clean up: 垃圾清除过程，如果发现一个Region中没有存活对象，则把该Region加入到空闲列表中

- full gc

  如果对象内存分配速度过快，mixed gc来不及回收，导致老年代被填满，就会触发一次full gc，G1的full gc算法就是单线程执行的serial old gc，会导致异常长时间的暂停时间，需要进行不断的调优，尽可能的避免full gc.

**只要并行收集，就没办法处理浮动垃圾**



### 关于方法区的GC

多人以为方法区（或者HotSopt VM中的永久代）是没有垃圾收集的，Java虚拟机规范中确实说过可以不要求虚拟机在方法区实现垃圾收集，而且性价比一般较低，在对的新生代生一般能回收70%~95%的空间，而永久代远低于此。

永久代的垃圾手机主要回收两部分内容：废弃常量和无用的类。 回收废弃常量与回收Java堆中的对象非常相似。以常量池中字面量的回收为例，若字符串“abc”已经进入常量池中，但当前系统没有任何String对象引用常量池中的“abc”常量，也没有其他地方引用该字面量，若发生内存回收，且必要的话，该“abc”就会被系统清理出常量池。常量池中其他的类（接口）、方法、字段的符号引用与此类似。

无用的类需要满足3个条件：

（1）该类所有的实例都已经被回收，即Java堆中不存在该类的任何实例； 
（2）加载该类的ClassLoader已经被回收； 
（3）该类对应的java.lang.Class对象没有在任何地方被引用，无法在任何地方通过反射访问该类的方法。

虚拟机可以对满足上述3个条件的无用类进行回收，此处仅仅是“可以”，而并不是和对象一样（不使用了就必然回收）

### 补充：触发Full GC的情况

##### System.gc()方法的调用

一种是System.gc()方法的调用，这是一种程序员对GC器的建议，并不是一定执行的，增加Full GC的频率 就会导致Stop-the-World的频繁，和时间变长

##### 老年代空间满了

老年代空间只有在新生代对象转入及创建为大对象、大数组时才会出现不足的现象，当执行Full GC后空间仍然不足，则抛出如下错误：
java.lang.OutOfMemoryError: Java heap space 

为避免以上两种状况引起的Full GC，调优时应尽量做到让对象在Minor GC阶段被回收、让对象在新生代多存活一段时间及不要创建过大的对象及数组。

##### 永生代空间满了

JVM规范中运行时数据区域中的方法区，在HotSpot虚拟机中又被习惯称为永生代或者永生区，Permanet Generation中存放的为一些class的信息、常量、静态变量等数据，当系统中要加载的类、反射的类和调用的方法较多时，Permanet Generation可能会被占满，在未配置为采用CMS GC的情况下也会执行Full GC。如果经过Full GC仍然回收不了，那么JVM会抛出如下错误信息：
java.lang.OutOfMemoryError: PermGen space 

为避免Perm Gen占满造成Full GC现象，可采用的方法为增大Perm Gen空间或转为使用CMS GC。





##### 补充一下CMS，对不同的分代用不同的收集算法，甚至是不同的收集器

CMS全称 `Concurrent Mark Sweep`，是一款并发的、使用标记-清除算法的垃圾回收器，
如果老年代使用CMS垃圾回收器，需要添加虚拟机参数-”XX:+UseConcMarkSweepGC”。

#### 使用场景：

GC过程短暂停，适合对时延要求较高的服务，用户线程不允许长时间的停顿。

#### 缺点：

服务长时间运行，造成严重的内存碎片化。
另外，算法实现比较复杂（如果也算缺点的话）



# JVM调优

JVM调优 其实我看就是 对xmn 

Java -Xms<size> 初始Java堆的大小

Java -Xmx<size> 最大Java堆的大小 

> 其实这个是最核心的命令

Java -Xss<size> Java线程堆栈大小

### **年轻代大小选择**

##### 响应时间优先的应用：

##### 吞吐量优先的应用：

响应时间优先的应用：尽可能设大，直到接近系统的最低响应时间限制（根据实际情况选择）。在此种情况下，年轻代收集发生的频率也是最小的。同时，减少到达年老代的对象。
吞吐量优先的应用：尽可能的设置大，可能到达Gbit的程度。因为对响应时间没有要求，

垃圾收集可以并行进行，一般适合8CPU以上的应用。

### **年老代大小选择**

响应时间优先的应用：年老代使用并发收集器，所以其大小需要小心设置，一般要考虑并发会话率和会话持续时间等一些参数。如果堆设置小了，可以会造成内存碎片、高回收频率以及应用暂停而使用传统的标记清除方式；如果堆大了，则需要较长的收集时间。最优化的方案，一般需要参考以下数据获得：

- 并发垃圾收集信息
- 持久代并发收集次数
- 传统GC信息
- 花在年轻代和年老代回收上的时间比例
- 减少年轻代和年老代花费的时间，一般会提高应用的效率
- 吞吐量优先的应用：一般吞吐量优先的应用都有一个很大的年轻代和一个较小的年老代。原因是，这样可以尽可能回收掉大部分短期对象，减少中期的对象，而年老代尽存放长期存活对象。



### **较小堆引起的碎片问题**

因为年老代的并发收集器使用标记、清除算法，所以不会对堆进行压缩。当收集器回收时，他会把相邻的空间进行合并，这样可以分配给较大的对象。但是，当堆空间较小时，运行一段时间以后，就会出现“碎片”，如果并发收集器找不到足够的空间，那么并发收集器将会停止，然后使用传统的标记、清除方式进行回收。如果出现“碎片”，可能需要进行如下配置：
-XX:+UseCMSCompactAtFullCollection：使用并发收集器时，开启对年老代的压缩。
-XX:CMSFullGCsBeforeCompaction=0：上面配置开启的情况下，这里设置多少次Full GC后，对年老代进行压缩

##### 补充：如何避免FullGC？

对与G1收集器 内存分配过快，mixedGC来不及回收，就会导致对象年龄增加 进入老年代，如果老年代

### 补充：两种错误类型如何解决

### StackOverFlow

### OutOfMemoryError

两种错误类型 



