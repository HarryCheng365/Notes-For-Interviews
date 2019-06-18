## 剑指Offer

剑指Offer中题出现的频率很高



### 1. 二维数组查找 ✅

##### 情境一：二维排序数组查找

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


public void swap(int [] array){
    for(int i=array.length-1;i>=0;i--){
        for(int j=i;j>=array.length-i;j--){
            if(array[j-1]==0&&array[j]!=0){
                int temp=array[j];
                array[j]=array[j-1];
                array[j-1]=temp;
            }          
                
        }      
    }
    
}
```

##### 情景二：二维排序数组TopK

```java
//PriorityQueue 堆排序，或者 二分查找，这个二分查找真的很有意思
//取了估值，然后二分查找的遍历形式实现
public int binarySearch(int [][]array,int k){
    int low=array[0][0];
    int high=array[array[0].length-1][array.length-1]+1;
    int guess =(low+high)/2;
    int j=array[0].length-1;
    int count=0;
    while(low<high){    
         for(int i=0;i<array.length;i++){
        while(j>=0&&array[i][j]>guess){
             	j--;
        }
             count+=j+1;//如果边界条件0还是大于array[i][j]的话 刚好
             //执行到j-- count+=0； 很巧妙这种解法
        
    }
        if(count==k)
            return guess;
        if(count<k){
            low=guess+1;
        }else
            high=guess-1;
                  
    
    }
    return -1;
   
        
    
    
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
          //取出根的位置，界定 
        head.left=preIn(p,pi+1,pi+index-ni,n,ni,index-1,map);//因为可能是右子树的左子树，所以
          //pi+index-ni
          // index-ni 就是 左子树的长度 ，所以 pi+1,pi+index-ni
          //和 pi+index-ni+1,pj 原先长度 注意这里的左子树长度就好了
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
//这是为n的解法，还有不为n的
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
//
public int minNumber(int []array){
    int low=0;
    int high=array.length-1;
   int mid=( low+high)/2;
    if(array[low]>=array[high]){
        while(low<high){
            if(array[mid]==array[high]&&array[mid]==array[low])
                break;//此时必须用顺序查找法了
            if(high-low==1)
                return array[mid];
        if(array[mid]>array[0])
            low=mid+1;
        if(array[mid]<array[array.length-1])
            high=mid-1;
            
            
    }
    }else
        return array[0];
    
    
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
//这个是分割模型，一个线段，或者一个台阶，或者一个硬币，分割成n段，这个是典型的分割模型，和钢铁切割 绳子切割都是一致的，只不过这里变成了问切割方法，同时，切割也可以变成组合问题
//这个也是非递归 带记忆的dp 找到子父状态转移方程就好
//dp 就是常见于分割问题啊 如按某种条件分割，找到状态转移方程，把大问题拆解成小问题，
//分割又多少种 类似这种问题，一般都是可以用dp的，不要去想什么中心扩展法，那是回文串专属的
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



### 16.树的子结构 ✅

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
            return true;//我懂了 子结构不一定是完整的子树，可以仅仅是一部分
        if(root1.val==root2.val)
        {
            return isSubtree(root1.left,root2.left)&&isSubtree(root1.right,root2.right);
        }
        else    
            return false;
        
    }
}

```

### 17.二叉树镜像，二叉树反转 ✅

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
//自己又写了一遍，真的没什么技术含量
public class Solution {
    public void Mirror(TreeNode root) {
        mirror(root);
        
        
    }
    public TreeNode mirror(TreeNode root){
        if(root==null)
            return null;
        TreeNode temp=root.left;
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

//不用 其实包含Min数的栈 用两个栈实现就好了
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

### 20. 二叉树深度优先广度优先

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

//二叉树的深度优先遍历

//带回溯的搜索
```

### 21.二叉搜索树的后序遍历序列 ✅

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

##### 情景一：二叉树中和为某一值的路径

```

```

##### 情景二：树的深度

```java
//这个真的是，可以按照堆的思想，怎么说一个树的最大深度，总是由其左边最大深度，和右边最大深度的最大值决定的
//因此，可以是个递归的过程
//同理 多叉树也是一样，这个可以不只适用于二叉树
public int findDeepth(TreeNode node){
    if(node==null)
        return 0;
    int left=findDeepth(node.left);
    int right=findDeepth(node.right);
    return (left>right)? left+1:right+1;
}
```

##### 情景三：树是否为平衡二叉树

