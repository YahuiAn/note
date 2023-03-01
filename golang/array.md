## 声明

```go
var arrayName [arraySize]dataType
eg:
var array1 [5]int
```

在声明数组时，必须指定数组名，数组长度和数组元素的类型。

## 初始化

当数组定义好以后，如果没有给数组元素指定值，则所有元素被自动初始化为类型所对应的零值

```go
var array1 [5]int
// [0 0 0 0 0]
```

------

**变量的类型零值**

在Go语言中，当一个变量被定义为某一种类型后，Go语言会自动初始化其值为零（Zero Value）

零值并不等于空值，而是当变量被声明为某种类型后的默认值

|                       类型                        | 类型零值 |
| :-----------------------------------------------: | -------- |
|                       bool                        | false    |
|          int, int8, int16, int32, int64           | 0        |
|        uint, uint8, uint16, uint32, uint64        | 0        |
|                 float32, float64                  | 0        |
|               complex64, complex128               | 0+0i     |
|                    byte, rune                     | 0        |
|                      string                       | ""       |
| pointer, function, interface, slice, channel, map | nil      |

------

（1）在定义数组时对数组元素赋初值

```go
var array1 = [5]int{0, 1, 2, 3, 4}
// [0 1 2 3 4]
```

注意：花括号中的元素个数不得超过arraySize

（2）可以只给一部分元素赋初值

```go
var array1 = [5]int{0, 1, 2}
// [0 1 2 0 0]
```

注意：未赋值的默认初始化为类型零值

（3）可以由初始化列表决定数组长度

```go
var array1 = [...]int{0, 1, 2, 4, 5, 6}
// [0 1 2 4 5 6]
```

注意：

- 该例中数组长度为 ··· 标识符，表示没有指定数组长度，数组长度由初始化列表决定

- ··· 标识符不可省略，否则将变成后文将要讲的**切片**

（4）按照下标初始化元素

```go
var array1 = [5]int{0: 5, 4: 5}
// [5 0 0 0 5]
```

## 访问和遍历

**访问**

数组元素按照下标进行访问，下标从0开始，可以是整型常量或者整型表达式

```go
var array1 = [8]int{1, 2, 3, 4, 5, 6, 7, 8}
fmt.Println(array1[0], array1[6], array1[2*3])
// 1 7 7
```

**遍历**

（1）for语句

```go
var array1 = [8]int{1, 2, 3, 4, 5, 6, 7, 8}
for i := 0; i < 8; i++ {
    fmt.Printf("%d ", array1[i])
}
// 1 2 3 4 5 6 7 8 
```

（2）for range语句

```go
var array1 = [3]int{1, 2, 3}
for i, v := range array1 {
    fmt.Printf("index is %d, value is %d.\n", i, v)
}
// index is 0, value is 1.
// index is 1, value is 2.
// index is 2, value is 3.
```

range 具有两个返回值，第一个返回值i是数组元素的下标，第二个返回值v是数组元素的值

## 二维数组

```go
var array2 = [3][4]int{{1, 2, 3, 4}, {5, 6, 7, 8}, {}} // 三行四列
fmt.Println(array2)
// [[1 2 3 4] [5 6 7 8] [0 0 0 0]]
```