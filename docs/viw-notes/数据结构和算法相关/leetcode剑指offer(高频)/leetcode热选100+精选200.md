[TOC]





# 简单



## [104. 二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)-Math.max(left,right) 递归左 和右  最后一个+1

难度简单778

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

**说明:** 叶子节点是指没有子节点的节点。

**示例：**
给定二叉树 `[3,9,20,null,null,15,7]`，

```
    3
   / \
  9  20
    /  \
   15   7
```

返回它的最大深度 3 。



```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */

/**
1.树一般使用递归了
2.树的最大深度 
3.递归下去，每一层+1
4.直到传入的root节点为null 
5.大佬的下面这个题解一开始直接没看懂
6.递归太难了
*/
class Solution {
    public int maxDepth(TreeNode root) {
            if(root==null){
                return 0;
            }
            return  Math.max(maxDepth(root.left),maxDepth(root.right))+1;
    }
}

```



##  [136. 只出现一次的数字](https://leetcode-cn.com/problems/single-number/)-^运算，相同为0 不同为1

难度简单1674

给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

**说明：**

你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

**示例 1:**

```
输入: [2,2,1]
输出: 1
```

**示例 2:**

```
输入: [4,1,2,1,2]
输出: 4
```

```java
class Solution {
    public int singleNumber(int[] nums) {
        int temp =0;
        for(int i =0;i<nums.length;i++){
            temp ^=   nums[i];
        }
      return temp;
    }
}
```







## [169. 多数元素](https://leetcode-cn.com/problems/majority-element/)--排序

难度简单920收藏分享切换为英文接收动态反馈

给定一个大小为 *n* 的数组，找到其中的多数元素。多数元素是指在数组中出现次数 **大于** `⌊ n/2 ⌋` 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

 

**示例 1：**

```
输入：[3,2,3]
输出：3
```

**示例 2：**

```
输入：[2,2,1,1,1,2,2]
输出：2
```





```java
class Solution {
    public int majorityElement(int[] nums) {
        // int cur = nums.length/2;
        // Arrays.sort(nums);
        // return nums[nums.length/2];
        int cur = 0;
        int count = 0;
        for(int i:nums){
            if(count==0) cur = i; 
            if(i == cur ) {
            count++;
            }else{
                count--;
            }
        }
        return cur;
    }
}
// 方法2 排序
  public int majorityElement(int[] nums) {
        Arrays.sort(nums);
        return nums[nums.length >> 1];
}

```



## [283. 移动零](https://leetcode-cn.com/problems/move-zeroes/)-双指针

难度简单1002收藏分享切换为英文接收动态反馈

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

**示例:**

```
输入: [0,1,0,3,12]
输出: [1,3,12,0,0]
```

```java

class Solution {
    // 0 1 0 3 12
    // 0 0 1 3 12
    // 1 0 0 3 12
    // 1 3 0 0 12
    // 1 3 12 0 0
    public void moveZeroes(int[] nums) {
        int n = nums.length, left = 0, right = 0;
        while (right < n) {
            if (nums[right] != 0) {
                swap(nums, left, right);
                left++;
            }
            right++;
        }
    }
    public void swap(int[] nums, int left, int right) {
        int temp = nums[left];
        nums[left] = nums[right];
        nums[right] = temp;
    }
}
//方法2
 public void moveZeroes(int[] nums) {
        int j = 0 ;
        for(int i = 0;i<nums.length;i++){
            if(nums[i]!=0 ){
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
                j ++; 
            }
        }
    }
```



## [448. 找到所有数组中消失的数字](https://leetcode-cn.com/problems/find-all-numbers-disappeared-in-an-array/)-遍历一次标记为负数，再遍历一次 不为负数的+1 返回

难度简单674收藏分享切换为英文接收动态反馈

给定一个范围在 1 ≤ a[i] ≤ *n* ( *n* = 数组大小 ) 的 整型数组，数组中的元素一些出现了两次，另一些只出现一次。

找到所有在 [1, *n*] 范围之间没有出现在数组中的数字。

您能在不使用额外空间且时间复杂度为*O(n)*的情况下完成这个任务吗? 你可以假定返回的数组不算在额外空间内。

**示例:**

```
输入:
[4,3,2,7,8,2,3,1]

输出:
[5,6]
```



```java
class Solution {
    // 1 2 2 3  => -1 -2 -2 3
   public List<Integer> findDisappearedNumbers(int[] nums) {
        List<Integer> list = new LinkedList<>();
        int length = nums.length;
        for(int i=0;i<length;i++){
            int cur = Math.abs(nums[i]);// 防止 明明已经是-1 再赋值一次变 +1
            int  index =  cur - 1; // 当前索引
            if(nums[index]>0){ // 正的变负的
                nums[index] = -nums[index];
            }
        }
        for(int i = 0;i<length;i++){
            if(nums[i]>0){
                list.add(i+1);
            }
        }
        return list;
    }
}
```



## [121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)-从下标1遍历+Math.min(下标和min去比较)+Math.max(max,当前-min)

难度简单1522收藏分享切换为英文接收动态反馈

给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 `i` 天的价格。

你只能选择 **某一天** 买入这只股票，并选择在 **未来的某一个不同的日子** 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 `0` 。

 

**示例 1：**

```
输入：[7,1,5,3,6,4]
输出：5
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
```

**示例 2：**

```
输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下, 没有交易完成, 所以最大利润为 0。
```



```java
class Solution {
    public int maxProfit(int[] prices) {
        if(prices.length==0){
            return 0;
        }
       int maxRes = 0;
        int min = prices[0];
        for(int i =1;i<prices.length;i++){
            min = Math.min(min,prices[i]);
            maxRes = Math.max(maxRes,prices[i]-min);
        }

        return maxRes;
    }
}
```



## [53. 最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)

难度简单3030收藏分享切换为英文接收动态反馈

给定一个整数数组 `nums` ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

 

**示例 1：**

```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```

**示例 2：**

```
输入：nums = [1]
输出：1
```

**示例 3：**

```
输入：nums = [0]
输出：0
```

**示例 4：**

```
输入：nums = [-1]
输出：-1
```

**示例 5：**

```
输入：nums = [-100000]
输出：-100000
```





```java
class Solution {
    public int maxSubArray(int[] nums) {
        int sum=nums[0];
        int res=sum;
        if(nums.length==1) return nums[0];
        for(int i =1;i<nums.length;i++){
            sum = Math.max(nums[i],nums[i]+sum);
            res  =  Math.max(res,sum);
        }
        return res;
    }
}

```





## [70. 爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

难度简单1546收藏分享切换为英文接收动态反馈

假设你正在爬楼梯。需要 *n* 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

**注意：**给定 *n* 是一个正整数。

**示例 1：**

```
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1.  1 阶 + 1 阶
2.  2 阶
```

**示例 2：**

```
输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。
1.  1 阶 + 1 阶 + 1 阶
2.  1 阶 + 2 阶
3.  2 阶 + 1 阶
```



```java
class Solution {
    public int climbStairs(int n) {
        if(n == 1) return 1;
        if(n ==2) return 2;
        int a = 1,b= 2,target;
        for( int i = 3;i<=n;i++){
            target = a;
            a = b;
            b = target + b;
        }
        return b;
    }
}
//  1 2 3 5 8
class Solution {
public int climbStairs(int n) {
int[] dp = new int[n+1];
if(n<=1) return 1;
    dp[1]= 1;dp[2] = 2;
for(int i = 3;i<=n;i++){
        dp[i] = dp[i-1] +dp[i-2];
}
return dp[n];
}
}
```



## [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)

难度简单10584收藏分享切换为英文接收动态反馈

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** 的那 **两个** 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

你可以按任意顺序返回答案。

 

**示例 1：**

```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

**示例 2：**

```
输入：nums = [3,2,4], target = 6
输出：[1,2]
```

**示例 3：**

```
输入：nums = [3,3], target = 6
输出：[0,1]
```



```java
class Solution {
    /**
     * 1.首先想到了有map来解决
     * 2.这里会思考 下标作为key 还是 数组的值作为key
     *      2.1 下标作为key的话  就没法根据值来获取key了，因为最后是要返回 下标的
     *      所以这里我们要用 数组值作为key,下标作为value，
     *      当满足条件 我们就根据key获取value 也就是我们的下标，就可以了
     * 3.因为target 是数组中两个值相加得到的
     * @param nums
     * @param target
     * @return
     */
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer, Integer> map = new HashMap<>();
//        for(int i = 0;i<nums.length;i++){
//            map.put(nums[i],i);
//        }
        for (int j = 0; j<nums.length;j++){
            int temp = target - nums[j];
            if (map.containsKey(temp)) {
                return new int[]{j,map.get(temp)};
            }else
            {
                map.put(nums[j],j);
            }
        }
        return new  int[2];
    }
}

```



## [20. 有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)-右括号入栈，左括号去匹配，匹配到了就出栈

难度简单2260收藏分享切换为英文接收动态反馈

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s` ，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。

 

