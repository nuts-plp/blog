

## shell使用

这里使用bash shell

#### 变量

声明一个变量     `user_name="sdnfjskdfas"`等号左右两边不能有空格

变量作为参数的传递 `echo "用户名字${user_name}"`大括号用于变量变量边界的声明

#### 数组

bash shell只支持一维数组，初始化时不需要定义数组大小，下标从0开始，各元素用空格隔开

##### 一维数组

例：`array=("asdfsa" "asfas" "fsadfas")`

或  

```shell
array[0]=A
array[2]=B
array[3]="C"
```

###### 读取数组格式

```shell
echo ${array[index]}
```

##### 关联数组

关联数组类似go中的map， k_v形式存储

关联数组可以使用字符串或整数作为下标  关联数组使用`declare`来声明 

格式如下

```shell
declare -A array
array["A"]=a
array["taobao"]="www.taobaoa.com"
array[9]="890"
```

###### 访问指定的键

```shell
echo ${array["taobao"]}
```

 ###### 获取关联数组的所有元素

```shell
echo ${array[*]}
# 或
echo ${array[@]}
```

借助*或@可以获取关联数组中所有的值

###### 获取数组所有的键

```shell
echo ${!array[*]}
# 或
echo ${!array[@]}
```

借助！和*/@获取关联数组中所有的键

###### 获取数组的长度

```shell
echo ${#array[*]}
# 或
echo ${#array[@]}
```

借助#和*/@获取数组的长度

#### 运算符及test测试

**注意：**

	- 运算符与变量之间要有空格
	- 完整的表达式要用反引号``包围



##### 算术运算符



| 运算符 | 说明                                                     | 举例           |
| ------ | -------------------------------------------------------- | -------------- |
| +      | 加法                                                     | `expr $a + $b` |
| -      | 减法                                                     | `expr $a - $b` |
| *      | 乘法                                                     | `expr $a * $b` |
| /      | 除法                                                     | `expr $a / $b` |
| %      | 取余                                                     | `expr $a % $b` |
| =      | 赋值                                                     | a=$b           |
| ==     | 等于                                                     | `[$a == $ b]`  |
| !=     | xxxxxxxxxx1 1{"msg":{"date":"date必须要晚于当前日期"}}go | `[$a != $b]`   |

示例

```shell
a=10
b=20
if test $[a] -eq $[b]
then
	echo "两数相等！"
else
	echo "两数不相等！"
fi
```

**注意：**

- []执行基本的算术运算

例

```shell
a=5
b=6
result=$[a + b]
echo "$result"
```



##### 关系运算符

| 运算符 |                       说明                       | 举例          |
| ------ | :----------------------------------------------: | ------------- |
| -eq    |         检测两个数是否相等，相等返回true         | `[$a -eq $b]` |
| -ne    |       检测两个数是否不相等，不相等返回true       | `[$a -ne $b]` |
| -gt    |      检测两个数是否大于右边的，大于返回true      | `[$a -gt $b]` |
| -lt    |     检测左边的数是否小于右边的，小于返回true     | `[$a -lt $b]` |
| -ge    | 检测左边的数是否大于等于右边的，大于等于返回true | `[$a -ge $b]` |
| -le    | 检测左边的数是否小于等于右边的，小于等于返回true | `[$a -le $b]` |



##### 布尔运算符

| 运算符 | 说明                                   | 举例                       |
| ------ | -------------------------------------- | -------------------------- |
| ！     | 非运算，表达式为true则返回false        | `[!true]`                  |
| -o     | 或运算，有一个表达式为true则返回true   | `[$a -lt $b -o $b -eq $a]` |
| -a     | 与运算，两个表达式都为true时才返回true | `[$a -lt $b -a $b -eq $a]` |



##### 字符串运算符

