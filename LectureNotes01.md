# [Go编程时光](https://golang.iswbm.com/)基础知识

## 五种变量创建的方法

**第一种：** 一行声明一个变量

```go
var <name> <type>
```

var是关键字，固定不变，`name`是变量名，`type`是类型。使用var虽然只指定了类型，但是会进行隐式初始化（string初始化为空字符串，int初始化为0，float初始化为0.0，bool初始化为false，指针初始化为nil）。声明时也可以自己初始化：

```go
var name string = "Hello World!"
```

右值如果明显对应某种类型，则可以省略`type`（比如string），如果是浮点数，编辑器默认声明为`float64`，因此应该显示类型声明。

```go
var name = "Hello World!"
```

**第二种：** 多个变量一起声明
使用一个`var`声明多个变量：

```go
var (
    name string
    age int
    height float32
)
```

**第三种：** 声明和初始化一个变量
使用`:=`可以声明变量并进行显示初始化。这是$\color{red}{推导声明}$写法或者$\color{red}{短类型声明}$法，编译器会根据右值类型推断出左值的对应类型。这种写法只能在函数内部使用。

```go
name := "Hello World!"

var name string = "Hello World!"

var name = "Hello World!"
```

**第四种：** 声明和初始化多个变量

```go
name, age = "dangkh", 100
```

这种方法也经常用于变量交换

```go
var a int = 100
var b int = 200
b, a = a, b
```

**第五种：** new函数声明一个指针变量
变量分为`普通变量`和`指针变量`。`普通变量`存放数据本身，`指针变量`存放数据的地址。（即C系列的变量和指针）和C++类似可以使用**取指符号**`&`获取变量的地址并赋给指针变量。

```go
package main
import "fmt"

func main() {
    var age int = 28 //普通变量
    var ptr = &age //指针变量
}
```

`new`函数将创建一个的**匿名变量**，初始化为对应类型的零值，然后返回变量的地址：

```go
package main
import "fmt"

func main() {
    ptr := new(int)

    value := *ptr
}
```

`new`函数与C++的new操作符用法有点相似。但在golang中使用new创建变量和普通变量声明方式没有什么区别，只是省去了给临时变量取名的过程，也可以在表达式中使用`new(Type)`。

如下写法是等价的：

```go
func newInt1() *int {
    return new(int)
}

func newInt2() *int {
    var dummpy int
    return &dummpy
}
```

以上方法不管怎样，变量/常量都只能声明一次。golang中与python类似，也有匿名变量，即占位符`_`。通常可以使用匿名变量来接收用不到的返回值。

匿名变量的优点有三个：

* 不分配内存，不占用内存空间
* 不需要命名无用的变量名
* 多次声明不会有问题

```go
func GetDate() (int, int) {
    return 100, 200
}
func main() {
    a, _ = GetData()
    _, b = GetData()
}
```

## 数据类型

### 1、整型

![整型细分](figures/variables.png)

uint是无符号类型，后面的数字(8, 16, 32, 64)表示类型占用的空间大小。因此一般情况下应该使用更加精确的类型，在诸如二进制传输、读写文件的时候保证数据位数的正确性。

#### 不同进制的表达方法

一般初始化数据类型都会使用10进制。2进制以`0b`或`0B`为前缀；8进制以`0o`或`0O`为前缀；16进制以`0x`为前缀。

```go
func main() {
    var numBinary int = 0b1100
    var numOctal int = 0o14
    var numHex int = 0xCF10

    fmt.Printf("binary num %b is %d \n", numBinary, numBinary)
    fmt.Printf("octal num %o is %d \n", numOctal, numOctal)
    fmt.Printf("hexadecimal num %o is %d \n", numHex, numHex)
}
```

fmt的`Printf`输出格式化与C++类似，
> %b 表示为二进制 <br>
> %c 该值对应的unicode码值 <br>
> %d 表示为十进制 <br>
> %o 表示为八进制 <br>
> %q 该值对应的单引号括起来的go语法字符字面值，必要时会采用安全的转义表示 <br>
> %x 表示为十六进制，使用a-f <br>
> %X 表示为十六进制，使用A-F <br>
> %U 表示为Unicode格式：U+1234，等价于"U+%04X" <br>
> %E 用科学计数法表示 <br>
> %f 用浮点数表示 <br>

#### 2、浮点型

