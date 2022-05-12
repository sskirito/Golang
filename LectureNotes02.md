# [Go编程时光](https://golang.iswbm.com/)面向对象

## 一、结构体与继承

Go语言的结构体`struct`即相当于其他语言的类`class`，是一种多个类型的变量组合在一起的聚合数据类型。结构体没有继承。

### 1、定义结构体

声明结构体：

```go
type Profile struct {
    name string
    age int
    gender string
    mother *Profile
    father *Profile
}
```

若相邻的属性（字段）是相同类型，则可以写在一起：

```go
type Profile struct {
    name, gender string
    age int
    mother *Profile
    father *Profile
}
```

#### 通过结构体定义对象的规则

**规则一：** 当最后一个字段和结果不在同一行时，逗号`,`不可省略：

```go
dkh := Profile {
    name: "dangkh",
    age: 24,
    gender: "shiro",
}
```

如果在同一行就可以省略：

```go
dkh := Profile {
    name: "dangkh",
    age: 24,
    gender: "shiro"}
```

**规则二：** 字段名要么全写，要么全不写。以下为错误写法：

```go
dkh := Profile {
    name: "dangkh",
    24,
    "male",
}
```

**规则三：** 初始化结构体时，不一定要所有字段都赋值，未被赋值的字段会自动赋值为对应类型的零值。只有通过指定字段名的方式`字段名: 值`才可以赋值部分字段，不指定字段名会报错。

```go
dkh := Profile {
    name: "dangkh",
}
fmt.Println(dkh.age) //output: 8
```

### 2、绑定方法

Go语言中无法在结构体内定义方法，需要使用组合函数的方式来定义结构体方法：

```go
// 定义一个名为Profile 的结构体
type Profile struct {
    name   string
    age    int
    gender string
    mother *Profile // 指针
    father *Profile // 指针
}

// 定义一个与 Profile 的绑定的方法
func (person Profile) FmtProfile() {
    fmt.Printf("名字：%s\n", person.name)
    fmt.Printf("年龄：%d\n", person.age)
    fmt.Printf("性别：%s\n", person.gender)
}

func main() {
    // 实例化
    dkh := Profile{name: "dangkh", age: 24, gender: "male"}
    // 调用函数
    dkh.FmtProfile()
}
```

其中`FmtProfile`是方法名，`(person Profile)`表示将方法与结构体`Profile`的实例绑定。把`Profile`称为方法的接收者，而`person`表示实例本身，相当于python中的`self`或C++的`this`指针。

### 3、方法的参数传递方式

如果要在方法内改变结构体实例的属性，必须使用指针作为方法的接收者：

```go
type Profile struct {
    name   string
    age    int
    gender string
    mother *Profile
    father *Profile
}

// 传递指针以修改属性
func (person *Profile) increase_age() {
    person.age += 1
}

func main() {
    myself := Profile{name: "小明", age: 24, gender: "male"}
    fmt.Printf("当前年龄：%d\n", myself.age)
    myself.increase_age()
    fmt.Printf("当前年龄：%d", myself.age)
}
```

一般情况下，如果 (1)需要在方法内修改结构体内容；(2)结构体过大导致性能下降 时，都应该使用指针作为方法的接收者。如果某些情况下使用值传递或指针传递都可以，那么考虑到代码一致性，都建议使用指针传递。

### 4、结构体的组合（即继承的实现）

Go语言本身不支持继承，可以使用**组合**的方法实现类似继承的效果。把一个结构体嵌入到另一个结构体中的方法叫做**组合**。

```go
type company struct {
    companyName string
    companyAddr string
}

type staff struct {
    name string
    age int
    gender string
    position string
    company // 匿名字段，将结构体嵌入到此类中，使其拥有所有的属性
}

func main()  {
    myCom := company{
        companyName: "Alibaba",
        companyAddr: "Nanshan, Shenzhen",
    }
    staffInfo := staff{
        name:     "dangkh",
        age:      24,
        gender:   "male",
        position: "Cloud Platform",
        company: myCom,
    }

    fmt.Printf("%s 在 %s 工作\n", staffInfo.name, staffInfo.companyName)
    fmt.Printf("%s 在 %s 工作\n", staffInfo.name, staffInfo.company.companyName)
```

上例中，`staffInfo.companyName`和`stafInfo.company.companyName`效果是一样的。

### 5、内部方法与外部方法

在Go语言中，函数名的首字母大小写很重要，被用来实现控制对方法的访问权限。

* 当方法的首字母为大写时，这个方法对于所有包都是Public，其他包可以随意调用；
* 当方法的首字母为小写时，这个方法就是Private，其他包是无法访问的。

### 6、三种实例化方法

#### 第一种：正常实例化

```go
func main() {
    dkh := Profile {
        name: "dangkh",
        age: 18,
        gender: "male",
    }
}
```

#### 第二种：使用new函数

