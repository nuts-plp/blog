## AIR实现热加载配置文件

程序运行中修改配置文件，程序重新加载配置文件

**air特性**

- 彩色日志输出
- 自定义或构建二进制命令
- 支持忽略子目录
- 启动后支持监听新目录
- 更好的构建过程

#### air的安装：

​	 `go get -u github.com/cosmtrek/air`

在配置好go环境的情况下，在任意终端执行上述命令，下载air。

#### 生成可执行文件

进入air安装路径，我的是在``D:\GO_workspace\pkg\mod\github.com\cosmtrek\air@v1.40.4`(gopath路径下)

打开终端进入此air目录下执行`go build .`会产生一个`air.exe`文件

#### 配置环境变量

把该文件路径加入到环境变量中

#### 设置配置文件

在go项目的根目录中配置`.air.conf`文件,如果没有就自己创建一个，名字即是`.air.conf`

把以下内容复制进`.air.conf`文件中

```shell
# [Air](https://github.com/cosmtrek/air) TOML 格式的配置文件
 
# 工作目录
# 使用 . 或绝对路径，请注意 `tmp_dir` 目录必须在 `root` 目录下
root = "."
tmp_dir = "tmp"
 
[build]
# 只需要写你平常编译使用的shell命令。你也可以使用 `make`
# Windows平台示例: cmd = "go build -o ./tmp/main.exe ."
cmd = "go build -o ./tmp/main.exe ."
# 由`cmd`命令得到的二进制文件名
# Windows平台示例：bin = "tmp/main.exe"
bin = "tmp/main.exe"
# 自定义执行程序的命令，可以添加额外的编译标识例如添加 GIN_MODE=release
# Windows平台示例：full_bin = "./tmp/main.exe"
# Linux平台示例：full_bin = "APP_ENV=dev APP_USER=air ./tmp/main.exe"
full_bin = "./tmp/main.exe"
# 监听以下文件扩展名的文件.
include_ext = ["go", "tpl", "tmpl", "html"]
# 忽略这些文件扩展名或目录
exclude_dir = ["assets", "tmp", "vendor", "frontend/node_modules"]
# 监听以下指定目录的文件
include_dir = []
# 排除以下文件
exclude_file = []
# 如果文件更改过于频繁，则没有必要在每次更改时都触发构建。可以设置触发构建的延迟时间
delay = 1000 # ms
# 发生构建错误时，停止运行旧的二进制文件。
stop_on_error = true
# air的日志文件名，该日志文件放置在你的`tmp_dir`中
log = "air_errors.log"
 
[log]
# 显示日志时间
time = true
 
[color]
# 自定义每个部分显示的颜色。如果找不到颜色，使用原始的应用程序日志。
main = "magenta"
watcher = "cyan"
build = "yellow"
runner = "green"
 
[misc]
# 退出时删除tmp目录
clean_on_exit = true
```

#### 运行air

进入go项目的根目录，打开终端执行air命令

