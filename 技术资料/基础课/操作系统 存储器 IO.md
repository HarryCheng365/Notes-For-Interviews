# 操作系统基本概念

# 存储器管理

### 存储器的层次结构

​	多级存储器的层次结构如下：

![](../assets/%E5%A4%9A%E7%BA%A7%E5%AD%98%E5%82%A8%E5%99%A8%E5%B1%82%E6%AC%A1%E7%BB%93%E6%9E%84.png)

注：

- 主存储器（主存、内存）

  保存进程运行时的程序和数据。

- 寄存器

  访问速度最快、容量小、价格昂贵，位于CPU内，暂存指令、地址、数据等。

- 高速缓存

  容量大于寄存器，速度快于主存。利用程序执行的局部性原理，经主存中经常要用到的信息存放在高速缓存中，减少访问主存的次数。

- 磁盘缓存

  将频繁使用的磁盘数据暂时存放在磁盘缓存中，可以减少访问磁盘的次数。

### 程序的装入和链接

##### 整体流程图

![](../assets/%E7%A8%8B%E5%BA%8F%E7%9A%84%E8%A3%85%E5%85%A5%E5%92%8C%E9%93%BE%E6%8E%A5%E6%B5%81%E7%A8%8B.png)

##### 程序的装入

##### 绝对装入方式(Absolute Loading Mode) 

目标模块采用绝对地址。即逻辑地址和实际内存地址完全相同，装入时不需对地址进行变换。

特点：适用于单道环境。

##### 可重定位装入方式(Relocation Loading Mode)

一次重定位，但是程序运行时不能在内存中移动，程序需要连续的空间，难以共享

多道环境下，目标模块的起始地址通常从0开始。根据内存的当前情况，将目标模块装入到内存适当位置。采用静态地址变换。

静态地址变换：又称静态地址重定位，地址变换在程序装入时一次完成，以后不再改变。

特点：不需硬件支持，但程序运行时不能在内存移动，程序需要连续存储空间，难以共享。

