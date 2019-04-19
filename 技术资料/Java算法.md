- 3,4,5集中 准备务必把代码敲熟练 敲习惯 从IO，Scanner，到数组 list转换数据类型转换，再到各种题型

  动态规划





## 算法

- 排序算法

  快速排序，归并排序，计数排序

  

  

- 动态规划

  背包问题，最长子序列，计数问题

- 基础技巧

  分治，倍增，二分，贪心





3天算法速成 笑死我了



## 算法：

- 我现在觉得斐波那契数列有点意思了

  ```
  有9元钱，规定：每次只可以花1元或2元钱，试问;共有多少种用法？
  分析： 一元钱只有一种用法，两元钱则有两种用法（2或1+1），三元钱可有三种用法（1+1+1，1+2，2+1），四元钱就有五种用法（1+1+1+1，1+2+1，2+1+1，2+2，1+1+2）……依次算下去，9元钱共有55种方法。
  
  遇到求总数，或者求总共几种用法的题目时，一定不要先把简单的题目想复杂，可以先试着推导几步看一看，是否是斐波那契数列或者斐波那契数列的变种。 
  兔子问题，母猪问题，花钱种数问题
  ```

  

## 排序算法

- 常用排序算法 记忆汇总

![img](http://blog.chinaunix.net/attachment/201201/18/21457204_1326898064RUxx.jpg)



```
比较类排序规则：
交换排序：冒泡排序，快速排序
插入排序：简单插入排序，shell（希尔）排序
选择排序：简单选择排序，堆排序
归并排序：二路归并排序，多路归并排序

非比较类排序，可以突破nlog2n的平均限制，线性时间非比较类排序 
（这个n是来自，对于每一个元素都要走一遍，对单个元素，反而像是查找）
基数排序，桶排序，计数排序
```

- 交换排序

  ```
  冒泡排序： 时间复杂度 最好O(n) 最坏O(n^2) 平均O(nlog2n) 空间复杂度 O(1)
  for(int i=0;i<nums.length;i++){
      for(int j=0;j<nums.length-1-i;j++){
          if(nums[j]>nums[j+1]){
              int temp=nums[j+1];
              nums[j+1]=nums[j];
              nums[j]=temp;
          }
      }   
  }
  // 排序完后，如果是从小到大排序，一定可以找到最大值
  ```

- 选择排序

  ```
  选择排序(Selection-sort)是一种简单直观的排序算法。
  它的工作原理：首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。
  选择排序： 时间复杂度 最好O(n^2) 最坏 O(n^2) 平均O(n^2) 空间复杂度 O(1)
  for(int i=0;i<nums.length;i++){
      int minIndex=i;
      int min=nums[i];
      for(int j=i+1;j<nums.length;j++){
          if(nums[j]<min){
              minIndex=j;
              min=nums[j];
          }
      }
      int temp=nums[i];
      nums[i]=min;
      nums[minIndex]=temp;  
  }
  //数据规模越小越好 一定能找到最大最小 将最小值和相应位置上的值进行交换
  ```

- 插入排序

  ```
  插入排序: 最好O(n) 最坏 O(n^2) 平均O(n^2) 空间复杂度O(1)
  for(int i=1;i<nums.length;i++){
  	int preindex=i-1;
  	int temp=nums[i];
     while(preindex>=0&&nums[preindex]>temp){
         nums[preindex+1]=nums[preindex];
         preindex--;
     }
     nums[preindex+1]=temp;
  }
  //与前一个比较，如果发现前一个比当今小，就不会进行插入，所以 最好为O(n)
  ```

- 插入排序（shell排序）

  ```java
  希尔排序：关键在设置增量 最好 O(n) 最坏O(n^2) 平均O(n^1.3)
  if(arrays == null || arrays.length <= 1){
              return;
          }
          //增量
          int incrementNum = arrays.length/2;
          while(incrementNum >=1){
              for(int i=0;i<arrays.length;i++){
                  //进行插入排序
                  for(int j=i;j<arrays.length-incrementNum;j=j+incrementNum){
                      if(arrays[j]>arrays[j+incrementNum]){
                          int temple = arrays[j];
                          arrays[j] = arrays[j+incrementNum];
                          arrays[j+incrementNum] = temple;
                      }
                  }
              }
              //设置新的增量
              incrementNum = incrementNum/2;
          }
      }//也是交换排序的一种，希尔排序
  ```

- 归并排序(分治法的典型应用)

  ```
  int nums[]=new int[100];
  public void merge(){
      
  }
  ```

  用归并要有额外的O(n)空间

- 快速排序

  ```java
  public void quickSort(int arr[],int left,int right){
      if(arr=null)
      return;
      while(left<right){
          int pivot=partition(arr,left,right);
          quickSort(arr,left,pivot-1);
          quickSort(arr,pivot+1,right);
      }  
  }
  
  private int partition(int[] arr,int left,int right){
      int temp=arr[left];
      while(left<right){
          while(left<right&&arr[right]>temp)
              --right;//继续查找，看看有没有不一样的
          // 如果有右值大于等于temp 这个基数 这时候就打断了
          arr[left]=arr[right];
          
          while(left<right&&arr[left]<=temp)
              ++left;
          arr[right]=arr[left];
          
      }
      arr[left]==temp;
      return left;
  }
  ```

  

## 搜索算法 

- 回溯、递归、剪枝搜索算法 其实就是查找算法

- 查找算法的分类（七种）

  ```
  插值查找：二分查找，插值查找，斐波那契查找
  其他：哈希查找，树表查找
  查找算法分类：
  1.静态查找和动态查找（动态静态都是针对查找表而言的，即在查找过程中是否需要对表进行改动）
  2.无序查找和有序查找（即被查找数列是否必须为有序数列）
  其他关键字：平均查找长度 ASL
  ```

- 顺序查找

  ```
  顺序查找适合于存储结构为顺序存储结构或链接存储结构的线性表
  顺序查找适合于存储结构为顺序存储或链式存储的线性表
  时间复杂度O(n) 空间复杂度O(0)
  就一个个的查找和比较
  想要快，就要空间换时间，或者找到个折中的办法
  ```

- 二分查找

  ```
  二分查找，元素必须是有序的，如果是无序的则要先进行排序操作。
  也被称为折半查找
  其实，和堆排序 和  排序的原理一致
  就是先和关键字比较，等于就m 小了就m-1 大了就m+1 然后重新计算中点
  时间复杂度为(log2n)
  折半查找的前提条件是需要有序表顺序排序，对于静态查找表，一次排序后不再变化，折半查找能得到不错的效率。但对于需要频繁执行插入或者删除的数据集来说，维护有序的排序会带来不小的工作量，所以动态表并不是太推荐使用二分查找。
  
  非递归版本
  public int BinarySearch(int nums[],int value){
      int high=nums.length-1;
      int low=0;
      int mid=(high+low)/2或者 low+(high-low)/2;
      Arrays.sort(nums);
      while(low<=high){
      mid=(high+low)/2或者 low+(high-low)/2;
      if(nums[mid]==value)
      return mid;
      if(nums[mid]>value)
      low=mid+1;
      if(nums[mid]<value)
      high=mid-1;    
      }
      return -1;    
  }
  
  递归版本
  public int BinarySearch(int nums[],int value,int low ,int high){
  	int mid=(high+low)/2或者 low+(high-low)/2;
  	if(nums[mid] ==value)
  		return mid;
  	if(nums[mid]>value)
  		return BinarySearch(nums,value,mid+1,high);
  		
  		if(nums[mid]<value)
  		return BianrySearch(nums,value,low,mid-1);
  }
  
  ```

  二分查找

  ```java
  public int BinarySearch(int arr[],int value,int low,int high){
      if(arr==null)
          return -1;
      if(low>high)
          return -1;
      int mid=(low+hight)/2;
      if(arr[mid]==value)
          return mid;
      if(arr[mid]>value)
          return BinarySearch(arr,value,low,mid-1);
      if(arr[mid]<value)
          return BinarySearch(arr,value,mid+1,high);
  }
  
  二分查找的前提是数组必须有序。
  ```

  

- 插值查找

  ```
  插值查找，改变了比例，比如二分查找一定是折半的
  mid=(low+high)/2, 即mid=low+1/2*(high-low);
  但是我们可以自行计算出 一个区间比例值
  将二分之一改成自适应的
  mid=low+(key-a[low])/(a[high]-a[low])*(high-low)，其实这里有点概率论的味道了
  让mid值更靠关键字key 间接减少了比较次数。
  如果数值中如果分布非常不均匀，那么其实和二分查找差不多
  插值查找时间复杂度O(log2log2n)
  
  递归版本
  public int InsertSearch(int nums[],int value,int low, int high){
      int mid=low+(value-nums[low]/nums[high]-nums[low])*(high-low)/2;
      if(nums[mid]==value)
      return mid;
      if(nums[mid]>value)
      return InsertSearch(nums,value,mid+1,high);
      if(nums[mid]<value)
      return InsertSearch(nums,value,low,mid-1);
  }
  ```

- 斐波那契查找

  ```
  斐波那契数列和0.618的关系，自己要知道
  斐波那契查找与折半查找很相似，他是根据斐波那契序列的特点对有序表进行分割的。他要求开始表中记录的个数为某个斐波那契数小1，及n=F(k)-1; 求得k值
  开始将k值与第F(k-1)位置的记录进行比较(及mid=low+F(k-1)-1),比较结果也分为三种
  1）相等，mid位置的元素即为所求
  2）>，low=mid+1,k-=2; 因为high=n=F(k-1)-1 这点初始条件时写好了
  说明：low=mid+1说明待查找的元素在[mid+1,high]范围内，k-=2 说明范围[mid+1,high]内的元素个数为n-(F(k-1))= Fk-1-F(k-1)=Fk-F(k-1)-1=F(k-2)-1个，所以可以递归的应用斐波那契查找。
  3）<，high=mid-1,k-=1。
  　　说明：low=mid+1说明待查找的元素在[low,mid-1]范围内，k-=1 说明范围[low,mid-1]内的元素个数为F(k-1)-1个，所以可以递归 的应用斐波那契查找。即调整n length的长度 用F(k)的大小，记住原理即好
  　　
  　　但是还有一点麻烦的是，n即nums.length 不总是和F(k)-1贴合的 所以找到这个k 对n有要求不通用
  　　但是 这个查找方法虽然范围限定，效果却很好为log2n最坏情况也是log2n
  ```

**其他：**

- 树表查找 

  ```
  查找不就等于搜索嘛？ 深度优先，广度优先 自己要先了解一下搜索和查找有什么区别
  ```

- 二叉查找树

  ```
  基本思想：二叉查找树是先对待查找的数据进行生成树，确保树的左分支的值小于右分支的值，然后在就行和每个节点的父节点比较大小，查找最适合的范围。 这个算法的查找效率很高，但是如果使用这种查找方法要首先创建树。 
  
  时间复杂度就是log2n，和二分查找一致，但是在最坏下还是有O(n)的时间复杂度，所以 直接用二分查找就好了
  堆则是一种完全二叉树
  ```

- 关于二叉查找树的实现

  ```
  二叉查找树并不能保持平衡，所以插入和查找最坏情况还是n
  另外二叉查找树需要有一个key值可以用于比较，因为存储的value不一定是可以比较的
  所以 
  	private Node root;
      private class Node
      {
          public Node Left { get; set; }
          public Node Right { get; set; }
          public int Number { get; set; }
          public TKey Key { get; set; }
          public TValue Value { get; set; }
  
          public Node(TKey key, TValue value, int number)
          {
              this.Key = key;
              this.Value = value;
              this.Number = number;//记录该节点及子结点个数的number值 空间换时间      
         }
      }
  也是中序遍历就可以出结果
  ```

- 平衡查找二叉树

  ```
  2-3 Tree
  和二叉树不一样，2-3树运行每个节点保存1个或者两个的值。对于普通的2节点(2-node)，他保存1个key和左右两个自己点。对应3节点(3-node)，保存两个Key，2-3查找树的定义如下：
  1）要么为空，要么：
  2）对于2节点，该节点保存一个key及对应value，以及两个指向左右节点的节点，左节点也是一个2-3节点，所有的值都比key要小，右节点也是一个2-3节点，所有的值比key要大。
  3）对于3节点，该节点保存两个key及对应value，以及三个指向左中右的节点。左节点也是一个2-3节点，所有的值均比两个key中的最小的key还要小；中间节点也是一个2-3节点，中间节点的key值在两个跟节点key值之间；右节点也是一个2-3节点，节点的所有key值比两个key中的最大的key还要大。
  
  2-3Tree的性质
  1.中序遍历得排列好的序列
  2.在一个完全平衡的2-3查找树中，根结点到每一个为空结点的距离都相同，因此最坏情况也有对数复杂度
  
  复杂度分析：
  - 最坏情况 就是所有节点都是2节点，查找为logN
  - 最坏 所有节点都是 3-node 查找效率为log3N 即clogN  c<1
  ```

- 平衡查找树 —— 红黑树

  ```
  2-3查找树能保证在插入元素之后能保持树的平衡状态，最坏情况下即所有的子节点都是2-node，树的高度为lgn，从而保证了最坏情况下的时间复杂度。但是2-3树实现起来比较复杂，于是就有了一种简单实现2-3树的数据结构，即红黑树（Red-Black Tree）。
  2-3树实现很复杂，理论上听起来很简单，但是怎么实现保持平衡 比如AVL树 就是靠旋转
  
  红黑树的思想，就是链接分为了两种，一种是普通的黑色链接，链接普通的2-3结点，一种是红色链接，链接两个2节点表示一个三节点，并且向左倾斜，后一个节点是前一个节点的左节点
  
  红黑树的定义：
  红黑树是一种具有红色和黑色链接的平衡查找树，同时满足：
  
  红色节点向左倾斜
  一个节点不可能有两个红色链接，否则就会违背第一条 红色节点向左倾斜
  整个树完全黑色平衡，即从根节点到所以叶子结点的路径上，黑色链接的个数都相同。
  
  红黑树的复杂度分析
  最好情况，整个树完全黑色平衡，从根结点到叶子结点的距离都相等
  最坏情况，就是 全部由三节点构成，即红黑相间的路径长度是全黑长度的两倍
  但是 
  红黑树依旧是最稳定的数据结构之一，
  插入删除查找 的时间复杂度 全都是对数级 clogN
  红黑树的应用之一就是 Java中的java.util.TreeMap,java.util.TreeSet；
  ```

- 2-3树和红黑树的基础上，他们每个结点都是2-3节点，红黑树则用黑色链条和红色链条来区分2节点和三节点，那么，如果单个节点的key值数量高于2呢？

- B树和B+树(B tree/B+ Tree)

  ```
  B树可以看成是对2-3查找树的一种扩展，即ta允许每个结点有M-1个节点，而M是我们自己定义的
  B树的性质：
  1. 根节点至少有两个子结点[2,M]个
  2.而非叶子结点最多就是M>2个
  3.然后 除非叶子结点儿子数[2/m,M]个
  
  
  插入：子结点关键字多了就往父结点送，父结点也多了，就往根结点送，根结点也多了，大于M就，把中间值为新的根结点，左右为新的非叶子结点
  ```

- 分块查找，又称索引顺序查找，它是顺序查找的一种改进方法

  ```
  将n个数据元素"按块有序"划分为m块（m ≤ n）。每一块中的结点不必有序，但块与块之间必须"按块有序"；即第1块中任一元素的关键字都必须小于第2块中任一元素的关键字；而第2块中任一元素又都必须小于第3块中的任一元素
  有点，归并排序，分治的思想
  step1 先选取各块中的最大关键字构成一个索引表
  step2 查找分两个部分，先对索引表进行二分查找或者顺序查找，然后再在块中进行顺序法查找
  ```

- 分块查找有点低等级哈希查找的感觉，但是都是用桶来装数据，分治的思想，可以看一下桶排序

- 哈希查找

  ```
  直接定址（哈希函数有哪些）
  解决冲突（解决冲突的办法）
  哈希函数的规则是：通过某种转换关系，使关键字适度的分散到指定大小的的顺序结构中，越分散，则以后查找的时间复杂度越小，空间复杂度越高。
  对 哈希聚集也很有毒的，必须分散度要高。
  算法流程：
  选定给的哈希函数构造哈希表
  根据选择的冲突处理方法解决地址冲突
  在哈希表的基础上执行哈希查找
  ```

- 哈希表，本身就是一个在时间和空间上做出权衡的经典例子。如果没有内存限制，那么可以直接将键作为数组的索引（需要很大一块连续内存），如果没有时间限制，我们可以用顺序查找。

  ```
  哈希表在两者做出了权衡，只需要调整哈希函数算法即可在时间和空间上做出取舍。
  单论查找复杂度：对于无冲突的hash表而言，查找复杂度为O(1)
  但是在运用hash查找之前
  我们首先要构建一个hash表，形成一个key值和数组索引或者存储地址之间的一个映射关系，并且这个记录表的大小通常是不固定的，所以典型的以空间换时间的解决方案。
  ```

  

  

  

- DFS(Deep First Search) 运用栈实现，

## 动态规划

- 动态规划与贪心算法的区别

  ```
  动态规划与贪心算法，都是一种递推算法，均有局部最优解来推导全局最优解
  不同：
  贪心算法中，作出的没步贪心决策无法改变，第i天的解必须源自第i-1天，贪心策略是由上一步的最优解推导下一步的最优解，而上一步之前的最优解不作保留。
  
  求最优解问题，从根本上来说是一种对解空间的遍历。
  最直接的暴力分析容易得到，最优解的解空间通常是以指数阶增长，因此无法暴力穷举
  最优解问题大部分问题都可以拆分成一个个的子问题，把解的遍历视为对子树的遍历，则以某种形式对树整个的遍历就可以求出最优解。
  
  贪心是动态规划方法的一个特例，贪心可以在，不需要关注上一步，不需要知道这个节点的所有子树情况就可以求出这个节点的值。
  贪心和动态规划本质上是对子问题树的一种修剪。两种算法要求问题都具有的一个性质就是“子问题最优性”。即，组成最优解的每一个子问题的解，对于这个子问题本身肯定也是最优的。
  
  动态规划方法代表了这一类问题的一般解法。我们自底向上（从叶子向根）构造子问题的解，对每一个子树的根，求出下面每一个叶子的值，并且以其中的最优值作为自身的值，其它的值舍弃。动态规划的代价就取决于可选择的数目（树的叉数）和子问题的的数目（树的节点数，或者是树的高度？）。
  
  贪心与动态规划都是找到最优解，只不过，贪心每一个字问题，子树的根的值与叶子无关，只取决于当前情况，所以不需要知道一个节点所有子树的情况，就可以求出这个节点的值，由于贪心算法的这个特性，它对解空间树的遍历不需要自底向上，而只需要自根开始，选择最优的路，一直走到底就可以了。
  ```

- 动态规划常用模型

  ```
  
  ```

  

## 图论