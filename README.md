

# LeetCode刷题笔记

# 一. 树

## 1.中序遍历

适用：二叉搜索树

### 1.1 [二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

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

    node := root
    // 当node不为空或者stack不为空
    for node != nil || len(stack) != 0 {
        // 入栈
      	for node != nil {
            stack = append(stack,node)
            node = node.Left
        }
        // 获得栈顶元素，出栈
        node = stack[len(stack)-1]
        stack = stack[:len(stack)-1]
        result = append(result,node.Val)

        node = node.Right

    }
    return result
}
```



### 1.2 [不同的二叉搜索树 II](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)

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



### 1.3.[验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

陷阱：以下这种递归的写法是错误的，单纯的比较左右节点的值与根节点的值。**而右节点的左节点有可能会小于根节点的值**

![二叉搜索树](https://gitee.com/lzw657434763/pictures/raw/master/Blog/20210923105952.png)

```GO
func isValidBST(root *TreeNode) bool {
    if root == nil {
        return true
    }

    if (root.Left.Val >= root.Val) {
        return false;
    }

    if (root.Right.Val <= root.Val) {
        return false;
    }
    
    return isValidBST(root.Left)&&isValidBST(root.Right)
}
```



因为搜索二叉树的中序遍历一定是递增的，所以可以采用中序遍历这个二叉树进行判断

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func isValidBST(root *TreeNode) bool {
    if root == nil {
        return true
    }

    stack := []*TreeNode{}
  	//左节点入栈
    for root != nil {
        stack = append(stack,root)
        root = root.Left
    }

    tmp := math.MinInt64
		
  	//出栈，遍历右节点的左节点
    for len(stack) != 0 {
        cur := stack[len(stack)-1]
        stack = stack[:len(stack)-1]
        if( tmp!= math.MinInt64 && tmp >= cur.Val){
            return false
        }else{
            tmp = cur.Val
        }

        cur = cur.Right
        for cur != nil {
            stack = append(stack,cur)
            cur = cur.Left
        }
    }
    return true
}
```



### 1.4 [恢复二叉搜索树](https://leetcode-cn.com/problems/recover-binary-search-tree/)

中序遍历，从栈顶弹出时记录，与下一个栈顶节点做比较，如果大于下一个节点，则不符合中序遍历搜索二叉树的递增规则，x,y 进行记录两个节点

例：[1,2,4,3]则交换3和4  如果是[1,4,3,2]则会把x记录为第一个不符合的4，y为3，后续不更新x，只更新y，将y更新为第二个不符合的数为2

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func recoverTree(root *TreeNode)  {
    stack := []*TreeNode{}
    var x, y, pre *TreeNode

    current := root
    for current != nil || len(stack) > 0 {
        for current != nil {
            stack = append(stack,current)
            current = current.Left
        }
        current = stack[len(stack)-1]
        stack = stack[:len(stack)-1]

        if pre != nil && pre.Val > current.Val{
            y = current
            if x == nil{
                x = pre
            }else{
                break
            }
        }

        pre = current
        current = current.Right
    }

    x.Val , y.Val = y.Val, x.Val
}
```



## 2. 递归

二叉树的遍历框架

```go
func BST(root *TreeNode,target int) {
    if (root.val == target)
        // 找到目标，做点什么
    if (root.val < target) 
        BST(root.right, target);
    if (root.val > target)
        BST(root.left, target);
}

```

### 2.1 [相同的树](https://leetcode-cn.com/problems/same-tree/)

判断两个二叉树是否相同

```go
func isSameTree(p *TreeNode, q *TreeNode) bool {
  	// 都为空，则相同
    if p==nil && q==nil {
        return true
    }
		// 一个为空，一个不为空，显然不同
    if p==nil || q==nil {
        return false
    }
    // 节点值不一样，不同
    if p.Val != q.Val {
        return false
    }

    return isSameTree(p.Left,q.Left)&&isSameTree(p.Right,q.Right)
}
```

### 2.2 [验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)( 与1.3相同 )

注意框架思维

```go
func isValidBST(root *TreeNode) bool {
    return validBST(root,math.MinInt64,math.MaxInt64)
}