![](../assets/%E9%9D%99%E6%80%81%E5%9C%B0%E5%9D%80%E5%8F%98%E6%8D%A2%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

##### 动态运行时装入方式(Dynamic Run-time Loading)

动态运行时的装入程序，在把装入模块装入内存后，并不立即把装入模块中的相对地址转换为绝对地址，而是把这种地址转换推迟到程序真正要执行时才进行。因此， 装入内存后的所有地址都仍是相对地址。 

重定位寄存器，逻辑地址，可以实现虚拟存储

运行时装入 需要硬件支持，就如同虚拟内存一样

特点：需要硬件支持。程序可以在内存中移动，可以实现虚拟存储。



现在计算机用的都是动态运行时装入的方式

![](../assets/%E5%8A%A8%E6%80%81%E5%9C%B0%E5%9D%80%E5%8F%98%E6%8D%A2%E7%A4%BA%E6%84%8F%E5%9B%BE.png)



### 静态链接与动态链接 ✅

1. 静态链接

   程序运行前，先将各目标模块以及它们所需的库函数，链接成一个完整的装入模块，不在拆开。

   有点静态代码块的感觉 

   JVM中的解析也是这一步，但是仅仅是获得直接引用 并不赋值

2. 装入时动态链接

   在装入时采用边装入边链接的方式装入。

   优点：便于修改和更新；便于实现目标模块共享

3. 运行时动态链接

   对模块的链接推迟到程序执行时才去进行链接。

   优点：加快装入过程；节省内存空间

### CPU通过MMU

界限地址寄存器和重定位寄存器，和数组差不多，基地址+指针）



### 内存分配管理方式

##### 分区概述

```
固定分区，将内存分为多个固定大小的分区，每个分区只能容纳一个进程，因此多道程序的，
动态分区，
再一个还有个适配的解决方案
1.首次适应
2.最佳适应
3.最差适应
无论是固定分区还是动态分区，都会存在内存碎片的问题，分区的解决方案，无论是内部碎片还是外部碎片
```

##### 连续分配方式

1. 简介

   连续分配方式，是指为一个用户程序分配一个连续的内存空间。包括单一连续分配、固定分区分配、动态分区分配、动态重定位分配

2. 单一连续分配

   把内存分为系统区和用户区两部分，系统区仅提供给OS使用，通常是放在内存的低址部分；用户区是指除系统区以外的全部内存空间，提供给用户使用。 

   特点：最简单；适用于单用户、单任务的OS

3. 固定分区分配

   - 简介

     ​	固定分区分配是多道程序系统中采用的一种最简单的存储管理方法。预先将内存空间划分为若干个固定大小的分区，每个分区中可以装入一道程序。分区的位置及大小在运行期间不能改变。各分区的大小可以相等，也可以不相等。

     ​	两个固定：

     	1、各分区的大小固定不变；

     	2、总分区的个数固定不变

     常用的空闲分区管理的数据结构有：空闲分区表和空闲分区链。

   - 分区使用表

     用一个空闲分区表来登记系统中的空闲分区，其表项类似于固定分区。空闲分区表示意图如下：

     ![](../assets/%E7%A9%BA%E9%97%B2%E5%88%86%E5%8C%BA%E8%A1%A8.png)

     

   - 空闲分区链

     将内存中的空闲分区以链表方式链接起来，构成空闲分区链。空闲分区链示意图如下：

     ![](../assets/%E7%A9%BA%E9%97%B2%E5%88%86%E5%8C%BA%E9%93%BE.png)

   - **分区分配算法**

     ​	不论是空闲分区链管理还是空闲分区表管理，链和表中的空闲区都可按一定规则排列，例如按空闲区从大到小排，以方便空闲区的查找和回收。

     **一个分区装配一个程序**

     ​	常用的动态分区管理的分配算法有：首次适应算法、循环首次适应算法、最佳适应算法、最坏适应算法。

     - 首次适应算法

       ​	首次适应算法又称最先适应算法，**该算法要求空闲分区按地址递增的次序排列**。

       ​	在进行内存分配时，从空闲分区表（或空闲分区链）首开始顺序查找，直到找到第一个能满足其大小要求的空闲分区为止。然后，再按照作业大小，从该分区中划出一块内存空间分配给请求者，余下的空闲分区仍然留在空闲分区表（或空闲分区链）中。

       ​	特点：优先利用内存低地址端，高地址端有大空闲区。但低地址端有许多小空闲分区时会增加查找开销。

     - 循环首次适应算法

       ​	循环首次适应算法又称下次适应算法，它是首次适应算法的变形。**空闲分区仍然是按地址递增的次序排列。**

       ​	该算法在为进程分配内存空间时，从上次找到的空闲分区的下一个空闲分区开始查找，直到找到第一个能满足其大小要求的空闲分区为止。然后，再按照作业大小，从该分区中划出一块内存空间分配给请求者，余下的空闲分区仍然留在空闲分区表（或空闲分区链）中。

       ​	特点：使存储空间的利用更加均衡，但会使系统缺乏大的空闲分区。

     - 最佳适应算法

       ​	**最佳适应算法要求空闲分区按容量大小递增的次序排列。**

       ​	在进行内存分配时，从空闲分区表（或空闲分区链）首开始顺序查找，直到找到第一个能满足其大小要求的空闲分区为止。如果该空闲分区大于作业的大小，则从该分区中划出一块内存空间分配给请求者，将剩余空闲区插入到空闲分区表（或空闲分区链）中的适当位置。

       ​	按最佳适应算法为作业分配内存，就能把既满足作业要求又与作业大小最接近的空闲分区分配给作业。

       ​	特点：保留了大的空闲区。但分割后的剩余空闲区很小。

     - 最坏适应算法

       ​	**最坏适应算法要求空闲分区按容量大小递减的次序排列。**

       ​	在进行内存分配时，先检查空闲分区表（或空闲分区链）中的第一个空闲分区，若第一个空闲分区小于作业要求的大小，则分配失败；否则从该空闲分区中划出与作业大小相等的一块内存空间分配给请求者，余下的空闲分区仍插入到空闲分区表（或空闲分区链）中的适当位置。

       ​	特点：分区分配之后剩下的空闲区域（新空闲区）比较大，但当大作业到来时，其存储空间的申请往往得不到满足。

   - 分区回收

     回收分区时，应将空闲区插入适当位置，此时待回收的分区可能会出现如下四种情况：

     - 回收分区r上面邻接一个空闲分区

       此时应将回收区r与上邻接分区F1合并成一个连续的空闲区。合并分区的首地址为空闲区F1的首地址，其大小为二者之和。总的空闲分区数不变。

     - 回收分区r下面邻接一个空闲分区

       此时应将回收区r与下邻接分区F2合并成一个连续的空闲区。合并分区的首地址为回收分区r的首地址，其大小为二者之和。总的空闲分区数不变。

     - 回收分区r上面、下面各邻接一个空闲分区

       此时应将回收区r与上、下邻接分区合并成一个连续的空闲区。合并分区的首地址为与r上邻接空闲区F1的首地址，其大小为三者之和，且应将与r下邻接的空闲区F2从空闲分区表(或空闲分区链)中删去。总的空闲分区数减少一个。

     - 回收分区r不与任何空闲分区相邻

       这时应为回收区单独建立一个新表项，填写分区大小及起始地址等信息，并将其加入到空闲分区表(或空闲分区链)中的适当位置。总的空闲分区数增加一个。

4. 动态分区分配

   优点：相对灵活，没有固定分区中程序数目的限制和程序大小的限制。

   缺点：每道程序总是要求占用主存的连续存储区域，主存中会产生许多碎片（外碎片）。

5. 伙伴系统

   ​	固定分区存储管理限制了内存中的进程数，动态分区的拼接需要大量时间，而伙伴系统是一种较为实用的动态存储管理办法。伙伴系统**采用伙伴算法对空闲内存进行管理**。该方法**通过不断对分大的空闲存储块来获得小的空闲存储块。当内存块释放时，应尽可能合并空闲块**。

   ​	设系统初始时可供分配的空间为2m个单元（作为一个空闲块）。当进程申请大小为n的空间时，设2^i-1<n≤2^i，则为进程分配大小为2^i的空间。如系统不存在大小为2^i 的空闲块，则查找系统中是否存在大于2^i

   的空闲块，若找到则对其进行对半划分，直到产生大小为2^i的空闲块为止。

   ​	当一块被分成两个大小相等的块时，这两块称为伙伴。当进程释放存储空间时，应检查释放块的伙伴是否空闲，若空闲则合并。合并后得到的较大的空闲块也可能存在空闲伙伴，此时也应合并。重复上述过程，直至没有可以合并的伙伴为止。

   ​	伙伴系统的缺点：分配和回收时需要对伙伴进行分拆及合并，增加了系统开销（但好于拼接）；存储空间有浪费。

6. 动态重定位分配

   ​	连续分配方式中，必须把作业装入到一片连续的内存空间中。这种分配方法能满足多道程序设计的需要，但存在碎片问题。碎片也可称为零头，是指内存中无法被利用的存储空间。

   - **内部碎片是指分配给作业的存储空间中未被利用的部分。**
   - **外部碎片是指系统中无法利用的小存储块。**

   ​	**拼接**：解决碎片问题的办法之一，即通过移动内存中的进程，把多个分散的小分区拼接成一个大分区，也可称为紧缩或紧凑。拼接的缺点是要耗费大量处理机时间。

   **紧缩 或者拼凑的方式，这个其实如同标记整理法，将存活的对象向一侧移动** 

   ​	拼接示意图如下：

   ![](../assets/%E6%8B%BC%E6%8E%A5%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

   ​	拼接要解决的技术问题：空闲区放在何处：拼接后的空闲区放在何处不能一概而论，应根据移动信息量的多少来决定。

   ​	拼接的时机：
   
- 每次回收分区时拼接。只有一个空闲区，但拼接频率过高增加系统开销。
   - 找不到足够大的空闲区且系统空闲空间总量能满足要求时拼接。拼接频率小于前者，空闲区管理稍复杂。也可以只拼接部分空闲区。

   

   
   
   ​	**动态重定位**：拼接后程序在内存的位置发生变化，因此需要动态重定位技术支持。其示意图如下：
   
   动态运行时装入的实现？
   
   ​	![](../assets/%E5%8A%A8%E6%80%81%E9%87%8D%E5%AE%9A%E4%BD%8D%E7%A4%BA%E6%84%8F%E5%9B%BE.png)
   
   

##### 基本分页存储管理方式 

内存管理单元（MMU）管理着地址空间和物理内存的转换，其中的页表（Page table）存储着页（程序地址空间）和页框（物理内存空间）的映射表。

##### 虚拟内存

一个虚拟地址分成两个部分，一部分存储页面号，一部分存储偏移量。

下图的页表存放着 16 个页，这 16 个页需要用 4 个比特位来进行索引定位。例如对于虚拟地址（0010 000000000100），前 4 位是存储页面号 2，读取表项内容为（110 1），页表项最后一位表示是否存在于内存中，1 表示存在。后 12 位存储偏移量。这个页对应的页框的地址为 （110 000000000100）。

​	



​	分页式存储管理允许把一个作业存放到若干不相邻接的分区中：

- 免去移动信息
- 充分利用主存空间

无论哪种分区方式 或者分区分配算法 都会出现碎片问题

​	连续分配方式存在碎片，而紧凑技术开销太大，若能取消作业对存储区的连续性要求，则能较好地解决碎片问题。分页存储管理就是基于这一思想提出的。

​	在分页存储管理中，**将进程的逻辑地址空间划分成若干大小相等的页（或称页面），相应地将主存空间也划分成与页大小相等的块（或称物理块、页框）**。在为进程分配存储空间时，总是以块为单位来分配，即将进程中的某一页存放到主存的某一空闲块中。

​	在页面大小的选择上：页面的大小应适中。若页面太大，以至和一般进程大小相差无几，则页面分配退化为：分区分配，同时页内碎片也较大。若页面太小，虽然可减少页内碎片，但会导致页表增长。因此，页面大小应适中，通常为2的幂，一般在512B到8KB之间。

**页面太小，太大则会产生页内碎片，也表增长也会频繁的IO取表**

​	**为了在内存中找到进程的每个页面所对应的物理块，系统为每个进程建立一张页面映象表，简称页表。**

​	页表：记录页面在内存中对应物理块的数据结构。页表一般存放在内存中。页表的结构如下：

![](../assets/%E9%A1%B5%E8%A1%A8%E7%9A%84%E4%BD%9C%E7%94%A8.png)

​	此外，基于分页式存储管理方式还需要借助地址变换机构。地址变换机构的任务是实现逻辑地址到物理地址的变换，即将逻辑地址中的页号转换为内存中的物理块号。

​	现代计算机系统都支持非常大的逻辑地址空间，致使页表很大，用连续空间存放页表不现实。将页表再分页，使每页与内存物理块大小相同，并为它们进行编号0、1、…，同时还为离散存放的页表建立一张页表，称为外层页表。即两级页表、多级页表。

多级页面，其实就是个二层索引，逻辑地址的前几位代表也表号，拿页表号 去映射表取物理地址，逻辑地址的后几位为偏移量，物理地址+偏移量即可在物理内存中索引到

##### 基本分段存储管理方式 

​	引入分段存储管理，主要是满足用户需求：

- 方便编程，模块化程序设计中，程序或数据被划分成若干个大小不等，具有独立逻辑意义的分段。
- 以分段为单位进行管理，便于共享和保护，可以实现动态链接及段的动态增长。

**动态连接 就是为了模块可以共享，在运行期间才链接到具体地址**

​	在分段存储管理系统中，作业的地址空间由若干个逻辑分段组成，每个分段是一组逻辑意义相对完整的信息集合，每个分段都有自己的名字，每个分段都从0开始编址并采用一段连续的地址空间。

​	在进行存储分配时，以段为单位分配内存，每段分配一个连续的内存区，但各段之间不要求连续。

**但各段之间不连续，连续了不就是分区内存管理了，又会产生碎片问题**

​	为了实现从逻辑地址到物理地址的变换，必须为每个进程建立一个段表，用来记录每段在内存的起始地址及相关信息。其中每个表项描述一个分段的信息，至少包含：段号、段长、段在内存的起始地址、其他信息，段表一般存放在内存。段表结构如下：



用来记录每段在内存中的起始地址以及相关信息



![](../assets/%E6%AE%B5%E8%A1%A8%E7%BB%93%E6%9E%84.png)

​	为实现从逻辑地址到物理地址的转换，在系统中设置了段表寄存器，用于存放段表始址和段表长度。

段号，段长，段的起始物理地址

​	**分段与分页的区别：**

- 页是信息的物理单位，是为了减少内存碎片及提高内存利用率，是系统管理的需要。段是信息的逻辑单位，它含有一组意义相对完整的信息，分段的目的是为了更好地满足用户的需要。

- 页的大小固定且由系统决定，由硬件把逻辑地址划分为页号和页内地址两部分。段的长度不固定且由用户所编写的程序决定，通常由编译系统在对源程序进行编译时根据信息的性质来划分。

- 分页系统中作业的地址空间是一维的，分段系统中作业的地址空间是二维的（因为有长度）

  分段系统中作业的地址空间是二维的（因为有长度）

# 页调度算法

### FIFO 先进先出调度算法

没什么好说的 

### LRU 最近最少使用调度算法

- 调入也算调用一次
- 相同使用次数，替换掉最近的
- 没有其实不统计历史使用次数，就替换掉存在在cache空间里的最少使用的页



