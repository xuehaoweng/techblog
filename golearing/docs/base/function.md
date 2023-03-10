几乎所有的编程语言都支持函数或者类似函数的编程结构。现代计算机进程执行模型大部分是基于”堆栈“的，而编译器不需要多函数做过多的转换就能让其在栈上运行，只需要处理好参数和返回值的传递即可；另一方面函数对代码的抽象程度适中，就像胶水，很容易将编程语言的不同层级抽象体”黏结“起来；同时近几年函数式语言因其数值不变性在高并发的场景备受青睐，新的语言将其视为”第一公民“

函数是程序执行的一个基本语法结构。
函数特点表现在

- 函数是一种类型，函数类型变量可以像其他类型变量一样使用，可以作为其他函数参数的参数或者返回值，也可以直接调用执行
- 函数支持多值返回
- 支持闭包
- 函数支持可变参数

## 基本概念

### 函数定义

函数是 go 语言程序源代码的基本构造单位，一个函数的定义包含:

- 函数声明关键字 func
- 函数名
- 参数列表
- 返回列表
- 函数体

**函数的特点**

1. 函数可以没有输入参数，也可以没有返回值
2. 多个相邻的相同类型的参数可以使用简写模式 a,b int
3. 支持又名的返回值，参数名相当于函数体最外层的局部变量，命名返回值变量会被初始化为类型零值，最后的 return 可以不带参数名直接返回
4. 不支持默认值参数
5. 不支持函数重载
6. 不支持函数嵌套(不支持命名函数的嵌套定义)

### 多值返回

```
func swap(a,b int)(int,int){
return b,a
}
```

### 函数传参

```
package main
import {
    "fmt"
}
func chvalue(a int) int {
  a = a+1
  return a
}
func chpointer(a *int) int {
  *a = *a+1
  return a
}
func main(){
  a:=10
  chvalue(a) //实参传递给形参是值拷贝
  chpointer(&a) //实参传递给形参是值拷贝，只不过是复制的a的内存地址值
}
```

### 不定参数 args

不定数目的参数形式，不定参数声明使用`param ...type`的语法格式

- 所有的不定参数类型必须是相同的
- 不定参数必须是函数的最后一个参数
- 不定参数在函数体内相当于切片，对切片的操作同样适合于对不定参数的操作

## 函数类型(函数签名)

函数类型又叫做函数签名，一个函数的类型就是函数定义首行去掉函数名、参数名和`{`,可以用`fmt.Printf`的`%T`格式化参数打印函数的类型

```
package main
import "fmt"
func add(a,b int) int {
  return a+b
}
func main(){
  fmt.Printf("%T\n",add) // func(int,int) int
}
```

两个函数类型相同的条件是：

- 拥有相同的形参列表和返回值列表、列表元素次序+个数+类型都相同

也可以使用 type 定义函数类型，函数类型变量可以作为函数的参数或者返回值

## 匿名函数

- 有名函数
- 匿名函数

匿名函数可以看作函数字面量，所有直接使用函数类型变量的地方都可以由匿名函数代替。匿名函数可以直接赋值给函数变量，可以当作是惨也可以作为返回值，还可以直接被调用。
