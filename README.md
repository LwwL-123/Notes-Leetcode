# LeetCode刷题笔记

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

