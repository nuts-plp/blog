## docker-compose.yaml文件的编写

后缀以.ymal或者.yml均可

##### 1、安装docker-compose

[官网安装教程](https://docs.docker.com/compose/install/linux/)

方式一 ` yum install docker-compose-plugin` or ` apt-get install docker-compose-plugin`

方式二 `pip install docker-compose`

验证安装 `docker compose version`



***注意***： docker-compose与docker compose的区别 

- 版本区别 docker-compose为v1版本，由python实现，docker compose为v2版本，由go实现
- 指令不同：v1版本的指令为docker-compose，v2版本的指令为docker compose