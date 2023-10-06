### go连接etcd

```go
import (
	"encoding/json"
	"fmt"
	"go.etcd.io/etcd/clientv3"
	"golang.org/x/net/context"
	"time"
)

type LogEntry struct {
	Path  string `json:path`
	Topic string `json:topic`
}

func main() {
	client, err := clientv3.New(clientv3.Config{
		Endpoints:   []string{"127.0.0.1:2379"},
		DialTimeout: time.Second * 5,
	})
	if err != nil {
		fmt.Println("connected to etcd failed")
		return
	}
	fmt.Println("connected to etcd successed")
	ctx, cancel := context.WithTimeout(context.Background(), time.Second)

	//PUT
	str := `[{"path":"d:/K_V/tmp/etcd.log","topic":"etcd_log"},{"path":"d:/K_V/tmp/kafka.log","topic":"kafka_log"}]
`
	_, err = client.Put(ctx, "xxx", str)
	cancel()
	if err != nil {
		fmt.Println("put data failed")
		return
	}

	//GET
	ctx, cancel = context.WithTimeout(context.Background(), time.Second)
	resp, err := client.Get(ctx, "xxx")
	cancel()
	if err != nil {
		fmt.Println("get data failed")
		return
	}
	var logEntryConf []*LogEntry
	for _, event := range resp.Kvs {
		fmt.Printf("%s:%v\n", event.Key, string(event.Value))
		err = json.Unmarshal(event.Value, &logEntryConf)
		if err != nil {
			fmt.Println("---------------------")
			return
		}
	}
	fmt.Printf("%v", logEntryConf)
}

```



#### etcd

