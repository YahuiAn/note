Array 动态大小，任意类型的元素

## 创建

```javascript
	// Array
    {
      let a1 = new Array(); // []
      let a2 = Array(); // new 可以省略
      let a3 = Array(3); // [] length=3
      let a4 = Array(3, 4, 5) // [3, 4, 5]
    }

    // 数组字面量
    {
      let a1 = [] // []
      let a2 = ['red', 'blue'] // ['red', 'blue']
    }

	// Array.of() 把一组参数转化为数组
    {
      let a1 = Array.of(1, 2, 3) // [1, 2, 3]
    }
```

## 索引

```javascript
    {
      let a = ['red', 'blue', 'green']
      // 自动扩容
      a[4] = 'black'
      console.log(a) // ['red', 'blue', 'green', empty, 'black']
      console.log(a[3] === undefined) // true
    }

    {
      // 修改 length 可以在数组尾部删除或添加元素
      let a = ['red', 'blue', 'green']
      a.length = 2
      console.log(a) // ['red', 'blue']

      a.length = 4
      console.log(a) // ['red', 'blue', empty × 2]
    }
```

## 检测数组

```javascript
let a = [1, 2, 3]
console.log(a instanceof Array)

console.log(Array.isArray(a)) // 更优
```

## 迭代器方法

```javascript
    let a = ['a', 'b']
    console.log(Array.from(a.keys())) // [0, 1]

    console.log(Array.from(a.values())) // ['a', 'b']

    for (const [i, v] of a.entries()) {
      console.log(i, v)
    }
    // 0 'a'
    // 1 'b'
```

## push pop

```javascript
    let a = ['a', 'b']

    a.push('c')
    console.log(a) // ['a', 'b', 'c']

    console.log(a.pop()) // c
    console.log(a) // ['a', 'b']
```

## shift unshift

```javascript
    let a = ['a', 'b']

    console.log(a.shift()) // a
    console.log(a) // ['b']

    a.unshift('A')
    console.log(a) // ['A', 'b']
```

## reverse sort

```javascript
    let a = [1, 5, 10]
    // 反转
    console.log(a.reverse()) // [10, 5, 1]

    // 注意 sort 是字符串比较
    console.log(a.sort()) // [1, 10, 5]

    // compareFn 返回负值则升序
    console.log(a.sort((x, y) => x - y)) // [1, 5, 10]
```

## map filter

> 每个方法接收两个参数：以每一项为参数运行的函数，以及可选的作为函数运行上下文的作用域对象（影响函数中this的值）。
>
> 传给每个方法的函数接收3个参数：数组元素、元素索引和数组本身。

**filter**

```javascript
    let a = [1, 2, 3, 4, 5]
    console.log(a.filter((v) => v % 2 == 0)) // [2, 4]
    console.log(a.filter((_, i) => i % 2 == 0)) // [1, 3, 5]
```

**map**

```javascript
    let a = [1, 2, 3]
    console.log(a.map((v) => v ** 2)) // [1, 4, 9]
    console.log(a.map((v) => v += 1)) // [2, 3, 4]
```

## reduce

> 这两个方法都接收两个参数：对每一项都会运行的归并函数，以及可选的以之为归并起点的初始值。
>
> 传给reduce()和reduceRight()的函数接收4个参数：上一个归并值、当前项、当前项的索引和数组本身。
>
> 如果没有给这两个方法传入可选的第二个参数（作为归并起点值），则第一次迭代将从数组的第二项开始，因此传给归并函数的第一个参数是数组的第一项，第二个参数是数组的第二项。

```javascript
    let a = [1, 2, 3]
    let sum = a.reduce((prev, cur) => prev + cur)
    console.log(sum) // 6

    a.reduceRight(function (prev, cur, index, array) {
      console.log(`prev is ${prev}, cur is ${cur}, index is ${index}, array is ${array}`)
      // prev is 3, cur is 2, index is 1, array is 1,2,3
      // prev is undefined, cur is 1, index is 0, array is 1,2,3
    })
```
