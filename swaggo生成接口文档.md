## swaggo生成接口文档

[swagger更多知识](https://github.com/swaggo/gin-swagger)

### 下载第三方包

`go get -u github.com/swaggo/swag/cmd/swag`

### 安装swag

`go install github.com/swaggo/swag/cmd/swag@latest `

### 初始化

在项目的根目录(与main函数同级)下执行指令

`swag init`

后会在项目根目录下生成一个docs文件夹里面有 docs.go、swagger.json、swagger.yaml三个文件

### 引入gin-swagger渲染

` go get -u github.com/swaggo/gin-swagger `

`go get -u github.com/swaggo/files`

### 注册swagger的路由

`rOUTE.GET("/swagger/index.html",gin-swagger.WrapHandler(swaggerFiles.Handler))`

gin-swagger还提供了gin-swagger.DisablingWrapHandler通过设置环境变量来控制swagger的开关

```go
router.GET("/swagger/*any", gin-swagger.DisablingWrapHandler(swaggerFiles.Handler, "NAME_OF_ENV_VARIABLE"))
```

## 注释规则

### controllers层的注释

```go

// PostVoteHandler 帖子投票的接口
// @Summary 用于给帖子投票1
// @Description 给帖子投票的接口2
// @Tags 帖子相关的接口
// @Accept application/json
// @Produce application/json
// @Param Authorization header string false "Bearer 用户令牌"
// @Param object query models.VoteData false "查询参数"
// @Security ApiKeyAuth
// @Success 200 {object} controllers.ResponseData
// @Router /vote [post]
func PostVoteHandler(c *gin.Context) {}
```

效果如下

![image-20220903213100804](C:/Users/88463/AppData/Roaming/Typora/typora-user-images/image-20220903213100804.png)

### main函数的注释

```go
// @title bluebell这是我写的第一个项目
// @version 1.0
// @description 这是我李某人些的第一个web后端，我去，忒激动了
// @termsOfService http://swagger.io/terms/

// @contact.name 红木资本
// @contact.url http://www.swagger.io/support
// @contact.email sncot123@aliyun.com

// @license.name Apache 2.0
// @license.url http://www.apache.org/licenses/LICENSE-2.0.html

// @host 这里写接口服务的host
// @BasePath 这里写base path
func main() {}
```

效果如下

![image-20220903213435592](C:/Users/88463/AppData/Roaming/Typora/typora-user-images/image-20220903213435592.png)



### 结构体的注释

```go
// ResponseData 响应信息
type ResponseData struct {
	Code ResCode     `json:"code"`           //业务响应状态码
	Msg  interface{} `json:"msg,omitempty"`  //提示信息
	Data interface{} `json:"data,omitempty"` //数据
}
```

效果如下

![image-20220903213718796](C:/Users/88463/AppData/Roaming/Typora/typora-user-images/image-20220903213718796.png)

