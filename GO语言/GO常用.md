### Go常用到的

#### 操作数据库 mysql

```go
package main

import (
    //导入数据库用到的库
	"database/sql"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
	//"github.com/jmoiron/sqlx"
)

func main() {
    //打开数据库连接
	Db, err := sql.Open("mysql", "root:123456@tcp(localhost:3306)/demo")
	//Db, err := sqlx.Open("mysql", "root:123456@tcp(localhost:3306)/demo")
	//if err != nil {
	//	fmt.Println(err)
	//}
	//延迟关闭数据库连接
	//defer Db.Close()
	//fmt.Printf("mysql ok")
    //执行查询操作
	rows,err := Db.Query("SELECT * FROM t_demo")
	if err != nil{
		fmt.Println("select db failed,err:",err)
		return
	}
	for rows.Next(){
		var name string
		var age string
		err = rows.Scan(&name,&age)
		if err != nil{
			fmt.Println(err)
			return
		}
		fmt.Println(name,age)
	}
	fmt.Println(result)

	//这种应该是用的很多的，需要结构体
	var user User
	for i := 1; i < 6; i++ {
		err = Db.Get(&user, "SELECT username,age FROM t_demo")
		fmt.Println(user)
	}

	//这种实际就是用一个切片把数据保存起来，不需要结构体
	var userlist []User
	err = Db.Select(&userlist, "select * from t_demo")
	if err != nil {
		fmt.Println(err)
	}
	fmt.Println(userlist)
	for _, data := range userlist {
		fmt.Println(data)
	}

	//Db.Exec()是一个通用的结构。里面可以写删除，更新，增加
	result, err := Db.Exec("update t_demo set username = ? where id = ?", "dhy",2)
	if err != nil{
		fmt.Println(err)
	}
	//增加的id是多少
	//i,_ = result.LastInsertId()
	//影响的行数
	i, _ := result.RowsAffected()
	fmt.Println(i)
}
type User struct {
	Username string
	Age      string
}
```

#### 操作 json 数据

```go
package main

import (
	"encoding/json"
	"fmt"
)

func main() {
	var s Student
	str := `{"name":"dhy","age":"26"}`
	i := 1
	fmt.Println(&i)
	fmt.Println(&s)
	//把结构体变为json
	if error := json.Unmarshal([]byte(str), &s)
		error != nil {
		fmt.Println(error)
	}
	fmt.Println(s)
	name := s.Name
	fmt.Println(name)

	s.Name = "56"
	//返回的是一个字节数组
	//把json转为结构体
	bytes, e := json.Marshal(s)
	if e != nil {
		fmt.Println(e)
	}
	i2 := []byte("adad")
	fmt.Print(i2)
	fmt.Printf("%T\n", bytes)
	fmt.Printf("%s\n", bytes)
}

type Student struct {
	Name string
	Age  string
}
```

#### 框架 iris

> 这个框架学起来应该不难，跟java差不多的

```go
package GETDemo

import (
	"github.com/kataras/iris"
)

func main(){
	application := iris.New()
	application.Get("/dhy/{name}", func(context iris.Context) {
		path := context.Path()
		param := context.URLParam("name")
		application.Logger().Info(param)
		context.WriteString(path)
	})
	application.Run(iris.Addr(":6126"))
}

```

