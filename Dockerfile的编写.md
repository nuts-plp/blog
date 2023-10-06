## Dockerfile的编写

##### 1、Dockerfile的指令

**FROM**	

- 指示基础镜像

- 用法  `FROM 镜像名称:镜像tag`
- 示例 `FROM golang:1.20.1`  

**WORKDIR**

- 用法类似于linux中的cd
- 示例  `WORKDIR /user `    `WORKDIR /id` 此时便是在/user/id路径下

**ADD**

- 拷贝   

- 用法 `COPY src源文件 dest容器`

- 示例  `COPY main.go .` 把本地当前路径下的main.go拷贝到容器中的当前路径

  ***ADD精彩用法：*** 

  	- ADD可以解压缩文件并添加到镜像中
  	- ADD可以从url拷贝文件到镜像中
  	- 示例 `ADD http://example.com/big.tar.gz /usr/local/test`  此命令会从远端下载并解压文件到/test路径下
  	- ***注意***  精彩用法在实践中应避免使用，因为会创建更多层镜像导致镜像文件过大   应使用COPY  以及wget   curl  tar等命令代替

**COPY**

- 用法与ADD相似
- 不同点 ： COPY可以在多层构建中把上层镜像构建的产物拷贝到本层容器中 详细见下文示例

**RUN**

- 构建镜像，每一个RUN均会构建一层镜像，在实际应用中尽可能的少RUN多&&
- 示例 `RUN go mod init test && go mod tidy && go build -o hello main.go  `

**CMD**

- docker run 时才会运行的指令

**ARG**

- 定义变量的指令，唯一可以在FROM之前的指令
- 格式 `ARG key=value`  
- 示例      注意：大写的意义在于与参数做区分

```shell
ARG VERSION=latest
FROM golang:$VERSION    
```



**AS**

- 别名
- 示例 `FROM golang:latest AS builder`

**LABEL**

- 为镜像添加元数据
- 格式 `LABLE key1=value1 key2=value2`
- 示例 ` LABLE version="1.0" maintainer=nuts_plp@126.com` 

**EXPOSE**

- 用来在运行时通知容器监听指定的端口
- 用法 `EXPOSE port/proto`
- 示例 `EXPOSE 80/tcp`  没有指定协议时默认为tcp

**注意** ：RUN 指令有shell和exec两种格式  语法如下：

```shell
#方式一
RUN <command>
#方式二
RUN ["executable","param1","param2"]
```

CMD除了由以上两种格式外还有参数这一格式 ,这一格式主要作为EMTRYPOINT的指令的默认参数     格式如下

```shell
CMD ["param1","param2"]
```

***示例***

文件夹/tmp/test下有{Dockerfile,main.go,go.mod}三个文件

Dockerfile文件内容如下

```shell
ARG VERSION=1.20.1	#定义变量
FROM golang:$VERSION AS builder
WORKDIR /workfile1
COPY . .   #将本地/tmp/test文件夹下的文件拷贝到容器/workfile路径下
RUN go build -o hello main.go

FROM apline
WORKDIR /workfile2
COPY --from=builder /workfile1/hello /workfile2/hello  # 把上一层镜像中的文件拷贝到这一层
EXPOSE 8090
CMD["./hello"]

```

main.go文件内容如下

```shell
packeage main
import(
	"net"
	"net/http"
)
func main(){
	coon,_:=net.Listen("tcp",":8090")
	http.HandleFunc("/",func(w http.ResponseWriter,r http.Request){
		w.Write([]byte("hi!"))
	})
	http.Serve(coon,nil)
}
```

go.mod文件为在/test目录下执行go mod init test生成的空白go module文件

在当前路径下执行`docker build -t hello:1 .`生成以1为tag名称为hello的镜像

***注意***   两个特殊的镜像 

- 镜像一 alpine 一个轻型的linux发行版，非商业组织维护，提供了自己的包管理工具apk，大小仅为5MB
- 镜像二 scratch 完全空白的镜像