golang中浮点数的表示只能用10进制，不能用2进制或16进制或其他。

`float32`即单精度浮点数，存储占用4个字节，32位，1位符号，8位指数，23位尾数。指数用移码表示。
`float64`即双精度浮点数，存储占用8个字节，64为，1位符号，11位指数，52位尾数。指数用移码表示。

浮点数取值范围可以在math包中找到：

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.MaxFloat64)
    fmt.Println(math.MaxFloat32)
}
```

浮点数表示的范围很大，这是由于指数的存在，但是浮点数的精度有限，**单精度**浮点数能够提供6个十进制数的精度，**双精度**浮点数能提供15个十进制数的精度。下面的例子中由于精度不够，使得计算结果不相同。

```go
import "fmt"

var myfloat01 float32 = 100000182
var myfloat02 float32 = 100000187

func main() {
    fmt.Println("myfloat: ", myfloat01)
    fmt.Println("myfloat: ", myfloat01+5)
    fmt.Println(myfloat02 == myfloat01+5)
}
```

#### 3、byte、rune和字符串

`byte`占用一个字节，8位，与`uint8`没有本质区别，用来表示ACSII表中的一个字符

```go
import "fmt"

func main() {
    var a byte = 65
    // 8进制写法: var a byte = '\101'     其中 \ 是固定前缀
    // 16进制写法: var a byte = '\x41'    其中 \x 是固定前缀
    var aChar byte = 'A' 
    // a 和 aChar具有相同的定义

    var b uint8 = 66
    var bChar uint8 = 'B'
    fmt.Printf("a 的值: %c \nb 的值: %c", a, b)

    // 或者使用 string 函数
    // fmt.Println("a 的值: ", string(a)," \nb 的值: ", string(b))
}
```

`rune`占用4个字节，32位，和`int32`没有本质区别，表示一个Unicode字符。

```go
import (
    "fmt"
    "unsafe"
)

func main() {
    var a byte = 'A'
    var b rune = 'B'
    fmt.Printf("a 占用 %d 个字节数\nb 占用 %d 个字节数", unsafe.Sizeof(a), unsafe.Sizeof(b))
}
```

由于`byte`位数过少，表达中文只能使用`rune`类型。golang中单引号`'A'`表示字符，双引号`"A"`表示字符串，二者是不等价的。这与C++相同，而和python不同。

尽管byte和rune和对应的int型相同，但它们的存在让人们消除对象是一个数值的错觉，而会认为是一个字符。

多个字符组成字符串`string`。字符串本质就是一个字符数组。

```go
var myStr string = "hello"

var myBytes [5]byte = [5]byte{104, 101, 108, 108, 111}
```

golang的`string`使用utf-8进行编码，英文字母占一个字节，中文字符占3个字节：

```go
import (
    "fmt"
)

func main() {
    var country string = "hello,中国"
    fmt.Println(len(country))
}
// 输出 12  5 + 1 + 3 * 2
```

除了使用双引号表示字符串，还可以用反引号（即键盘左上角的）来表示。大多数情况二者没有区别，字符串中存在转义字符时就有区别了。使用反引号包裹的字符串会忽略字符串中的转义（与python中的raw类似）：

```go
var str1 string = "\\r\\n"  // 解释型表示法
var str2 string = `\r\n`    // 原生型表示法

// 输出结果是一样的
```

fmt库的`%q`可以还原解释型字符串：

```go
import (
    "fmt"
)

func main() {
    var mystr01 string = `\r\n`
    fmt.Print(`\r\n`)
    fmt.Printf("的解释型字符串是： %q", mystr01)
}

// 输出是：
// \r\n的解释型字符串是： "\\r\\n"
```

反引号不能使用换行符来表示一个多行的字符串：

```go
var str1 string = `hello
world!`
```

#### 4、数组和切片

有关切片可以参考[这里](https://www.cnblogs.com/sparkdev/p/10704614.html)

声明数组并给每个元素赋值：

```go
var arr [3]int
arr[0] = 1
arr[1] = 2
arr[2] = 3
```

声明并直接初始化数组：

```go
var arr1 [3]int = [3]int{1, 2, 3}

arr2 := [3]int{1, 2, 3}

arr := [3]int{2: 3, 0: 1, 1: 2}
```

可以使用`...`让编译器根据实际情况分配空间：

```go
arr := [...]int{1, 2, 3}
arr := [...]int{4, 5, 6, 7}
fmt.Printf("%d 的类型是: %T\n", arr01, arr01)
fmt.Printf("%d 的类型是: %T", arr02, arr02)

