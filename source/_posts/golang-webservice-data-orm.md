---
title: Use golang to develop webservice-ORM/Relation database
date: 2019-10-22 14:23:52
tags: golang
catogeries: golang
---

# ORM opensource in Go

ORM in golang is acutually implemeted by reflect of go, it's simiar as json/encoding package,
system can get struct name and fields by reflect.
Details can be found in https://golang.org/pkg/reflect/

## beego orm

https://beego.me/docs/mvc/model/orm.md

beego is complete web framework, beego/orm is part of it, but it can also be used sperately.

```c++
package main

import (
    "fmt"
    "github.com/astaxie/beego/orm"
    _ "github.com/go-sql-driver/mysql"
)



//This is the Customer table in database
type Customer struct{
	Id int64
	FirstName string `orm:"column(FirstName)"` // orm is actually reflection in golang
	//if the field name is different, can also define it.
	 
	LastName string `orm:"column(Lastname)"`
	Email string `orm:"column(Email)"`
}


func init() {
    orm.RegisterDriver("mysql", orm.DRMySQL)
	orm.RegisterDataBase("default", "mysql", "root:pass1234@/hearing?charset=utf8")
	
	//need to register the Customer table firstly
	orm.RegisterModel(new(Customer))
}

func main() {
    o := orm.NewOrm()
	o.Using("default") // Using default, you can use other database
	
	customer := &Customer{
		FirstName:"Ye",
		LastName: "W",
		Email:"test1234@gmail.com",
	}
    fmt.Println(o.Insert(customer))
}

```

## goorp 

https://github.com/go-gorp/gorp



```c++
package main

import (
	"database/sql"

	_ "github.com/go-sql-driver/mysql"
	"gopkg.in/gorp.v1"
)


//Table struct
type Post struct {
	Id   int
	Name string
}


func initDb() *gorp.DbMap {
	// connect to db using standard Go database/sql API
	// use whatever database/sql driver you wish
	db, err := sql.Open("mysql", "root:pass1234@/hearing")
	if err != nil {
		panic(err)
	}

	// construct a gorp DbMap
	//define the mysqlDialect
	dbmap := &gorp.DbMap{Db: db, Dialect: gorp.MySQLDialect{
		Engine:   "InnoDB",
		Encoding: "utf8",
	}}

	// add a table, setting the table name to 'posts' and
	// specifying that the Id property is an auto incrementing PK
	dbmap.AddTableWithName(Post{}, "posts").SetKeys(true, "Id")

	// create the table. in a production system you'd generally
	// use a migration tool, or create the tables via scripts
	err = dbmap.CreateTablesIfNotExists()
	return dbmap
}

func main() {
	dbmap := initDb()
	p := &Post{
		Name: "first post",
	}
	
	
	//add a new entry
	dbmap.Insert(p)
	
	
	
	//get the entry by its unique id
	data, _ := dbmap.Get(Post{}, 1)
	d := data.(*Post)
	fmt.Println("got entry from db:", d.Name)
	
}

```


# goorm 
https://github.com/jinzhu/gorm



```c++
package main

import (
	_ "github.com/go-sql-driver/mysql"
	"github.com/jinzhu/gorm"
)

type Product struct {
	ID    uint `gorm:"primary_key"`
	Code  string
	Price uint
}

func main() {
	db, err := gorm.Open("mysql", "root:pass1234@/hearing")
	if err != nil {
		panic("failed to connect database")
	}
	defer db.Close()

	// Migrate the schema
	db.AutoMigrate(&Product{})

	// Create
	db.Create(&Product{Code: "L1212", Price: 1000})

	// Read
	var product Product
	db.First(&product, 1)                   // find product with id 1
	db.First(&product, "code = ?", "L1212") // find product with code l1212

	// Update - update product's price to 2000
	db.Model(&product).Update("Price", 2000)

	// Delete - delete product
	db.Delete(&product)
}

```

