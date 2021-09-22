# LeetCode刷题笔记

# 一. 树

### 1. [二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

递归

```go
var result []int
func inorderTraversal(root *TreeNode) []int {
    result = []int{}
    order(root)
    return result
}

func order(root *TreeNode){
    if root == nil {  
        return
    }

    order(root.Left)
    result = append(result,root.Val)
    order(root.Right)
}
```

迭代

将所有左子树入栈，弹出栈顶，加入结果集，如有右子树，则把他和他的所有左子树入栈，直至栈为空

```go
func inorderTraversal(root *TreeNode) []int {
    result := []int{}    
    stack := []*TreeNode{}

    for root != nil {
        stack = append(stack,root)
        root = root.Left
    }

    for len(stack) != 0 {
        node := stack[len(stack)-1]
        stack = stack[:len(stack)-1]
        result = append(result,node.Val)

        node = node.Right
        for node != nil {
            stack = append(stack,node)
            node = node.Left
        }
    }
    return result
}
```

### 2.[不同的二叉搜索树 II](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)

```
给你一个整数 n ，请你生成并返回所有由 n 个节点组成且节点值从 1 到 n 互不相同的不同 二叉搜索树 。可以按 任意顺序 返回答案。
输入：n = 3
输出：[[1,null,2,null,3],[1,null,3,2],[2,1,3],[3,1,null,null,2],[3,2,null,1]]
```

二叉搜索树关键的性质是根节点的值大于左子树所有节点的值，小于右子树所有节点的值，且左子树和右子树也同样为二叉搜索树。

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func generateTrees(n int) []*TreeNode {
    if n == 0 {
        return nil
    }
    return doSelect(1,n)
}

func doSelect(start,end int) []*TreeNode {
    if start > end {
        return []*TreeNode{nil}
    }

    allTrees := []*TreeNode{}

    //枚举所有可行的结点
    for i := start; i <= end; i++ {
        //获得所有可能的左子树
        leftTrees := doSelect(start,i-1)
        //获得所有可能的右子树
        rightTrees := doSelect(i+1,end)
        // 从左子树集合中选出一棵左子树，从右子树集合中选出一棵右子树，拼接到根节点上
        for _, left := range leftTrees {
            for _, right := range rightTrees{
                tmp := &TreeNode{i,nil,nil}
                tmp.Left = left
                tmp.Right = right
                allTrees = append(allTrees,tmp)
            }
        }
    } 
    return allTrees
}
```









# 二.动态规划

1. 确定dp数组以及下标含义
2. 确定递推公式
3. dp数组初始化
4. 确定遍历顺序
5. 举例推导dp数组



### 1.[不同的二叉搜索树](https://leetcode-cn.com/problems/unique-binary-search-trees/)

```
给你一个整数 n ，求恰由 n 个节点组成且节点值从 1 到 n 互不相同的 二叉搜索树 有多少种？返回满足题意的二叉搜索树的种数。
```





# 三.其他

### 1.两数之和

设置哈希表，如果有则返回，没有则插入哈希表中

```go
func twoSum(nums []int, target int) []int {
   // 创建hashtable,key值为num，value为索引i
   hashTable := map[int]int{}
   for i, x := range nums {
     	// 如果哈希表中有值，则ok为true
      if p, ok := hashTable[target-x]; ok {
         return []int{p, i}
      }
      hashTable[x] = i
   }
   return nil
}
```

### 2.合并双链表

**设置哨兵节点**

```go
func mergeTwoLists(l1 *ListNode, l2 *ListNode) *ListNode {
    prehead := &ListNode{}
  	//设置头结点
    result := prehead
    for l1 != nil && l2 != nil {
        if l1.Val < l2.Val {
            prehead.Next = l1
            l1 = l1.Next
        }else{
            prehead.Next = l2
            l2 = l2.Next
        }
        prehead = prehead.Next
    }
    // 如果l1不为空，则将l1链接上prehead
    if l1 != nil {
        prehead.Next = l1
    }
  	// 如果l2不为空，则将l2链接上prehead
    if l2 != nil {
        prehead.Next = l2
    }
    return result.Next
}
```



### 3.两数相加(链表)

```
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.
```

```go
func addTwoNumbers(l1 *ListNode, l2 *ListNode) *ListNode {

    tmp := &ListNode{0,nil}
    result := tmp
    num := 0

    // 当l1不为空，l2不为空，num不为0 一直循环
    for l1 != nil || l2 != nil || num > 0 {
        tmp.Next = &ListNode{0,nil}
        tmp = tmp.Next
        if l1 != nil {
            num += l1.Val
            l1 = l1.Next
        }

        if l2 !=nil {
            num += l2.Val
            l2 = l2.Next
        }
        tmp.Val = num % 10
        num = num/10
    }
    return result.Next
}
```

