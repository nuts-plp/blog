# day-05

## 结构体(struct)

### 结构体标签tag

`Tag`是结构体的元信息，可以在运行的时候通过反射的机制读取出来。 `Tag`在结构体字段的后方定义，由一对**反引号**包裹起来，具体的格式如下：

\`key1:"value1"  key2:value2\`不同键值对之间用空格隔开

**注意：**为结构体编写tag时，必须严格遵守键值对的规则。结构体标签的解析代码的容错率很差，一旦格式写错，编译和运行时都不会报错，通过反射也无法正常取值。例如不要在key和value之间加空格。

### 匿名字段结构体

匿名字段结构体只能通过 `.字段类型` 的方式调用，且在匿名字段结构体内不能有同数据类型的字段

![image-20220607142718168](C:\Users\88463\AppData\Roaming\Typora\typora-user-images\image-20220607142718168.png)

![image-20220607142922905](C:\Users\88463\AppData\Roaming\Typora\typora-user-images\image-20220607142922905.png)



### 嵌套结构体

**结构体嵌套时，内部的结构体字段匿名时**

![image-20220607142613446](C:\Users\88463\AppData\Roaming\Typora\typora-user-images\image-20220607142613446.png)

![image-20220607142632680](C:\Users\88463\AppData\Roaming\Typora\typora-user-images\image-20220607142632680.png)

**内部结构体字段不匿名时**

![image-20220607144933027](C:\Users\88463\AppData\Roaming\Typora\typora-user-images\image-20220607144933027.png)



![image-20220607145001028](C:\Users\88463\AppData\Roaming\Typora\typora-user-images\image-20220607145001028.png)



![image-20220607145014895](C:\Users\88463\AppData\Roaming\Typora\typora-user-images\image-20220607145014895.png)

#### 结构体实现继承

匿名结构体的另类使用

![image-20220607145852084](C:\Users\88463\AppData\Roaming\Typora\typora-user-images\image-20220607145852084.png)

![image-20220607145908521](C:\Users\88463\AppData\Roaming\Typora\typora-user-images\image-20220607145908521.png)













### 序列化和反序列化



### json.Marshal

序列化：使go中的结构体转化为json格式的字符串

![image-20220611164946426](C:\Users\88463\Desktop\笔记\image-20220611164946426.png)



### json.Unmarshal

反序列化：使json格式的数据转化为go语言结构体的形式

![image-20220611165008294](C:\Users\88463\Desktop\笔记\image-20220611165008294.png)
