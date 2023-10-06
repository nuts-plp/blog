# rabbitmq的介绍、容器安装及使用

#### 1、rabbitmq的介绍

#####什么是AMQP 和 JMS？

**AMQP**：即Advanced Message Queuing Protocol，是一个应用层标准高级消息队列协议，提供统一消息服务。是应用层协议的一个开放标准，为面向消息的中间件设计。基于此协议的客户端与消息中间件可传递消息，并不受客户端/中间件不同产品，不同的开发语言等条件的限制。**Erlang中的实现有RabbitMQ**等。

**JMS**：即Java消息服务（Java Message Service）**应用程序接口**，由sun公司提出，并且sun公司定义好了接口。包括create、send、recieve。只要想使用它，就得实现它定义的接口。 消息服务是一个与具体平台无关的API，绝大多数MOM提供商都对JMS提供支持。不好的地方是语言层面的限制，只能为JAVA，这其实稍微有点和微服务的观点相违背。要求语言只能是JAVA，而不能是py等。

##### 常见的MQ产品

ActiveMQ：基于JMS，Apache

RocketMQ：（Rocket，火箭）阿里巴巴的产品，基于JMS，目前由Apache基于会维护

Kafka：分布式消息系统，亮点：吞吐量超级高，没秒中数十万的并发。

RabbitMQ：（Rabbit，兔子）由erlang语言开发，基于AMQP协议，在erlang语言特性的加持下，RabbitMQ稳定性要比其他的MQ产品好一些，而且erlang语言本身是面向高并发的编程的语言，所以RabbitMQ速度也非常快。且它基于AMQP协议，对分布式、微服务更友好。

##### rabbitmq的几个概念

- virtual host
- exchange（交换机）
- routing key
- binding key
- 队列
- channel

#### 2、docker安装rabbitmq

> 拉取镜像 `docker pull rabbitmq:latest`
>
> 把rabbitmq镜像跑起来 `docker run -d --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:latest`
>
> 进入容器 `docker exec -it rabbitmq /bin/sh`
>
> 运行rabbitmq的web管理器 `rabbitmq-plugins enable rabbitmq-management`
>
> 列出rabbitmq的用户 `rabbitmqctl list_users`
>
> 修改用户密码 `rabbitmqctl change_password guest '950629'`
>
> 使超级用户可以远程登陆（默认guest用户不可以远程登陆）`echo loopback_users = none >> etc/rabbitmq/conf.d/10-defaults.conf`
>
> **注意：** 解决rabbitmq management API return status code 500 报错问题 `echo management_agent.disable_metrics_collector = false > etc/rabbitmq/conf.d/20-management_agent.disable_metrics_collector.conf`
>
> 退出容器 `exit`
>
> 重启容器 `docker restart rabbitmq`
>
> 连接管理页面 `47.92.232.226:15672`
>
> 

#### 3、rabbitmq的五种消息模型

##### 简单模型

