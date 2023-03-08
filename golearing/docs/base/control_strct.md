#  控制结构

程序执行从本质上来说就是两种模式: 顺序和跳转

- 顺序就是按照程序指令在存储器上的存放顺序逐条执行
- 跳转就是遇到跳转指令就跳转到某处继续线性执行

## if 语句

1. if 语句后面的条件判断子句不需要用小括号
2. {必须放在行尾，和 if 或 if else 放在一行
3. if 后面可以带简单的初始化语句，分号分隔，该简单语句声明的变量作用域整个 if 语句块，包括后面的 if else 或者 else 分支
4. go 语言没有条件运算符
5. if 分支语句遇到 return 后直接返回

```
package main

import "fmt"

func main() {
	x := 1
	y := 10
	if x < y {
		fmt.Println("if简单语句执行成功")
	}
	if z := 101; z > y {
		fmt.Println("if初始化语句声明变量z")
	}
}

```

## switch 语句

switch 语句会根据传入的参数检测并执行符合条件的分支

其他特点和 if 语句基本一致

支持 default 语句

```
package main

import "fmt"

func main() {
	score := 85
	switch {
	case score >= 90:
		fmt.Println("90分以上得分A")
	case score >= 80:
		fmt.Println("80分以上得分B")
	case score >= 70:
		fmt.Println("70分以上得分C")
	}
}


```

## for 语句

for 循环语句

支持对数组、切片、字符串、map 和通道的访问

```
package main

import "fmt"

func funcDemo(args ...int) {
	for i := 0; i < len(args); i++ {
		fmt.Println(i, args[i])
	}
	// range 默认返回两个值,下标和值类似python的enumerate
	for index, value := range args {
		fmt.Println(index, value)
	}
	// for 循环后面为空则表示死循环
	//for {
	//
	//}

	//对于 map 类型来说，for range 遍历时，key 和 value 分别代表 map 的索引键 key 和索引对应的值，
	//一般被称为 map 的键值对，因为它们是一对一对出现的，下面的代码演示了如何遍历 map。
	m := map[string]int{
		"hello": 100,
		"world": 200,
	}
	for key, value := range m {
		fmt.Println(key, value)
	}
}
func main() {
	funcDemo(1, 2, 3)
	//fmt.Println(key, value)
}

```

## 标签和跳转

goto、break、continue

go 语言使用标签 Label 来标识一个语句的位置、用于关键字跳转

goto 用于函数的内部的跳转，需要配合标签一起使用

### goto

`goto Label`

- goto 只能在函数内跳转
- goto 不能跳过内部变量声明语句，这些变量在 goto 语句的标签语句处又是可见的
- goto 只能跳到统计作用域或者上层作用域，不能跳到内部作用域内的标签

### break

用于函数内跳出 for、switch、select 语句的执行

- 单独使用，直接跳出 for、switch、select 语句的执行
- 配合 Label，用于跳出标签所标识的 for、switch、select 语句块的执行

### continue

用于跳出 for 循环的本次迭代，跳到下一次迭代的 post 语句处执行

- 单独使用，用于跳出 for 循环的本次迭代
- 配合 Label，用于跳出标签标识的 for 循环的本次迭代，标签和 continue 必须在同一个函数内

### return 和函数调用
