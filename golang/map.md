## 字典

Map是一种特殊的数据结构，由一对无序的数据项组成，被称为键值对（Key-value Pair）

## 声明

```go
var mapName map[keyType]valueType
eg:
var map1 map[string]int
// 字典声明好后必须经过初始化或者创建才能使用
// 未初始化或创建的字典值为nil
```

## 初始化和创建

（1）使用 ```{}``` 操作符对字典进行初始化

```go
var map1 map[string]int
map1["egg"] = 1  // 对字典没有初始化就尝试增加数据，编译时就会报错
panic: assignment to entry in nil map

var map1 = map[string]int{} // ！！！使用{}来初始化map，也就意味着系统给map1分配了存储空间
map1["apple"] = 2  // 使用=向map中添加元素
fmt.Println(map1)  // map[apple:2]

var map1 = map[string]int{"microsoft":0}  //在初始化的时候就指定key-value元素
```

（2）使用 ```make()``` 函数来创建字典

```go
var map1 = make(map[string]int)  // 如下引用所示，我们可以指定map的长度，也可以省略
// 注意：make创建map是不可指定cap属性，当然cap()函数也不可用于map类型
map1["egg"] = 1
fmt.Println(map1) // map[egg:1]
```

> ```go
> //  Map: An empty map is allocated with enough space to hold the
> // specified number of elements. The size may be omitted, in which case
> // a small starting size is allocated.
> func make(t Type, size ...IntegerType) Type
> ```

## 访问和遍历

（1）通过key来访问Value

```go
var map1 = map[string]int{"Microsoft": 0}
map1["egg"] = 1
map1["apple"] = 2
fmt.Println(map1)        // map[Microsoft:0 apple:2 egg:1]
// 通过Key来访问value
fmt.Println(map1["egg"]) // 1
```

（2）字典项查找

```go
v,ok := mapName[Key]
// 如果查找的key值存在，则将key对应的value值赋予v,ok为true
// 如果查找的key值不存在，则将v为0，ok为false
eg:
if _, ok := map1["agg"]; ok == false {
    map1["agg"] = 666
}
fmt.Println(map1) // map[Microsoft:0 agg:666 apple:2 egg:1]
```

（3）使用for,range遍历

```go
for k, v := range map1 {
    fmt.Printf("key is %s,value is %d.\n", k, v)
}
// key is Microsoft,value is 0.
// key is egg,value is 1.
// key is apple,value is 2.
// key is agg,value is 666.
```

注意：map是无序的，所以遍历的结果也是无序的。

## 内置操作

（1）len()

获取字典元素的个数

（2）delete（）

> ```go
> // The delete built-in function deletes the element with the specified key
> // (m[key]) from the map. If m is nil or there is no such element, delete
> // is a no-op.
> func delete(m map[Type]Type1, key Type)
> ```

```go
var map1 = map[string]int{"Microsoft": 0}
map1["egg"] = 1
map1["apple"] = 2
fmt.Println(map1) // map[Microsoft:0 apple:2 egg:1]

delete(map1, "egg") // key存在于map1中
fmt.Println(map1)   // map[Microsoft:0 apple:2]

delete(map1, "none") // key不存在于map1中,没有任何作用，不会panic
fmt.Println(map1)    // map[Microsoft:0 apple:2]

var map2 map[string]int
delete(map2, "nil") // map2为nil，也没有任何作用,不会panic
fmt.Println(map2)   // map[]
```

## 总结

**小结**

本文简述了Go语言中最基本，最常用的三种数据结构的基本知识点，在掌握基础后，我们可以针对自己的兴趣点，去深入的研究底层的知识。

**参考资料**

[《Go语言程序设计》王鹏 编著 清华大学出版社](http://www.tup.tsinghua.edu.cn/booksCenter/book_05443601.html)

https://coolshell.cn/articles/21128.html

https://golang.org/ref/spec#Slice_expressions