| 运算符 | 说明                                     | 举例         |
| ------ | ---------------------------------------- | ------------ |
| =      | 检测两个字符串是否相等，相等返回true     | `[$a = $b]`  |
| !=     | 检测两个字符串是否不相等，不相等返回true | `[$a != $b]` |
| -z     | 检测字符串长度是否为0，为0返回true       | `[ -z $b]`   |
| -n     | 检测字符串长度是否不为0，不为0返回true   | `[-n $b]`    |
| $      | 检测字符串是否不为空，不为空返回true     | `[ $b]`      |

例

```shell
a="www.baidu.com"
if test [-z $a]
then
	echo "a为空"
else
	echo "a不为空"
fi
```



##### 逻辑运算符

| 运算符 | 说明      | 举例                         |
| ------ | --------- | ---------------------------- |
| &&     | 逻辑的and | `[[$a -lt $b && $a -eq $b]]` |
| \|\|   | 逻辑的or  | `[[$a -lt $b || $a -eq $b]]` |



##### 文件测试运算符

| 运算符  | 说明                                                         | 举例         |
| ------- | ------------------------------------------------------------ | ------------ |
| -b file | 检测文件是否是块设备文件，如果是，则返回 true。              | [ -b $file ] |
| -c file | 检测文件是否是字符设备文件，如果是，则返回 true。            | [ -c $file ] |
| -d file | 检测文件是否是目录，如果是，则返回 true。                    | [ -d $file ] |
| -f file | 检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。 | [ -f $file ] |
| -g file | 检测文件是否设置了 SGID 位，如果是，则返回 true。            | [ -g $file ] |
| -k file | 检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。  | [ -k $file ] |
| -p file | 检测文件是否是有名管道，如果是，则返回 true。                | [ -p $file ] |
| -u file | 检测文件是否设置了 SUID 位，如果是，则返回 true。            | [ -u $file ] |
| -r file | 检测文件是否可读，如果是，则返回 true。                      | [ -r $file ] |
| -w file | 检测文件是否可写，如果是，则返回 true。                      | [ -w $file ] |
| -x file | 检测文件是否可执行，如果是，则返回 true。                    | [ -x $file ] |
| -s file | 检测文件是否为空（文件大小是否大于0），不为空返回 true。     | [ -s $file ] |
| -e file | 检测文件（包括目录）是否存在，如果是，则返回 true。          | [ -e $file ] |
| -S file | 判断某文件是否 socket。                                      | [ -S $file ] |
| -L file | 检测文件是否存在并且是一个符号链接。                         | [ -L $file ] |

例

```shell
if test -e ./bash
then
	echo "文件已存在"
else
	echo "文件不存在"
fi
```



#### 获取标准输入

使用`read`关键字

例如

```shell
read name
echo "$name is my lover"
```

作用类似go中fmt.Scan()系列



#### echo输出

###### 显示普通字符

```shell
echo "this is a test file"
# 或
echo this is a test file
```

###### 显示转义字符

```shell
echo "\"It is a test\""
```

###### 显示变量

```shell
read name			#标准输入 类似于go中的fmt.Scan系列
echo "$name , this is a test"
```

###### 显示换行

```shell
echo -e "ok! \n"  	#-e 开启转义 换行
```

###### 显示不换行

```shell
echo -e "ok! \c"
```

###### 显示结果定向至文件

```shell
echo "this is a test" > myfile
```

 ###### 原样输出字符串不进行转义或取变量

```shell
echo '$name\"'
```

###### 显示命令执行结果

```shell
echo `date`    #这里使用的是反引号 结果显示当前日期
```

#### 格式化输出printf

##### printf命令语法

`printf  format-string  [arguments......]`

- format-string为格式控制字符串
- arguments 为参数列表

示例

