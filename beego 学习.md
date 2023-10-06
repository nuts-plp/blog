beego 学习

- bee的安装 `go get -u github.com/beego/bee`
- beego的安装 `go get github.com/beego/beego`

```golang
package main


func main(){
    beego.run
}
```







## beego模板

beego使用了go语言·默认的模板引擎

- 指定模板 ： c.TPLname="index.tpl"
  	- 默认支持tpl和html
  	- beego。AddTemplateExt("后缀名")    如果不设置该参数，那么默认回去模板目录Colltroller/<方法名>.tpl 查找，例如上面的方法会找 maincontroller/get.tpl
- 数据渲染：c.Data["变量名"]=“变量值”
  	- 前端数据获取：{{.变量名}}
- 设置模板路径：

	- beego默认的路径是view，自己可以设置
 - 重新设置模板路径：
   - beego.SetViewPath("testview")
   - 配置文件中设置： viewpath= "myview"

## 渲染数据

### 结构体渲染

```golang
//view层
<div>
username:{{.user.UserName}}<br>
userage:{{.user.UserAge}}<br>
</div>

//model层
type User struct{
    UserName string
    UserAge int
}

//controllers层
func(c*MainController)Get(){
    user:=User{"潘丽萍"，18}
    c.Data["user"]=user
}
```

### 数组渲染

普通数组

```go
//view层							//view层也可以   {{range $i,$v:=arrays}}
<div>							//				{{$i}}->{{$v}}<br>
{{range .arrays}}				 //				{{end}}
	{{.}}						//
{{end}}
</div>

//controllers层
func(c*MainController)Get(){
    arrays:=[3]{1,2,3}
    c.Data["arrays"]=arrays
}
```

结构体数组

```go
//view
<div>
 <hr>
    {{ range .array_struct}}
    {{.UserName}}->{{.Age}}<br>
    {{end}}

    <hr>
    {{ range $i,$v:=.array_struct}}
    {{$i}}->{{$v.UserName}}-->{{$v.Age}}<br>
    {{end}}

</div>
//models层
type User struct {
	UserName string
	Age      int
}

//controllers层
func(c*MainController)Get{
    arrayStruct := [3]models.User{{"潘丽萍", 18}, {"于欢", 21}, {"樊雪姨", 23}}
    u.Data["array_struct"] = arrayStruct
}
```

思考：根据数组和结构体数组，猜测map和结构体map、切片和切片结构体的使用



## 引入静态文件

- beego默认注册了static目录为静态处理的目录

- 如果要更换static的目录

  -  在main.go的beego.Run前面加：Beego.SetStaticPath("/static")="front"

    - 路径对应文件夹名

  - 设置多个文件处理目录，在main.go的beego.Run前面加

    - beego.SetStaticPath("/down1","download1")
    - beego.SetStaticPath("/down2",download2)

    http://localhost:8080/down1/123.txt则会请求download1目录下的123.txt文件

## 前端传数据到后端

Get

方式一

```go
//传递参数方式一
http://localhost/8080/para?name=潘丽萍
//router层
beego.Router("/para",&controller.ParaController{})
//controller层
type ParaController struct{
    beego.Controller
}
func(p*ParaController)Get{
    //获取参数方式一
    name:=p.GetString("name")
    //获取参数方式二
    name:=p.Input().Get("name")
    fmt.Println(name)
    
}
```

方式二

```go
//传递参数方式一
http://localhost/8080/para/潘丽萍
//router层
beego.Router("/para/？:name",&controller.ParaController{})
//controller层
type ParaController struct{
    beego.Controller
}
func(p*ParaController)Get{
    //获取参数方式一
    name:=p.GetString(":name")
    //获取参数方式二
    name:=p.Ctx.Input.Param(":name")
    fmt.Println(name)
    
}
```



post