**示例 1：**

```
输入：s = "()"
输出：true
```

**示例 2：**

```
输入：s = "()[]{}"
输出：true
```

**示例 3：**

```
输入：s = "(]"
输出：false
```

**示例 4：**

```
输入：s = "([)]"
输出：false
```

**示例 5：**

```
输入：s = "{[]}"
输出：true
```



```java
class Solution {
     private Map<Character,Character> map  = null;
    public Solution()
    {
        map = new HashMap<Character,Character>();
        map.put(')','(');
        map.put('}','{');
        map.put(']','[');
    }
    public boolean isValid(String s) {
        Stack<Character> stack  = new Stack<Character>();
        for(int i =0;i<s.length();i++)
        {
            Character ch = s.charAt(i);
            if(map.containsValue(ch))  //		{[]}
            {
                stack.push(ch);
            }
            else if(map.containsKey(ch))
            {
                if(stack.empty())  return false; // 如果为null就 返回false 
                if(stack.peek()==map.get(ch)) stack.pop(); // 栈顶元素 匹配上了map的key 也就是左边括号 就pop 出去   
                else return false;
            }
        }
        return  stack.empty() ? true: false; // 如果还有元素 就是false  如果没有元素 说明都匹配完了
    }
}
```



## [461. 汉明距离](https://leetcode-cn.com/problems/hamming-distance/)

难度简单383收藏分享切换为英文接收动态反馈

两个整数之间的[汉明距离](https://baike.baidu.com/item/汉明距离)指的是这两个数字对应二进制位不同的位置的数目。

给出两个整数 `x` 和 `y`，计算它们之间的汉明距离。

**注意：**
0 ≤ `x`, `y` < 231.

**示例:**

```
输入: x = 1, y = 4

输出: 2

解释:
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑

上面的箭头指出了对应二进制位不同的位置。
```



```java
class Solution {
    public int hammingDistance(int x, int y) {
        /**
        位运算+计算多少个1
        使用 temp = temp&(temp-1)可以 直接跳过  1000001   中间的 0 
        */
        int temp = x^y; // 1^4 = 01 ^100 =  101 
        int res = 0;
        while(temp!=0){
            res ++; 
            temp = temp&(temp-1); // 101 & 100 = 100   100&011 = 000  res = 2
        }
        return res;
    }
}
```



## [617. 合并二叉树](https://leetcode-cn.com/problems/merge-two-binary-trees/)

难度简单642收藏分享切换为英文接收动态反馈

给定两个二叉树，想象当你将它们中的一个覆盖到另一个上时，两个二叉树的一些节点便会重叠。

你需要将他们合并为一个新的二叉树。合并的规则是如果两个节点重叠，那么将他们的值相加作为节点合并后的新值，否则**不为** NULL 的节点将直接作为新二叉树的节点。

**示例 1:**

```
输入: 
	Tree 1                     Tree 2                  
          1                         2                             
         / \                       / \                            
        3   2                     1   3                        
       /                           \   \                      
      5                             4   7                  
输出: 
合并后的树:
	     3
	    / \
	   4   5
	  / \   \ 
	 5   4   7
```

**注意:** 合并必须从两个树的根节点开始。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
        /**
        DFS 或者是BFS  同时递归两树，终止条件是 node1和node2同时为null
        把 树2加到树1上面
        */
        if(root1==null||root2==null) return root1==null?root2:root1;
        return dfs(root1,root2);
    }
    TreeNode dfs(TreeNode r1,TreeNode r2){
        if(r1==null||r2==null) return r1==null?r2:r1;
        r1.val += r2.val;
        r1.left = dfs(r1.left,r2.left); // 都是从根节点开始的 把 r1和r2的值相加 放到r1上面去
        r1.right = dfs(r1.right,r2.right);// 从根节点开始的
        return r1;
    }
}
```



## [543. 二叉树的直径](https://leetcode-cn.com/problems/diameter-of-binary-tree/)

难度简单649收藏分享切换为英文接收动态反馈

给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过也可能不穿过根结点。

 

**示例 :**
给定二叉树

```
          1
         / \
        2   3
       / \     
      4   5    
```

返回 **3**, 它的长度是路径 [4,2,1,3] 或者 [5,2,1,3]。

 

**注意：**两结点之间的路径长度是以它们之间边的数目表示。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    int maxed =0;
    public int diameterOfBinaryTree(TreeNode root) {
        /**
        DFS
        */
        dfs(root);
        return maxed;
    }

    int dfs(TreeNode node){
        if(node == null) return 0 ;
        int L = dfs(node.left);
        int R  = dfs(node.right);
        maxed = Math.max(maxed,L+R);
        return Math.max(L,R)+1;
    }
}
```



## [234. 回文链表](https://leetcode-cn.com/problems/palindrome-linked-list/)

难度简单912收藏分享切换为英文接收动态反馈

请判断一个链表是否为回文链表。

**示例 1:**

```
输入: 1->2
输出: false
```

**示例 2:**

```
输入: 1->2->2->1
输出: true
```



```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public boolean isPalindrome(ListNode head) {
        /**
        利用栈解决
        */
        ListNode temp = head;
        Stack<Integer> stack = new Stack();
        // 把链表放入栈中，
        while(temp!=null){
            stack.push(temp.val);
            temp = temp.next;
        }

        while(head!=null){
            if(head.val != stack.pop()){
                return false;
            }
            head = head.next;
        }
        return true;
    }
}
```

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    ListNode temp;
    public boolean isPalindrome(ListNode head) {
      /**
        递归
      */
      temp = head;
      return check(head);
    }

    boolean check(ListNode head){
        if(head==null) return  true;
        boolean res = check(head.next)&& (temp.val==head.val);
        temp = temp.next;
        return res;
    }
}
```



## [226. 翻转二叉树](https://leetcode-cn.com/problems/invert-binary-tree/)

难度简单792收藏分享切换为英文接收动态反馈

翻转一棵二叉树。

**示例：**

输入：

```
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```

输出：

```
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```



```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public TreeNode invertTree(TreeNode root) {
        /**
        DFS递归
        */
        if(root==null) return null;
        //交换当前节点的左右子树  交换引用
        //如当前节点是root 交换当前root的左右子树，如果root.left 交换 root.left.left ....   直到null，相当于栈空，root.right入栈，root.right.right...交换 直到null
        TreeNode temp = root.right;
        root.right = root.left;
        root.left = temp;
        // 递归 
        invertTree(root.left);
        invertTree(root.right);
        return root;

    }
}
```





##  [206. 反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

难度简单1619收藏分享切换为英文接收动态反馈

反转一个单链表。

**示例:**

```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

**进阶:**
你可以迭代或递归地反转链表。你能否用两种方法解决这道题？



```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        // 双指针迭代
	ListNode cur = head;//在pre前面
  	ListNode pre  = null;
    while(null!=cur){
      	ListNode temp  = cur.next; //临时存放交换用
    		cur.next = pre; // cur的下一个节点指向pre 实现翻转
      	pre=cur;// pre向左走
      	cur = temp; //cur向左走
    }
    //cur 为null 说明 pre已经是现第一个节点,返回pre
  	return pre;
    }
}
```



==用一下王尼玛大佬的递归理解图，帮助理解==

<img src="https://pic.leetcode-cn.com/f89c9e095d414b8366dd7d490508ba9c99d6c93953b4429168af262169f18e83-%E5%B9%BB%E7%81%AF%E7%89%876.jpg" alt="img" style="zoom:50%;" />



<img src="https://pic.leetcode-cn.com/04550182527e6570d9d04f2eeae330848b83d3ff13b23ea4153410210586dc85-%E5%B9%BB%E7%81%AF%E7%89%878.jpg" alt="img" style="zoom:50%;" />

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        /**
        递归
        1->2->3
        */
        if(head == null || head.next == null) return head;
        ListNode cur = reverseList(head.next); // 最后返回的cur = 3
        head.next.next = head; // 3后面返回的是2。递归是压栈出栈的问题 2.next.next = 2 == 3.next = 2
        head.next = null; // 2.next.next =2    2.next = null 
        return cur;
    }
}
```



## [160. 相交链表](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)--offer52题 一模一样





## [155. 最小栈](https://leetcode-cn.com/problems/min-stack/)--offer第30题

难度简单848收藏分享切换为英文接收动态反馈

设计一个支持 `push` ，`pop` ，`top` 操作，并能在常数时间内检索到最小元素的栈。

- `push(x)` —— 将元素 x 推入栈中。
- `pop()` —— 删除栈顶的元素。
- `top()` —— 获取栈顶元素。
- `getMin()` —— 检索栈中的最小元素。

 

**示例:**

```
输入：
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

输出：
[null,null,null,null,-3,null,0,-2]

