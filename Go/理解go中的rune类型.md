# 理解go类型中的rune

参考：     
[理解go中rune数据类型](https://zhuanlan.zhihu.com/p/137339284) 

**官方定义**

```shell
// rune is an alias for int32 and is equivalent to
// int32 in all ways. It is
// used, by convention, to distinguish character
// values from integer values.

// int32的别名，几乎在所有方面等同于int32
// 它用来区分字符值和整数值

type rune = int32
```

划重点：**用它来区分字符值和整数值**
>大家知道计算机在底层处理的都是零和一的数字，那么字符串也不另外。
> string字符串在转换成byte之后其实都是一个数值，
> 我们知道常规的英文字符是ascii码是通过一个字节( 2^8 其实还有一位是不用的 )
> 来存储，中国文字、日本文字常用文字就有4000+，通过2^8肯定表达不了，
> 所有可以通过unicode来存储，占用2个字节。

现在我们来做个试验

```golang
package main

import "fmt"

func main() {
	var zzw = "go算法"
	for i, k := range zzw {
		fmt.Printf("k type: %T\t", k)
		fmt.Println("i k:", i, k)
	}
	fmt.Println("")
	n := len(zzw)
	for i := 0; i < n; i++ {
		fmt.Printf("k type: %T\t", zzw[i])
		fmt.Println("i k:", i, zzw[i])
	}
}
```
运行结果：
```shell
k type: int32   i k: 0 103
k type: int32   i k: 1 111
k type: int32   i k: 2 31639
k type: int32   i k: 5 27861

k type: uint8   i k: 0 103
k type: uint8   i k: 1 111
k type: uint8   i k: 2 231
k type: uint8   i k: 3 174
k type: uint8   i k: 4 151
k type: uint8   i k: 5 230
k type: uint8   i k: 6 179
k type: uint8   i k: 7 149
```

go语言的编码是按照UTF-8编码规则来的。
查询到"算"和"法"这两个汉字的16进制值得区间在UTF-8的第三区段，那么在go的编码下会占用三个字符。

```shell
var str = "go算法"
fmt.Println(str[:4])
// 输出：go�
```

所以在go语言中引进了`rune`的概念。
在我们对字符串进去处理的时候只需要将字符串通过range去遍历，
会按照`rune`为单位自动去处理，极其便利

在 Go 1.9 版本中对rune的定义如下：
```shell
type byte = uint8
type rune = int32
```

**总结**  
通过上面的代码我们已经很清楚的知道rune类型实质其实就是int32，
他是go语言内在处理字符串及其便捷的字符单位。
它会自动按照字符独立的单位去处理方便我们在遍历过程中按照我们想要的方式去遍历。
另外一个使用场景就是：我们在处理字符串的时候可以通过map[rune]int类型方便
的判断字符串是否存在，其中 rune表示字符的UTF-8的编码值，
int表示字符在字符串中的位置(按照字节的位置)