```go
//首先访问至表单页
http://localhost/8080/tform
//view层的tform.html
<form action="/params" method="post">  //注意此处的动作可以把表单以post动作传送至"/params"路由下，也可以选择其他的，这里选择了/params
名字：<input type="text" name="username" ><br>
  年龄：<input type="text" name="userage"><br>
  价格：<input type="text" name="price"><br>
  单身：<input type="radio" name="single" value="true">yes&nbsp;&nbsp;<input type="radio" name="single" value="false">no<br>
  <input type="submit" value="提交">
</form>

//router层
beego.Router("/params",&controller.ParaController{})
//controller层
type ParasController struct{
    beego.Controller
}
func(p*ParaController)Get{
    p.TplName="tform.html"
}
//接着创建表单提交路由，由于上文选择的依然是/params路由  


//view层
<div>
   login successfully!!!!!!!! <br>
    {{.name}}<br>
    {{.age}}<br>
    {{.price}}<br>
    {{.single}}
<div>
//router层
beego.Router("/params",&controller.ParaController{})
//controller 层
type ParasController struct{
    beego.Controller
}

func(p*ParaController)Post{
    name:=p.GetString("name")
    age,_:=p.GetInt("age")
    price,_:=p.GetFloat("price")
    single,_:=p.GetBool("single")
    p.Data["name"] = name
	p.Data["age"] = age
	p.Data["price"] = price
	p.Data["single"] = single
    fmt.Println(name, "->", age, "->", price, "->", single)
    p.TplName="success.html"
}
```

渲染数据到结构体

情况一  结构体的变量名与前端form中name属性值相同

```go

//view层
<form action="/paramsy" method="post">
  名字：<input type="text" name="UserName" ><br>
  年龄：<input type="text" name="UserAge"><br>
  价格：<input type="text" name="Price"><br>
  单身：<input type="radio" name="Single" value="true">yes&nbsp;&nbsp;<input type="radio" name="single" value="false">no<br>
  <input type="submit" value="提交">

//router层
beego.Router("/paramsy", &controllers.TFormController{})
//controller层

type TFormController struct {
	beego.Controller
}

func (t *TFormController) Get() {
	t.TplName = "tform.html"

}
func (t *TFormController) Post() {
	var user models.TForm
	err := t.ParseForm(&user)
	if nil != err {
		return
	}
}
//model层
type TForm struct {
	UserName string
	UserAge  int
	Price    float64
	Single   bool
}



```

情况二   结构体的变量名与前端form的name属性值不同  使用orm

```go
//view层
<form action="/paramsy" method="post">
  名字：<input type="text" name="UserName" ><br>
  年龄：<input type="text" name="UserAge"><br>
  价格：<input type="text" name="Price"><br>
  单身：<input type="radio" name="Single" value="true">yes&nbsp;&nbsp;<input type="radio" name="single" value="false">no<br>
  <input type="submit" value="提交">

//router层
beego.Router("/paramsy", &controllers.TFormController{})
//controller层

type TFormController struct {
	beego.Controller
}

func (t *TFormController) Get() {
	t.TplName = "tform.html"

}
func (t *TFormController) Post() {
	var user models.TForm
	err := t.ParseForm(&user)
	if nil != err {
		return
	}
}
//model层
type TForm struct {
	UserName string
	UserAge  int
	Price    float64
	Single   bool
}

```

## beego中的flash

- 这不是adobe的产品
- flash有三个级别的对象
  - Notice 提示信息
  - Warning 警告信息
  - Error 错误信息
- 模板中读取数据：
  - {{.flash.error}}  错误
  - {{.flash.warning}} 警告
  - {{.flash.notice}} 成功

```go
//view 层
 
//登陆页面
<form action="/flash" method="post">
    name: <input type="text" name="username"><br>
    password: <input type="password" name="pwd"><br>
    <input type="submit" value="submit">

</form>
//警告页面
<body>
{{.flash.warning}}
</body>

//错误页面
<body>
{{.flash.error}}
</body>

// 成功页面  notice
<body>

{{.flash.notice}}
</body>


//router层
beego.Router("/flash", &controllers.FlashController{})

//controller层

type FlashController struct {
	beego.Controller
}

func (f *FlashController) Get() {
	flash := beego.ReadFromRequest(&f.Controller)
	notice := flash.Data["notice"]
	err := flash.Data["error"]
	warning := flash.Data["warning"]
	if len(warning) != 0 {
		f.TplName = "warningflash.html"
	} else if len(err) != 0 {
		f.TplName = "errorflash.html"
	} else if len(notice) != 0 {
		f.TplName = "noticeflash.html"
	} else {
		f.TplName = "flash.html"
	}

}

func (f *FlashController) Post() {

	//创建一个flash对象
	flash := beego.NewFlash()
	name := f.GetString("username")
	pwd := f.GetString("pwd")
	if name == "" {
		fmt.Println("用户名不能为空")
		flash.Warning("用户名不能为空！！！")
		flash.Store(&f.Controller)
		f.Redirect("/flash", 302) //重定向
	} else if pwd != "123456" {
		fmt.Println("密码错误")
		flash.Error("密码错误！！！")
		flash.Store(&f.Controller)
		f.Redirect("/flash", 302) //重定向
	} else {
		fmt.Println("登陆成功")
		flash.Notice("登陆成功！！！")
		flash.Store(&f.Controller)
		f.Redirect("/flash", 302) //重定向
	}

}
//整个逻辑就是先进入/flash路由，此时请求体本身的数据没有notice、warning、error三个数据，判断过后进入登陆页面flash.html
//当对登陆页面输入相关数据后在后台会对输入的数据进行处理，并将处理保存至controller中，并重定向到/flash路由，此时请求中带了flash数据
//根据flash数据显示对应的页面
```