解释：
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.
```



##  [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

难度简单997收藏分享切换为英文接收动态反馈

给定一个链表，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，我们使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 `pos` 是 `-1`，则在该链表中没有环。**注意：`pos` 不作为参数进行传递**，仅仅是为了标识链表的实际情况。

如果链表中存在环，则返回 `true` 。 否则，返回 `false` 。

 

**进阶：**

你能用 *O(1)*（即，常量）内存解决此问题吗？

 

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist.png)

```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```

**示例 2：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test2.png)

```
输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
```

**示例 3：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test3.png)

```
输入：head = [1], pos = -1
输出：false
解释：链表中没有环。
```





```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public boolean hasCycle(ListNode head) {
        /**
        快慢指针 一个走两步  一个走一步
        */
        if(head==null) return false;
        ListNode slow = head,fast = head;
        while(fast!=null && fast.next !=null){
            slow = slow.next;
            fast = fast.next.next;
            if(slow == fast) return true; 
        }
        return false;
    }
}
```

## [101. 对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)--offer28题

难度简单1300收藏分享切换为英文接收动态反馈

给定一个二叉树，检查它是否是镜像对称的。

 

例如，二叉树 `[1,2,2,3,4,4,3]` 是对称的。

```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

 

但是下面这个 `[1,2,2,null,3,null,3]` 则不是镜像对称的:

```
    1
   / \
  2   2
   \   \
   3    3
```

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public boolean isSymmetric(TreeNode root) {
		/**
		BFS
		*/
   if(root==null || (root.left==null && root.right==null)) {
			return true;
		}
		//用队列保存节点
		LinkedList<TreeNode> queue = new LinkedList<TreeNode>();
		//将根节点的左右孩子放到队列中
		queue.add(root.left);
		queue.add(root.right);
		while(queue.size()>0) {
			//从队列中取出两个节点，再比较这两个节点
			TreeNode left = queue.removeFirst();
			TreeNode right = queue.removeFirst();
			//如果两个节点都为空就继续循环，两者有一个为空就返回false
			if(left==null && right==null) {
				continue;
			}
			if(left==null || right==null) {
				return false;
			}
			if(left.val!=right.val) {
				return false;
			}
			//将左节点的左孩子， 右节点的右孩子放入队列
			queue.add(left.left);
			queue.add(right.right);
			//将左节点的右孩子，右节点的左孩子放入队列
			queue.add(left.right);
			queue.add(right.left);
		}
		
		return true;
	}


}
```



## [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)--offer25

难度简单1617收藏分享切换为英文接收动态反馈

将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/10/03/merge_ex1.jpg)

```
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]
```

**示例 2：**

```
输入：l1 = [], l2 = []
输出：[]
```

**示例 3：**

```
输入：l1 = [], l2 = [0]
输出：[0]
```



# 中等



## [56. 合并区间](https://leetcode-cn.com/problems/merge-intervals/)

难度中等918收藏分享切换为英文接收动态反馈

以数组 `intervals` 表示若干个区间的集合，其中单个区间为 `intervals[i] = [starti, endi]` 。请你合并所有重叠的区间，并返回一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间。

 

**示例 1：**

```
输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
输出：[[1,6],[8,10],[15,18]]
解释：区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```

**示例 2：**

```
输入：intervals = [[1,4],[4,5]]
输出：[[1,5]]
解释：区间 [1,4] 和 [4,5] 可被视为重叠区间。
```



```java
class Solution {
    public int[][] merge(int[][] intervals) {
        /**
        排序
        */
        if(intervals.length == 0) return new int[0][2];

        // 排序首位 =》 1-3 2-4 
        Arrays.sort( intervals , (i1,i2)->i1[0]-i2[0]
        );
        List<int[]> res = new ArrayList<>();
        for(int i = 0 ;i<intervals.length ; ++i ){
            int l = intervals[i][0], r = intervals[i][1]; //    2-4  
            if(res.size() == 0 || res.get(res.size() - 1)[1] < l ){ // 已经有1-3   3>2   
                res.add(new int[]{l,r});
            }else {
               int resLeft = res.get(res.size() - 1)[1]; // 3
                int tmp =   Math.max(resLeft , r); // 结果集中 最后一个的右边 和 当前数组的右边比较 得到大的那个 3<4   tmp = 4
                res.get(res.size() -1 )[1] = tmp; // 把 4 给 1-3  中的 int[0][1] 也就是 1-4  现在1-3 =》1-4
            }
        }
        return res.toArray(new int[res.size()][] );
    }
}
```



## [200. 岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

难度中等1065收藏分享切换为英文接收动态反馈

给你一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

 

**示例 1：**

```
输入：grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
输出：1
```

**示例 2：**

```
输入：grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
输出：3
```



```java
class Solution {
    public int numIslands(char[][] grid) {
        int count = 0;
        //DFS 遍历
        for(int i =0;i<grid.length;i++){
            for(int j =0;j<grid[0].length;j++){
                 if(grid[i][j]=='1'){
                     dfs(grid,i,j);
                     count++;
                 }
            }
        }
        return count;
    }

    void dfs(char[][] grid,int i,int j){
        if(i<0||j<0||i>=grid.length|| j>=grid[0].length|| grid[i][j]=='0') return ;
        grid[i][j] = '0';
        dfs(grid,i+1,j);
        dfs(grid,i,j+1);
        dfs(grid,i-1,j);
        dfs(grid,i,j-1);
    }
}
```





# 精选-简单



## [387. 字符串中的第一个唯一字符](https://leetcode-cn.com/problems/first-unique-character-in-a-string/)

难度简单364收藏分享切换为英文接收动态反馈

给定一个字符串，找到它的第一个不重复的字符，并返回它的索引。如果不存在，则返回 -1。

 

**示例：**

```
s = "leetcode"
返回 0

s = "loveleetcode"
返回 2
```



```java
class Solution {
    public int firstUniqChar(String s) {
        /**
        map遍历一遍，key存索引，value存char
        */
        Map<Character,Integer> map = new HashMap<>();
        char[] cs = s.toCharArray();
        for(char c :cs)
            map.put(c,map.getOrDefault(c,0)+1);
        for(int i =0;i<s.length();i++)
            if(map.get(cs[i])== 1) return i;
        return -1;
    }
}
```



##  [326. 3的幂](https://leetcode-cn.com/problems/power-of-three/)

难度简单146收藏分享切换为英文接收动态反馈

给定一个整数，写一个函数来判断它是否是 3 的幂次方。如果是，返回 `true` ；否则，返回 `false` 。

整数 `n` 是 3 的幂次方需满足：存在整数 `x` 使得 `n == 3x`

 

**示例 1：**

```
输入：n = 27
输出：true
```

**示例 2：**

```
输入：n = 0
输出：false
```

**示例 3：**

```
输入：n = 9
输出：true
```

**示例 4：**

```
输入：n = 45
输出：false
```

```java
class Solution {
    public boolean isPowerOfThree(int n) {
      // log3(3)=1  log3(9) = 2 ....
        return ( (Math.log10(n))/(Math.log10(3)) )%1==0;
    }
}
```





## [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)



给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** 的那 **两个** 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

你可以按任意顺序返回答案。

 

**示例 1：**

```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

**示例 2：**

```
输入：nums = [3,2,4], target = 6
输出：[1,2]
```

**示例 3：**

```
输入：nums = [3,3], target = 6
输出：[0,1]
```

 

**提示：**

- `2 <= nums.length <= 103`
- `-109 <= nums[i] <= 109`
- `-109 <= target <= 109`
- **只会存在一个有效答案**

```java
class Solution {
    /**
     * 1.首先想到了用map来解决
     * 2.这里会思考 下标作为key 还是 数组的值作为key
     *      2.1 下标作为key的话  就没法根据值来获取key了，因为最后是要返回 下标的
     *      所以这里我们要用 数组值作为key,下标作为value，
     *      当满足条件 我们就根据key获取value 也就是我们的下标，就可以了
     * 3.因为target 是数组中两个值相加得到的
     * @param nums
     * @param target
     * @return
     */
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer, Integer> map = new HashMap<>();
//        for(int i = 0;i<nums.length;i++){
//            map.put(nums[i],i);
//        }
        for (int j = 0; j<nums.length;j++){
            int temp = target - nums[j];
            if (map.containsKey(temp)) {
                return new int[]{j,map.get(temp)};
            }else
            {
                map.put(nums[j],j);
            }
        }
        return new  int[2];
    }
}

```



## [204. 计数质数](https://leetcode-cn.com/problems/count-primes/)

难度简单648收藏分享切换为英文接收动态反馈

统计所有小于非负整数 *`n`* 的质数的数量。

 

**示例 1：**

```
输入：n = 10
输出：4
解释：小于 10 的质数一共有 4 个, 它们是 2, 3, 5, 7 。
```

**示例 2：**

```
输入：n = 0
输出：0
```

**示例 3：**

```
输入：n = 1
输出：0
```



##  [13. 罗马数字转整数](https://leetcode-cn.com/problems/roman-to-integer/)

难度简单1245收藏分享切换为英文接收动态反馈

罗马数字包含以下七种字符: `I`， `V`， `X`， `L`，`C`，`D` 和 `M`。

```
字符          数值
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```

