#### [136. 只出现一次的数字](https://leetcode-cn.com/problems/single-number/)

首先针对异或运算，这里做一个知识点的总结：

任何数和自己做异或运算，结果为 00，即 a⊕a=0a⊕a=0 。
任何数和 00 做异或运算，结果还是自己，即 a⊕0=⊕a⊕0=⊕。
异或运算中，满足交换律和结合律，也就是 a⊕b⊕a=b⊕a⊕a=b⊕(a⊕a)=b⊕0=ba⊕b⊕a=b⊕a⊕a=b⊕(a⊕a)=b⊕0=b

```go
func singleNumber(nums []int) int {
    single := 0
    for _, num := range nums {
        single ^= num
    }
    return single
}
```



#### [7. 整数反转](https://leetcode-cn.com/problems/reverse-integer/)

```go
import "math"
func reverse(x int) int {
    res := 0
    for x != 0 {
        res = res * 10 + x % 10  
        x= x / 10
    }

    if res <= math.MinInt32 || res >= math.MaxInt32 {
		return 0
	}

    return res
}
```

