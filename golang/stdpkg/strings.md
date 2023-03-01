## func Split(s, sep string) []string

- 按照 sep 分隔 s，返回字串数组

```go
fmt.Printf("%q\n", strings.Split("a,b,c", ",")) // ["a" "b" "c"]
fmt.Printf("%q\n", strings.Split("a man a plan a canal panama", "a ")) // ["" "man " "plan " "canal panama"]
```

- 如果 s 中不包含 sep，且 sep 非空，结果就是 []string{s}

```go
fmt.Printf("%q\n", strings.Split("ABC", "xyz")) // ["ABC"]
```

- 如果 sep 为空，则按照 UTF-8 顺序分隔

```go
fmt.Printf("%q\n", strings.Split("Hi，中国", "")) // ["H" "i" "，" "中" "国"]
```

- 如果 s 和 sep 均为空，则返回空数组

```go
fmt.Printf("%q\n", strings.Split("", "")) // []
```

- 等价于 ``SplitN(s,sep,-1)``

## func SplitN(s, sep string, n int) []string

- n > 0 最多切分出 n 个子串

```go
fmt.Printf("%q\n", strings.SplitN("a,b,c", ",", 2)) // ["a" "b,c"]
```

- n == 0 结果为 nil

```go
fmt.Println(strings.SplitN("a,b,c", ",", 0) == nil) // true
```

- n < 0 所有的字串，等价于 ``Split``

- 边界条件和 ``Split`` 一致