```java
public class Solution {
    public boolean IsBalanced_Solution(TreeNode root) {
        return getDepth(root) != -1;
    }
     
    private int getDepth(TreeNode root) {
        if (root == null) return 0;
        int left = getDepth(root.left);
        if (left == -1) return -1;
        int right = getDepth(root.right);
        if (right == -1) return -1;
        return Math.abs(left - right) > 1 ? -1 : 1 + Math.max(left, right);
    }
    //只需要判断就好了，不需要求树的深度 我不知道怎么传值
}
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
//数组 hashmap统计 partition 摩尔投票法 
//其实这道题 是个求众数的问题
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



### 30.树中两个节点的最低公共祖先 ✅

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



### 32.丑数 ✅

```java
class Solution {
  
        //这里指针的意思，不是含有几个因数，也不是各个序列第几小的数，而是
        //要求之前一个丑数，分别乘上2，3，5得到大于当前最小丑数的值
        //而这里的i2，i3，i5指的是 乘以相应因数后小于等于当前丑数的最小的值，因此是对全体丑数的一个滑动指针
        //一开始思路就不对，自然就做不对，这下终于搞明白了
    
        public int nthUglyNumber(int n) {
        int[] dp = new int[n];
        dp[0] = 1;
        int i2 = 0, i3 = 0, i5 = 0;
        for (int i = 1; i < n; i++) {
            int min = Math.min(dp[i2] * 2, Math.min(dp[i3] * 3, dp[i5] * 5));
            if (min == dp[i2] * 2) i2++;
            if (min == dp[i3] * 3) i3++;
            if (min == dp[i5] * 5) i5++;
            dp[i] = min;
        }

        return dp[n - 1];
    }


}

```

##### 情景二：超级丑数

```java
//丑数这个知识点，我应该完全掌握了
class Solution {
    public int nthSuperUglyNumber(int n, int[] primes) {
        int []num=new int[primes.length];
        int[]dp = new int[n];
        dp[0]=1;
        int count=1;
        while(count<n){
            int index=0;
            int min=Integer.MAX_VALUE;
            for(int i=0;i<primes.length;i++){
                if(dp[num[i]]*primes[i]<min){
                    min=dp[num[i]]*primes[i];
                }  
            }
            
           for(int i=0;i<primes.length;i++){
               if(min==dp[num[i]]*primes[i])
                   num[i]++;
           }//去重，防止重复，这点之前考虑到了
            dp[count]=min;
            count++;
        }
        return dp[n-1];
    }
}
```



### 33.长为n最大为n的数组中的重复数字 ✅

```java
//hashmap做
//创立一个长度为n的数组 用作统计
//或者干脆用交换做 如果可以修改数组的话
//如果只用找到一个就用partition做 但是需要统计比mid小的数目，进而进一步锁定区间nlogn的方法
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

依旧是用hash做，但是 如果是数字的话，应该用&操作做 最好了

##### 情景1

```

```



##### 情景2

数组中只出现一次的数字

```java
//num1,num2分别为长度为1的数组。传出参数
//将num1[0],num2[0]设置为返回结果
import java.util.*;
public class Solution {
    public void FindNumsAppearOnce(int [] array,int num1[] , int num2[]) {
        HashMap<Integer,Integer> hash = new HashMap<>();
        for(int i=0;i<array.length;i++){
            if(hash.containsKey(array[i]))
                hash.put(array[i],hash.get(array[i])+1);
            else
                hash.put(array[i],1);
        }
        List<Integer> list = new ArrayList<>();
        for(Integer key:hash.keySet()){
            if(hash.get(key)==1){
                list.add(key);
            }      
        }
        num1[0]=list.get(0);
        num2[0]=list.get(1);
        
        
    }
    
}
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

### 38.数在排序数组中的出现次数 ✅

GetFirst GetLast

```java
//O(n)的做法有两种，然后还有一种是二分搜索找到后，向前向后搜索
ublic class Solution {
    public int GetNumberOfK(int [] array , int k) {
        int start=0;
        int end=array.length;
        for(int i=array.length-1;i>=0;i--){
           if(array[i]==k){
               end=i;
               break;
           }
        }
            
        for(int i=0;i<array.length;i++){
             if(array[i]==k){
                 start=i;
                 break;
             }              
        }
        if(end-start>=0&&end-start<array.length)
            return end-start+1;
        
        return 0;       
    }
}
//第一种，前后搜索，取差值，分别搜索，一个数字的开头，再搜索一个数字的结尾，连续写两个二分搜索函数就好了

```



### 39.归并排序

尤其应该注意归并的过程

```java
//归并排序
public static void sort(int arr[]){
    if(arr == null)
        return;
    
    mergeSort(arr, 0, arr.length-1);
}

