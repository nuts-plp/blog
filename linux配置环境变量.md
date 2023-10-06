linux配置环境变量

## 1、方法一

编辑 /etc/profile文件，加入你所需要加入的变量，如下例

~~~bash
export GO="/usr/local/go/bin"
export PATH="$PATH:$GO"
~~~

保存退出并重新加载文件

~~~bash
source /etc/profile
~~~