// 输出结果是：
// [1 2 3] 的类型是: [3]int
// [1 2 3 4] 的类型是: [4]int
```

使用关键字`type`可以为类型定义一个类型字面量，即别名类型（类似于C++中的`using`）。比如将大小为3的整形数组这个类型定义一个名字，后面再需要定义相同的容器都可以使用这个别名。

```go
import (
    "fmt"
)

func main() {
    type arr3 [3]int

    myarr := arr3{1,2,3}
    fmt.Printf("%d 的类型是: %T", myarr, myarr)
}
```

**切片**是对数组的一个连续片段的引用，终止索引标识对应的项不包括在切片内（即是左闭右开的区间）。

```go
import (
    "fmt"
)

func main() {
    myarr := [...]int{1, 2, 3}
    fmt.Printf("%d 的类型是: %T", myarr[0:2], myarr[0:2])
}
```

对数组片段进行截取有两种方式，第一个数表示起始位置，第二个数表示结束位置，第三个数表示容器大小（如果不指定则默认切到数组结束）：

```go
// 定义一个数组
myarr := [5]int{1,2,3,4,5}

// 1 表示从索引1开始，直到到索引为 2 (3-1)的元素
mysli1 := myarr[1:3]

// 1 表示从索引1开始，直到到索引为 2 (3-1)的元素，切片终止索引到4
mysli2 := myarr[1:3:4]

fmt.Printf("mysli1 的长度为：%d，容量为：%d\n", len(mysli1), cap(mysli1))
// mysli1 的长度为：2，容量为：4
fmt.Printf("mysli2 的长度为：%d，容量为：%d\n", len(mysli2), cap(mysli2))
// mysli2 的长度为：2，容量为：3
```

从头声明赋值切片。切片和数组的声明方式十分相似，其中切片的声明不会在中括号`[]`中指定大小。

```go
// 声明字符串切片
var strList []string

// 声明整型切片
var numList []int

// 声明一个空切片
var numListEmpty = []int{}

var strList1 = []string{99:""}
```

使用make函数构造切片，格式为：`make([]Type, size, capacity)`：

```go
import (
 "fmt"
)

func main() {
    a := make([]int, 2)
    b := make([]int, 2, 10)
}
```

切片与数组相比，数组的容器大小固定，而切片本身是引用类型，更像是python的list或者C++的vector，可以动态添加元素或改变容量：

```go
import (
    "fmt"
)

func main() {
    myarr := []int{1} // 初始化
    myarr = append(myarr, 2) // 追加一个元素
    myarr = append(myarr, 3, 4) // 追加多个元素
    myarr = append(myarr, []int{7, 8}...) // 追加一个切片, ... 表示解包，不能省略
    myarr = append([]int{0}, myarr...) // 在第一个位置插入元素
    myarr = append(myarr[:5], append([]int{5,6}, myarr[5:]...)...) // 在中间插入一个切片(两个元素)
    fmt.Println(myarr)
}
```

### 5、字典与布尔类型

由多个键值对`key:value`组成的映射结构，是哈希表的一个实现，每个key值都是唯一的，因此key必须是可哈希的。Go语言中除了切片、字典、函数以外的其他内建类型都是可哈希的。

声明字典时必须指定key和value的类型：`map[Key_Type]Value_Type`

#### 字典的声明和初始化

类似其他声明有三种方法：

```go
var scores map[string]int = map[string]int{"English":20, "Chinese": 85}

scores := map[string]int{"English":20, "Chinese": 85}

scores := make(map[string]int)
scores["English"] = 80
scores["Chinese"] = 85
```

字典赋值与C++类似，如果没有key则会添加元素，如果已经存在则会更新value。删除元素用`delete`函数。

```go
scores["math"] = 95 // 添加元素
scores["math"] = 90 // 修改元素

fmt.Println(scores["math"])

delete(scores, "math")
```

#### 判断key是否存在

读取元素时，如果key不存在也不会报错，而是返回value对应类型的零值。字典的下标读取时返回的第二个值表示key是否存在：

```go
import "fmt"