private static void mergeSort(int arr[], int left, int right){
    if(left < right){
        int mid = left + (right-left)/2;
        mergeSort(arr, left, mid);			//[left, mid]
        mergeSort(arr, mid+1, right);		//[mid+1, right]
        if(arr[mid] > arr[mid+1])
            merge(arr, left, mid, right);
    }
}

private static void merge(int arr[], int left, int mid, int right){
   	//申请一个新的临时数组
    int temp[] = new int[right-left+1];
    int i = left, j = mid+1, k = 0;
    
    while(i <= mid && j <= right){
        if(arr[i] < arr[j])
            temp[k++] = arr[i++];
        else
            temp[k++] = arr[j++];
    }
    while(i <= mid)
        temp[k++] = arr[i++];
    while(j <= right)
        temp[k++] = arr[j++];
    for(int t = 0; t < temp.length; ++t)
        arr[left+t] = temp[t];//或者直接可以用新的数组进行返回 
}
```

##### 情景二：

```java
//在求数组中的逆序对的时候用到了这个概念
//当时求了一个right-length+1 是为了计数用的，本身归并排序的过程中不会用到这个length，只有初始化数组的时候用到了
```



### 40.堆排序 ✅

### 41.翻转单词序列 ✅

##### 情景一：反转单词序列

```java
import java.util.*;
public class Solution {
    public String ReverseSentence(String str) {
        if(str.trim().length()==0)
            return str;
        String[] strs=str.split(" ");
        Stack<String> stack  = new Stack<>();
        for(String str2:strs){
            stack.push(str2);
        }
        
        StringBuffer strb= new StringBuffer();
       
        while(!stack.isEmpty()){
            if(stack.size()>1)
                strb.append(stack.pop()+" ");
            else
                strb.append(stack.pop());   
        }
       
        return strb.toString();
        
    }
}
```

##### 情景二：左旋转字符串

```java
//其实这种思想还可以用在链表，任何线性表的翻转上，两部分各自反转，再反转总的
public class Solution {
    public String LeftRotateString(String str,int n) {
        
        char[] array = str.toCharArray();
        if(n>array.length||array.length<=0)
            return str;
        reverse(array,0,n-1);
        reverse(array,n,array.length-1);
        reverse(array,0,array.length-1);
        
        StringBuffer strb = new StringBuffer();
        for(char c:array){
            strb.append(c);
        }
        return strb.toString(); 
    }
    public void reverse(char[] array,int low,int high){
        while(low<high){
            char temp=array[low];
            array[low]=array[high];
            array[high]=temp;
            low++;
            high--;
        }
          
    }
}
```



### 42.和为S的连续正序列

其实思想是差不多的

##### 情景一：和为S的连续正序列

```java
//其实本来一开始，是以为一个动态规划类题目，但是一看和为S这个条件，同时又要求连续，因此需要维护一个窗口
//最好，维护一个窗口。
//这就是和为S的连续正序列，运用一个small 一个big快慢指针，维护一个滑动窗口
//其实是求连续序列和的一个小技巧，通常我们可以用循环求一个连续序列的和，但是考虑到每次操作之后的序列和操
//操作之前的序列相比 大部分数字是一样的，这时候就需要维护一个滑动窗口了，但是不一定是增加一个或者几个数字
//然后外部套一个while循环设置终止条件
import java.util.ArrayList;
public class Solution {
    public ArrayList<ArrayList<Integer> > FindContinuousSequence(int sum) {
             
      int small =1;
       int big=2;
       int middle=(sum+1)/2;
        int curSum=small+big;//因为至少是连续的两个整数，所以要小于sum的二分之一
        ArrayList<ArrayList<Integer>> lists = new ArrayList<>();
        while(small<middle){
            
            if(curSum==sum){
                ArrayList<Integer> list = new ArrayList<>();
                for(int i=small;i<=big;i++){
                    list.add(i);      
                }
                lists.add(list);
                big++;
                curSum+=big;
            }
            while(curSum>sum){
                curSum-=small;
                small++;
            }
            while(curSum<sum){
                big++;
                curSum+=big;
            }
            
        }
        return lists;
       
    }
    
}