[![img](assets/1496926-20190708125542629-2135674001.png)](https://img2018.cnblogs.com/blog/1496926/201907/1496926-20190708125542629-2135674001.png)

P（producer/ publisher）：生产者，发送消息的服务

C（consumer）：消费者，接收消息的服务

红色区域就是MQ中的Queue，可以把它理解成一个邮箱

- 首先信件来了不强求必须马上马去拿
- 其次,它是有最大容量的(受主机和磁盘的限制,是一个缓存区)
- 允许多个消费者监听同一个队列，争抢消息，此时便是worker模型

**示例：**

```go
//生产者
package main

import (
	"log"
	"strconv"
	"time"
	"github.com/streadway/amqp"
)

func main() {
	conn, err := amqp.Dial("amqp://guest:950629@47.92.232.226:5672//test")
	if err != nil {
		log.Fatal("连接失败")
		return
	}
	defer conn.Close()
	//创建channel
	chann, err := conn.Channel()
	if err != nil {
		log.Fatal("创建channel失败")
		return
	}
	defer chann.Close()

	//发送消息前必须声明一个queue，往这个队列中发送消息
	queue, err := chann.QueueDeclare(
		"hello",
		false, // 消息持久化
		false,
		false, //独有的，排他的
		false,
		nil)
	if err != nil {
		log.Fatal("创建队列失败！")
		return
	}
	//均衡策略 收到消费者的确认ack后再消费消息
	err = chann.Qos(
		1,
		0,
		false)
	if err != nil {
		return
	}
	message := "hello 潘丽萍！"
	i := 0
	for {
		err = chann.Publish(
			"",
			queue.Name,
			false,
			false,
			amqp.Publishing{
				//DeliveryMode: amqp.Persistent, //将mq中的消息持久化
				ContentType: "text/plain",
				Body:        []byte(message + strconv.Itoa(i)),
			})
		if err != nil {
			log.Fatal("发送消息失败")
			return
		}
		i++
		time.Sleep(time.Millisecond * 1000)
	}
}
```



```go
//消费者
package main

import (
	"fmt"
	"log"

	"github.com/streadway/amqp"
)

func main() {
	//连接到rabbitmq
	conn, err := amqp.Dial("amqp://guest:950629@47.92.232.226:5672//test")
	if err != nil {
		log.Fatal("连接失败")
		return
	}
	defer conn.Close()
	// 创建一个channel
	chann, err := conn.Channel()
	if err != nil {
		log.Fatal("创建channel失败")
		return
	}
	defer chann.Close()

	//声明要消费的channel
	queue, err := chann.QueueDeclare(
		"hello",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		log.Fatal("声明消费的队列失败")
		return
	}

	delieverMsg, err := chann.Consume(
		queue.Name,
		"",
		true, //自动ACK 默认开启（有丢失消息的风险）
		false,
		false,
		false,
		nil,
	)
	if err != nil {
		log.Fatal("消费消息失败")
		return
	}
	err = chann.Qos(
		1,
		0,
		false)
	if err != nil {
		fmt.Println("设置均衡策略")
		return
	}

	for msg := range delieverMsg {
		fmt.Printf("RECV:%s\n", msg.Body)
		//msg.Ack(false) //手动ack，把上方自动ack设置为false
	}
}
```







##### worker模型

[![img](assets/1496926-20190708125528529-1014015990.png)](https://img2018.cnblogs.com/blog/1496926/201907/1496926-20190708125528529-1014015990.png)

Worker模型中也只有一个工作队列。但它是一种竞争消费模式。可以看到同一个队列我们绑定上了多个消费者

相较于简单模型多增加一个consumer

##### 发布/订阅模型

**注意：** 以下三种模型，定义一个exchange后，可以绑定多个不同键（key）的队列，可以创建多个消费者向此exchange发送消息，exchange根据routing key向绑定的队列发送消息

###### 发布/订阅之direct

[![img](assets/1496926-20201206162141800-646293539.png)](https://img2020.cnblogs.com/blog/1496926/202012/1496926-20201206162141800-646293539.png)

注意交换机的名称为direct 以及添加 特定的routingkey，不支持通配符

**示例：**

```go
//生产者1
package main

import (
	"fmt"
	"strconv"
	"time"

	"github.com/streadway/amqp"
)

func main() {
	conn, err := amqp.Dial("amqp://guest:950629@47.92.232.226:5672//test")
	if err != nil {
		fmt.Println("连接rabbitmq失败")
		return
	}
	defer conn.Close()
	chann, err := conn.Channel()
	if err != nil {
		fmt.Println("创建channel失败")
		fmt.Println(err)
		return
	}
	//声明一个交换机
	err = chann.ExchangeDeclare(
		"direct-ttt",
		"direct",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("创建channel失败")
		fmt.Println(err)
		return
	}
	//声明两个队列并绑定到交换机上
	queue, err := chann.QueueDeclare(
		"direct-log2",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("创建queue失败")
		return
	}
	err = chann.QueueBind(
		queue.Name,
		"error",
		"direct-ttt",
		false,
		nil)
	if err != nil {
		fmt.Println("绑定queue到change logs失败")
		return
	}
	queue1, err := chann.QueueDeclare(
		"direct-log3", //队列名称
		false,         //持久化
		false,         //自动删除
		false,         //唯一的
		false,         //不等待
		nil)           //参数
	if err != nil {
		fmt.Println("创建队列1失败")
		return
	}
	err = chann.QueueBind(
		queue1.Name,
		"info",
		"direct-ttt",
		false,
		nil)
	if err != nil {
		fmt.Println("queue1绑定change失败")
		return
	}
	msg := "你好！小潘"
	i := 0
	for {
		err = chann.Publish(
			"direct-ttt",
			"error",
			false,
			false,
			amqp.Publishing{
				ContentType: "text/plain",
				Body:        []byte(msg + strconv.Itoa(i)),
			})
		if err != nil {
			fmt.Println("生产者生产消息失败")
			return
		}
		i++
		time.Sleep(time.Second)
	}
}
//生产者2
package main

import (
	"fmt"
	"time"

	"github.com/streadway/amqp"
)

func main() {
	conn, err := amqp.Dial("amqp://guest:950629@47.92.232.226//test")
	if err != nil {
		fmt.Println("创建连接失败")
		return
	}
	defer conn.Close()

	chann, err := conn.Channel()
	if err != nil {
		fmt.Println("创建channel失败！")
		return
	}
	err = chann.ExchangeDeclare(
		"direct-ttt",
		"direct",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("声明exchange失败")
		return
	}
	queue1, err := chann.QueueDeclare(
		"direct-log3",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("创建queue1失败")
		return
	}
	err = chann.QueueBind(
		queue1.Name,
		"info",
		"direct-ttt",
		false,
		nil)
	if err != nil {
		fmt.Println("绑定队列失败")
		return
	}
	for {

		err = chann.Publish(
			"direct-ttt",
			"info",
			false,
			false,
			amqp.Publishing{ContentType: "test/plain",
				Body: []byte("小潘小潘")})
		if err != nil {
			fmt.Println("信息生产失败")
			return
		}
		time.Sleep(time.Second)
	}

}

//消费者1
package main

import (
	"fmt"

	"github.com/streadway/amqp"
)

func main() {
	conn, err := amqp.Dial("amqp://guest:950629@47.92.232.226//test")
	if err != nil {
		fmt.Println("创建连接失败")
		return
	}
	defer conn.Close()
	chann, err := conn.Channel()
	if err != nil {
		fmt.Println("创建channel失败")
		return
	}
	err = chann.ExchangeDeclare(
		"direct-ttt",
		"direct",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("exchange声明失败！")
		return
	}
	queue, err := chann.QueueDeclare(
		"direct-log2",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("queue声明失败")
		return
	}
	err = chann.QueueBind(
		queue.Name,
		"error",
		"direct-ttt",
		false,
		nil)
	if err != nil {
		fmt.Println("queue绑定exchange失败")
		return
	}
	msg, err := chann.Consume(
		queue.Name,
		"",
		true,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("消费者消费消息失败")
		return
	}
	for d := range msg {
		fmt.Printf("consumer2 RECV:%s\n", d.Body)
	}
}
//消费者2
package main

import (
	"fmt"

	"github.com/streadway/amqp"
)

func main() {
	conn, err := amqp.Dial("amqp://guest:950629@47.92.232.226:5672//test")
	if err != nil {
		fmt.Println("创建连接失败")
		return
	}
	defer conn.Close()
	chann, err := conn.Channel()
	if err != nil {
		fmt.Println("创建channel失败")
		return
	}
	err = chann.ExchangeDeclare(
		"direct-ttt",
		"direct",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("exchange声明失败！")
		return
	}
	queue, err := chann.QueueDeclare(
		"direct-log3",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("创建队列失败")
		return
	}
	err = chann.QueueBind(
		queue.Name,
		"info",
		"direct-ttt",
		false,
		nil)
	if err != nil {
		fmt.Println("queue绑定exchange失败")
		return
	}
	msg, err := chann.Consume(
		queue.Name,
		"",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("消费者消费消息失败")
		return
	}
	for d := range msg {
		fmt.Printf("consumer1 RECV:%s\n", d.Body)
		_ = d.Ack(false)
	}
}
```

以下两种模式和此模式类似，以下两种模式的示例省略部分代码。

###### 发布/订阅之Topic

[![img](assets/1496926-20201206162144760-1606745935.png)](https://img2020.cnblogs.com/blog/1496926/202012/1496926-20201206162144760-1606745935.png)

和Direct模型相似，不同点：type为topic、并别routingkey支持正则表达式。#匹配一个至多个单词，*匹配一个单词。

**示例：**

```go
//生产者  
package main

import (
	"fmt"
	"strconv"
	"time"

	"github.com/streadway/amqp"
)

func main() {
	conn, err := amqp.Dial("amqp://guest:950629@47.92.232.226:5672//test")
	if err != nil {
		fmt.Println("连接rabbitmq失败")
		return
	}
	defer conn.Close()
	chann, err := conn.Channel()
	if err != nil {
		fmt.Println("创建channel失败")
		fmt.Println(err)
		return
	}
	//声明一个交换机
	err = chann.ExchangeDeclare(
		"lllf",
		"topic",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("创建channel失败")
		fmt.Println(err)
		return
	}
	//声明两个队列并绑定到交换机上
	queue, err := chann.QueueDeclare(
		"topic-log",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("创建queue失败")
		return
	}
	err = chann.QueueBind(
		queue.Name,
		"c.*",
		"lllf",
		false,
		nil)
	if err != nil {
		fmt.Println("绑定queue到change logs失败")
		return
	}
	queue1, err := chann.QueueDeclare(
		"topic-log1", //队列名称
		false,        //持久化
		false,        //自动删除
		false,        //唯一的
		false,        //不等待
		nil)          //参数
	if err != nil {
		fmt.Println("创建队列1失败")
		return
	}
	err = chann.QueueBind(
		queue1.Name,
		"c.g",
		"lllf",
		false,
		nil)
	if err != nil {
		fmt.Println("queue1绑定change失败")
		return
	}
	msg := "你好！小潘"
	i := 0
	for {
		err = chann.Publish(
			"lllf",
			"c.k",
			false,
			false,
			amqp.Publishing{
				ContentType: "text/plain",
				Body:        []byte(msg + strconv.Itoa(i)),
			})
		if err != nil {
			fmt.Println("生产者生产消息失败")
			return
		}
		i++
		time.Sleep(time.Second)
       
	}
}
//消费者
package main

import (
	"fmt"

	"github.com/streadway/amqp"
)

func main() {
    conn, err := amqp.Dial("amqp://guest:950629@47.92.232.226:5672//test")
	if err != nil {
		fmt.Println("创建连接失败")
		return
	}
	defer conn.Close()
	chann, err := conn.Channel()
	if err != nil {
		fmt.Println("创建channel失败")
		return
	}
	err = chann.ExchangeDeclare(
		"lllf",
		"topic",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("exchange声明失败！")
		return
	}
	queue, err := chann.QueueDeclare(
		"topic-log",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("queue声明失败")
		return
	}
	err = chann.QueueBind(
		queue.Name,
		"c.*",
		"lllf",
		false,
		nil)
	if err != nil {
		fmt.Println("queue绑定exchange失败")
		return
	}
	msg, err := chann.Consume(
		queue.Name,
		"",
		true,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("消费者消费消息失败")
		return
	}
	for d := range msg {
		fmt.Printf("consumer2 RECV:%s\n", d.Body)
	}
}
```









###### 发布/订阅之fanout

[![img](assets/1496926-20190708125522017-1931971535.png)](https://img2018.cnblogs.com/blog/1496926/201907/1496926-20190708125522017-1931971535.png)

这个模型的特点就是它在发送消息的时候，并没有指明Rounting Key ， 或者说他指定了Routing Key，但是所有的消费者都知道，大家都能接收到消息,就像听广播。

生产者：在获取channel之后紧接着创建一个交换机，交换机的类型为 fanout 扇出。

注意，fanout模式会忽略路由规则和queue名称，所有绑定到该exchange的队列都会收到消息

**示例：**

```go
//生产者
package main

import (
	"fmt"
	"strconv"
	"time"

	"github.com/streadway/amqp"
)

func main() {
	conn, err := amqp.Dial("amqp://guest:950629@47.92.232.226:5672//test")
	if err != nil {
		fmt.Println("连接rabbitmq失败")
		return
	}
	defer conn.Close()
	chann, err := conn.Channel()
	if err != nil {
		fmt.Println("创建channel失败")
		return
	}
	err = chann.ExchangeDeclare(
		"logs",//交换机名称
		"fanout",//交换机类型
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("创建channel失败")
		return
	}
	queue, err := chann.QueueDeclare(
		"",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("创建queue失败")
		return
	}
	err = chann.QueueBind(
		"",
		"",
		"logs",
		false,
		nil)
	if err != nil {
		fmt.Println("绑定queue到change logs失败")
		return
	}
	msg := "你好！小潘"
	i := 0
	for {
		err = chann.Publish(
			"logs",
			"",
			false,
			false,
			amqp.Publishing{
				ContentType: "text/plain",
				Body:        []byte(msg + strconv.Itoa(i)),
			})
		if err != nil {
			fmt.Println("生产者生产消息失败")
			return
		}
		i++
		time.Sleep(time.Second)
	}
}
```

```go
//消费者  
package main

import (
	"fmt"

	"github.com/streadway/amqp"
)

func main() {
    conn, err := amqp.Dial("amqp://guest:950629@47.92.232.226:5672//test")
	if err != nil {
		fmt.Println("创建连接失败")
		return
	}
	defer conn.Close()
	chann, err := conn.Channel()
	if err != nil {
		fmt.Println("创建channel失败")
		return
	}
	err = chann.ExchangeDeclare(
		"logs",
		"fanout",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("exchange声明失败！")
		return
	}
	queue, err := chann.QueueDeclare(
		"fanout-log",
		false,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("queue声明失败")
		return
	}
	err = chann.QueueBind(
		"",
		"rrrrr",
		"logs",
		false,
		nil)
	if err != nil {
		fmt.Println("queue绑定exchange失败")
		return
	}
	msg, err := chann.Consume(
		"",
		"",
		true,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("消费者消费消息失败")
		return
	}
	for d := range msg {
		fmt.Printf("consumer2 RECV:%s\n", d.Body)
	}
}

```

**注意：** fanout、topic和direct模式的区别在于键（key）的不同

#### 4、rabbitmq的消息持久化

#####  交换机持久化

```go
	err = chann.ExchangeDeclare(
		"direct-ttt",
		"direct",
		true,   //durable 设置为true
		false,
		false,
		false,
		nil)
```



##### 队列持久化

 ```go
 queue, err := chann.QueueDeclare(
 		"direct-log2",
 		true,  //durable设置为true
 		false,
 		false,
 		false,
 		nil
 ```

##### 持久化消息

```go
err = chann.Publish(
			"direct-ttt",
			"error",
			false,
			false,
			amqp.Publishing{
				DeliveryMode: amqp.Persistent,//持久化消息
				ContentType:  "text/plain",
				Body:         []byte(msg + strconv.Itoa(i)),
			})
```



#### 5、rabbitmq的消息确认机制

##### 自动确认 

消费端自动确认的部分代码

```go
msg, err := chann.Consume(
		queue.Name,
		"",
		true,
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("消费者消费消息失败")
		return
	}
	for d := range msg {
		fmt.Printf("consumer2 RECV:%s\n", d.Body)
	}
```



##### 手动确认

贴入消费者cli的部分代码

```go
msg, err := chann.Consume(
		queue.Name,
		"",
		false,  //自动确认设置为false
		false,
		false,
		false,
		nil)
	if err != nil {
		fmt.Println("消费者消费消息失败")
		return
	}
	for d := range msg {
		fmt.Printf("consumer1 RECV:%s\n", d.Body)
		_ = d.Ack(false) // 设置了手动确认
	}
```