func main() {
    scores := map[string]int{"English": 80, "Chinese": 85}
    if math, ok := scores["math"]; ok {
        fmt.Printf("math 的值是: %d", math)
    } else {
        fmt.Println("math 不存在")
    }
}
```

#### 字典的遍历

A. 获取key和value：

```go
import "fmt"

func main() {
    scores := map[string]int{"English": 80, "Chinese": 85}

    for subject, score := range scores {
        fmt.Printf("key: %s, value: %d\n", subject, score)
    }
}
```

B. 只获取key，注意不需要占位符

```go
import "fmt"

func main() {
    scores := map[string]int{"English": 80, "Chinese": 85}

    for subject := range scores {
        fmt.Printf("key: %s\n", subject)
    }
}
```

C. 只获取value，用一个占位符

```go
import "fmt"

func main() {
    scores := map[string]int{"English": 80, "Chinese": 85}

    for _, score := range scores {
        fmt.Printf("value: %s\n", score)
    }
}
```

### 布尔类形

真值用`true`表示，不与1相等，也不能和不同类型进行比较，假值用`false`表示，同样不能与0比较。如果要比较则需要实现`bool`和`int`类型的转换：

```go
func bool2int(b bool) int {
    if b {
        return 1
    }
    return 0
}

func int2bool(i int) bool {
    return i != 0
}
```

使用`!`对布尔值取反，用`&&`表示与，用`||`表示或，有短路行为（从左到右，已经能确定表达式的值则不会再计算右边，同C++）

```go
func main() {
    var male bool = true

    fmt.Println(!male == false)

    var age int = 15
    var gender string = "male"

    var result bool = age > 18 && gender == "male"
}
```

## 6、指针类型

指针类型用来存储一个变量的内存地址。

### 指针的创建

与C++相同，`&`表示获取一个普通变量的地址，`*`表示获取指针对应的变量（或对应地址的内存内容）取指针创建有三种方法

```go
// 第一种：先定义对应的变量，再通过变量取得内存地址：
aint := 1
ptr := &aint 

// 第二种：先创建指针，再给指针写入对应的内存地址：
pstr := new(string) // new 分配指针变量
*pstr := "dangkh"

// 第三种，先声明一个指针变量，再从其他变量取得内存地址并赋值给它
var bint *int
aint := 1
bint = &aint
```

可以使用`%p`来格式化输出指针内容：

```go
fmt.Printf("%p", ptr)
```

### 指针的类型

指针类型与存储的变量类型有关：

```go
package main

import "fmt"

func main() {
    astr := "hello"
    aint := 1
    abool := false
    arune := 'a'
    afloat := 1.2

    fmt.Printf("astr 指针类型是：%T\n", &astr) // astr 指针类型是：*string
    fmt.Printf("aint 指针类型是：%T\n", &aint) // aint 指针类型是：*int
    fmt.Printf("abool 指针类型是：%T\n", &abool) // abool 指针类型是：*bool
    fmt.Printf("arune 指针类型是：%T\n", &arune) // arune 指针类型是：*int32
    fmt.Printf("afloat 指针类型是：%T\n", &afloat) //afloat 指针类型是：*float64
}
```

定义接收指针类型的函数可以写成：

```go
func testFunc(ptr *int) {
    fmt.Println(*ptr)
}
```

指针的零值是`nil`，未初始化的指针即是零值。

### 指针与切片——对数组的修改

指针与切片一样都是引用类型，想要通过函数修改一个数组可以通过指针或者切片。但一般情况下更倾向于使用切片的方法，因为带有指针的代码可读性更差。

```go
func modifyBySlice(slice []int, value int) {
    slice[0] = value
}

func modifyByPtr(arr *[3]int, value int) {
    (*arr)[0] = value
}

