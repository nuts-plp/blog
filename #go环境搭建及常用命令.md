# 

day1-go语言学习笔记

## go环境搭建

1、下载开发包并安装  url：golang.google.cn

2、创建一个存放源文件的文件夹  例：c:\user\desktop\go

3、将存放源文件的文件夹路径添加到环境变量中 gopath：c:\user\desktop\go

4、在c:\user\desktop\go文件夹下创建bin、pkg、src三个文件夹

5、把bin路径添加到环境变量path中，win10默认创建一个gopath值%USERPROFILE%go

查看go相关的环境变量

![image-20220531235829658](C:\Users\88463\Desktop\笔记\#go语言学习笔记.assets\image-20220531235829658.png)

## 项目目录结构

![image-20220601000415338](C:\Users\88463\Desktop\笔记\#go语言学习笔记.assets\image-20220601000415338.png)

![image-20220601000444139](C:\Users\88463\Desktop\笔记\#go语言学习笔记.assets\image-20220601000444139.png)

## 下载编辑器

下载vscode并安装go拓展

![image-20220601001916260](C:\Users\88463\Desktop\笔记\#go语言学习笔记.assets\image-20220601001916260.png)

## 常用指令

### go mod init 

go mod init 项目名 用于创建一个新的项目后的初始化，会在项目下创建一个go.mod文件

### go run

go run main.go也可以执行该程序，该命令本质上也是先编译再执行

### go install

go install表示安装的意思，他表示把源文件编译得到可执行文件，然后将可执行文件移动到gopath的bin文件夹中，所以我们就可以在任何地方执行可执行文件了。

### go doc builtin.函数名

**如 go doc builtin.delete**

![image-20220602142419025](C:\Users\88463\Desktop\笔记\image-20220602142419025.png)

在线文档 `https://www.studygolong.com/pkgdoc`

## 跨平台编译

go build默认编译的都是当前系统可执行的文件，go语言支持跨平台编译--在当前平台（window平台）编译其他平台（linux mac）平台的可执行文件

### 例：windows编译linux可执行文件

**注意目标平台的的操作系统和架构以及不同终端使用不同的指令**

如果你的windows使用的是cmd使用如下命令指定环境变量

1、SET CGO_ENABLED=0

2、SET GOOS="linux"

3、SET GOARCH=amd64

如果你的windows使用的是powershell终端，那么设置环境变量的指令为

1、$ENV:CGO_ENABLED=0

2、$ENV GOOS="linux"

3、$ENV GOARCH=amd64

在windows终端执行完上述命令后，再执行以下命令就可以得到linux平台的可执行文件了

go build

### 变量的声明

匿名变量用__接收,不存在重复声明，不占用空间

同一作用域不能重复声明同一个变量

![image-20220601092217775](C:\Users\88463\Desktop\笔记\image-20220601092217775-16540465396691-16540465424533-16540465454555.png)

**声明变量的同时并且赋值后必须使用**

var name string ="潘丽萍"

### 局部变量声明的方式

**注意类推声明和简短声明方式只能用于方法体内**

1、直接声明且赋值

var name string ="潘丽萍"

2、类推声明且赋值

var age = 19

3、简短声明赋值

var ：= false

## 常量的声明

常量声明后必须赋值

const pi=3.14159

批量声明

iota是常量的计数器，当出现const时iota重置为0，每增**加一行**变量的声明iota值加1

const（

a1，a2 = iota+1,iota+2//iota=0

__ = iota+1//此时存在一个匿名变量，iota=1

a3, a4 =iota+1,iota+2 //iota=2



）

a1:1    a2:2     a3:3   a4:4

批量声明变量时，当给一个变量赋值后，后面的变量没有继续赋值，那么后面没赋值的变量默认与上一行赋值的变量等值

const(

a=1

b

c

)

a:1     b:1   c:1

**数量级常量**

const（

__=iota

KB=1 << (10*iota)   

MB=1 << (10*iota)

GB=1 << (10*iota)

TB=1 << (10*iota)

PB=1 << (10*iota)

）

### 整型

![image-20220601105258547](C:\Users\88463\Desktop\笔记\image-20220601105258547.png)

### 特殊整型

![image-20220601105339486](C:\Users\88463\Desktop\笔记\image-20220601105339486.png)

### 进制转换

![image-20220601104823567](C:\Users\88463\Desktop\笔记\image-20220601104823567.png)

## 浮点数、复数、布尔

**浮点数**

![image-20220601112836793](C:\Users\88463\Desktop\笔记\image-20220601112836793.png)

**复数**

![image-20220601112924285](C:\Users\88463\Desktop\笔记\image-20220601112924285.png)

**布尔**

![image-20220601112959384](C:\Users\88463\Desktop\笔记\image-20220601112959384.png)

## 字符串

![image-20220601132301164](C:\Users\88463\Desktop\笔记\image-20220601132301164.png)

![image-20220601135352587](C:\Users\88463\Desktop\笔记\image-20220601135352587.png)

## 字符串转义符

![image-20220601132555433](C:\Users\88463\Desktop\笔记\image-20220601132555433.png)

![image-20220601133524518](C:\Users\88463\Desktop\笔记\image-20220601133524518.png)

## for循环

![image-20220601150722892](C:\Users\88463\Desktop\笔记\image-20220601150722892.png)

![image-20220601150736276](C:\Users\88463\Desktop\笔记\image-20220601150736276.png)

![image-20220601203201205](C:\Users\88463\Desktop\笔记\image-20220601203201205.png)

## switch

![image-20220601152338274](C:\Users\88463\Desktop\笔记\image-20220601152338274.png)

**特殊的**

fallthrough和goto（一般情况下不要用）

![image-20220601152411307](C:\Users\88463\Desktop\笔记\image-20220601152411307.png)

![image-20220601153044952](C:\Users\88463\Desktop\笔记\image-20220601153044952.png)

## 运算符

### **算术运算符**

![image-20220601153352855](C:\Users\88463\Desktop\笔记\image-20220601153352855.png)

![image-20220601221625843](C:\Users\88463\Desktop\笔记\image-20220601221625843.png) 

### 逻辑运算符

![image-20220601153558657](C:\Users\88463\Desktop\笔记\image-20220601153558657.png)

![image-20220601221553887](C:\Users\88463\Desktop\笔记\image-20220601221553887.png)

### 关系运算符

![image-20220601153522900](C:\Users\88463\Desktop\笔记\image-20220601153522900.png)

![image-20220601221516569](C:\Users\88463\Desktop\笔记\image-20220601221516569.png) 

### 位运算符

![image-20220601153705501](C:\Users\88463\Desktop\笔记\image-20220601153705501.png)

![image-20220601221417203](C:\Users\88463\Desktop\笔记\image-20220601221417203.png)

### 赋值运算符

![image-20220601153732263](C:\Users\88463\Desktop\笔记\image-20220601153732263.png)

![image-20220601221357261](C:\Users\88463\Desktop\笔记\image-20220601221357261.png)

## 格式化打印

![image-20220603233735104](C:\Users\88463\Desktop\笔记\image-20220603233735104.png)





![image-20220603233408520](C:\Users\88463\Desktop\笔记\image-20220603233408520.png)

### 宽度标识符

![image-20220603233828797](C:\Users\88463\Desktop\笔记\image-20220603233828797.png)