### 路由

#### 固定路由

```go
beego.Router("/upload",&controllers.UploadController{})  //固定路由
```





#### 正则路由

```go
beego.Router("/upload/?:id:int",&controllers.UploadController{})  //upload后面匹配的只能为数字   当upload路由中有？时，url的upload后有无参数都可以匹配成功，但是没有？时，只能匹配有参数的url
beego.Router("/upload/?:id[0-9]+",&controllers.UploadController{})  //  :id[\d]+   或者 :id:int
```

获取参数

```go
this.Ctx.Input.Param(":id")
//或者
this.GetString(":id")
```





#### 自动路由

```go
beego.AutoRouter(&controllers,UploadController{})  
```

注意：使用自动路由时应注意controller层的结构体命名应符合” “+Controller格式，例如此例UploadController

格式：  控制器名/方法名/后面都是参数......

#### 自定义路由

```go
beego.Router("/upload",&controller.UploadController{},"get:Login;post:Regist")
beego.Router("/upload",&controller.UploadController{},"get，post:login")
beego.Router("/upload",&controller.UploadController{},"*:Login")
```

注意：后面的应用分号隔开       格式：  method：方法名



### XSRF跨站请求伪造



开启XSRF

方式一

```go
beego.BConfig.EnableXSRF = tru
```





### 文件上传

```go
//view层
<form action="/upload" method="post" enctype="multipart/form-data">
<input type="file" name="upload_file"><br>
<input type="submit" value="submit">
</form>

//router层
beego.Router("/upload",&controllers.UploadController{})

//controller层
type UploadController struct{
    beego.Controller
}
func(u*UploadController)Get(){
    u.TplName="upload.html"
}
func(u*UploadController)Post(){
    file,header,err:=u.GetFile("upload_file")  //获取文件变量
    if nil!=err{
        return
    }
    filename:=header.Filename   //获取文件名称
    defer file.Close()		//关闭文件
    unix：=time.Now.Unix()
    formatInt:=strconv.FormatInt(unix,10)  //将时间戳转化为十进制字符串
    u.SaveToFile("upload_file","static/upload/"+formatInt+"-"+filename)
    u.TplName="success.html"
}
```

注意：文件上传一般是存储在内存里，可以设置缓存中内存的大小

```go
beego.BConfig.MaxMemary = 1<<28    //单位B   此处时64
```



### view模块



- #### go统一使用{{ 和  }}作为左右标签，可以修改为其他的符号

  - beego.BConfig.WebConfig.TemplateLeft="xxx"
  - beego.BConfig.WebConfig.TemplateRight="xxx"

- 基本语法

  - 使用`.`来访问当前位置的上下文
  - 使用·`$`来引用当前模板根级的上下文
  - 使用`$.`来引用模板中根级的上下文

- 支持go语言的符号，这里只是符号的支持

  - 字符串:`{{"潘丽萍"}}`
  - 原始字符串：{{`潘丽萍`}}
  - 字节类型：{{ 'a' }}    -->97
  - nil:{{ print nil }}     {{   nil   }}只有nil会报错； nil is not  a command

- pipeline：可以是上下文的变量输出，也可以是函数通过管道传递的返回值

  - {{ .Age }}  是上下文的变量输出，是个pipeline
  - {{ “00000”| len}} 势函数通过管道传递的返回值，是个pipeline
  - pipeline被认为是空的情况，当pipeline的值等于
    - false 或 0
    - nil 的指针或 interface
    - 长度为 0 的array、slice、map、string