func main() {
    arr1 := [3]int(89,90,91)
    modifyBySlice(arr1, 100)
    modifyByPtr(arr1, 105)
}
```

## 流程控制

* if-else条件语句
* switch-case选择语句
* for-range循环语句
* goto跳转
* defer延迟执行

### 1、if-else条件语句

Go语言对于包裹的大括号`{`和`}`有着严格的要求，要求`else`和`else if`两边的大括号必须在同一行。由于Go语言是强类型，因此表达式必须返回布尔类型`true`或`false`，而不能是nil或0或1或其他值。

```go
if condition1 {
    branch1
} else if condition2 {
    branch2
} else if conditionN ... {
    branchN ...
} else {
    defaultBranch
}
```

Go语言的`if`里允许先运行一个表达式，取得变量后再对其进行判断：

```go
func main() {
    if age := 20; age > 18 {
        fmt.Println(age)
    }
}
```

### 2、switch-case

选择语句模型如下：

```go
switch value {
    case value == condition1:
        branch1
    case value == condition2:
        branch2
    case value == condition3:
        branche3:
    default:
        defaultBranch
}
```

一个case可以包含多个条件，用逗号`,`隔开：

```go
func main() {
    month := 3

    switch month {
        case 2, 3, 4:
            fmt.Println("234")
        case 5, 6, 7:
            fmt.Println("567")
        case 8, 9, 10:
            fmt.Println("8910")
        case 11, 12, 1:
            fmt.Println("11121")
        default:
            fmt.Println("Error!")
    }
}
```

`case`后面接的是**常量**，该常量不能重复出现。

`switch`后面可以接一个函数，注意`case`的值类型与返回值一致即可：

```go
func IsValid(args ...int) bool {
    for _, grade := range args {
        if grade < 60 {
            return false
        }
    }
    return true
}

func main() {
    Chinese := 80
    English := 50
    Math := 100

    switch IsValid(Chinese, English, Math) {
        case true:
            fmt.Println("Valid")
        case false:
            fmt.Println("Not Valid")
        default:
            fmt.Println("Error")
    }
}
```

`switch`不接任何变量、表达式、函数，此时用法相当于`if-else`：

```go
func main(){
    score := 30

    switch {
        case score >= 95 && score <= 100:
            fmt.Println("优秀")
        case score >= 80:
            fmt.Println("良好")
        case score >= 60:
            fmt.Println("合格")
        case score >= 0:
            fmt.Println("不合格")
        default:
            fmt.Println("输入有误...")
    }
}
```

一般情况下，有一个`case`满足条件并执行后，就会退出模块。使用关键字`fallthrough`开启`switch`的穿透能力，能够让条件穿透**一层**，即不需要判断条件**直接**执行下一个`case`语句

```go
func main() {
    s := "hello"
    switch {
    case s == "hello":
        fmt.Println("hello")
        fallthrough
    case s == "xxxx":
        fmt.Println("xxxx")
    case s != "world":
        fmt.Println("world")
    }
    // 输出 hello xxxx
}
```

### 3、for循环

Go语言的for循环可以概括为以下四种用法：

1. 接一个条件表达式
2. 接三个表达式（一般的C++用法）
3. 接一个range表达式（range后可接数组、切片、字符串等可迭代对象）
4. 不接表达式（While循环）

```go
// 1. 接一个条件表达式
func Loop1() {
    a := 1
    for a <= 5 {
        fmt.Println(a)
        a++
    }
}

// 2. 接三个表达式（一般的C++用法）
func Loop2() {
    for i:= 1; i <= 5; i++ {
        fmt.Println(i)
    }
}

// 3. 接一个range表达式（range后可接数组、切片、字符串等可迭代对象）
func Loop3() {
    languageList := [...]string{"C++", "python", "go"}
    for i, item := range languageList {
        fmt.Printf("Hello, %s\n", item)
    }
}

// 4. 不接表达式（While循环）
func Loop4() {
    i := 1
    for {
        if i > 5 {
            break
        }
        i++
    }
    for ;; {
        if i > 10 {
            break
        }
        i++
    }
}
```

### 4、goto无条件跳转

`goto`后接一个标签，告诉程序下一步执行哪里的代码：

```go
func main() {
    goto flag
    fmt.Println("B")
flag:
    fmt.Println("A")
}
```

`goto`通常与条件语句配合使用，来实现条件转移、循环、跳出循环等。

使用`goto`实现一个循环：

```go
func main() {
    i := 1
flag:
    if i <= 5 {
        fmt.Println(i)
        i++
        goto flag
    }
}
```

使用`goto`实现break：

```go
func main() {
    i := 1
    for {
        if i > 5 {
            goto flag
        }
        i++
    }
flag:
    fmt.Println(i)
}
```

使用`goto`实现continue：

```go
func main() {
    i := 1
flag:
    for i <= 10 {
        if i % 2 == 1{
            i++
            goto flag
        }
        fmt.Println(i)
        i++
    }
}
```

`goto`语句与标签之间不能有变量声明，否则会有编译错误。

### 5、defer延迟语句

A、`defer`在后面跟一个函数调用，即可是想将该函数的调用延迟到当前函数执行完之后。

```go
func EndFunc() {
    fmt.Println("B")
}