```shell
# format-string为双引号
printf "%d %s\n" 1 "abc"
# 单引号与双引号效果一样
printf '%d %s\n' 1 "abc"
# 没有引号也可以输出
printf %s abcdef
# 格式只指定了一个参数，但多出的参数仍然会按照该格式输出，format-string 被重用
printf %s abc def
printf "%s\n" abc def
printf "%s %s %s\n" a b c d e f g h i j
# 如果没有 arguments，那么 %s 用NULL代替，%d 用 0 代替
printf "%s and %d \n"
printf "%-10s %-8s %-4.2f" 潘丽萍 女 45.3454
# %-10s 指一个宽度为 10 个字符（- 表示左对齐，没有则表示右对齐），任何字符都会被显示在 10 个字符宽的字符内，如果不足则自动以空格填充，超过也会将内容全部显示出来
# %-4.2f 指格式化为小数，其中 .2 指保留2位小数。
```

| 格式替代符 | 描述                     |
| ---------- | ------------------------ |
| %s         | 输出一个字符串           |
| %c         | 整型输出                 |
| %d         | 输出一个字符             |
| %f         | 输出实数，以小数形式输出 |

##### printf转义序列

| 序列  | 说明                                                         |
| ----- | ------------------------------------------------------------ |
| \a    | 警告字符，通常为ASCII的BEL字符                               |
| \b    | 后退                                                         |
| \c    | 抑制（不显示）输出结果中任何结尾的换行字符（只在%b格式指示符控制下的参数字符串中有效），而且，任何留在参数里的字符、任何接下来的参数以及任何留在格式字符串中的字符，都被忽略 |
| \f    | 换页（formfeed）                                             |
| \n    | 换行                                                         |
| \r    | 回车（Carriage return）                                      |
| \t    | 水平制表符                                                   |
| \v    | 垂直制表符                                                   |
| \\\   | 一个字面上的反斜杠字符                                       |
| \d    | 表示1到3位数八进制值的字符。仅在格式字符串中有效             |
| \0ddd | 表示1到3位的八进制值字符                                     |

#### 流程控制

##### 格式1

```shell
if condition
then
	command1
	command2
	......
fi
```

##### 格式2

```shell
if condition
then
	command1
	command2
	......
else
	command3
	......
fi
```

##### 格式3

```shell
if condition1
then
	command1
	......
elif condition2
then
	command2
	....
else
	command3
	......
fi
```

##### for循环

格式

```shell
for var in item1 item2...
do
	command1
	command2
	......
done
```

示例

```shell
for loop in 1 2 3 4 5
do
	echo $loop
done
```

##### while 循环

格式

```shell
while condition	#直到条件为false时停止
do
	command
	......
done
```

##### 无限循环

格式

```shell
while :
do
	command
done
```

或者

```shell
for (( ; ; ))
```

##### until循环

```shell
until condition  #知道条件为true时停止
do
	command
done
```

##### case...esac

**case ... esac** 为多选择语句，与其他语言中的 switch ... case 语句类似，是一种多分支选择结构，每个 case 分支用右圆括号开始，用两个分号 **;;** 表示 break，即执行结束，跳出整个 case ... esac 语句，esac（就是 case 反过来）作为结束标记。

可以用 case 语句匹配一个值与一个模式，如果匹配成功，执行相匹配的命令。

格式

```shell
case 值 in
模式1)
	command
	......
	;;
模式2)
	command
	......
	;;
esac
```

示例

```shell
echo '输入 1 到 4 之间的数字:'
echo '你输入的数字为:'
read aNum
case $aNum in
    1)  echo '你选择了 1'
    ;;
    2)  echo '你选择了 2'
    ;;
    3)  echo '你选择了 3'
    ;;
    4)  echo '你选择了 4'
    ;;
    *)  echo '你没有输入 1 到 4 之间的数字'
    ;;
esac
```

##### break

跳出循环

示例

```shell
while :
do
    echo -n "输入 1 到 5 之间的数字:"
    read aNum
    case $aNum in
        1|2|3|4|5) echo "你输入的数字为 $aNum!"
        ;;
        *) echo "你输入的数字不是 1 到 5 之间的! 游戏结束"
            break
        ;;
    esac
done
```



##### continue

跳过

示例

