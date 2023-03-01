## func Slice(x interface{}, less func(i, j int) bool)

- 不能保证排序是稳定的

```go
func main() {
	a := []int{1, 3, 5}
	sort.Slice(a, func(i, j int) bool {
		return a[i] > a[j]
	})
	fmt.Println(a) // [5 3 1]

	sort.Slice(a, func(i, j int) bool {
		return a[i] < a[j]
	})
	fmt.Println(a) // [1 3 5]
}
```