```

##### 情景二：和为S的两个整数

```java
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> FindNumbersWithSum(int [] array,int sum) {
        int low=0;
        int high=array.length-1;
        int min=Integer.MAX_VALUE;
        ArrayList<Integer> list = new ArrayList<>();
        while(low<high){
            if(array[low]+array[high]==sum){
                if(min>array[low]*array[high]){
                    min=array[low]*array[high];
                    if(!list.isEmpty())
                        list.clear();
                    list.add(array[low]);
                    list.add(array[high]);
                }
                low++;
                high--;
            }
            if(array[low]+array[high]>sum)
                high--;
            if(array[low]+array[high]<sum)
                low++; 
        }
        return list;
    }
}
```



### 43.二叉树的下一个结点

##### 情景一：二叉树的下一个结点

```Java
//二叉树的还比较简单，多叉树的就会很复杂了
public class Solution {
    public TreeLinkNode GetNext(TreeLinkNode pNode)
    {
        if(pNode.right!=null){
            TreeLinkNode pCurrent=pNode.right;
            while(pCurrent.left!=null)
                pCurrent=pCurrent.left;
            return pCurrent;
        }
        else{
            if(pNode.next!=null){
                TreeLinkNode pParent=pNode.next;
                while(pParent!=null&&pNode==pParent.right){
                    pNode=pParent;
                    pParent=pNode.next;
                }
                return (pParent==null)? null:pParent;
                    
            }   
            else
                    return null;
        }     
    }
}
```

##### 情景二：二叉树的上一个结点

```java
//这个上一个结点是对二叉树中序遍历顺序来说的
if(current.left!=null)
    return current.left;
if(current==current.parent.right){
    return current.parent;
}

```



### 44.字符串的排列                                                                   

##### 情景一：全排列类

```java
//全排列和组合，不是不可以求
```

##### 情景二：组合类

```

```



### 45.回文串系列

##### 情景一：验证回文串，验证回文数

##### 情景二：回文子串，最长回文子串，最长回文串

```java
//其实求回文子串的题目
//有两种方法，一种是动态规划法，一种是中心扩展法
//动态规划法
class Solution {
  
    public int countSubstrings(String s){
    char[] temp=s.toCharArray();
    boolean [][] dp=new boolean[temp.length][temp.length];
    for(int i=0;i<temp.length;i++){
        dp[i][i]=true;
    }
    int result=0;
        int max=0;
    result+=temp.length;
    for(int i=temp.length-2;i>=0;--i){
         if(temp[i]==temp[i+1]){
        dp[i][i+1]=true;
             result+=1;
             max=Math.max(max,2);
    }
        //这里就是以i为开头的回文 
        //j=i+2,也就是 j第一圈的时候并不会执行
        for(int j=i+2;j<temp.length;++j){
            //跳过了i+1说名不连续，然后这里j又可以增长
            if(temp[i]==temp[j]&&dp[i+1][j-1]){
                dp[i][j]=true;
                result+=1;
                math.Math.max(max,j-i+1);
            }
        }   
    }
    return result;
    } 
}
//中心扩展法
//回文串，只有 中心扩展才可以去重
public int countSubstrings(String s){
    char[]temp=s.toCharArray();
    int result=1;
    if(temp.length==0)
        return 0;
    for(int i=1;i<temp.length;i++){
        int left=i;
        int right=i;
        while(left>=0&&right<temp.length&&s[left--]==s[right++]){
            result+=1;
        }
    }
}

```





### 60.树中两个结点的最低公共祖先

##### 情境一：二叉树最低公共祖先

```java
class Solution {
    
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        List<TreeNode> list = new ArrayList<>();
        find(root,p,q,list);
        if(list.isEmpty())
            return null;
        else
            return list.get(0);
        
        
    }
    
    public boolean find(TreeNode root,TreeNode p,TreeNode q,List<TreeNode> list){
        
        if(root==null)
            return false;
        boolean temp =find(root.left,p,q,list);
        boolean temp2=find(root.right,p,q,list);
        boolean temp3=(root.val==p.val||root.val==q.val);
        if(temp&&temp2||temp&&temp3||temp2&&temp3){
             list.add(root);
            return true;
        } 
        else if(temp3)
            return true;
        else if(temp||temp2)
            return true; 
        return false;
          
    }
}
```

##### 情景二：二叉搜索树的最低公共祖先

```java
public TreeNode find(TreeNode root,TreeNode p,TreeNode q){
    if(root==null)
        return null;
    if(root.val>p.val&&root.val>q.val)
        return find(root.left,p,q);
    if(root.val<p.val&&root.val<q.val)
        return find(root.right,p,q);
    if(root.val>=Math.min(p.val,q.val)&&root.val<=Math.max(p.val,q.val))
        return root;
    return null;
}
//这样 在递归过程中 遇到的第一个root在两值之间的，就是最低公共祖先了。
//二叉搜索树的最低公共祖先还简单一点
```

##### 情境三：带有指向父结点指针的树的最低公共祖先

```
//转换成两个链表，首先递归找到结点，然后根据父结点构造出两个链表出来
//变成两个链表求交？
```



### 61.二叉树的遍历的非递归实现 ✅

##### 情景一：二叉树的前序非递归前序遍历

```java
public List<Integer> preOrder(TreeNode root){
    
    Stack<TreeNode> stack = new Stack<>();
    List<Integer> list = new ArrayList<>();
    if(root==null)
        return list;
    stack.push(root);
    while(!stack.isEmpty()){
        TreeNode temp = stack.pop();
        list.add(temp.val);
        if(temp.left!=null)
            stack.push(temp.left);
        if(temp.right!=null)
            stack.push(temp.right);
    }
    return list;  
}
```

##### 情景二：二叉树的中序遍历非递归

```java
public List<Integer> inorderTraversal(TreeNode root) {
        if(head != null) {
            Stack<Node> stack = new Stack<>();
            while(!stack.isEmpty() || head != null) {
                if(head != null) {
                    // 当前节点不为空, 将自己压进栈并将自己的左孩子作为当前节点（压入左边界）
                   	//如果一直有左孩子就一直压入
                    stack.push(head);
                    head = head.left;
                }else {
                    // 当前节点为空（没有左孩子了）, 将栈顶元素弹出作为当前节点, 并将当前节点的右孩子压进栈
                    head = stack.pop();
                    System.out.print(head.val + " ");
                    head = head.right;
                }
            }
        }
    }
