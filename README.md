# 面试经历
Java后端- Web开发 - 后台开发 面试笔记

# 头条一面

#### 介绍自己

#### 挑一个项目重点介绍

#### SpringBoot的项目

- 详细说一下你用的这用到的这几个技术，分别是怎么用的
- 整个项目中你认为最有技术价值的点在哪
- Spring的IOC和AOP知道吧，单实例无状态你怎么理解

- SortAndPage Repository接口你是怎么用的

- 分页和排序这两个功能，在Hibernate打印出来的SQL语句里是怎么样的？

- SQL语句怎么执行的知道吗，底层原理是什么？

  （…我就说其实和编译的原理类似，先扫描一遍索引

#### 数据库

- 通过SQL聊到数据库了
- 你谈谈你对数据库的事务是怎么理解的
- 四种隔离级别知道吗？
- 悲观锁和乐观锁呢？悲观和乐观锁的具体实现原理呢？
- 索引的种类知道吗？组合索引的最左匹配原则？
- 索引的底层原理呢？

#### 探讨题

- 假设现在给你几千万条数据，存在外存里，你如何找到最大的前100条，综合考虑时间和空间复杂度

  ```
  我答的是
  1.先联想到搜索选法的分块搜索，再想到排序的多路归并，所以可以先给数据分块，
  每次取个1万两万条放进内存，然后对这一万条数据用多路归并，时间复杂度应该是nlogn，
  然后取出这一万条的前一百条存进一个临时表或者干脆就放在内存里，
  然后对剩下的数据采取同样的操作。
  这样，就变成了100组 每组含100条有序数据的排序问题，
  刚好符合多路归并的条件，再多路归并取前100就好了。
  2.用哈希，也是联想到搜索有个哈希搜索，所以联想到桶排序。
  只有计数排序和桶排序可以突破nlogn的上线，
  之前的多路归并已经是效率最大值了，就是空间不是那么友好。
  所以，依旧是分块放进内存，用精心选取的hash函数把，
  数据映射到一个个桶里，对每个桶进行排序，
  然后把不为空的桶合并（这里可以用桶排序递归或者其他算法），
  但是这个算法的问题就是，空间开销太大，因为要维护一个映射表，还要新建一个临时数组。
  3.其实多路归并，不断的二分直到长度为1，
  时间复杂度和用堆排序差不多，但是堆排序更省空间，
  只需要维护一个100节点的最小堆，然后取出新的数据来，
  如果比最小堆的根大，就插入然后删除最小堆，堆顶结点。
  ```

  

# 头条二面

#### 介绍自己

#### 说一说项目

#### 重点介绍一下难点

#### 手撕代码

- [432123] ,[4321],[1234] 输入这样一个阶梯式的数组，如何找到最小值？

  ```
  因为普通遍历算法是暴力解法，对于这样一个部分有序，阶梯式的数组肯定不是最高效的。
  二分查找，插值查找，斐波那契查找都是需要数组有序，因此可以尝试这三种算法。
  分块查找适用于较大数据量，哈希查找和桶查找差不多，
  其实对于数据量较小比较复杂，空间开销比较大
  最后用二分查找实现了，即mid值需要小于前者和小于后者，
  如果mid-1>mid>mid+1 则往右半区找，
  如果mid-1<mid<mid+1则往左半区找，
  同时注意边界值，即mid-1<0 和mid+1>=arr.length的情况
  这道题完全做出来了，并且通过了所有测试用例
  ```

- 1->2->3->4->5->6这样一个链表，如何排序成1->6->2->5->3->4这种结构，注意是分别有序的，说一说思路

  ```
  这道题没让编程就是说思路，注意观察也是部分有序。
  1.两个队列实现，快慢指针找到中点然后，然后对后半部分进行反转
  2.反转链表，然后类似于二路归并的感觉，只不过不需要互相比较，逐个结点接起来。
  3.一个队列一个栈实现，进行反转。
  ```

#### 计算机网络

- 浏览器输入一个域名访问服务器的整体流程，尽可能说详细。

  这部分主要是，TCP连接时三次握手，四次挥手的过程，然后就是对DNS的分层结构也顺带提一下，并对HTTP协议，报文的结构和无状态的特性也提及了一点，最后将和TCP和UDP的区别说一下，并着重讲了TCP是面向字节流的。

#### 讨论题

- 假设有15个球，你可以每次拿1 or 2 or 3个，两个人，你第一次拿，对方也拿，然后如何保证最后一次也是自己拿？

  ```
  很明显这也是道思路题，第一开始我以为是考动态规划的，所以按硬币解法来做了
  但是我发现个问题就是，如果用动态规划的思想来做，我可以知道硬币总共有几种解法，
  也可以知道所拿硬币最多和最少的方法，但是策略？
  动态和贪心这两种策略，都是对问题解空间的遍历，
  只不过贪心是只走一条路，即每次拿最大/最小这个看问题问硬币最多还是最小来决定。
  动态是找到所有路，统计各种总额下的结果，对着解空间一层层遍历下去。
  其实就像是一条树，拿不同的球或者硬币就是链路上值不同，
  然后每一层都在这三种硬币中选一种最后得出所有解法，所以子树
  然后看每一条链路的深度，如果为奇数，就可以，偶数则不行。
  
  但是这个题不是，这个题是问怎么拿可以保证必赢，不是问你所有可能情况统计出来。
  好了，其实我答了一圈还是没想出来，其实策略是...算了，好像面试官也不知道
  因为他给出的是 先拿3个 这样剩个12 然后之后每次和对面拿一样的，怎么拿都必赢
  
  可以 3 2 2 3 3 2 这种解法，最后一次是对面拿了 我们输了 
  其实是f(i)=(f(i-1)==0||f(i-2)==0||f(i-3)==0);
  对 12 13 14 这三种情况 分别代入
  https://blog.csdn.net/Gease_Gg/article/details/82257907
  ```

  

### 其他的都没问，数据库，并发编程 JAVA容器乃至JVM模型

### 下一面可能会问操作系统，然后是一道Leetcode的Hard级别的算法题