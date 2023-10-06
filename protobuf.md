## protobuf

#### 第一步 安装proto编译器

`github上搜索protobuf 下载对应版本`  **`https://github.com/protocolbuffers/protobuf/releases`**

#### 第二步 下载protoc-gen-go插件

`go get github.com/golang/protobuf/protoc-gen-go`



#### 第三步 下载protoc-gen-grpc插件

`go get google.golang.org/grpc`



#### 第四步 下载protoc-gen-micro

`go get github.com/micro/micro/v3/cmd/protoc-gen-micro`



#### 第五步 把proto文件编译成grpc和micro对应的go文件

`编译成grpc对应的go文件` 

```go
protoc --go-grpc_out=./ *.proto
```

`编译成micro对应的go文件`

```go
protoc --micro_out=./ *.proto
```

`编译成普通对应的go文件`

```go
protoc --go_out=./ *.proto
```



