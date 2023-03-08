# 复合数据类型

## 1.1. 简介

其他数据类型组合而成的数据类型。GO 语言基本的复合数据类型有:

- 指针
- 数组
- 切片
- 字典(map)
- 通道
- 结构
- 接口a

### 1.2. 指针

Go 语言支持指针、指针的声明类型是`*T`、Go 同样支持多级指针`**T`、通过变量名前面的&符号类获取变量的地址
```
package main

import "fmt"

// 指针
// *T 指针的声明类型为 *T 也支持多级指针**T
// 通过在变量名前面& 来获取变量的地址
// *T 在左边表示指针声明、在右边表示取指针指向的值
func main() {
	var a = 11
	p := &a
	fmt.Print(*p)
}

```

#### 1.2.1 赋值语句

```
*T 出现在等号左边表示声明指针、在等号右边表示取指针指向的值
```

#### 1.2.2 结构体中

```
结构体指针访问支持点操作
```

#### 1.2.3 不支持指针运算

```
go原生支持垃圾回收，若支持运算会导致垃圾回收必变，所以紧张对指针的运算
```

#### 1.2.4 函数中允许返回局部变量的地址

```
Go编译器使用‘栈逃逸’机制，将这种局部变量的空间分配在堆上
```

### 1.3. 数组

数组的类型名是 `[n]elementType`,n 是数组长度，elementType 是数组元素的类型。数组一般在创建时通过字面量初始化，单独声明一个数组而不进行初始化是没有意义的

```
a:=[3]int{1,2,3} //指定数组长度、数组元素类型、并初始化字面量

a:=[...]int{1,2,3,4,5} //不指定长度、数组长度由字面量初始化列表决定

a:=[3]int{1:1,2:3} //指定数组长度、通过索引值初始化、没有初始化的元素时使用该类型的默认值

a:=[...]int{1:1,2:3} //不指定长度、通过索引值初始化、数组长度由最后一个索引值来决定，没有指定索引的元素则被初始化为类型的默认值

```

数组特点:

- 创建则长度固定，不可再追加元素
- 数组是值类型的、数组赋值或者是作为函数参数都是值拷贝
- 数组长度是数组类型的组成部分、[0]int 和[2]int 是不同的类型
- 可以根据数组来创建切片类型

**数组类型示例**
```
package main

import "fmt"

// TODO 数组是值类型
// [n]elementType n作为数组长度、elementType表示数组元素类型
// 数组创建需要初始化、未初始化的数组类型变量是没有意义的
func main() {
	var arr [2]int                     // 指定长度、
	a := [...]int{1, 2, 3, 4, 5, 6, 7} // 不指定长度、数组长度由后面初始化列表决定
	b := [3]int{1: 1, 2: 3}            // 指定数组长度、并通过索引值初始化、未初始化的索引的值为数组元素的类型默认值
	c := [...]int{1: 1, 2: 3}          // 不指定长度、数组长度由最后一个索引值决定
	fmt.Println(arr)
	fmt.Println(a)
	fmt.Println(b)
	fmt.Println(c)
}

```

### 1.4. 切片

背景:go 数组的定长性和值拷贝限制了使用场景，go 的切片类型 slice，是一种可变长数组，其数据结构中有指向数组的指针，所以是一种引用类型。

go 切片维护三个元素------指向底层数组的指针、切片元素的数量、底层数组的容量

#### 1.4.1 切片如何创建

##### 1.4.1.1 由数组创建

```
数组的切片
var array:=[...]int{1,2,3,4,5,6,7,8,9}
s1 := array[0:3]
```

##### 1.4.1.1 由内置函数 make 来创建

```
由make创建的切片各个元素都没默认初始化为切片元素类型的零值
a:=make([]int,10)
```

#### 1.4.2 切片支持操作

- 内置函数 len()返回切片长度
- 内置函数 cap()返回切片底层数组容量
- 内置函数 append()对切片追加元素
- 内置函数 copy()用于复制一个切片
- 字符串和切片的相关转换

**切片示例代码**
```
package main

// TODO 切片是引用类型
import "fmt"

// 切片维护三个元素
// 指向底层数组的指针
// 切片元素的数量
// 底层数组的容量
func main() {
	// 通过数组创建
	var array = [...]int{1, 2, 3, 4, 5, 6}
	slice1 := array[0:4]
	fmt.Println(slice1)

	// 通过make内置函数创建 长度10、底层数组容量10
	// TODO make 创建的切片、各个元素都默认初始化为切片元素类型的零值
	a := make([]int, 10)
	fmt.Println(a)

	// 支持len()、支持cap()、支持append()、支持copy()
	//fmt.Println(len(a))
	//fmt.Println(cap(a))
	b := append(a, 10086)
	//fmt.Println()
	fmt.Println("追加后", b)
	//fmt.Println(copy(a, b))

	// 字符串操作
	str := "hello,世界"
	fmt.Println(str)
	e := []byte(str) // 字符串转[]byte类型切片
	f := []rune(str) // 字符串转[]rune类型切片

	fmt.Println(e)
	fmt.Println(f)
}
```
### 1.5. map

Go 语言内置的字典类型叫 map，`map[K]T`,K 可以是任意可以进行比较的类型，T 是值类型，map 也是一种引用类型

```
map创建
ma:=map[string]int{"a":1,"b":2}
make内置函数创建
mp1:=make(map[int]string)
mp1[1]= "tom"
map 支持操作：mapName[key]
```

**map示例**
```
package main

import "fmt"

// TODO map 是一种引用类型
// map[K]T K可以是任意可以进行比较的类型、T是值类型
// 内置字典类型map
func main() {
	// map 创建
	ma := map[string]int{"a": 1, "b": 2}
	fmt.Println(ma)
	fmt.Println(ma["a"])

	// make内置函数构建
	mp1 := make(map[int]string)
	mp1[1] = "tom"
	fmt.Println(mp1[1])
}

```

### 1.6. 结构体

Go 结构体 struct、不同类型元素组合而成

- struct 类型字面量
- 自定义 struct 类型
- type 自定义类型
```
package main

import "fmt"

func main() {
	type Person struct {
		Name string
		Age  int
	}
	type Student struct {
		*Person
		Number int
	}

	a := Person{
		"Tom",
		18,
	}
	p := &Person{
		Name: "zhangsan",
		Age:  19,
	}
	student := Student{
		Person: p,
		Number: 110,
	}
	fmt.Println(a)
	fmt.Println(p)
	fmt.Println(student)
}

```