例如， 罗马数字 2 写做 `II` ，即为两个并列的 1。12 写做 `XII` ，即为 `X` + `II` 。 27 写做 `XXVII`, 即为 `XX` + `V` + `II` 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 `IIII`，而是 `IV`。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 `IX`。这个特殊的规则只适用于以下六种情况：

- `I` 可以放在 `V` (5) 和 `X` (10) 的左边，来表示 4 和 9。
- `X` 可以放在 `L` (50) 和 `C` (100) 的左边，来表示 40 和 90。 
- `C` 可以放在 `D` (500) 和 `M` (1000) 的左边，来表示 400 和 900。

给定一个罗马数字，将其转换成整数。输入确保在 1 到 3999 的范围内。

 

**示例 1:**

```
输入: "III"
输出: 3
```

**示例 2:**

```
输入: "IV"
输出: 4
```

**示例 3:**

```
输入: "IX"
输出: 9
```

**示例 4:**

```
输入: "LVIII"
输出: 58
解释: L = 50, V= 5, III = 3.
```

**示例 5:**

```
输入: "MCMXCIV"
输出: 1994
解释: M = 1000, CM = 900, XC = 90, IV = 4.
```



```java
class Solution {
    public int romanToInt(String s) {
     
	        int n = s.length();
			int roman_int = 0;
			for(int i=0;i<n;i++)
			{
				char str = s.charAt(i);
				
				switch(str) 
				{
				case 'I' : roman_int = roman_int + 1;break;
				case 'V' : roman_int = roman_int + 5;break;
				case 'X' : roman_int = roman_int + 10;break;
				case 'L' : roman_int = roman_int + 50;break;
				case 'C' : roman_int = roman_int + 100;break;
				case 'D' : roman_int = roman_int + 500;break;
				case 'M' : roman_int = roman_int + 1000;break;
				default: System.out.println("default");break;
				}

				if(i!=0)
				{	char ch = s.charAt(i-1);
					if(((str=='V')||(str=='X'))&&(ch=='I')) 
						roman_int = roman_int-1*2;
					if(((str=='L')||(str=='C'))&&(ch=='X'))
						roman_int = roman_int-10*2;
					if(((str=='D')||(str=='M'))&&(ch=='C'))
						roman_int = roman_int-100*2;
				}
			}
			return roman_int;
	    }	
    }

```



## [26. 删除有序数组中的重复项](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

难度简单1900收藏分享切换为英文接收动态反馈

给你一个有序数组 `nums` ，请你**[ 原地](http://baike.baidu.com/item/原地算法)** 删除重复出现的元素，使每个元素 **只出现一次** ，返回删除后数组的新长度。

不要使用额外的数组空间，你必须在 **[原地 ](https://baike.baidu.com/item/原地算法)修改输入数组** 并在使用 O(1) 额外空间的条件下完成。

 

**说明:**

为什么返回数值是整数，但输出的答案是数组呢?

请注意，输入数组是以**「引用」**方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

你可以想象内部操作如下:

```
// nums 是以“引用”方式传递的。也就是说，不对实参做任何拷贝
int len = removeDuplicates(nums);

// 在函数里修改输入数组对于调用者是可见的。
// 根据你的函数返回的长度, 它会打印出数组中 该长度范围内 的所有元素。
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

**示例 1：**

```
输入：nums = [1,1,2]
输出：2, nums = [1,2]
解释：函数应该返回新的长度 2 ，并且原数组 nums 的前两个元素被修改为 1, 2 。不需要考虑数组中超出新长度后面的元素。
```

**示例 2：**

```
输入：nums = [0,0,1,1,1,2,2,3,3,4]
输出：5, nums = [0,1,2,3,4]
解释：函数应该返回新的长度 5 ， 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4 。不需要考虑数组中超出新长度后面的元素。
```

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        TreeSet<Integer> hashSet = new TreeSet<>();
        for (int i = 0; i < nums.length; i++) {
            hashSet.add(nums[i]);
        }
        ArrayList<Integer> arrayList = new ArrayList<>(hashSet);
        int[] arr = arrayList.stream().mapToInt(Integer::valueOf).toArray();
        for (int i = 0; i < arr.length; i++) {
            nums[i] = arr[i];
        }
        return hashSet.size();
    }
}

```

##  [66. 加一](https://leetcode-cn.com/problems/plus-one/)

难度简单649收藏分享切换为英文接收动态反馈

给定一个由 **整数** 组成的 **非空** 数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储**单个**数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

 

**示例 1：**

```
输入：digits = [1,2,3]
输出：[1,2,4]
解释：输入数组表示数字 123。
```

**示例 2：**

```
输入：digits = [4,3,2,1]
输出：[4,3,2,2]
解释：输入数组表示数字 4321。
```

**示例 3：**

```
输入：digits = [0]
输出：[1]
```



```java
class Solution {
    /**
    1.处理 9  99  999  等这种情况
    2.如 89+1 = 90 899 +1 =900
    3.处理  123 + 1 = 124的情况
    */
    public int[] plusOne(int[] digits) {
        int length = digits.length;
        
        for(int i = length-1;i>=0;i--){
            // 如果是 899的情况 或者 123的情况
            if(digits[i]!=9){
                digits[i]++;
                return digits;
            }
            // 如果是 999的情况
            digits[i] = 0;
        }       
        //处理 999的情况 长度+1  第一位变0
       digits = new int[length+1];
       digits[0] = 1; 
       return digits;
    }
}
```

## [69. x 的平方根](https://leetcode-cn.com/problems/sqrtx/)

难度简单627收藏分享切换为英文接收动态反馈

实现 `int sqrt(int x)` 函数。

计算并返回 *x* 的平方根，其中 *x* 是非负整数。

由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

**示例 1:**

```
输入: 4
输出: 2
```

**示例 2:**

```
输入: 8
输出: 2
说明: 8 的平方根是 2.82842..., 
     由于返回类型是整数，小数部分将被舍去。
```

```java
class Solution {
    public int mySqrt(int x) {
        /** 根号8
        1. （8+8/8）/2 = 4
        2.  （4+4/8）/2 = 2
        3.   2*2>8 false
        */
       long a = x;
        while (a * a > x) {
            a = (a + x / a) / 2;
        }
        return (int) a;

    }
}
```



## [88. 合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)

难度简单808收藏分享切换为英文接收动态反馈

给你两个有序整数数组 `nums1` 和 `nums2`，请你将 `nums2` 合并到 `nums1` 中*，*使 `nums1` 成为一个有序数组。

初始化 `nums1` 和 `nums2` 的元素数量分别为 `m` 和 `n` 。你可以假设 `nums1` 的空间大小等于 `m + n`，这样它就有足够的空间保存来自 `nums2` 的元素。

 

**示例 1：**

```
输入：nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
输出：[1,2,2,3,5,6]
```

**示例 2：**

```
输入：nums1 = [1], m = 1, nums2 = [], n = 0
输出：[1]
```

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        for(int i =0;i<n;i++){
                nums1[i+m] = nums2[i];
        }
        Arrays.sort(nums1);
    }
}
```



## [118. 杨辉三角](https://leetcode-cn.com/problems/pascals-triangle/)

难度简单463收藏分享切换为英文接收动态反馈

给定一个非负整数 *numRows，*生成杨辉三角的前 *numRows* 行。

![img](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)

在杨辉三角中，每个数是它左上方和右上方的数的和。

**示例:**

```
输入: 5
输出:
[
     [1],
    [1,1],
   [1,2,1],
  [1,3,3,1],
 [1,4,6,4,1]
]
```

```java
class Solution {
    /**
     * 帕斯卡三角形
     *      1
     *     1 1
     *    1 2 1
     *   1 3 3 1
     *  1 4 6 4 1
     * 1 5 10 10 5 1
        1.第1排第1个 = 1
        2.第2排第1个 =1 第2个 = 1
        3.第三排第i个的 第二排 第i-1个 + 第i个
        4.第n排的第i个 = 第n-1排 的第i个+第i-1个
     */
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> resultList = new ArrayList<List<Integer>>();
        
        // 有numRows排
        for(int i =0;i<numRows;++i){
            List<Integer> row = new ArrayList<Integer>();
            // 每一排有i个
            for(int j = 0;j<=i;++j){
                //最左 和最右 为1
                if(j==0 || j==i){
                    row.add(1);
                }else{
                    //满足规则第n排的第i个 = 第n-1排 的第i个+第i-1个
                    row.add(resultList.get(i-1).get(j-1)+resultList.get(i-1).get(j));
                }
            }
            resultList.add(row);
        }
        return resultList;
    }
}
```

## [122. 买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

难度简单1155收藏分享切换为英文接收动态反馈

给定一个数组，它的第 *i* 个元素是一支给定股票第 *i* 天的价格。

设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

**注意：**你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

 

**示例 1:**

```
输入: [7,1,5,3,6,4]
输出: 7
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。
```

**示例 2:**

```
输入: [1,2,3,4,5]
输出: 4
解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。
     因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
```

**示例 3:**

```
	输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
