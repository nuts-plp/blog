### go连接mysql

go连接mysql要使用`github.com/go-sql-driver/mysql`中的init函数，需要匿名引用

go连接mysql可以使用`database/sql`和`github.com/jmoiron/sqlx`前者为内置包，后者为第三方包



```go
import (
	"database/sql"
	"fmt"
	_"github.com/go-sql-driver/mysql"
)

func main(){
	dsn :="root:root@tcp(127.0.0.1:3306)/shop"
	db,err :=sql.Open("mysql",dsn)
	if err != nil {
		fmt.Printf("Error opening	err: %v\n", err)
		return
	}
	err = db.Ping()
	if err != nil {
		fmt.Printf("database connection error: %v\n", err)
		return
	}
	fmt.Println("database connection established!")
}
```





```go
import (
	"fmt"
	_ "github.com/go-sql-driver/mysql"
	// "database/sql"
	"github.com/jmoiron/sqlx"
)

var (
	db  *sqlx.DB
	err error
)

func initDB() {
	dsn := "root:root@tcp(127.0.0.1:3306)/sql_test"
	db, err := sqlx.Connect("mysql", dsn)
	if err != nil {
		fmt.Printf("error check connecting failed!err:%v\n", err)
		return
	}
	db.SetMaxIdleConns(5)
	db.SetMaxOpenConns(10)
	fmt.Println("database initialized successfully!")

}

type user struct {
	id   int
	name string
	age  int
}

func main() {
	initDB()
	//单条查询
	SQLstr1 := "SELECT id,name,age FROM users WHERE id = 1"
	var u user
	db.Get(&u, SQLstr1)
	fmt.Printf("u:%#v\n", u)

	//多条查询
	SQLstr2 := "SELECT id,name,age FROM users "
	var userList []user
	db.Select(&userList, SQLstr2)
	fmt.Printf("userList:%#v\n", userList)

}

```