```shell
while :
do
    echo -n "输入 1 到 5 之间的数字: "
    read aNum
    case $aNum in
        1|2|3|4|5) echo "你输入的数字为 $aNum!"
        ;;
        *) echo "你输入的数字不是 1 到 5 之间的!"
            continue
            echo "游戏结束"
        ;;
    esac
done
```

#### 函数

格式

```shell
[function] funname [()]
{
	action;
	[return int;]
}
```

**注意：**

	- 可以带function fun()定义，也可以直接fun()定义
	- 参数返回可以显示地加return返回，如果不加，将以最后一条命令运行结果作为返回值。return后跟数值（0-255）

##### 无参函数

示例

```shell
fun(){
	echo "bulabulabula!"
	read num
	echo $num
	return $num
}
fun
echo "返回值：$?"
```

**注意：**

	- 返回值用$?来访问
	- 所有函数在使用前必须定义。这意味着必须将函数放在脚本开始部分，直至shell解释器首次发现它时，才可以使用。调用函数仅使用其函数名即可。

##### 有参函数

在Shell中，调用函数时可以向其传递参数。在函数体内部，通过 $n 的形式来获取参数的值，例如，$1表示第一个参数，$2表示第二个参数...

示例：

```shell
fun(){
	echo "这是第一个参数：$1"
	echo "这是第一个参数：$2"
	echo "这是第一个参数：$3"
	echo "这是第一个参数：$4"
	echo "作为一个字符串输出所有参数：$*"
}
fun 1 2 3 4	#传入参数

```

**注意：**

	- 当参数多于九个时，需要${n}来获取参数，例如第十个参数 ${10}

另外还有几个特殊字符来处理参数

| 参数处理 | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| $#       | 传递到脚本或函数的参数个数                                   |
| $*       | 以一个单字符串显示所有向脚本传递的参数                       |
| $$       | 脚本运行的当前进程ID号                                       |
| $!       | 后台运行的最后一个进程的ID号                                 |
| $@       | 与$*相同，但是使用时加引号，并在引号中返回每个参数。         |
| $-       | 显示Shell使用的当前选项，与set命令功能相同。                 |
| $?       | 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。 |

#### 输入/输出重定向

大多数 UNIX 系统命令从你的终端接受输入并将所产生的输出发送回到您的终端。一个命令通常从一个叫标准输入的地方读取输入，默认情况下，这恰好是你的终端。同样，一个命令通常将其输出写入到标准输出，默认情况下，这也是你的终端。

重定向命令列表如下：

| 命令            | 说明                                               |
| --------------- | -------------------------------------------------- |
| command > file  | 将输出重定向到 file。                              |
| command < file  | 将输入重定向到 file。                              |
| command >> file | 将输出以追加的方式重定向到 file。                  |
| n > file        | 将文件描述符为 n 的文件重定向到 file。             |
| n >> file       | 将文件描述符为 n 的文件以追加的方式重定向到 file。 |
| n >& m          | 将输出文件 m 和 n 合并。                           |
| n <& m          | 将输入文件 m 和 n 合并。                           |
| << tag          | 将开始标记 tag 和结束标记 tag 之间的内容作为输入。 |

**注意:**文件描述符0通常是标准输入(STDIN)，1是标准输出(STDOUT)，2是标准错误输出(STDERR)

##### 输出重定向

重定向一般通过在命令间插入特定的符号来实现。特别的，这些符号的语法如下所示:

```shell
command > file
```

上面这个命令执行command1然后将输出的内容存入file1。

注意任何file1内的已经存在的内容将被新内容替代。如果要将新内容添加在文件末尾，请使用>>操作符。

如果不希望文件被覆盖可以使用>>追加到文件末尾

例

```shell
echo "www.baidu.com" > test.sh
echo "www.baidu.com" >> test.sh
```

##### 输入重定向

和输出重定向一样，Unix 命令也可以从文件获取输入，语法为：

```shell
command < file
```

这样，本来需要从键盘获取输入的命令会转移到文件读取内容。

注意：输出重定向是大于号(>)，输入重定向是小于号(<)。

##### 重定向深入