```

```java
class Solution {
    public int maxProfit(int[] prices) {
        int result = 0;
        for(int i =1;i<prices.length;i++){
           int temp = prices[i] - prices[i-1];
            if(temp>0) result+=temp;
        }
        return result;
    }
}
```



## [217. 存在重复元素](https://leetcode-cn.com/problems/contains-duplicate/)

难度简单376收藏分享切换为英文接收动态反馈

给定一个整数数组，判断是否存在重复元素。

如果存在一值在数组中出现至少两次，函数返回 `true` 。如果数组中每个元素都不相同，则返回 `false` 。

 

**示例 1:**

```
输入: [1,2,3,1]
输出: true
```

**示例 2:**

```
输入: [1,2,3,4]
输出: false
```

**示例 3:**

```
输入: [1,1,1,3,3,4,3,2,4,2]
输出: true
```



```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        // if(nums.length==1) return false;
        // Arrays.sort(nums);
        // int cur = 0;
        // for(int i=1;i<nums.length;i++){
        //     if(nums[i]==nums[i-1]){
        //         return true;
        //     }
        // }
        // return false;
        Set<Integer> set = new HashSet<Integer>();
        for(int i : nums){
            if(!set.add(i)){
                return true;
            }
        }
        return false;
    }
}
```





## [268. 丢失的数字](https://leetcode-cn.com/problems/missing-number/)

难度简单362

给定一个包含 `[0, n]` 中 `n` 个数的数组 `nums` ，找出 `[0, n]` 这个范围内没有出现在数组中的那个数。

 

**进阶：**

- 你能否实现线性时间复杂度、仅使用额外常数空间的算法解决此问题?

 

**示例 1：**

```
输入：nums = [3,0,1]
输出：2
解释：n = 3，因为有 3 个数字，所以所有的数字都在范围 [0,3] 内。2 是丢失的数字，因为它没有出现在 nums 中。
```

**示例 2：**

```
输入：nums = [0,1]
输出：2
解释：n = 2，因为有 2 个数字，所以所有的数字都在范围 [0,2] 内。2 是丢失的数字，因为它没有出现在 nums 中。
```

**示例 3：**

```
输入：nums = [9,6,4,2,3,5,7,0,1]
输出：8
解释：n = 9，因为有 9 个数字，所以所有的数字都在范围 [0,9] 内。8 是丢失的数字，因为它没有出现在 nums 中。
```

**示例 4：**

```
输入：nums = [0]
输出：1
解释：n = 1，因为有 1 个数字，所以所有的数字都在范围 [0,1] 内。1 是丢失的数字，因为它没有出现在 nums 中。
```



```java
class Solution {
    //排序
    public int missingNumber(int[] nums) {
         Arrays.sort(nums);
        //处理第一个不是 0 的情况
        if(nums[0]!=0){
            return 0;
        }
        //处理末位 不是长度的情况
        if(nums[nums.length-1]!=nums.length){
            return nums.length;
        }
        for(int i = 1;i<nums.length;i++){
            if(nums[i]-1!=nums[i-1]){
                return nums[i-1]+1;
            }
        }
        return -1;   
    }
}
```



```java
class Solution {
    //1. 0 1 3 缺少2 长度为3
    //2. res=3^0=3  res=3^0=3 相当于3^0^0=3
    //3. res=3^1=2  res=2^1=3 相当于3^1^1=3
    //4. res=3^3=0  res=0^2=2 相当于3^3^2=2
    // 5. res = 2
    public int missingNumber(int[] nums) {
        
        int res = nums.length;
        for(int i =0;i<nums.length;i++){
            res^=nums[i];
            res^=i;
        }
               return res;
   }
}
```

## [344. 反转字符串](https://leetcode-cn.com/problems/reverse-string/)

难度简单371收藏分享切换为英文接收动态反馈

编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 `char[]` 的形式给出。

不要给另外的数组分配额外的空间，你必须**[原地](https://baike.baidu.com/item/原地算法)修改输入数组**、使用 O(1) 的额外空间解决这一问题。

你可以假设数组中的所有字符都是 [ASCII](https://baike.baidu.com/item/ASCII) 码表中的可打印字符。

 

**示例 1：**

```
输入：["h","e","l","l","o"]
输出：["o","l","l","e","h"]
```

**示例 2：**

```
输入：["H","a","n","n","a","h"]
输出：["h","a","n","n","a","H"]
```

```java
class Solution {
    public static void reverseString(char[] s){
        
     int n = s.length;
        for (int i = 0; i < n / 2; ++i) {
            int j = n - 1 - i;
            s[i] ^= s[j];
            s[j] ^= s[i];
            s[i] ^= s[j];
        }
        System.out.println(s);
    }
}
```



## [350. 两个数组的交集 II](https://leetcode-cn.com/problems/intersection-of-two-arrays-ii/)

难度简单465收藏分享切换为英文接收动态反馈

给定两个数组，编写一个函数来计算它们的交集。

 

**示例 1：**

```
输入：nums1 = [1,2,2,1], nums2 = [2,2]
输出：[2,2]
```

**示例 2:**

```
输入：nums1 = [4,9,5], nums2 = [9,4,9,8,4]
输出：[4,9]
```



```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        List<Integer> list = new ArrayList<>();
Map<Integer,Integer> map = new HashMap<>();

for(Integer num : nums1 ){
map.putIfAbsent(num,0); 
map.put(num, map.get(num)+1);
}
for(Integer num : nums2){
if(map.containsKey(num) && map.get(num)>0){
list.add(num);
map.put(num,map.get(num) -1);
}
}
int[] resArr = new int[list.size()];
for(int i=0;i<list.size();i++ ){
  resArr[i] = list.get(i);
}
return resArr;
    }
}
//方法2
 class Solution {
 public int[] intersect(int[] nums1, int[] nums2) {

        Arrays.sort(nums1);
        Arrays.sort(nums2);

        int i = 0, j = 0;
        List<Integer> list = new ArrayList<>();

        while (i < nums1.length && j < nums2.length) {

            if (nums1[i] == nums2[j]) {
                list.add(nums1[i]);
                i++;
                j++;
            } else if (nums1[i] < nums2[j]) i++;
            else  j++;
        }
        int[] res = new int[list.size()];
        int index = 0;
        for (int num : list) {
            res[index++] = num;
        }
        return res;
    }
 }


```



## [237. 删除链表中的节点](https://leetcode-cn.com/problems/delete-node-in-a-linked-list/)

难度简单866收藏分享切换为英文接收动态反馈

请编写一个函数，使其可以删除某个链表中给定的（非末尾）节点。传入函数的唯一参数为 **要被删除的节点** 。

 

现有一个链表 -- head = [4,5,1,9]，它可以表示为:

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/01/19/237_example.png)

 

**示例 1：**

```
输入：head = [4,5,1,9], node = 5
输出：[4,1,9]
解释：给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9.
```

**示例 2：**

```
输入：head = [4,5,1,9], node = 1
输出：[4,5,9]
解释：给定你链表中值为 1 的第三个节点，那么在调用了你的函数之后，该链表应变为 4 -> 5 -> 9.
```

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public void deleteNode(ListNode node) {
        /**
        1->2->3  删除2
        node.val = node.next.val;
    node.next = node.next.next;
        */
        node.val= node.next.val;
        node.next = node.next.next;

    }
}
```





## [204. 计数质数](https://leetcode-cn.com/problems/count-primes/)

难度简单648收藏分享切换为英文接收动态反馈

统计所有小于非负整数 *`n`* 的质数的数量。

 

**示例 1：**

```
输入：n = 10
输出：4
解释：小于 10 的质数一共有 4 个, 它们是 2, 3, 5, 7 。
```

**示例 2：**

```
输入：n = 0
输出：0
```

**示例 3：**

```
输入：n = 1
输出：0
```





```java
class Solution {
    public boolean isHappy(int n) {
        /**
        快慢指针  慢一个每次走一步 快的每一次走两步
        如果 是快乐数 fast 比 slow 要先跑到1，
        如果 不是快乐数  fast 会相遇  
        */
        int slow = n; 
        int fast = next(n);
        while(fast!=1&&fast!=slow){
            slow = next(slow);
            fast = next(next(fast));
        }
        return fast == 1;

    }

    // 计算下一个数字
    public int next(int n){
            int sum = 0;
            while(n>0){
                int a = n%10;
                n = n/10;
                sum += a*a;
            }
            return sum;
        }

}
```



## [191. 位1的个数](https://leetcode-cn.com/problems/number-of-1-bits/)

难度简单332收藏分享切换为英文接收动态反馈

