## 剑指Offer

剑指Offer中题出现的频率很高



### 1. 二维数组查找 ✅

```java
//二维数组的二分搜索，多维的二分搜索，关键就在一个部分有序或者的列表里，找到一个有序序列并排除掉，比如
//1 2 8 9
//2 4 9 12 这种二维数组 就是 1 2 8 9 （拐个弯）12有序 
//有序 二分，某种形式的二分，可以是自己找二分点，也可能是题目中给出了二分点。
//当然除了递归的做法，还有迭代的做法，这点就不多说了，我做过
//对了，当然可以把每一行看成一维数组，然后通过检查头尾的方式 排除掉不符合条件的行 然后查找
//当然了，还有一种变形，就是查找数组中第k大的元素。可以用这个选取右上角的方式，进行变形。
public class Solution {
    public boolean Find(int target, int [][] array) {
        if(array==null||array.length==0||array[0].length==0)
            return false;
        int rowLength=array[0].length;
        int lineLength=array.length;
        if(target>array[lineLength-1][rowLength-1])
            return false;
        return search(array,rowLength-1,0,target);

    }
    
    public boolean search(int [][]array,int row,int line,int target){
        if(row<0||line>array.length-1)
            return false;
        if(array[line][row]==target)
            return true;
        if(array[line][row]>target)
            return search(array,row-1,line,target);
        if(array[line][row]<target)
            return search(array,row,line+1,target);
        return false;
    }
        
    
}
```

### 2. 替换空格 ✅

```java
//我自己写的
public class Solution {
    public String replaceSpace(StringBuffer str) {
        if(str.length()<=0)
            return "";
        int length=str.length();
        StringBuilder strb = new StringBuilder();
        for(int i =0;i<length;i++){
            if(str.charAt(i)==' '){
                strb.append('%');
                strb.append('2');
                strb.append('0');
            }
            else
                strb.append(str.charAt(i));    
        }
        return strb.toString();
 	
    }
}
//第一种思路：时间复杂度O(n) 空间复杂度O(n)，其实思路我已经体会到了 String 在Java里是一个final类型，所以 每次对String
//的更改，都需要对String进行一个copy 再生成一个新的final类型的值
//第二种思路：扫描一遍数组先，不对，数组扩容还是要copy，不如用可变数组，更简单
public class Solution {
    public String replaceSpace(StringBuffer str) {
        if(str==null){
            return null;
        }
        
       StringBuilder newStr = new StringBuilder();
        for(int i=0;i<str.length();i++){
            if(str.charAt(i)==' '){
                newStr.append('%');
                newStr.append('2');
                newStr.append('0');
            }else{
                newStr.append(str.charAt(i));
            }
        }
        return newStr.toString();
    }
}
//其实主要是熟悉 StringBuffer和StringBuilder这两个类的操作
```

### 3.从尾到头输出链表值 ✅

```java
import java.util.*;
public class Solution {
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        ArrayList<Integer> list = new ArrayList<>();
        if(listNode==null||listNode.next==null)
            return list;
        Stack<Integer> stack = new Stack<>();
        while(listNode!=null){
            stack.add(listNode.val);
            listNode=listNode.next;
        }
        while(!stack.isEmpty()){
            list.add(stack.pop());
        }
        return list;    
    }
}
//递归本质上就是一个栈结构，于是很自然地想到了用递归来实现，而这个递归，可以参考二叉树的后序遍历
//只不过这里把思想用在了单链表上。
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        ArrayList<Integer> list = new ArrayList<Integer>();
        if(listNode==null)
            return list;
        return print(list,listNode);
       
    }
    
    public ArrayList<Integer> print(ArrayList<Integer> list ,ListNode listNode){
        if(listNode.next==null){
            list.add(listNode.val);
            return list;
        }
        print(list,listNode.next);
        list.add(listNode.val);
        return list;                
    }
}
```

### 4.前序和中序遍历 重建二叉树 ✅

