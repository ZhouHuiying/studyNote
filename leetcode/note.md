### 循环、判断
  while for if-else 

### 正则 reg.test( )

### 位运算

## 数据结构：
  数组、队列、栈、二叉树、堆、链表、
  队列：先进先出(FIFO-first in first out)    
    入队 unshift(): 它能在数组首端添加任意个项并返回新数组的长度   
    出队pop():删除并返回数组的最后一个元素
  栈：后进先出(LIFO-last in first out) 
    入栈push(): 在数组最后添加一个元素  
    出栈pop()
  其他数组方法：　shift()：删除并返回数组的第一个元素

### 链表

### 二叉树：
  深度优先遍历 Depth-First-Search、广度优先遍历
  ##### 例子：100.判断两个二叉树是否相同
     ```
      function isSameTree(p, q) {   
        if(p==null && q==null){
            return true;
        }else if(p==null || q==null){
            return false;
        }else if(p.val!=q.val){
            return false;
        }else{
            return isSameTree(p.left,q.left) && isSameTree(p.right , q.right);
        }
      };
    ```
   二叉树的最大深度：深度优先遍历：分别计算左右子树中的最大深度 + 1
                  广度优点遍历：用队列。广度优先搜索的队列里存放的是「当前层的所有节点」。每次拓展下一层的时候，我们需要将队列里的所有节点都拿出来进行拓展，
                    这样能保证每次拓展完的时候队列里存放的是当前层的所有节点，即我们是一层一层地进行拓展，最后我们用一个变量 \textit{ans}ans 来维护拓展的次数，该二叉树的最大深度即为 \textit{ans}ans。
  二叉树的最大深度：深度优先遍历：分别计算左右子树中的最大深度 + 1
                  广度优点遍历：用队列。广度优先搜索的队列里存放的是「当前层的所有节点」。每次拓展下一层的时候，我们需要将队列里的所有节点都拿出来进行拓展，
                    这样能保证每次拓展完的时候队列里存放的是当前层的所有节点，即我们是一层一层地进行拓展，最后我们用一个变量 \textit{ans}ans 来维护拓展的次数，该二叉树的最大深度即为 \textit{ans}ans。

### 递归 recursion:

### 迭代 iteration:
  递归是一个树结构，从字面可以其理解为重复“递推”和“回归”的过程，当“递推”到达底部时就会开始“回归”，其过程相当于树的深度优先遍历。
  迭代是一个环结构，从初始状态开始，每次迭代都遍历这个环，并更新状态，多次迭代直到到达结束状态。



set、map、hashMap、
  
### 动态规划DP

双指针法
快慢指针
[回文链表的例子](https://leetcode-cn.com/problems/palindrome-linked-list/solution/hui-wen-lian-biao-by-leetcode-solution/)

位运算

例子： 
  杨辉三角  
  二分查找  367：有效的完全平方数
  求和
  排序
 