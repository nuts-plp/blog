## 小知识



## 1、 配置静态ip

- 进入linux系统

- 编辑/etc/sysconfig/network-scripts/ifcfg-ens33文件
- 添加以及修改几个内容

```shell
# 添加的内容
IPADDR=192.168.222.102  #静态ip
NETMASK=255.255.255.0  #子网掩码
GATEWAY=192.168.222.1   #网关 一般为该网段的第一个
DNS1=8.8.8.8    #可以有多个

# 修改的内容
BOOTPROTO=static      #即设置为静态
```

- 重置网络

```shell
systemctl restart network
```



## 2、开放端口

- 列出已经开放的端口

```shell
firewall-cmd --list-all
```

- 添加端口

```shell
firewall-cmd --add-port=8080/tcp --permanent
```

- 重启防火墙

```shell
firewall-cmd --reload
```

## 3、配置环境变量

- 修改/etc/profile

```shell
export jdk=/usr/local/jdk/bin
export PATH=$PATH:$jdk
```

- j加载修改

```shel
source /etc/profile
```

 

方法二

- 直接把该bin中文件加入到/usr/local/bin中

## 4、scp格式

```shell
scp user@host:/file  user@host:/file
```