```java
public class Solution {
     
     
    public TreeNode reConstructBinaryTree(int [] pre,int [] in) {
               if(pre==null||in==null){
            return null;
        }
 
        java.util.HashMap<Integer,Integer> map= new java.util.HashMap<Integer, Integer>();
        for(int i=0;i<in.length;i++){
            map.put(in[i],i);
        }
        return preIn(pre,0,pre.length-1,in,0,in.length-1,map);
    }
     
      public TreeNode preIn(int[] p,int pi,int pj,int[] n,int ni,int nj,java.util.HashMap<Integer,Integer> map){
 
        if(pi>pj||ni>nj){
            return null;
        }
        TreeNode head=new TreeNode(p[pi]);
        int index=map.get(p[pi]);//是的 我就在想怎么省去查找这一步
        head.left=preIn(p,pi+1,pi+index-ni,n,ni,index-1,map);
        head.right=preIn(p,pi+index-ni+1,pj,n,index+1,nj,map);
          //这是把左右子树列出来了 ni是最左值，也是起始值
          //如果pi+index-n1+1 取根的右面，如果小了
        return head;
    }
}


//第二种解法，其实就是分别找到左右子树，然后通过遍历for循环来找到根结点，if(in[i]==pre[startPre])是用来找到根结点的，但是可以用 一个hashmap来替代
//其实这个题，就是寻找到左右子树，对根结点进行建树的操作，自顶而上的。
//主要搞明白左右结点的位置
public class Solution {
    public TreeNode reConstructBinaryTree(int [] pre,int [] in) {
        TreeNode root=reConstructBinaryTree(pre,0,pre.length-1,in,0,in.length-1);
        return root;
    }
    //前序遍历{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}
    private TreeNode reConstructBinaryTree(int [] pre,int startPre,int endPre,int [] in,int startIn,int endIn) {
         
        if(startPre>endPre||startIn>endIn)
            return null;
        TreeNode root=new TreeNode(pre[startPre]);
         
        for(int i=startIn;i<=endIn;i++)
            if(in[i]==pre[startPre]){
                root.left=reConstructBinaryTree
                (pre,startPre+1,startPre+i-startIn,in,startIn,i-1);
                root.right=reConstructBinaryTree
                (pre,i-startIn+startPre+1,endPre,in,i+1,endIn);
                      break;
            }
                 
        return root;
    }
}

//二叉树前序遍历的迭代实现

```

### 5.旋转数组最小数字 ✅

```java
import java.util.ArrayList;
public class Solution {
    public int minNumberInRotateArray(int [] array) {
        if(array==null||array.length==0)
            return 0;
        if(array.length==1)
            return array[0];
        for(int i=0;i<array.length-1;i++){
            if(array[i]>array[i+1])
                return array[i+1];     
        }
        return 0;
    }
}
```

### 6.斐波那契数列 ✅

```java
//递归是不带记忆的，考虑到带记忆的解法，所以往dp上靠拢，找到父子状态的转移方程，所以
//dp一般是不递归的
public class Solution {
    public int Fibonacci(int n) {
        if(n<=0)
            return 0;
        if(n==1)
            return 1;
        else
            return Fibonacci(n-1)+Fibonacci(n-2);
    }
}
//带记忆的斐波那契数列，不用递归的方法，用遍历方式做，其实一样，就是找到
//子父状态的转移
public class Solution {
    public int Fibonacci(int n) {
        if(n==0)
            return 0;
        if(n==1)
            return 1;       
        int []result =new int[n+1];
        result[0]=0;
        result[1]=1;
        for(int i=2;i<result.length;i++){
            result[i]=result[i-1]+result[i-2];
        }
        return result[n];
    }
}
```

### 7.跳台阶✅

```java
public class Solution {
    public int JumpFloor(int target) {
        if(target <= 0) return 0;
        if(target == 1) return 1;
        if(target == 2) return 2;
        int one = 1;
        int two = 2;
        int result = 0;
        for(int i = 2; i < target; i++){
            result = one+ two;
            one = two;
            two = result;
        }
        return result;
    }
}
//这种简单dp 找到父子状态转移方程就好了。
public class Solution {
    public int JumpFloor(int target) {
        
        int []coin = {1,2};
        
        int []result=new int[target+1];
        if(target==0)
            return 0;
        if(target==1)
            return 1;
        if(target==2)
            return 2;
        result[0]=0;
        result[1]=1;
        result[2]=2;
        for(int i=3;i<result.length;i++){
            for(int j=0;j<coin.length;j++){
                result[i]+=result[i-coin[j]];
            }
        }
        return result[target];
    }
}

```

