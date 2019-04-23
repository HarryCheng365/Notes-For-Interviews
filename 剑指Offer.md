## 剑指Offer

剑指Offer中题出现的频率很高

### 1. 二维数组查找

```

```

### 2. 替换空格

```java
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

### 3.从尾到头输出链表值

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
```

### 4.前序和中序遍历 重建二叉树

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
 
        if(pi>pj){
            return null;
        }
        TreeNode head=new TreeNode(p[pi]);
        int index=map.get(p[pi]);
        head.left=preIn(p,pi+1,pi+index-ni,n,ni,index-1,map);
        head.right=preIn(p,pi+index-ni+1,pj,n,index+1,nj,map);
        return head;
    }
}
```

### 5.旋转数组最小数字

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

### 6.斐波那契数列

```java
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
```

### 7.跳台阶

```
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
```

### 8.跳台阶2

```

```

### 9.矩形覆盖

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
    }
}其实是个理解问题
```

### 10.二进制中1的个数

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

### 12.调整数组顺序使奇数位于偶数前面

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

### 13.链表中倒数第k个结点

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
```

### 14.反转链表 

```
Leetcode刷过
```



### 15.合并两个排序链表 

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

### 18.顺时针打印矩阵 ✅

```
没什么技术含量，就是个数学问题
```

19.包含Min数的栈

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

### 19.出栈，入栈序列 ✅

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

### 22.二叉树中和为某一值的路径 ✅

```

```

### 23.复杂链表的复制 ✅

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

### 24.二叉搜索树转为双向链表 ✅

```java
用栈，然后一个pre标记上一个结点,和反转链表一致
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

### 25.数组中出现次数超过一半的数字

```java
import java.util.*;
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
```

### 26.最小的K个数

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

### 27.最大连续子序列的和

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

### 29.把数组从小到大排序

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



### 32.丑数

```java
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
        return result[index - 1];
    }
 
    private int min(int a, int b) {
        return (a > b) ? b : a;
    }
}

```

### 33.快排



### 34.归并排序



### 35.堆排序