func main() {
    defer EndFunc()
    fmt.Println("A")
}
```

B、使用`defer`只是延时函数调用，此时传递给函数里的变量不会受到后续程序的影响。

```go
func main() {
    name := "C++"
    defer fmt.Println(name)

    name = "go"
    fmt.Println(name)

    // 输出 C++ go
}
```

如果`defer`后面跟匿名函数，需要注意变量的值会有所不同：

```go
func main () {
    name := "go"
    defer func1() {
        fmt.Println(name)
    }()
    defer func2(_name string) {
        fmt.Println(_name)
    }(name)

    name := "C++"
    fmt.Println(name)
}
```

C、多个`defer`执行顺序是后进先出（程序压栈）：

```go
func main() {
    name := "go"
    defer fmt.Println(name)

    name = "python"
    defer fmt.Println(name)

    name = "C++"
    fmt.Println(name)

    // 输出 C++ python go
}
```

D、`defer`在`return`后才调用

```go
var gName string = "go"

func func1() string {
    defer func() {
        gName = "python"
    }()

    fmt.Printf("myfunc 函数里的name：%s\n", gName)
    return gName
}

func main() {
    name := func1()
    fmt.Printf("main 函数里的gName: %s\n", gName)
    fmt.Println("main 函数里的name: ", name)
}

// ：输出
// myfunc 函数里的name：go
// main 函数里的name: python
// main 函数里的myname:  go
```

### 6、select用法

`select-case`用法相比于`switch-case`比较单一，仅能用于 **信道/通道** 的相关操作。

A、创建信道，并在`select`前给通道c2发送数据：

```go
func main() {
    c1 := make(chan string, 1)
    c2 := make(chan string, 1)

    c2 <- "hello"

    select {
    case msg1 := <-c1:
      fmt.Println("c1 received: ", msg1)
    case msg2 := <-c2:
      fmt.Println("c2 received: ", msg2)
    default:
      fmt.Println("No data received.")
    }

    // 输出： c2 received:  hello
}
```

运行`select`时，会遍历所有的`case`表达式，只要有一个信道接收到了数据，`select`就会结束。

B、`select`的执行过程中必须能够满足其中的一个`case`表达式。如果所有的`case`都不满足，此时会进入`default`分支。如果没有`default`分支，`select`模块就会阻塞，不断等待某个`case`可以命中，如果一直等不到满足条件的`case`，就会抛出`dedalock`错误。

```go
func main() {
    c1 := make(chan string, 1)
    c2 := make(chan string, 1)

    // c2 <- "hello"
    select {
    case msg1 := <-c1:
        fmt.Println("c1 received: ", msg1)
    case msg2 := <-c2:
        fmt.Println("c2 received: ", msg2)
        // default:
        //  fmt.Println("No data received.")
    }
}
```

因此在写任何分支（`switch`或者`select`）时一定要写上`default`分支，并尽可能保证至少有一个`case`可以命中。

C、select运行时并不是按照`case`顺序执行的，而是随机的

D、当`case`里的信道始终没有接收到数据，而且也没有`default`语句时，`select`模块就会阻塞，此时可以设置超时时间：

```go
func makeTimeout(ch chan bool, t int) {
    time.Sleep(time.Second * time.Duration(t))
    ch <- true
}