### 8.跳台阶2✅

```java
//这个也是非递归 带记忆的dp 找到子父状态转移方程就好
public class Solution {
    public int JumpFloorII(int target) {
        if(target==0)
            return 0;
        if(target==1)
            return 1;
        
        int []result=new int[target+1];
        result[0]=1;
        result[1]=1;
        for(int i=2;i<result.length;i++){
            for(int j=i;j>0;j--){
                result[i]+=result[i-j];
            }
        }
        return result[target];      
    }
}
```

### 9.矩形覆盖✅

```java
我们可以用2*1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2*1的小矩形无重叠地覆盖一个2*n的大矩形，总共有多少种方法？
public class Solution {
    public int RectCover(int target) {
        if(target==0)
            return 0;
        if(target==1)
            return 1;
        if(target==2)
            return 2;
        return RectCover(target-1)+RectCover(target-2);
    }//要么用2的边，要么用1的边，也是个dp问题。要么横着 要么竖着，不可能横竖在一起，所以这道题理解有
    //点难
}其实是个理解问题


```

### 10.二进制中1的个数 ✅

```java
public class Solution {
    public int NumberOf1(int n) {
        int count=0;
        while((n^0)!=0){
            if((n&1)==1){
                count++;    
        }
             n=n>>>1;       
        }       
        return count;
    }
}
```

### 11.数值的整数次方

```
给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。
public class Solution {
    public double Power(double base, int exponent) {
        return Math.pow(base,exponent);
        
  }
}
或者
```

### 12.调整数组顺序使奇数位于偶数前面 ✅

```java
//冒泡排序变形
public class Solution {
    public void reOrderArray(int [] array) {
        if(array==null||array.length==0)
            return;
        if(array.length==1)
            return;
        for(int i=0;i<array.length;i++){
            for(int j=0;j<array.length-i-1;j++){
                if((array[j]%2==0)&&(array[j+1]%2!=0)){
                    int temp=array[j];
                    array[j]=array[j+1];
                    array[j+1]=temp;
                }          
            }
        }
        
    }
}
```

### 13.链表中倒数第k个结点 ✅

```java
import java.util.*;
public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
        if(k==0)
            return null;
        if(head==null)
            return null;
        Stack<ListNode> newStack = new Stack<>();
        while(head!=null){
            newStack.push(head);
            head=head.next;
        }
        if(k>newStack.size())
            return null;
        while(k>1){
            newStack.pop();
            k--;
        }     
        return newStack.pop();       
    }      
}

//当然，可以用递归进行实现，但这里涉及到了一个传值，传引用的问题
//java中，执行方法之前，编译器会将参数的值压入堆栈之中，其中引用数据类型压入的为
//引用变量的地址。方法结束后，编译器会将之前压入堆栈的引用变量的地址重新赋值给引用变量
//故而java中的方法不能改变引用变量参数的地址，除非包裹在一个上层容器中，进而指向同一个对象，
//仅仅改变同一个对象的属性值

//递归实现版本，其实是参考了 反转链表，因为肯定要用到栈的结构
import java.util.*;
public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
        if(k<=0||head==null)
            return null;
        ArrayList<ListNode> list = new ArrayList<>();
        int temp=Find(head,k,list);
        if(temp<k)
            return null;
        else
            return list.get(0);

    }
    public int Find(ListNode head,int k, ArrayList<ListNode> list){
        if(head==null)
            return 0;
        int temp = Find(head.next,k,list);
        temp++;
        if(temp==k)
            list.add(head);
        return temp;
    }
}

```

### 14.反转链表 ✅