func validBST(root *TreeNode,min,max int) bool{
    //递归结束条件
    if root==nil{
        return true
    }
    // 判断节点的值是不是在区间呢，不是的话就false结束
    if root.Val<=min || root.Val>=max{
        return false
    }
    //左递归 最大值改为当前节点值
    //右递归 最小值改为当前节点值
    return validBST(root.Left,min,root.Val) && validBST(root.Right,root.Val,max)
}
```



### 2.3 [对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)

```go
// 输入值只有一个root，多用一个函数
func isSymmetric(root *TreeNode) bool {
    return check(root.Left,root.Right)
}

func check(l,r *TreeNode) bool {
    if l==nil && r==nil {
        return true
    }
    if l==nil || r==nil{
        return false
    }
    if l.Val != r.Val { 
        return false
    }

    return check(l.Left,r.Right)&&check(l.Right,r.Left)
}
```



### 2.4  [二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

```go
func maxDepth(root *TreeNode) int {
    if root == nil {
        return 0
    } 

    if root.Left == nil && root.Right == nil {
        return 1
    }

    left := maxDepth(root.Left)
    right := maxDepth(root.Right)   

    if left >right {
        return 1 + left
    }else {
        return 1 +right
    }
}
```



### 2.5 [从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

前序遍历第一个为根节点，在中序遍历中找到根节点，左边为左子树，右边为右子树，递归遍历整棵树

```go
func buildTree(preorder []int, inorder []int) *TreeNode {
    if len(preorder) == 0 && len(inorder) == 0 {
        return nil
    }

    res := &TreeNode{preorder[0],nil,nil}
    i := 0
    for ; i < len(inorder); i++ {
        if inorder[i] == preorder[0]{
            break
        }
    }

    res.Left = buildTree(preorder[1:i+1],inorder[:i])
    res.Right = buildTree(preorder[i+1:],inorder[i+1:])
    return res
}
```



### 2.6 [从中序与后序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

```go
func buildTree(inorder []int, postorder []int) *TreeNode {

    if len(inorder) == 0 && len(postorder) == 0 {
        return nil
    }

    l := len(postorder)-1
    res := &TreeNode{Val:postorder[l]}
    i :=0
    for ;i<len(inorder);i++{
      if inorder[i] == postorder[l]{
        break
      }
    }
    res.Left = buildTree(inorder[:i],postorder[:i])
    res.Right = buildTree(inorder[i+1:],postorder[i:l])
    return res
}
```



### 2.7 [将有序数组转换为二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/)

```go
func sortedArrayToBST(nums []int) *TreeNode {
    if len(nums) == 0 {
        return nil
    }
    res := &TreeNode{nums[len(nums)/2],nil,nil}
    res.Left = sortedArrayToBST(nums[:len(nums)/2])
    res.Right = sortedArrayToBST(nums[len(nums)/2+1:])

    return res
}
```



## 3. 层次遍历BFS

### 3.1 [二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

```go
func levelOrder(root *TreeNode) [][]int {
    result := [][]int{}

    if root == nil {
        return result
    }

    //创建队列，将root节点加入
    queue := []*TreeNode{}
    queue = append(queue,root)
    for len(queue)!=0 {
        tmp := []int{}
        //当前队列中的元素个数，为此层的元素个数
        lenth := len(queue)
        //遍历此层所有元素
        for i:=0;i<lenth;i++{
            //出队
            node := queue[i]
            tmp = append(tmp,node.Val)
            if node.Left!=nil{
                queue = append(queue,node.Left)
            }
            if node.Right!=nil {
                queue = append(queue,node.Right)
            }
        }
        queue = queue[lenth:]
        result = append(result,tmp)
    }
    return result
}
```



### 3.2 [二叉树的锯齿形层序遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

设置一个是否翻转数组的变量，如果需要反转，临时数组反向插入

```go
func zigzagLevelOrder(root *TreeNode) [][]int {
    var res [][]int
    if root == nil {
        return res
    }

    // 创建队列
    queue := make([]*TreeNode, 0)
    // root入队
    queue = append(queue,root)
    isLeftStart := true
    for len(queue) != 0 {
        length := len(queue)
        //当前队列中的元素个数，为此层的元素个数
        tmp := make([]int, length)
        //遍历此层所有元素
        for i := 0; i < length; i++ {
            //取出此层的节点，判断是否需要反转
            node := queue[i]
            if isLeftStart {
								tmp[i] = node.Val
						} else {
								tmp[length-i-1] = node.Val
						}
            // 入队
            if node.Left != nil {
                queue = append(queue,node.Left)
            }
            if node.Right != nil {
                queue = append(queue,node.Right)
            }
        }
        queue = queue[length:]
        res = append(res,tmp)
        isLeftStart = !isLeftStart
    }
    return res
}
```



### 3.3 [二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/) (与2.4相同)

```go
func maxDepth(root *TreeNode) int {
    n := 0
    if root == nil {
        return n
    }
    var queue []*TreeNode
    queue = append(queue,root)

    for len(queue) != 0 {
        length := len(queue)
        for i := 0; i < length; i++ {
            node := queue[i]
            if node.Left != nil {
                queue = append(queue,node.Left)
            }
            if node.Right != nil {
                queue = append(queue,node.Right)
            }
        }
        queue = queue[length:]
        n++
    }
    return n
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

1. dp数组 dp[i]为整数n的二叉搜索树种数

2. dp[i] += dp[j]  * dp[i-j-1]

3. dp[0] = 1

4. 遍历顺序是节点数

   | 下标i | 0    | 1    | 2    | 3    |
   | ----- | ---- | ---- | ---- | ---- |
   | dp[i] | 1    | 1    | 2    | 5    |

```go
func numTrees(n int) int {
    dp := make([]int,n+1)
    dp[0] = 1
    for i:=1; i<n+1; i++ {
        for j:=0; j<i; j++{
            dp[i] += dp[j] * dp[i-j-1]
        }
    }  
    return dp[n]
}
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





# **模板总结**

## 1. 中序遍历（迭代）

适用：二叉搜索树

```go
func inorderTraversal(root *TreeNode) []int {
    result := []int{}    
    stack := []*TreeNode{}

    node := root
    // 当node不为空或者stack不为空
    for node != nil || len(stack) != 0 {
        // 入栈
      	for node != nil {
            stack = append(stack,node)
            node = node.Left
        }
        // 获得栈顶元素，出栈
        node = stack[len(stack)-1]
        stack = stack[:len(stack)-1]
        result = append(result,node.Val)

        node = node.Right

    }
    return result
}
```

## 2. 递归

```GO
func BST(root *TreeNode,target int) {
    if (root.val == target)
        // 找到目标，做点什么
    if (root.val < target) 
        BST(root.right, target);
    if (root.val > target)
        BST(root.left, target);
}
```

## 3. 层次遍历

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func levelOrder(root *TreeNode) [][]int {
    var res [][]int
    if root == nil {
        return res
    }

    // 创建队列
    queue := make([]*TreeNode, 0)
    // root入队
    queue = append(queue,root)

    for len(queue) != 0 {
        length := len(queue)
        //当前队列中的元素个数，为此层的元素个数
        var tmp []int
        //遍历此层所有元素
        for i := 0; i < length; i++ {
             //取出此层的节点，将下一层入队
            node := queue[i]
            tmp = append(tmp,node.Val)
            if node.Left != nil {
                queue = append(queue,node.Left)
            }
            if node.Right != nil {
                queue = append(queue,node.Right)
            }
        }
      	//出队
        queue = queue[length:]
        res = append(res,tmp)
    }
    return res
}
```