- if 的使用

```go
{{if ge .age 18}}
年龄大于等于18
{{else}}
年龄小于18
{{end}}
```

注意：if 模板语法可以嵌套，但是注意对应{{end}}，是代码快的结束

- with  伴随

```go
{{with .stuc}}  //stuc对应结构体名称
{{.Age}}		//对应结构体内的字段  避免 {{.stuc.Age}} 方式的使用
{{.UserName}}
{{end}}
```

- range  和  with 中else的用法

```go
{{range .array}}
{{.}}
{{else}}
如果array数组为空数组，这句话就出现
{{end}}


{{with .stuc}}
{{.Age}}
{{else}}
如果stuc对应的是一个空结构体，这句话就打印出
{{end}}
```



- 嵌入式模板

  

  在一个模板中加入`{{template "template2.html" .}}` 便把template2.html嵌入到该模板中了 , `.`表示把该模板的上下文环境引入到template2.html中了，可以不使用

- define 自定义模板

  ```go
  {{define "mytemplate"}}
  <ul>
  <li>{{.name}}</li>
  </ul>
  {{end}}
  
  
  {{template "mytemplate" .}}  //引入自定义模板 并引入上下文环境
  
  ```

- printf

  - {{ printf"num is %s ,%d" (printf"%d-%d" 1 2) 3}}

- and

  - 会逐一判断每个参数，将返回第一个为空的参数，否则返回最后一个非空参数
  - {{and .X .Y .Z}}
  - 只要参数中有一个为空，则整体为空，如果都不为空，返回最后一个

- or

  - 会逐一判断每个参数，将返回第一个非空参数，否则返回最后一个参数
  - {{or .X .Y .Z}}
  - 只要有一个不为空的，则返回第一个不为空的，否则返回空

  #### call  可以调用函数，并传入参数

  - {{call .func_test .Args1 .Args}}
  - 调用的函数需要返回一个或者两个值，返回两个值时，第二个值用于返回error类型的错误，返回的错误不等于nil时，执行将终止

  无参

  ```go
  //后端
  
  
  func (t *Template2Controller) Get() {
  	t.Data["fun1"] = Test1
  	t.TplName = "template2_fun.html"
  }
  
  func Test1() string {
  	return "潘丽萍！"
  }
  
  //模板
  {{call .fun1}}<br>
  ```

  有参

  ```go
  //后端
  func (t *Template2Controller) Get() {
  	t.Data["fun2"] = Test2
  	t.TplName = "template2_fun.html"
  }
  func Test2(name string) string {
  	return fmt.Sprintf("%s,我有点想你了", name)
  }
  //模板
  {{call .fun2 "潘丽萍"}}
  ```

- index  读取指定类型对应下标的值

  - 支持map、slice、array、string
  - this.Data["maps"] = map[string]string{"name":Beego}
  - {{index .maps "name"}}

  ```go
  //后端
  func (t *Template2Controller) Get() {
  	t.Data["maps"] = map[string]string{"name": "潘丽萍"}
  	t.Data["array"] = []int{1, 2, 3, 4}
  	t.Data["str"] = "afasdfsd"
  	t.TplName = "template2_fun.html"
  }
  //模板
  {{index .maps "name"}}<br>
  {{index .array  0}}<br>
  {{index .str 3}}<br>
  
  ```

  

- not 取反

  ```go
  {{not 0}}<br>
  ```

  

  

- urlquery

```go
{{urlquery "https:www.baidu.com"}}
```

- https%3Awww.baidu.com     :被解析为十六进制字节码

### orm的使用

- 下载

```go
go get github.com/astaxie/beego/orm
go get github.com/go-sql-driver/mysql
```

- 连接数据库
  - `orm.RegisterDriver("mysql",orm.DRMySQL)`
  - `orm.RegisterDataBase("default","mysql","用户名：密码@tcp(IP:端口号)/数据库/charset=utf8",30)`
    - 设置最大空闲连接（可选）`orm.SetMaxIdleConns("default",30)`
    - 设置最大数据库连接（可选）`orm.SetMaxOpenConns("default",30)`
  - 注册模型：在init函数中：`orm.RegisterModel(new(Artical))`只有注册了模型才能使用