```java
//递归实现
public class Solution {
    public ListNode ReverseList(ListNode head) {
        if(head==null)
            return head;
        return reverse(head);

    }
    
    public ListNode reverse(ListNode head){
        if(head.next==null)
            return head;
        
        ListNode temp =reverse(head.next);
        head.next.next =head;
        head.next=null;
        return temp;
        
    }
}

//非递归实现

public class Solution {
    public ListNode ReverseList(ListNode head) {
       if(head==null)
           return head;
       ListNode tempHead=head;
       ListNode pre =null;
        while(tempHead.next!=null){
            ListNode next=tempHead.next;
            tempHead.next=pre;
            pre=tempHead;
            tempHead=next;
        }
        tempHead.next=pre;
        return tempHead;

    }  
}
//不要一步迈的脚步太大了！
```



### 15.合并两个排序链表 ✅ 

```
Leetcode刷过
```



### 16.树的子结构

```java
//和判断树是否对称有点类似
public class Solution {
    public boolean HasSubtree(TreeNode root1,TreeNode root2) {
        if(root2==null||root1==null)
            return false;
        boolean flag=isSubtree(root1,root2);
        if(!flag){
            flag=HasSubtree(root1.left,root2);
            if(!flag)
                flag=HasSubtree(root1.right,root2);
        }
        return flag;
        
        
    }
    public boolean isSubtree(TreeNode root1,TreeNode root2){
        if(root1==null&&root2!=null)
            return false;
        if(root2==null)
            return true;
        if(root1.val==root2.val)
        {
            return isSubtree(root1.left,root2.left)&&isSubtree(root1.right,root2.right);
        }
        else    
            return false;
        
    }
}

```

### 17.二叉树镜像，二叉树反转

```java
public class Solution {
    public void Mirror(TreeNode root) {
        if(root ==null)
            return;
       root=mirror(root);    
    }
    public TreeNode mirror(TreeNode root){
        if(root ==null)
            return null;
        TreeNode temp =root.left;
        root.left=mirror(root.right);
        root.right=mirror(temp);
        return root;
        
    }
}
```

### 18.顺时针打印矩阵 ⚠️

```
没什么技术含量，就是个数学问题
```

### 19.包含Min数的栈

```java
用PriorityQueue 和 栈两个容器实现
import java.util.*;

public class Solution {

    PriorityQueue<Integer> queue = new PriorityQueue<>();
    Stack<Integer> stack = new Stack<Integer>();
    public void push(int node) {
        stack.push(node);
        queue.offer(node);
        
            
    }
    
    public void pop() {
        int temp=stack.pop();
        queue.remove(temp);
        
    }
    
    public int top() {
        if(!stack.isEmpty())
        return stack.peek();
        else
           return  -1;
        
    }
    
    public int min() {
        if(!queue.isEmpty())
        return queue.peek();
        else
            return -1;
        
    }
}
```

### 19.出栈，入栈序列 ⚠️

```java
import java.util.ArrayList;
import java.util.Stack;
public class Solution {
    public boolean IsPopOrder(int [] pushA,int [] popA) {
        if(pushA.length == 0 || popA.length == 0)
            return false;
        Stack<Integer> s = new Stack<Integer>();
        //用于标识弹出序列的位置
        int popIndex = 0;
        for(int i = 0; i< pushA.length;i++){
            s.push(pushA[i]);
            //如果栈不为空，且栈顶元素等于弹出序列
            while(!s.empty() &&s.peek() == popA[popIndex]){
                //出栈
                s.pop();
                //弹出序列向后一位
                popIndex++;
            }
        }
        return s.empty();
    }//模拟出栈序列
}
```

### 20.二叉树||多叉树 层序遍历 BFS用队列，DFS用栈

```java
import java.util.*;
public class Solution {
    public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
        ArrayList<Integer> list =new  ArrayList<Integer>();
        if(root==null)
            return list;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while(!queue.isEmpty()){
            TreeNode temp = queue.poll();
            if(temp.left!=null)
                queue.add(temp.left);
            if(temp.right!=null)
                queue.add(temp.right);
            list.add(temp.val);
        }
        return list;
       
        
    }
}
```

### 21.二叉搜索树的后序遍历序列