编写一个函数，输入是一个无符号整数（以二进制串的形式），返回其二进制表达式中数字位数为 '1' 的个数（也被称为[汉明重量](https://baike.baidu.com/item/汉明重量)）。

 

**提示：**

- 请注意，在某些语言（如 Java）中，没有无符号整数类型。在这种情况下，输入和输出都将被指定为有符号整数类型，并且不应影响您的实现，因为无论整数是有符号的还是无符号的，其内部的二进制表示形式都是相同的。
- 在 Java 中，编译器使用[二进制补码](https://baike.baidu.com/item/二进制补码/5295284)记法来表示有符号整数。因此，在上面的 **示例 3** 中，输入表示有符号整数 `-3`。

 

**示例 1：**

```
输入：00000000000000000000000000001011
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。
```

**示例 2：**

```
输入：00000000000000000000000010000000
输出：1
解释：输入的二进制串 00000000000000000000000010000000 中，共有一位为 '1'。
```

**示例 3：**

```
输入：11111111111111111111111111111101
输出：31
解释：输入的二进制串 11111111111111111111111111111101 中，共有 31 位为 '1'。
```



```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        /**
        n&(n-1)  可以检测最后一位 是否为0
        */
        int res = 0;
        while(n!=0){  //  9  1001
            n &=n-1; //   1001 & 1000 = 1000 = 8   1000 & 0101 = 0
            res ++; //  res = 1   res = 2
        }
        return res;// 2
    }
}
```



## [125. 验证回文串](https://leetcode-cn.com/problems/valid-palindrome/)

难度简单353收藏分享切换为英文接收动态反馈

给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。

**说明：**本题中，我们将空字符串定义为有效的回文串。

**示例 1:**

```
输入: "A man, a plan, a canal: Panama"
输出: true
```

**示例 2:**

```
输入: "race a car"
输出: false
```



```java

class Solution {
    public boolean isPalindrome(String s) {
        /**
        双指针，一个从左到右  一个从右到左 忽略大小写 和字符
        */
        int len = s.length();
        int a = 0,b =len-1;
        while(a<b){
            // 如果是字母或者是数字就退出 比较
            while (a < b && !Character.isLetterOrDigit(s.charAt(a))) {
                ++a;
            }
            // 如果是字母或者是数字就退出
            while (a < b && !Character.isLetterOrDigit(s.charAt(b))) {
                --b;
            }
            
            if (a < b) {
                //比较两个字母是否一致
                if (Character.toLowerCase(s.charAt(a)) != Character.toLowerCase(s.charAt(b))) {
                    return false;
                }
                ++a;
                --b;
            }
          
        }

        return true;
    }
}

```







##  [28. 实现 strStr()](https://leetcode-cn.com/problems/implement-strstr/)

难度简单758收藏分享切换为英文接收动态反馈

实现 [strStr()](https://baike.baidu.com/item/strstr/811469) 函数。

给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回 **-1**。

**示例 1:**

```
输入: haystack = "hello", needle = "ll"
输出: 2
```

**示例 2:**

```
输入: haystack = "aaaaa", needle = "bba"
输出: -1
```

**说明:**

当 `needle` 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。

对于本题而言，当 `needle` 是空字符串时我们应当返回 0 。这与C语言的 [strstr()](https://baike.baidu.com/item/strstr/811469) 以及 Java的 [indexOf()](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#indexOf(java.lang.String)) 定义相符。





```java

class Solution {
    public int strStr(String haystack, String needle) {
        /**
        abc  b     3   1
        */
        int a=0,b=0,bigLen=haystack.length(),chileLen=needle.length(),bigIndex=0;
        while( (a<bigLen)&&(b<chileLen) ){ // 2<3  1<1  false
            if(haystack.charAt(a)==needle.charAt(b)) { // a!=b   b=b
                a++;b++;    // a=2  b = 1
            }else { 
                a= ++bigIndex; // a =1
                b=0; // 0
            }
        }
       return b>=chileLen?bigIndex:-1;
    }
}

```



## [108. 将有序数组转换为二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/)

难度简单732收藏分享切换为英文接收动态反馈

给你一个整数数组 `nums` ，其中元素已经按 **升序** 排列，请你将其转换为一棵 **高度平衡** 二叉搜索树。

**高度平衡** 二叉树是一棵满足「每个节点的左右两个子树的高度差的绝对值不超过 1 」的二叉树。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2021/02/18/btree1.jpg)

```
输入：nums = [-10,-3,0,5,9]
输出：[0,-3,9,-10,null,5]
解释：[0,-10,5,null,-3,null,9] 也将被视为正确答案：
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2021/02/18/btree.jpg)

```
输入：nums = [1,3]
输出：[3,1]
解释：[1,3] 和 [3,1] 都是高度平衡二叉搜索树。
```



```java

/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        /**
        二叉搜索树的中序遍历是有序的，选择一个作为root 然后递归 给这个root 添加left和right
        */
        return dfs(nums,0,nums.length-1);
    }

    TreeNode dfs(int[] a,int left,int right){
        if(left>right) return null;
        int mid = (left+right)/2; // 取中间的作为root
        TreeNode root = new TreeNode(a[mid]);
        root.left = dfs(a,left,mid-1); // 把root左边的作为root的left
        root.right = dfs(a,mid+1,right);// 右边的作为root的right
        return root;
    }
}
```



## [7. 整数反转](https://leetcode-cn.com/problems/reverse-integer/)

难度简单2641收藏分享切换为英文接收动态反馈

给你一个 32 位的有符号整数 `x` ，返回将 `x` 中的数字部分反转后的结果。

如果反转后整数超过 32 位的有符号整数的范围 `[−231, 231 − 1]` ，就返回 0。

**假设环境不允许存储 64 位整数（有符号或无符号）。**

 

**示例 1：**

```
输入：x = 123
输出：321
```

**示例 2：**

```
输入：x = -123
输出：-321
```

**示例 3：**

```
输入：x = 120
输出：21
```

**示例 4：**

```
输入：x = 0
输出：0
```



```java
class Solution {
    public int reverse(int x) {
        /**
        通过 123/100 = 1%10 = 1   123/10 = 12%10 = 2 123%10 = 3
        2147483647  
        */
        int res = 0,max = 0;
        while(x!=0){
            int temp = x%10; // temp = 2
            max = res;    //   max  = res = 746384741
            res = res*10+temp; // res =  -1126087180
            if(res/10 != max){ // 746384741  !=  -1126087180
                return 0;
            }
            x /= 10;
        }
        return res;
    }
}
```



## [14. 最长公共前缀](https://leetcode-cn.com/problems/longest-common-prefix/)

难度简单1529收藏分享切换为英文接收动态反馈

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 `""`。

 

**示例 1：**

```
输入：strs = ["flower","flow","flight"]
输出："fl"
```

**示例 2：**

```
输入：strs = ["dog","racecar","car"]
输出：""
解释：输入不存在公共前缀。
```



```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if((strs==null)|| (strs.length == 0) ) return "";
        StringBuilder sb = new StringBuilder();
        Arrays.sort(strs);
        char[] a = strs[0].toCharArray();
        char[] b = strs[strs.length-1].toCharArray();
        for(int i = 0;i<a.length;i++){
                if (i < b.length && a[i] == b[i]) {
                sb.append(a[i]);
            }
            else{
                break;
            }

        }
        return sb.toString();
    }
}
```



# 精选-中等



## [2. 两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

难度中等6089收藏分享切换为英文接收动态反馈

给你两个 **非空** 的链表，表示两个非负的整数。它们每位数字都是按照 **逆序** 的方式存储的，并且每个节点只能存储 **一位** 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

 

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2021/01/02/addtwonumber1.jpg)

```
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.
```

**示例 2：**

```
输入：l1 = [0], l2 = [0]
输出：[0]
```

**示例 3：**

```
输入：l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
输出：[8,9,9,9,0,0,0,1]
```



```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {

        /**
        1-2-3  +  1-2-8    251   
        */
            // 空的头节点
            ListNode res = new ListNode(0);
           //cur指针
            ListNode cur = res;
            int carry = 0;

            while(null!=l1 || null!=l2){
                int x = l1 == null ? 0 : l1.val;
                int y = l2 == null ? 0 :  l2.val;
                int sum = x+y+carry;  //  1+1 = 2  
                //得到当前结点的sum值
                carry = sum/10;    //   0
                //取出当前进位
                cur.next = new ListNode(sum%10);  // 2
                //将sum值指向下一个结点（如果是15则取5，是5的话就取5）
                cur = cur.next;
                //指针前移
                if(null != l1) l1 = l1.next;
                //l1链表前移
                if(null != l2) l2 = l2.next;
                //l2链表前移
            }
            if(carry != 0) cur.next = new ListNode(carry);
            //如果还有进位，则cur链表再新创一个节点，里面数值为carry这个进位
            return res.next;
    }
}
```





## [3. 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)-滑动窗口

难度中等5366收藏分享切换为英文关闭提醒反馈

给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度。

 

**示例 1:**

```
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

**示例 2:**

```
输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

**示例 3:**

```
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

**示例 4:**

