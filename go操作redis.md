# go操作redis

## go连接redis

redis-client库：redigo和go-redis    https://github.com/go-redis/redis

go-redis支持哨兵及集群模式的Redis

**普通连接**

```go
var rdb *redis.Client
//初始化连接
func initClient()(err error){
    rdb = redis.NewClient(&redis.Options{
        Addr:"localhost:6379",
        Password:"",
        DB:0,
        PoolSize:100,//连接池大小
    })
    _,err = rdb.Ping().Result()//测试是否连通
    return err
}
func main(){
    if err:=initClient();err!=nil{
        fmt.Println("init redis connection failed")
        return
    }
    defer rdb.Close()
    fmt.Println("init redis successfully")
}
```

**哨兵模式**

```go
func initClient()(err error){
    rdb = redis.NewFailoverClient(&redis.FailoverOptions{
        MasterName:"master",
        SentinelAddrs:[]string{"x.x.x.x:2394","x.x.x.x:72874"}
    })
    _,err = rdb.Ping().Result()
    return errr
}
```

**集群模式**

```go
func initClient()(err error){
    rdb = redis.NewClusterClient(&redis.ClusterOptions{
        Addrs:[]string{":8934",":4123"},
    })
    _,err = rdb.Ping().Result()
    return err
}
```

**基本用法**