```java
public class Solution {
    public boolean VerifySquenceOfBST(int [] sequence) {
        if(sequence.length == 0) return false;
        return IsTreeBST(sequence, 0, sequence.length-1);
    }
    public boolean IsTreeBST(int [] sequence,int start,int end ){
        if(end <= start) return true;
        int i = start;
        for (; i < end; i++) {
            if(sequence[i] > sequence[end]) break;
        }
        for (int j = i; j < end; j++) {
            if(sequence[j] < sequence[end]) return false;
        }
        return IsTreeBST(sequence, start, i-1) && IsTreeBST(sequence, i, end-1);
    }//然后 分别对左右子树再进行验证，其实和二分搜索或者二叉树反转 二叉树对称的思想差不多
}
```

### 22.二叉树中和为某一值的路径 ⚠️

```

```

### 23.复杂链表的复制 ⚠️

```java
*解题思路：
*1、遍历链表，复制每个结点，如复制结点A得到A1，将结点A1插到结点A后面；
*2、重新遍历链表，复制老结点的随机指针给新结点，如A1.random = A.random.next;
*3、拆分链表，将链表拆分为原链表和复制后的链表
*/
public class Solution {
    public RandomListNode Clone(RandomListNode pHead) {
        if(pHead == null) {
            return null;
        }
         
        RandomListNode currentNode = pHead;
        //1、复制每个结点，如复制结点A得到A1，将结点A1插到结点A后面；
        while(currentNode != null){
            RandomListNode cloneNode = new RandomListNode(currentNode.label);
            RandomListNode nextNode = currentNode.next;
            currentNode.next = cloneNode;
            cloneNode.next = nextNode;
            currentNode = nextNode;
        }
         
        currentNode = pHead;
        //2、重新遍历链表，复制老结点的随机指针给新结点，如A1.random = A.random.next;
        while(currentNode != null) {
            currentNode.next.random = currentNode.random==null?null:currentNode.random.next;
            currentNode = currentNode.next.next;
        }
         
        //3、拆分链表，将链表拆分为原链表和复制后的链表
        currentNode = pHead;
        RandomListNode pCloneHead = pHead.next;
        while(currentNode != null) {
            RandomListNode cloneNode = currentNode.next;
            currentNode.next = cloneNode.next;
            cloneNode.next = cloneNode.next==null?null:cloneNode.next.next;
            currentNode = currentNode.next;
        }
         
        return pCloneHead;
    }
}
```

### 24.二叉搜索树转为双向链表 ⚠️

```java
//用栈，然后一个pre标记上一个结点,和反转链表一致
import java.util.*;
public class Solution {
    public TreeNode Convert(TreeNode root) {
         if(root==null)
            return null;
        Stack<TreeNode> stack = new Stack<TreeNode>();
        TreeNode p = root;
        TreeNode pre = null;// 用于保存中序遍历序列的上一节点
        boolean isFirst = true;
        while(p!=null||!stack.isEmpty()){
            while(p!=null){
                stack.push(p);
                p = p.left;
            }
            p = stack.pop();
            if(isFirst){
                root = p;// 将中序遍历序列中的第一个节点记为root
                pre = root;
                isFirst = false;
            }else{
                pre.right = p;
                p.left = pre;
                pre = p;
            }      
            p = p.right;
        }
        return root;
    }
}
```

### 25.数组中出现次数超过一半的数字 ✅

```java
import java.util.*;
//排序的时间复杂度 至少为nlogn 显然不是面试官要的解法
public class Solution {
    public int MoreThanHalfNum_Solution(int [] array) {
        int len=array.length;
        if(len<1){
            return 0;
        }
        int count=0;
        Arrays.sort(array);
        int num=array[len/2];
        for(int i=0;i<len;i++){
            if(num==array[i])
                count++;
        }
        if(count<=(len/2)){
            num=0;
        }
        return num;
    }
}
//那么怎么用
```

### 26.最小的K个数 ✅ 

```java

import java.util.*;
public class Solution {
    public ArrayList<Integer> GetLeastNumbers_Solution(int [] input, int k) {
       
       
        ArrayList<Integer> list =new ArrayList<>();
         PriorityQueue<Integer> queue = new PriorityQueue<Integer>();
         if(input==null||input.length==0||k>input.length)
             return list;
        
        for(int i=0;i<input.length;i++){
            queue.add(input[i]);
        }
        
        while(k>0){
            list.add(queue.remove());
            k--;
        }
        return list;
        
    }
    
}
```

### 27.最大连续子序列的和 ✅