//其实也是一步步来，都怪自己太急躁了，哪有一步走到底的办法
```

##### 情景三：双栈实现后序遍历

```java
public List<Integer> postorderTraversal(TreeNode root) {   
        Stack<TreeNode> stack1=new Stack<>();
        Stack<TreeNode> stack2=new Stack<>();
        List<Integer> list = new ArrayList<>();
        if(root==null)
            return list;
        TreeNode temp=root;
       stack1.push(root);
        while(!stack1.isEmpty()){
           temp= stack1.pop();
           stack2.push(temp);
            if(temp.left!=null){
                stack1.push(temp.left);
            }
            if(temp.right!=null){
                stack1.push(temp.right);
            }     
        }
        while(!stack2.isEmpty()){
            list.add(stack2.pop().val);
        }
        
        return list;
        
    }
```

##### 情景四：叶结点弹出标记

```java
public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        if(root==null)
            return list;
        TreeNode h=root;
        if (h != null) {
            Stack<TreeNode> stack = new Stack<TreeNode>();
            stack.push(h);
            TreeNode c = null;
            while (!stack.isEmpty()) {
                
                c = stack.peek();
                System.out.println(c.val);
                if (c.left != null && h != c.left && h != c.right) {
                    stack.push(c.left);
                } else if (c.right != null && h != c.right) {
                    stack.push(c.right);
                } else {
                    list.add(stack.pop().val);
                    h = c;
                }
            }
        }
}
//弹出标记 ，记录上一次弹出的结点，如果是左结点 不能是当前结点的左结点，如果是右结点，不能是当前结点的右结点 