```
输入: s = ""
输出: 0
```

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        /**
        1.转char[]  可以用两个快慢指南针 a b  a!=b b向前走， a=b 保存当前长度 然后Math(cur,pre)，然后 a=b 再 b再走
        2. aba
        */
        if(s.length()==0) return 0;
        Map<Character, Integer> map = new HashMap<>();
        int res = 0;
        for(int a=0,b=0;b<s.length();b++){ // a不走，b走，相同时 把b给a  b再继续走  
            char ch =s.charAt(b);  //   a   b  a
            if(map.containsKey(ch)){ //        a
                a = Math.max(map.get(ch),a);  // a = max(1,0) = 1 
            }
            res = Math.max(b-a+1,res); // a从0开始   res = 1  res = (1-0+1,1) = 2
            map.put(s.charAt(b),b+1); // b+1  aba 表示从 第一个元素开始重复   put(a,1) put(b,2)
        }
        return res;
    }
}
```





## [11. 盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)

难度中等2410收藏分享切换为英文接收动态反馈

给你 `n` 个非负整数 `a1，a2，...，a``n`，每个数代表坐标中的一个点 `(i, ai)` 。在坐标内画 `n` 条垂直线，垂直线 `i` 的两个端点分别为 `(i, ai)` 和 `(i, 0)` 。找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。

**说明：**你不能倾斜容器。

 

**示例 1：**

![img](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

**示例 2：**

```
输入：height = [1,1]
输出：1
```

**示例 3：**

```
输入：height = [4,3,2,1,4]
输出：16
```

**示例 4：**

```
输入：height = [1,2,1]
输出：2
```



```java
class Solution {
    public int maxArea(int[] height) {
        /**
        双指针  小的移动
        */
        int left = 0 ,right = height.length-1,res = 0;

        while(left<right){ 
            if(height[left] < height[right]  ){ // 如果左 < 右  比如 3<5
                res = Math.max(res,(right-left)*height[left]); // 长 * 小的3
                left++;// 
            }else{
                res =  Math.max(res,(right-left)*height[right]);// 否则 左 >右  比如 5*3    长* 右
                right--;  // 然后  右边 的 左移
            }
        }
        return res;

    }
}
```





## [15. 三数之和](https://leetcode-cn.com/problems/3sum/)

难度中等3286收藏分享切换为英文接收动态反馈

给你一个包含 `n` 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 *a，b，c ，*使得 *a + b + c =* 0 ？请你找出所有和为 `0` 且不重复的三元组。

**注意：**答案中不可以包含重复的三元组。

 

**示例 1：**

```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
```

**示例 2：**

```
输入：nums = []
输出：[]
```

**示例 3：**

```
输入：nums = [0]
输出：[]
```



```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
	        List<List<Integer>> list = new ArrayList();
	        int len = nums.length;
	        if(nums == null || len < 3) return list;
	        Arrays.sort(nums); // 排序
	        for (int i = 0; i < len ; i++) {
	            if(nums[i] > 0) break; // 如果当前数字大于0，则三数之和一定大于0，所以结束循环
	            if(i > 0 && nums[i] == nums[i-1]) continue; // 去重
	            int L = i+1;
	            int R = len-1;
	            while(L < R){
	                int sum = nums[i] + nums[L] + nums[R];
	                if(sum == 0){
	                    list.add(Arrays.asList(nums[i],nums[L],nums[R]));
	                    while (L<R && nums[L] == nums[L+1]) L++; // 去重
	                   // while (L<R && nums[R] == nums[R-1]) R--; // 去重
	                    L++;
	                    R--;
	                }
	                else if (sum < 0) L++;
	                else if (sum > 0) R--;
	            }
	        }        
	        return list;
	    }
    }

```



## [17. 电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

难度中等1280收藏分享切换为英文接收动态反馈

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。答案可以按 **任意顺序** 返回。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/original_images/17_telephone_keypad.png)

 

**示例 1：**

```
输入：digits = "23"
输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

**示例 2：**

```
输入：digits = ""
输出：[]
```

**示例 3：**

```
输入：digits = "2"
输出：["a","b","c"]
```

```java
class Solution {
    public List<String> letterCombinations(String digits) {
       
        /**
        全排列
        */
        String[] letter_map = {"","*","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"};


        List<String> res = new ArrayList<>();
         if(digits.length() == 0) return res;
		//先往队列中加入一个空字符
		res.add("");
		for(int i=0;i<digits.length();i++) {        // 递归次数 2  3  两次
			//由当前遍历到的字符，取字典表中查找对应的字符串
			String letters = letter_map[digits.charAt(i)-'0'];
			int size = res.size();
			//计算出队列长度后，将队列中的每个元素挨个拿出来
			for(int j=0;j<size;j++) {                // "" 和abc       "a" "b"  "c" 和 "def"
				//每次都从队列中拿出第一个元素
				String tmp = res.remove(0);
				//然后跟"def"这样的字符串拼接，并再次放到队列中
				for(int k=0;k<letters.length();k++) {   // 第一次 "a" "b"  "c"    "a"和"def"  "b" 和"def"  "c" 和 "def"
					res.add(tmp+letters.charAt(k));
				}
			}
		}
		return res;


        
    }
}
```



## [19. 删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

难度中等1344收藏分享切换为英文接收动态反馈

给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

**进阶：**你能尝试使用一趟扫描实现吗？

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg)

```
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

**示例 2：**

```
输入：head = [1], n = 1
输出：[]
```

**示例 3：**

```
输入：head = [1,2], n = 1
输出：[1]
```



```java

/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        /**
        1.遍历两遍，第一次长度，第二次删除 len-n node
        2.维持前后两个节点 n个长度的node全局  比如 2   1 2 3 4 5   ;1 2 3  
        一开始前节点 在3  后节点在1 ，然后每次一起走， 快慢指针
        */
        ListNode res = new ListNode(0);
        res.next = head;
        ListNode r = res,l = res;
        // 维持n个长度的区间
       while(n!=0){
           r = r.next;
           n--;
       }
       // 前后节点 一起走
        while(r.next != null){
            r = r.next ;
            l = l.next;
        } 
        l.next = l.next.next;
        return res.next;// 在res上操作的，

    }
}
```













## [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)-二分查找

难度中等982收藏分享切换为英文接收动态反馈

给定一个按照升序排列的整数数组 `nums`，和一个目标值 `target`。找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 `target`，返回 `[-1, -1]`。

**进阶：**

- 你可以设计并实现时间复杂度为 `O(log n)` 的算法解决此问题吗？

 

**示例 1：**

```
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```

**示例 2：**

```
输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]
```

**示例 3：**

```
输入：nums = [], target = 0
输出：[-1,-1]
```

```java

class Solution {
    public int[] searchRange(int[] nums, int target) {
        /**
        1.排序，直接找中间 找到了 就向左  向右 找
        */
       int[] res = new int[]{-1,-1};
        if(nums.length == 0) return res;
        if(nums.length== 1 && nums[0] == target) return new int[]{0,0};    
        int numsLen = nums.length;
         int l = 0,r =numsLen - 1 ,index = -1;
         while(l<=r){
             int tmp = l+r >> 1;
             if(nums[tmp] == target){
                index = tmp;
                break;
             } 
             if(nums[tmp]>target)   r = tmp-1 ;
             if(nums[tmp]<target)   l = tmp+1;
         }
        if(index != -1 ){
            int start = index,end = index;
           while (start - 1 >= 0 && nums[start - 1] == target) {
                start--;
            }
            while (end + 1 <= numsLen - 1 && nums[end + 1] == target) {
                end++;
            }
            res[0] = start;
            res[1] = end;
        }
        return res;
     }
}
```



## [46. 全排列](https://leetcode-cn.com/problems/permutations/)

难度中等1300收藏分享切换为英文接收动态反馈

给定一个 **没有重复** 数字的序列，返回其所有可能的全排列。

**示例:**

```
输入: [1,2,3]
输出:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

```java
class Solution {
  List<List<Integer>> res = new ArrayList<>();
  List<Integer> path = new ArrayList<>();
    public List<List<Integer>> permute(int[] nums) {
        // 123  =  1  + [2,3] or [3,2]
        // 213 =   2  + [1,3] or [3,1]
        // 312 =   3  = [1,2] or [2,1]
        // dfs 
        int len  = nums.length;
        if(len == 0 ) return res;
        boolean[] isUse = new boolean[len];//是否已经使用过 默认都是false
        dfs(nums,len,0,path,isUse,res);
        return res;
    }
    void dfs(int[] nums,int len,int depth,List<Integer> path,boolean[] isUse, List<List<Integer>> res){
        if(depth == len){
            res.add(new ArrayList<>(path));
            return;
        }

        for(int i =0;i<len;i++){
            if(!isUse[i]){ //如果还没有使用  
                path.add(nums[i]);
                isUse[i] = true; // 表示已经使用过
                dfs(nums,len,depth+1,path,isUse,res); // 向下递归
                isUse[i] =false;// 让 2 和 3重新变为没有使用过  现在  123 132已经走完
                path.remove(path.size()-1);
            }

        }

    }
}
```