```go
func main() {
    dkh := new(Profile)
    // 等价于 var dkh *Profile = new (Profile)
    fmt.Println(dkh)

    dkh.name = "dangkh"
    dkh.age = 24
    dkh.gender = "male"
}
```

#### 第三种：使用引用 &

```go
func main() {
    var xm *Profile = &Profile{}
    fmt.Println(dkh)

    dkh.name = "dangkh"
    dkh.age = 24
    dkh.gender = "male"
}
```

### 7、指针的选择器

从结构体中获取字段的值，通常都是使用点`.`操作符进行，这个操作符叫做**选择器**。与C++不同，当你使用一个结构体的指针时，依然可以使用选择器`.`获取属性，解引用操作会自动完成：

```go
type Profile struct {
    name string
}
func main() {
    p1 := &Profile("dangkh")
    fmt.Println((*p1).name)
    fmt.Println(p1.name)
}
```

## 二、接口和多态

在面向对象领域，**接口定义一个对象的行为**。接口只规定了对象应该做什么，实现细节应该由对象本身去决定。

Go语言中的接口就是方法签名（Method Signature）的集合。当一个类型定义了接口中的所有方法，就称他实现了该接口。**接口指定了一个类型应该具有的方法，并由该类型决定如何实现这些方法**。

### 1、如何定义接口

使用`type`关键字来定义接口：

```go
type Phone interface {
    call()
}
```

### 2、如何实现接口

如果有一个类型/结构体，实现了一个接口要求的所有方法，就称之为实现了接口。如下例所示，由于`Huawer`实现了`call`方法，所以称它实现了接口`Phone`：

```go
type Huawei struct {
    name string
}

func(phone Huawei) call() {
    fmt.Println("Huawei is a phone!")
}
```

### 3、接口实现多态

**鸭子类型（Duck typing）** 定义是，只要你长得像鸭子，叫起来也像鸭子，那我认为你就是一只鸭子。

**Go语言中，通过接口来实现多态。**

先定义一个商品（Good）接口：

```go
type Good interface {
    settleAccount() int
    orderInfo() string
}
```

即如果有一个类型或结构体实现了`settleAccount()`和`orderInfo()`两个方法，那这个类型或结构体就是一个商品`Good`。接下来定义两个结构体，手机和赠品：

```go
type Phone struct {
    name string
    quantity int
    price int
}

type FreeGift struct {
    name string
    quantity int
    price
}
```

然后为他们实现`Good`接口的两个方法：

```go
func(phone Phone) settleAccount() int {
    return phone.quantity * phone.price;
}
func(phone Phone) orderInfo() string {
    return "You will buy " + strconv.Itoa(phone.quantity) + " " phone.name + " with totally " + strconv.Itoa(phone.settleAcount()) + " yuan."
}

func(gift FreeGift) int {
    return 0
}
func(FreeGift) orderInfo string {
    return strconv.Itoa(gift.quantity) + " gifts are free for you!"
}
```

此时可以定义两件商品，分别是手机和耳机（赠品），则可以把他们放在一个`Good`类型的数组或切片中了，即可以认为是同一个类型：

```go
mate40 := Phone {
    name: "Huawei",
    quantity: 1,
    price: 8000,
}
freeHub4 := FreeGift {
    name: "earPhone",
    quantity: 2,
    price: 2000,
}

goods := []Good{mate40, freeHub4}
```

定义一个方法来计算所有商品的总价格：

```go
func calculateAllPrice(goods []Good) int {
    var allPrice int
    for _, good := range goods {
        fmt.Println(good.orderInfo())
        allPrice += good.settleAccount()
    }
    return allPrice
}
```

## 三、结构体中的Tag用法

### 1、Tag的用处

结构体可以额外加一个属性，用反引号包裹的字符串，称之为`Tag`，或标签：

```go
type Person struct {
    Name string
    Age int
    Addr string
}

// 增加tag的写法
type Persont struct {
    Name string `json:"name"`
    Age int `json:"age"`
    Addr string `json:"addr"`
}
```

如下是使用Go语言的内置库`encoding/json`的用法：

```go
package main

import (
    "encoding/json"
    "fmt"
)

type Person struct {
    Name string `json:"name"`
    Age int `json:"age"`
    Addr string `json:"addr,omitempty"`
}

func main() {
    p1 := Person {
        Name: "dangkh",
        Age: 24,
    }

    data1, err := json.Marshal(p1)
    if err != nil {
        panic(err)
    }

    fmt.Printf("%s\n", data1)

    p2 := Person {
        Name: "dangkh",
        Age: 24,
        Addr: "China",
    }
    data2, err := json.Marshal(p2)
    if err != nil {
        panic(err)
    }

    fmt.Printf("%s\n", data2)
}

// 输出结果：
// {"name":"dangkh","age":24}
// {"name":"dangkh","age":24, "addr":"China"}
```

