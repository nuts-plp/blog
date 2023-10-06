## 随机生成服务器id

第三方库`github.com/bwmarrin/snowflake`

```go
package main

import (
   "fmt"
   "math/rand"
   "time"
   "github.com/bwmarrin/snowflake"
)
var node *snowflake.Node
func Init(startTime string, machineID int64) (err error) {
   rand.Seed(time.Now().UnixMicro())
   var st time.Time
   st, err = time.Parse("2006-01-02", startTime)
   if err != nil {
      return
   }
   snowflake.Epoch = st.UnixNano() / 1000000
   node, err = snowflake.NewNode(machineID)
   return
}
func GenID() int64 {
   return node.Generate().Int64()
}

func main() {
   if err := Init("2022-08-01", 1); err != nil {
      fmt.Println("init failed, error:", err)
      return
   }
   id := GenID()
   fmt.Println(id)
}
```





第三方库`github.com/sony/sonyflake`

```go
package main

import (
   "fmt"
   "time"

   "github.com/sony/sonyflake"
)

var (
   snowFlake     *sonyflake.Sonyflake
   sonyMachineID uint16
)

func getMachineID() (uint16, error) {
   return sonyMachineID, nil
}

//需要输入当前的机器id
func Init(startTime string, machineID uint16) (err error) {
   sonyMachineID = machineID
   var st time.Time
   st, err = time.Parse("2006-01-02", startTime)
   if err != nil {
      return err
   }
   setting := sonyflake.Settings{
      StartTime: st,
      MachineID: getMachineID,
   }
   snowFlake = sonyflake.NewSonyflake(setting)
   return
}
func GenID() (id uint64, err error) {
   if snowFlake == nil {
      err = fmt.Errorf("sony flake not Init")
      return
   }
   id, err = snowFlake.NextID()
   return
}
func main() {
   if err := Init("2022-08-01", 1); err != nil {
      fmt.Printf("init failed  error:%s\n", err)
      return
   }
   id, _ := GenID()
   fmt.Println(id)
}
```