![image.png](https://pic.leetcode-cn.com/0bf18f9b86a2542d1f6aa8db6cc45475fce5aa329a07ca02a9357c2ead81eec1-image.png)









## [22. 括号生成](https://leetcode-cn.com/problems/generate-parentheses/)

难度中等1733收藏分享切换为英文接收动态反馈

数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

 

**示例 1：**

```
输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]
```

**示例 2：**

```
输入：n = 1
输出：["()"]
```

```java
class Solution {
    List<String> res = new ArrayList<>();
    public List<String> generateParenthesis(int n) {
        /**
        递归
        */
        dfs(n,n,"");
        return res;
    }
    void dfs(int left ,int right,String tmp){
        if(left == 0 && right == 0){
            res.add(tmp);
            return ;
        }
        if(left>0) dfs(left-1,right,tmp+"(");// 表明可以添加 左括号
        if(right>left) dfs(left,right-1,tmp+")");// 表明可以添加右括号
    }
}
```



## [94. 二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

难度中等914收藏分享切换为英文接收动态反馈

给定一个二叉树的根节点 `root` ，返回它的 **中序** 遍历。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/09/15/inorder_1.jpg)

```
输入：root = [1,null,2,3]
输出：[1,3,2]
```

**示例 2：**

```
输入：root = []
输出：[]
```

**示例 3：**

```
输入：root = [1]
输出：[1]
```

**示例 4：**

![img](https://assets.leetcode.com/uploads/2020/09/15/inorder_5.jpg)

```
输入：root = [1,2]
输出：[2,1]
```

**示例 5：**

![img](https://assets.leetcode.com/uploads/2020/09/15/inorder_4.jpg)

```
输入：root = [1,null,2]
输出：[1,2]
```



```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        /**
        中： left root  right
        */
        List<Integer> res = new ArrayList<>();
        dfs(res,root);
        return res;
    }
    // dfs 
    void dfs(List res,TreeNode root){
        if(root==null) return ;
        dfs(res,root.left);
        res.add(root.val);
        dfs(res,root.right);
    }
}
```



## [102. 二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

难度中等857收藏分享切换为英文接收动态反馈

给你一个二叉树，请你返回其按 **层序遍历** 得到的节点值。 （即逐层地，从左到右访问所有节点）。

 

**示例：**
二叉树：`[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

返回其层序遍历结果：

```
[
  [3],
  [9,20],
  [15,7]
]
```



```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        /**
        队列 BFS 逐层 每一层 从左到右 放到队列中 放的同时判断 有没有左右节点 
        */
        if(root==null) return new ArrayList<List<Integer>>();

        List<List<Integer>>  res =  new ArrayList<List<Integer>>();
        LinkedList<TreeNode> curQueue = new LinkedList<>();
        curQueue.add(root);
        while(curQueue.size()>0){
            int size  =  curQueue.size();
            List<Integer> tmpRes = new ArrayList<>();
            for(int i = 0 ; i<size ; i ++){
              TreeNode  cur  =  curQueue.remove();
                tmpRes.add(cur.val);
                if(null != cur.left ) curQueue.add(cur.left);
                if(null != cur.right ) curQueue.add(cur.right);
            }
           res.add(tmpRes);
         }
    return res;
    }
}
```



## [105. 从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)-offer-07

难度中等1021收藏分享切换为英文接收动态反馈

根据一棵树的前序遍历与中序遍历构造二叉树。

**注意:**
你可以假设树中没有重复的元素。

例如，给出

```
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
```

返回如下的二叉树：

```
    3
   / \
  9  20
    /  \
   15   7
```



```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    Map<Integer,Integer> map = new HashMap<>();
    int[]  tmp = null;
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        // 3 9 20 15 7   9  3  15 20  7
        for(int i = 0;i<inorder.length;i++){
            map.put(inorder[i],i);
        }
        this.tmp = preorder;
       return build(0,0,inorder.length-1);
    }
    TreeNode build(int cur,int left,int right){
        if(left>right) return null;
        int index = map.get(tmp[cur]);
        TreeNode root = new TreeNode(tmp[cur]);
        root.left = build(cur+1,left,index-1);//1  0  0
        root.right = build(cur+index-left+1,index+1,right);//  15 15 7
        return root;
    }
}

```





## [146. LRU 缓存机制](https://leetcode-cn.com/problems/lru-cache/)- 自己实现数据结构

难度中等1363收藏分享切换为英文接收动态反馈

运用你所掌握的数据结构，设计和实现一个 [LRU (最近最少使用) 缓存机制](https://baike.baidu.com/item/LRU) 。

实现 `LRUCache` 类：

- `LRUCache(int capacity)` 以正整数作为容量 `capacity` 初始化 LRU 缓存
- `int get(int key)` 如果关键字 `key` 存在于缓存中，则返回关键字的值，否则返回 `-1` 。
- `void put(int key, int value)` 如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字-值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。

 

**进阶**：你是否可以在 `O(1)` 时间复杂度内完成这两种操作？

 

**示例：**

```
输入
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
输出
[null, null, null, 1, null, -1, null, -1, 3, 4]

解释
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1);    // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2);    // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1);    // 返回 -1 (未找到)
lRUCache.get(3);    // 返回 3
lRUCache.get(4);    // 返回 4
```



- 自己实现数据结构

```java
class LRUCache {
    // ===========================构建双向链表Cache（实现LRU）和Node（节点）开始

    // 双向链表节点
    class Node {
    public int key, val;
    public Node next, prev;
    public Node(int k, int v) {
        this.key = k;
        this.val = v;
        }
    }
    //双链表
    class DoubleList {

    private Node head, tail;
    private int size;
    //头部增加
    public void addFirst(Node node) {
        if (head == null) {
            head = tail = node;
        } else {
            Node n = head;
            n.prev = node;
            node.next = n;
            head = node;
        }
        size++;
    }
    // 删除
    public void remove(Node node) {
        // 只有一个
        if (head == node && tail == node) {
            head = null;
            tail = null;
            // 两个，删除目标值
        } else if (tail == node) {
            node.prev.next = null;
            tail = node.prev;
            // 删除头部
        } else if (head == node) {
            node.next.prev = null;
            head = node.next;
        } else {
            node.prev.next = node.next;
            node.next.prev = node.prev;
        }
        size--;
    }
    // 删除最少使用的 
    public Node removeLast() {
        Node node = tail;
        remove(tail);
        return node;
    }
    // 大小
    public int size() {
        return size;
        }
    }


    // ===================== 构建双向链表Cache和Node结束




    private HashMap<Integer, Node> map;
    private DoubleList cache;
    private int size;


    public LRUCache(int capacity) {
        this.size = capacity;
        map = new HashMap<>();
        cache = new DoubleList();
    }
    
    public int get(int key) {
          if(!map.containsKey(key)) return -1;//如果不存在 
            int val = map.get(key).val; // 存中 取出来Node.val  返回
         put(key, val); // 并且 添加到缓存链表头节点 满的时后删最后一个节点 
         return val;
    }
    
    public void put(int key, int value) {
        Node x = new Node(key, value);

        if (map.containsKey(key)){ // key 存在 就覆盖
        cache.remove(map.get(key)); // 从缓存链表中删除 原node
        cache.addFirst(x);     //  刚put进来 新元素 放头部
        map.put(key,x); // 放入map中
        } else {
        if (size == cache.size()) { // key不存在  新元素， 先看 容量是否满了
            Node last = cache.removeLast();  // 满了 就删除 缓存链表最后一个节点，因为是一直没使用
            map.remove(last.key); // 然后 从map删除
        }
        cache.addFirst(x);  //  不存中新添加的元素  容量也没满  就正常添加， 到缓存链表头部
        map.put(key,x);
        }
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```

- 继承LinkedHashMap实现LRU

```java
class LRUCache extends LinkedHashMap<Integer, Integer>{
    private int capacity;
    public LRUCache(int capacity) {
        super(capacity, 0.75f, true);
        this.capacity = capacity;
    }
    
    public int get(int key) {
        Integer n = super.get(key);
        return n == null ? -1 : n;
    }
    
    public void put(int key, int value) {
        super.put(key, value);  
    }
    @Override
   protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
       return size() > capacity;
   }
}
```



## [230. 二叉搜索树中第K小的元素](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/)

难度中等381收藏分享切换为英文接收动态反馈

给定一个二叉搜索树的根节点 `root` ，和一个整数 `k` ，请你设计一个算法查找其中第 `k` 个最小元素（从 1 开始计数）。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2021/01/28/kthtree1.jpg)

```
输入：root = [3,1,4,null,2], k = 1
输出：1
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2021/01/28/kthtree2.jpg)

```
输入：root = [5,3,6,2,4,null,null,1], k = 3
输出：3
```





```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public int count,res;
    public int kthSmallest(TreeNode root, int k) {    
        /**
        二叉搜索树中序遍历是升序
        */
        count = k;
         dfs(root,k);
         return res;
    }
    // 
    void dfs(TreeNode root, int k){
        if(root == null) return ;
        dfs(root.left,k);
        if(--count == 0){
            res = root.val;
            return ;
        } 
        dfs(root.right,k);
    }
}
```



 