```

##### 情景五：Z字形层次遍历二叉树

```java
import java.util.*;
/*
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    public ArrayList<ArrayList<Integer> > Print(TreeNode pRoot) {
    
        ArrayList<ArrayList<Integer>> lists=new ArrayList<>();
        
        if(pRoot==null)
            return lists;
        Stack<TreeNode> stack1 = new Stack<>();
        Stack<TreeNode> stack2 = new Stack<>();
        stack1.push(pRoot);
        while(!stack1.isEmpty()||!stack2.isEmpty()){
        if(!stack1.isEmpty()){
            ArrayList<Integer> list = new ArrayList<>();
            while(!stack1.isEmpty()){
                 TreeNode temp=stack1.pop();
                list.add(temp.val);
                if(temp.left!=null)
                    stack2.push(temp.left);
                if(temp.right!=null)
                    stack2.push(temp.right);  
            }
            lists.add(list);
        }
        else if(!stack2.isEmpty()){
            ArrayList<Integer> list = new ArrayList<>();
            while(!stack2.isEmpty()){
                TreeNode temp=stack2.pop();
                list.add(temp.val);
                if(temp.right!=null)
                    stack1.push(temp.right);
                if(temp.left!=null)
                    stack1.push(temp.left);  
                
            }
            lists.add(list);
        }
        }
        return lists;

    }

}
```

```java
ListNode listNode= new ListNode(0);
int count=0;
pre=null;
preEven=null;
while(list.next!=null){
count++;
if(count&1==1){
ListNode temp = list.next;
list.next=pre;
pre=list;
list=temp;
else{
ListNode temp=list.next;
list.next=preEven;
pre=list;
list=temp;//偶数结点有指向奇数结点的指针就不好了，不如拆分成两个结点 
}//这样两个链表就拆分开了  然后 再把链表reverse成 同向的 进行归并
//二叉树用数组存储 二叉树的序列化反序列化 还是？可以用两个数组来存？ 
//
}
}
```



### 62.前缀树Trie-树

```java
TreeNode{

char val;

boolean isWord;

HashMap<Character,TreeNode> hashMap;

public TreeNode(char val){

this.hashMap = new HashMap<>();

isWord=false;

this.val =val;

}

}

public void createTree(String str){

char [] array=str.toCharArray();

TreeNode newNode = new TreeNode(array[i]);

for(int i=0;i<array.length-1;i++){

if(i+1==array.length-1){

newNode.isWord=true;

break;

}

if(newNode.hashMap.containsKey(array[i+1])){

newNode =newNode.hashMap.get(array[i+1]);

}else{

newNode.hashMap.put(array[i],new TreeNode(array[i]));

}

}

}

```

### 63.排列组合

##### 情景一：递归求全排列

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> output = new LinkedList<>();
        ArrayList<Integer> list = new ArrayList<>();
        for(int i:nums){
            list.add(i);
        }
        backTrack(nums.length,list,output,0);
        return output;
        
        
    }
    
    public void backTrack(int n,ArrayList<Integer> list,List<List<Integer>> output,int first){
        if(first==n){
            output.add(new ArrayList<>(list));
        }else{
            for(int i=first;i<n;i++){
                Collections.swap(list,first,i);
                backTrack(n,list,output,first+1);
                Collections.swap(list,first,i);
            }  
        }
    }
}//全排列的思想还是很简单的，哎 没办法 自己没有复习到 就是菜
//我原来还一直好奇，如何将当前list中的结点移除，如何添加到结果集里，是不是要一个个复制，然后添加进去，这里
//原来一个new ArrayList<>(list)就解决了，同时 避免移除可以用交换的方法
```

##### 情景二：重复求全排列

```java
class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> output= new LinkedList<>();
        ArrayList<Integer> list = new ArrayList<>();
        for(int i:nums){
            list.add(i);
        }
        backTrack(nums.length,list,output,0);
        return output;
        
    }
    public void backTrack(int n,ArrayList<Integer> list,List<List<Integer>> output,int first){
        if(first==n){
            output.add(new ArrayList<>(list));
        }
        else{
             List<Integer> temp = new LinkedList<>();
             for(int i=first;i<n;i++){
                 if(temp.contains(list.get(i)))
                     continue; 
                 else{
                     temp.add(list.get(i));
                     Collections.swap(list,first,i);
                     backTrack(n,list,output,first+1);
                     Collections.swap(list,first,i);
                     
                 }
             }
        } 
    }
}
//用一个list 或者hashmap 保存扫描过的值 用来去重复，已经交换或者遇到过的值 直接跳过就好了。
//用swap的方式 保存交换的值，而不是用从列表中删除的方法，当然 交换后记得归位
//组合问题，组合 其实变成了求子集的问题了，和
```

##### 情景三：错位重排 错排

```

```



### 64.动态规划 背包问题

##### 情景一：0-1背包问题，放还是不放 是个问题

```java
//0-1背包问题需要一个二维数组来存，上一个用二维数组存状态的还是最长回文子串，
//一个维度是 
//而在切割问题中，是一维的只有背包容量，然后每一轮都会减去一个切割值，因为
//0-1背包 每件物品只有一件，所以每件物品会有放入和不放入的情况，如果放入了就变成了dp[i-1][j-w]的
//
int [][]dp = new int[N+1][W+1];
for(int i=1;i<=N;i++){
  int w=weights[i-1],v = valus[i-1];
  for(int j=1;j<=W;j++){
    if(j>=w){
      dp[i][j]=Math.max(dp[i-1][j],dp[i-1][j-w]+v);
    }else{
      dp[i][j]=dp[i-1][j];
      //这是容量不够的情况，j还会继续扩大的，这是对物品和容量w生成了二维数组
    }
  }
  //从实际意义 1，1 开始的，所以
  
}
//关于节省空间，
for(int i=1;i<=N;i++){
  int w =weights[i-1], v=values[i-1];
  for(int j=W,j>=1;j--){
    if(j>=w)
    	dp[j]=Math.max(dp[j],dp[j-w]+v);
    //dp[j]=max(dp[j],do[j-w]+v)对照之前方程左侧略去了i-1 所以
    //这里是物品只有一件时，所有w下 可能的取值，所以 当i增长dp[j] 和dp[j-w]自然就变成了dp[i-1][j]和
    //dp[i-1][j-w];
  }
}
```



##### 情景二：无限背包（完全背包）

```java
//无限背包和切割问题类似
```



情景三：



### 65.动态规划 矩阵路径

情景一：



### 66.动态规划 分割数组

情景一：

```
//这个题意是 已经是等差数列了，求多少个（大于三个元素
//等差数列论结尾dp[i]和dp[i-1]的关系，dp[i]比dp[i-1]靠后一位，所以以dp[i-1]结尾的所有结果，靠后挪一个位置就是dp[i]但是这样
//还有个问题就是 前面长度会空出一格来，因此要+1；
//所以 到底什么变量可以当作子结构 是个问题，到底以什么为标准进行dp
//一般是 数组的长度，解的长度，target为i 和 target 为 i-1有什么区别 有的时候是二维的，比如0-1背包问题
class Solution {
    public int numberOfArithmeticSlices(int[] A) {
        int []dp = new int[A.length+1];
        for(int i=2;i<A.length;i++){
            if(A[i]-A[i-1]==A[i-1]-A[i-2]){
                dp[i]=dp[i-1]+1;
            }
                
        }
        int result=0;
        for(int i:dp){
            result+=i;
        }
        return result;
        
    }
}
```



# 海量数据类题目

<http://www.sohu.com/a/278216703_652662>

### 海量数据求重复最高的记录

#### 场景1

海量日志数据，提取出某日访问百度次数最多的那个IP

IP的数目还是有限的，最多2^32个，所以可以考虑使用hash将ip直接存入内存，然后进行统计。

再详细介绍下此方案：首先是这一天，并且是访问百度的日志中的IP取出来，逐个写入到一个大文件中。注意到IP是32位的，最多有个2^32个 IP。同样可以采用映射的方法，比如模1000，把整个大文件映射为1000个小文件，再找出每个小文中出现频率最大的IP（可以采用hash_map进行频率统计，然后再找出频率最大的几个）及相应的频率。然后再在这1000个最大的IP中，找出那个频率最大的IP，即为所求。

**同样的数字/地址/字符串等等 用相同的哈希函数映射，最后一定在同一个文件里。**

**然后在各个小文件中用hashmap进行频率统计，再整体遍历所有的hashmap，并用小根堆存储频率的最大值。**这是频率TopK的解法，hash真的是神器，其实还是

equals和hashcode()的关系

#### 场景2

**搜索引擎会通过日志文件把用户每次检索使用的所有检索串都记录下来，每个查询串的长度为1-255字节。**

假设目前有一千万个记录（这些查询串的重复度比较高，虽然总数是1千万，但如果除去重复后，不超过3百万个。一个查询串的重复度越高，说明查询它的用户越多，也就是越热门。），请你统计最热门的10个查询串，要求使用的内存不能超过1G。

典型的Top K算法，还是在这篇文章里头有所阐述。 文中，给出的最终算法是：第一步、先对这批海量数据预处理，在O（N）的时间内用Hash表完成排序；然后，第二步、借助堆这个数据结构，找出Top K，时间复杂度为N‘logK。 即，借助堆结构，我们可以在log量级的时间内查找和调整/移动。因此，维护一个K(该题目中是10)大小的小根堆，然后遍历300万的Query，分别和根元素进行对比所以，我们最终的时间复杂度是：O（N） + N'*O（logK），（N为1000万，N’为300万）。ok，更多，详情，请参考原文。

或者：采用trie树，关键字域存该查询串出现的次数，没有出现为0。最后用10个元素的最小推来对出现频率进行排序。

其实还是 分治，hash，小文件内统计，小根堆TopK

#### 场景3

**有10个文件，每个文件1G，每个文件的每一行存放的都是用户的query，每个文件的query都可能重复。要求你按照query的频度排序。**

### 两个大文件求重复

给定a、b两个文件，各存放50亿个url，每个url各占64字节，内存限制是4G，让你找出a、b文件共同的url？

方案1：可以估计每个文件安的大小为5G×64=320G，远远大于内存限制的4G。所以不可能将其完全加载到内存中处理。考虑采取分而治之的方法。

遍历文件a，对每个url求取hash(url)%1000，然后根据所取得的值将url分别存储到1000个小文件（记为a0,a1,...,a999）中。这样每个小文件的大约为300M。

遍历文件b，采取和a相同的方式将url分别存储到1000小文件（记为b0,b1,...,b999）。这样处理后，所有可能相同的url都在对应的小文件（a0vsb0,a1vsb1,...,a999vsb999）中，不对应的小文件不可能有相同的url。然后我们只要求出1000对小文件中相同的url即可。

求每对小文件中相同的url时，可以把其中一个小文件的url存储到hash_set中。然后遍历另一个小文件的每个url，看其是否在刚才构建的hash_set中，如果是，那么就是共同的url，存到文件里面就可以了。

**hash求模分治，存在1000个小文件里，然后按对比较即可，不重复**

方案2：如果允许有一定的错误率，可以使用Bloom filter，4G内存大概可以表示340亿bit。将其中一个文件中的url使用Bloom filter映射为这340亿bit，然后挨个读取另外一个文件的url，检查是否与Bloom filter，如果是，那么该url应该是共同的url（注意会有一定的错误率）。

两个文件呢？

### 海量整数找到不重复的整数

#### 场景1

T级大小AB文档，2g内存，要求找出重合部分的数据(分治的思路答出来了，然而思路偏到bitmap上去了，其实就是简单的在uid上通过哈希分组保证每次两组比较一次无需复用即可)

方案1：采用2-Bitmap（每个数分配2bit，00表示不存在，01表示出现一次，10表示多次，11无意义）进行，共需内存内存，还可以接受。然后扫描这2.5亿个整数，查看Bitmap中相对应位，如果是00变01，01变10，10保持不变。所描完事后，查看bitmap，把对应位是01的整数输出即可。

方案2：也可采用与第1题类似的方法，进行划分小文件的方法。然后在小文件中找出不重复的整数，并排序。然后再进行归并，注意去除重复的元素。

#### 场景2

**腾讯面试题：给40亿个不重复的unsigned int的整数，没排过序的，然后再给一个数，如何快速判断这个数是否在那40亿个数当中？**

以下是其它更好的方法：

方案1：oo，申请512M的内存，一个bit位代表一个unsigned int值。读入40亿个数，设置相应的bit位，读入要查询的数，查看相应bit位是否为1，为1表示存在，为0表示不存在。

方案2：这个问题在《编程珠玑》里有很好的描述，大家可以参考下面的思路，探讨一下：又因为2^32为40亿多，所以给定一个数可能在，也可能不在其中；这里我们把40亿个数中的每一个用32位的二进制来表示假设这40亿个数开始放在一个文件中。

然后将这40亿个数分成两类: 1.最高位为0 2.最高位为1 并将这两类分别写入到两个文件中，其中一个文件中数的个数<=20亿，而另一个>=20亿（这相当于折半了）；与要查找的数的最高位比较并接着进入相应的文件再查找

再然后把这个文件为又分成两类: 1.次最高位为0 2.次最高位为1

并将这两类分别写入到两个文件中，其中一个文件中数的个数<=10亿，而另一个>=10亿（这相当于折半了）； 与要查找的数的次最高位比较并接着进入相应的文件再查找。 ....... 以此类推，就可以找到了,而且时间复杂度为O(logn)，方案2完。

附：这里，再简单介绍下，位图方法： 使用位图法判断整形数组是否存在重复 判断集合中存在重复是常见编程任务之一，当集合中数据量比较大时我们通常希望少进行几次扫描，这时双重循环法就不可取了。

##### 补充bitmap

位图法比较适合于这种情况，它的做法是按照集合中最大元素max创建一个长度为max+1的新数组，然后再次扫描原数组，遇到几就给新数组的第几位置上1，如遇到5就给新数组的第六个元素置1，这样下次再遇到5想置位时发现新数组的第六个元素已经是1了，这说明这次的数据肯定和以前的数据存在着重复。这种给新数组初始化时置零其后置一的做法类似于位图的处理方法故称位图法。它的运算次数最坏的情况为2N。如果已知数组的最大值即能事先给新数组定长的话效率还能提高一倍。

#### 关于Trie树用作统计

**一个文本文件，大约有一万行，每行一个词，要求统计出其中最频繁出现的前10个词，请给出思想，给出时间复杂度分析。**

方案1：这题是考虑时间效率。用trie树统计每个词出现的次数，时间复杂度是O(n*le)（le表示单词的平准长度）。然后是找出出现最频繁的前10个词，可以用堆来实现，前面的题中已经讲到了，时间复杂度是O(n*lg10)。所以总的时间复杂度，是O(n*le)与O(n*lg10)中较大的哪一个。



# 思维题目记录

##### 赛马

25匹马，5个赛道，求比赛几次可以获取前3名

##### 称硬币

8个硬币，一个比其他7个重，请问

##### 烧绳子