还有变种，最长连续子序列的和，再搞一个数组存每个长度的最长值就可以了

```java

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
 
public class Solution {
    public int FindGreatestSumOfSubArray(int[] array) {
       List<Integer> list = new ArrayList<>();
        for(int i=0;i<array.length;i++){
            int sum = 0;
            for(int j=i;j<array.length;j++){
                sum += array[j];
                list.add(sum);
            }
        }
        if(list.size() <=0) return 0;
        Collections.sort(list);
        return list.get(list.size()-1);
    }
}
//很简单，
//dp我的思想是对了，但是有一点就是能一个for循环内比较完的，一定要一个for循环内比较完毕。
//还有就是关于dp的两层循环还是一层循环 这种
public class Solution {
    public int FindGreatestSumOfSubArray(int[] array) {
        if(array==null||array.length==0)
            return 0;
        if(array.length==1)
            return array[0];
        int []result =new int[array.length];
        result[0]=array[0];
        result[1]=array[1];
        int max=Integer.MIN_VALUE;
        for(int i=1;i<array.length;i++){ 
           result[i]=Math.max(result[i-1]+array[i],array[i]);   
             max=Math.max(max,result[i]);
        }
       return max;
        
    }
}
dp解法：
public  int FindGreatestSumOfSubArray(int[] array) {
        int res = array[0]; //记录当前所有子数组的和的最大值
        int max=array[0];   //包含array[i]的连续数组最大值
        for (int i = 1; i < array.length; i++) {
            max=Math.max(max+array[i], array[i]);
            res=Math.max(max, res);
        }
        return res;
}
//这个max+array[i] 或者array[i]真是神了，没错就是这种，求和就是 如果相加不够大，就用array[i]作为初始值，这样保证了连续。这是相当于细分了一下和那个买卖股票一样。
```



28.整数中1出现的次数（从1到n整数中1出现的次数）

```java
public class Solution {
    public int NumberOf1Between1AndN_Solution(int n) {
        int count=0;
        while(n>0){
            String str=String.valueOf(n);
            char [] chars=str.toCharArray();
            for(int i=0;i<chars.length;i++){
                if(chars[i]=='1')
                    count++;
            }
            n--;
        }
        return count;
    }
}
//笑死我了 java真强大 转换成字符，把输入的n 一个个看就好了，哈哈哈哈哈哈
```

### 29.把字符数组从小到大排序 重写comparator

```java
public String PrintMinNumber(int [] numbers) {
        if(numbers == null || numbers.length == 0) return "";
        int len = numbers.length;
        String[] str = new String[len];
        StringBuilder sb = new StringBuilder();
        for(int i = 0; i < len; i++){
            str[i] = String.valueOf(numbers[i]);
        }
        Arrays.sort(str,new Comparator<String>(){
            @Override
            public int compare(String s1, String s2) {
                String c1 = s1 + s2;
                String c2 = s2 + s1;
                return c1.compareTo(c2);
            }
        });
    
    	
        for(int i = 0; i < len; i++){
            sb.append(str[i]);
        }
        return sb.toString();
    }

```



### 30.树中两个节点的最低公共祖先

```

```



### 31.两个链表的第一个公共结点 

```java
public class Solution {
    public ListNode FindFirstCommonNode(ListNode pHead1, ListNode pHead2) {
        if(pHead1==null||pHead2==null)
            return null;
        ListNode p1=pHead1;
        ListNode p2=pHead2;
        boolean isFirst1=true;
            boolean isFirst2=true;
        while(true){
             if(p1==null&&isFirst1){
                p1=pHead2;
                 isFirst1=false;
            }
             if(p2==null&&isFirst2){
                p2=pHead1;
                 isFirst2=false;
            }
            if(p1==null&&!isFirst1)
                break;
            if(p2==null&&!isFirst2)
                break;
           if(p1.val==p2.val)
               break;          
            p1=p1.next;
            p2=p2.next;
        }
       
        if(p1!=null&&p2!=null)
            return p1;
       else
           return null;
    }

}
```



### 32.丑数 ⚠️