由于Person结构体中的`Addr`字段有`omitempty`属性，因此`encoding/json`在将对象转化为json字符串时，只要发现对象里的`Addr`为false、0、空指针、空接口、空数组、空切片、空映射、空字符串中的一种，就会被忽略。因此`p1`的输出中没有`Addr`属性。

### 2、如何定义获取Tag

Tag由反引号包含，由一对或几对键值对组成，通过空格来分割键值，格式如下：

```json
`key01:"value01" key02:"value02" key03:"value03"`
```

定义完后，使用反射从结构体中取出Tag。获取Tag的三个步骤：

1. 获取字段field
2. 获取标签tag
3. 获取键值对`key:value`

如下例所示：

```go
// 三种获取field的方式
field := reflect.TypeOf(obj).FieldByName("Name")
field := reflect.ValueOf(obj).Type().Field(i) // i 表示第几个字段
field := reflect.ValueOf(&obj).Elem().Type().Field(i)

tag := field.Tag

labelValue := tag.get("label")
labelValue, ok = tag.Lookup("Lable")
```

从Tag获取键值对有Get和Lookup两种方法，其中Get只是对Lookup的简单封装，当没有获取到对应tag的内容，就会返回空字符串：

```go
func (tag StructTag) Get(key string) string {
    v, _ = tag.Lookup(key)
    return v
}
```

空Tag与不设置Tag效果是一样的。

### 3、使用Tag的示例

实现一个函数，在打印`Person`对象的时候能够美化输出：

```go
type Person struct {
    Name string `label:"Name is: "`
    Age int `label:Age is: "`
    Gender string `label:"Gender is: " default:"unknown`
}

func Print(obj interface{}) error {
    v := reflect.ValueOf(obj)

    for i := 0; i < v.NumField(); i++ {
        field := v.Type().Field()
        tag := field.Tag

        label := tag.Get("label")
        defaultValue := tag.Get("default")

        value := fmt.Sprintf("%v", v.Field(i))
        if value == "" {
            value = defaultValue
        }

        fmt.Println(label + value)
    }
    return nil
}
```

## 四、类型断言

### Type Assertion

类型断言主要做以下两件事：

1. 检查`i`是否为`nil`
2. 检查`i`存储的是否为某个类型

使用方法有两种：

**第一种：**

```go
t := i.(T)
```

这个表达式可以断言一个接口对象`i`里不是 `nil`，并且接口对象`i`存储的值的类型是`T`，如果断言成功，就会**返回值**给`t`，如果断言失败，就会触发`panic`。如下例：

```go
func main() {
    var i interface{} = 10
    t1 := i.(int)
    fmt.Println(t1)

    t2 := i.(string)
    fmt.Print(t2)

    var i2 interface{} // nil
    var t3 = i.(interface{})
}
```

上述`t1`会获得对应的值，`t2`运行时会失败，并触发`panic`，`t3`由于接口值是`nil`，也会触发`panic`。

**第二种：**

```go
t, ok := i.(T)
```

与第一种方法相同，这个表达式也可以进行类型断言。如果断言成功，`ok`的值就是`true`，如果断言失败，`ok`的值为false，`t`为对应类型的零值，且不会触发`panic`。

```go
func main() {
    var i interface{} = 10
    t1, ok := i.(int)
    fmt.Printf("%d-%t\n", t1, ok)

    t2, ok := i.(string)
    fmt.Printf("%s-%t\n", t2, ok)

    var k interface{} // nil
    t3, ok := k.(interface{})
    fmt.Println(t3, "-", ok)

    t4, ok := k.(interface{})
    fmt.Printf("%d-%t\n", t4, ok)

    t5, ok := k.(int)
    fmt.Printf("%d-%t\n", t5, ok)
}
```

### Type Switch

如果需要区分多种类型，可以使用`type switch`，这种方法比一个一个进行类型断言更简单。

```go
package main

import "fmt"

func findType(i interface{}) {
    switch x := i.(type) {
    case int:
        fmt.Println(x, "is int")
    case string:
        fmt.Println(x, "is string")
    case nil:
        fmt.Println(x, "is nil")
    default:
        fmt.Println(x, "not type matched")
    }
}

func main() {
    findType(10)      // int
    findType("hello") // string

    var k interface{} // nil
    findType(k)

    findType(10.23) //float64
}
```

几点需要注意的问题：

* 如果要断定的值是`nil`，则会匹配`case nill`
* 如果值在`switch-case`中没有匹配的类型，就会进入`default`分支
* 类型断言，仅能对静态类型为空接口(interface{})的对象进行断言，否则会抛出错误
* 类型断言完成后，实际上会返回静态类型为你断言的类型的对象，而要清楚原来的静态类型为空接口类型(interface{})，这是Go的隐式类型转换

**详细内容可以参考：**

[Explain Type Assertions in GO](https://stackoverflow.com/questions/38816843/explain-type-assertions-in-go)

[Go interface详解(四)：类型断言](https://sanyuesha.com/2017/12/01/go-interface-4/)

## 五、空接口