func main() {
    c1 := make(chan string, 1)
    c2 := make(chan string, 1)
    timeout := make(chan bool, 1)

    go makeTimeout(timeout, 2)

    select {
    case msg1 := <-c1:
        fmt.Println("c1 received: ", msg1)
    case msg2 := <-c2:
        fmt.Println("c2 received: ", msg2)
    case <-timeout:
        fmt.Println("Timeout, exit.")
    }
}
```

E、信道读取/写入操作

`select`模块中的`case`表达式只要求是对信道的操作，不管是读取操作还是写入操作。

```go
func main() {
    c1 := make(chan int, 2)

    c1 <- 2
    select {
    case c1 <- 4:
        fmt.Println("c1 received: ", <-c1)
        fmt.Println("c1 received: ", <-c1)
    default:
        fmt.Println("channel blocking")
    }
}
```

F、信道关闭也能命中

当一个信道被`close`后，`select`模块也能命中：

```go
func main()  {
    c1 := make(chan int, 1)
    c2 := make(chan int, 1)
    close(c1)
    for {
        select {
        case <-c1:
            fmt.Println("stop");
                        return
        case <-c2:
            fmt.Println("hhh")

        }
    }
}
```

#### 一些关于select的注意事项

* select 只能用于 channel 的操作(写入/读出/关闭)；
* select 的 case 是随机的，而 switch 里的 case 是顺序执行；
* select 要注意避免出现死锁，同时也可以自行实现超时机制；
* select 里没有类似 switch 里的 fallthrough 的用法；
* select 不能像 switch 一样接函数或其他表达式。

## 异常机制

### panic和recover

一些程序错误在编一阶段就能被编译器发现并提示，比如语法错误或类型错误等；一些错误在运行时才能发现并提示，比如数组访问越界，空指针引用等，这些运行时错误会引起程序报错退出。我们也可以自己按照预期条件为程序设置异常退出，手动触发`panic`：

```go
func main() {
    flag := -1
    if flag < 0 {
        panic("crash")
    }
}
```

可以使用`recover`来捕获抛出的异常，注意`recover`只能在`defer`函数中才能生效：

```go
var nums [10]int

func SetData(x int) {
    defer func() {
        if curError := recover(); curError != nil {
            fmt.Println(curError)
        }
    }()

    nums[x] = 11
}

func main() {
    SetData(20)

    fmt.Println("Program Done")
}
```

通常来说不应该对`panic`宕机的程序做任何处理，但有时需要在程序退出前完成一些工作，比如当Web服务器遇到问题要退出时，应先关闭所有连接，否则会使得客户端一直处于等待状态。

`panic`会使程序退出，但是如果有`defer`延迟函数存在，还要先执行完`defer`才会退出。这个特性在多个协程之间是没有效果的，子协程里出发`panic`只能触发自己协程内的`defer`，而不能调用主进程中的`defer`函数：

```go
func main() {
    // 这个defer不会执行
    defer fmt.Println("In main")

    go func() {
        defer fmt.Println("in goroutine")
        panic("Error")
    }
}
```

## 语法规则

### 语句块与作用域

语句块是用大括号`{}`包含的语句集合。语句块内声明的变量和函数不能被语句块外部访问，即就是定义了作用域。这种使用花括号定义的语句块属于显示语句块，还有一些隐式语句块：

* 主语句块：包括所有源码，对应内置作用域
* 包语句块：包括该包（package）中所有的源码（一个包可能会包括一个目录下的多个文件），对应包级作用域
* 文件语句块：包括该文件中的所有源码，对应文件级作用域
* for 、if、switch等语句本身也在它自身的隐式语句块中，对应局部作用域

作用域定义了变量能够使用的位置。四种作用域类型：

* 内置作用域：不需要自己声明，所有的关键字和内置类型、函数都拥有全局作用域
* 包（package）级作用域：必须函数外声明，在该包内的所有文件都可以访问
* 文件级作用域：不需要声明，导入即可。一个文件中通过import导入的包名，只在该文件内可用
* 局部作用域：在自己的语句块内声明，包括函数，for、if 等语句块，或自定义的大括号`{}`语句块形成的作用域，只在自己的局部作用域内可用

上述四种作用域从上到下范围逐渐缩小。对于其调用方式有以下几点：

* 底层作用域可以访问高层作用域
* 同一层级的作用域是相互隔离的
* 底层作用域里声明的变量会覆盖高层作用域声明的同名变量

**作用域**和**生命周期**是完全不同的概念。作用域是声明语句对应的源代码中的文本区域，是编译时的一个空间属性；生命周期是指程序运行时变量存在的有效时间段，在该时间段内可以被引用，是一个运行时的时间概念。

#### 静态作用域和动态作用域

根据局部作用域内变量可见性的不变性吗，可以将程序语言分成两类：

* 静态作用域，如Go语言
* 动态作用域，如Shell语言

```shell
#!/bin/bash
func01() {
    local value=1
    func02
}
func02() {
    echo "func02 sees value as ${value}"
}
```

上述Shell代码中func01函数中的局部变量value，由于func02函数的调用，暂时赋予了func02函数的可见性。脱离了func01的执行环境，放在全局环境或者其它函数中，func02是不能访问value的。

这种动态作用域的使用方法在其他语言中是不存在的，包括Go语言、C++等。