```java
//出队列，又重新入队列的操作
public class Solution {
    public int GetUglyNumber_Solution(int index) {
     
   if(index<=0)
            return 0;
        int[] result = new int[index];
        int count = 0;
        int i2 = 0;
        int i3 = 0;
        int i5 = 0;
 
        result[0] = 1;
        int tmp = 0;
        while (count < index-1) {
            tmp = min(result[i2] * 2, min(result[i3] * 3, result[i5] * 5));
            if(tmp==result[i2] * 2) i2++;//三条if防止值是一样的，不要改成else的
            if(tmp==result[i3] * 3) i3++;
            if(tmp==result[i5]*5) i5++;
            result[++count]=tmp;
        }
        return result[index - 1];//这个也不算动态规划吧，其实就是不重复的全排列？
    }
 
    private int min(int a, int b) {
        return (a > b) ? b : a;
    }
}

```

### 33.长为n最大为n的数组中的重复数字

```java
//hashmap做
//或者干脆用
```



### 34.两个栈实现队列 ✅

```java
//其实真的很简单，就是 利用stack2作为stack1的反转
import java.util.Stack;

public class Solution {
    Stack<Integer> stack1 = new Stack<Integer>();
    Stack<Integer> stack2 = new Stack<Integer>();
    
    public void push(int node) {
        stack1.push(node);
        
    }
    
    public int pop() {
        if(!stack2.isEmpty())
            return stack2.pop();
        else{
            while(!stack1.isEmpty()){
                stack2.push(stack1.pop());
            }
            return stack2.pop();
        }
            
    
    }
}
```

### 35.第一个只出现一次的字符 

```

```



### 36.快排 ✅

```java
public void sort(int arr[]){
    if(arr==null)
        return;
    quicksort(arr,0,arr.length-1);
}
//这个partition分治的思想非常重要！
public void quicksort(int arr[],int left,int right){
    if(left<right){
        int pivot=partition(arr,left,right);
        quicksort(arr,left,pivot-1);
        quicksort(arr,pivot+1,right);
    }
    
}
public int partition(int arr[],int left,int right){
    int temp=arr[left];
    while(left<right){
        while(left<right&&arr[right]>temp)
            --right;
        arr[left]=arr[right];
        while(left<right&&arr[left]<temp)
            ++left;
        arr[right]=arr[left];
    }
    arr[left]=temp;
    return left;
}

//还有指针交换的方法
```



### 37.旋转数组的最小数字

```java
//双指针技巧
```



### 38.归并排序



### 堆排序





# 海量数据类题目

### 两个大文件求重复

给定a、b两个文件，各存放50亿个url，每个url各占64字节，内存限制是4G，让你找出a、b文件共同的url？

<http://www.sohu.com/a/278216703_652662>

方案1：可以估计每个文件安的大小为5G×64=320G，远远大于内存限制的4G。所以不可能将其完全加载到内存中处理。考虑采取分而治之的方法。

遍历文件a，对每个url求取hash(url)%1000，然后根据所取得的值将url分别存储到1000个小文件（记为a0,a1,...,a999）中。这样每个小文件的大约为300M。

遍历文件b，采取和a相同的方式将url分别存储到1000小文件（记为b0,b1,...,b999）。这样处理后，所有可能相同的url都在对应的小文件（a0vsb0,a1vsb1,...,a999vsb999）中，不对应的小文件不可能有相同的url。然后我们只要求出1000对小文件中相同的url即可。

求每对小文件中相同的url时，可以把其中一个小文件的url存储到hash_set中。然后遍历另一个小文件的每个url，看其是否在刚才构建的hash_set中，如果是，那么就是共同的url，存到文件里面就可以了。

方案2：如果允许有一定的错误率，可以使用Bloom filter，4G内存大概可以表示340亿bit。将其中一个文件中的url使用Bloom filter映射为这340亿bit，然后挨个读取另外一个文件的url，检查是否与Bloom filter，如果是，那么该url应该是共同的url（注意会有一定的错误率）。



### 海量整数找到不重复的整数

T级大小AB文档，2g内存，要求找出重合部分的数据(分治的思路答出来了，然而思路偏到bitmap上去了，其实就是简单的在uid上通过哈希分组保证每次两组比较一次无需复用即可)