一般情况下，每个 Unix/Linux 命令运行时都会打开三个文件：

- 标准输入文件(stdin)：stdin的文件描述符为0，Unix程序默认从stdin读取数据。
- 标准输出文件(stdout)：stdout 的文件描述符为1，Unix程序默认向stdout输出数据。
- 标准错误文件(stderr)：stderr的文件描述符为2，Unix程序会向stderr流中写入错误信息。

默认情况下，command > file 将 stdout 重定向到 file，command < file 将stdin 重定向到 file。

如果希望 stderr 重定向到 file，可以这样写：

```shell
$ command 2>file
```

如果希望 stderr 追加到 file 文件末尾，可以这样写：

```shell
$ command 2>>file
```

**2** 表示标准错误文件(stderr)。

如果希望将 stdout 和 stderr 合并后重定向到 file，可以这样写：

```shell
$ command > file 2>&1

或者

$ command >> file 2>&1
```

如果希望对 stdin 和 stdout 都重定向，可以这样写：

```shell
$ command < file1 >file2
```

command 命令将 stdin 重定向到 file1，将 stdout 重定向到 file2。

##### Here Document

Here Document 是 Shell 中的一种特殊的重定向方式，用来将输入重定向到一个交互式 Shell 脚本或程序。

它的基本的形式如下：

```shell
command << delimiter
    document
delimiter
```

它的作用是将两个 delimiter 之间的内容(document) 作为输入传递给 command。

> 注意：
>
> - 结尾的delimiter 一定要顶格写，前面不能有任何字符，后面也不能有任何字符，包括空格和 tab 缩进。
> - 开始的delimiter前后的空格会被忽略掉。

实例

在命令行中通过 **wc -l** 命令计算 Here Document 的行数：

```
$ wc -l << EOF
    欢迎来到
    菜鸟教程
    www.runoob.com
EOF
3          # 输出结果为 3 行
$
```

我们也可以将 Here Document 用在脚本中，例如：

```
#!/bin/bash
# author:菜鸟教程
# url:www.runoob.com

cat << EOF
欢迎来到
菜鸟教程
www.runoob.com
EOF
```

执行以上脚本，输出结果：

```shell
欢迎来到
菜鸟教程
www.runoob.com
```

##### /dev/null 文件

如果希望执行某个命令，但又不希望在屏幕上显示输出结果，那么可以将输出重定向到 /dev/null：

```
$ command > /dev/null
```

/dev/null 是一个特殊的文件，写入到它的内容都会被丢弃；如果尝试从该文件读取内容，那么什么也读不到。但是 /dev/null 文件非常有用，将命令的输出重定向到它，会起到"禁止输出"的效果。

如果希望屏蔽 stdout 和 stderr，可以这样写：

```
$ command > /dev/null 2>&1
```

> **注意：**0 是标准输入（STDIN），1 是标准输出（STDOUT），2 是标准错误输出（STDERR）。
>
> 这里的 **2** 和 **>** 之间不可以有空格，**2>** 是一体的时候才表示错误输出。

#### shell文件包含

和其他语言一样，Shell 也可以包含外部脚本。这样可以很方便的封装一些公用的代码作为一个独立的文件。

Shell 文件包含的语法格式如下：

```shell
. filename   # 注意点号(.)和文件名中间有一空格

或

source filename
```

实例

创建两个 shell 脚本文件。

test1.sh 代码如下：

```shell
url="http://www.baidu.com"
```

test2.sh 代码如下：

```shell
#!/bin/bash
# author:菜鸟教程
# url:www.runoob.com

#使用 . 号来引用test1.sh 文件
. ./test1.sh

# 或者使用以下包含文件代码
# source ./test1.sh

echo "百度：$url"
```

接下来，我们为 test2.sh 添加可执行权限并执行：

```shell
$ chmod +x test2.sh 
$ ./test2.sh 
百度：http://www.baidu.com
```

> **注：**被包含的文件 test1.sh 不需要可执行权限。



