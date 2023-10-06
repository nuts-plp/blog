#### go学习中的小知识

##### 结构体

###### 内存对齐

在定义结构体时，把类型相同的字段相邻定义，使用内存空间更小

示例

```go
type v1 struct{
    id int8
    name string
    age int8
}
type v2 struct{
    id int8
    age int8
    name string
}
func TestStruct(t *testing.T){
    a1:=v1{
    id:1,
    name:"潘丽萍",
    age:18,
}
a2:=v2{
    id:1,
    name:"潘丽萍",
    age:18,
}
    fmt.Println(unsafe.SizeOf(a1),unsafe.SizeOf(a2))
}
```

##### validator  go自带的参数校验

tag标签 binding 

示例

```go
type c struct{
    id int64`binding:"required"`
    name string`binding:"required"`
}
//这样才绑定数据时，两个字段都是必须的
```

##### 前后端数据传输问题

###### 数字问题

前端js所能接受的数字范围是`-1<<53至1<<53`,而后端的int64的范围是`-1<<63至1<<63`,所以前后端在以json格式传输数据时可能会导致数据的失真

解决方法：在后端向前端传输数值时，先把数值转化成字符串，在序列化为json格式传到前端

即在结构体的json tag中加入string,这样在序列化时或自动把数值转化成字符串

```go
type user struct{
    ID int64`json:"id,string"`
}
```



##### 类型断言



