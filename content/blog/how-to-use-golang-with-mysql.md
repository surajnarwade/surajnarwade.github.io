+++
title = "How to use Golang with MySQL?"
author = "Suraj Narwade"
date = "2023-01-09T12:15:41.969Z"
category = "Blog"
+++

Golang is a widely used language for building scalable and reliable web applications.


When it comes to Storing data (relational), MySQL is one of the old and preferred ways as a database for many organisations.


This blog post will look at how to use Golang with MySQL by establishing the connection and printing the version using a simple query.


In upcoming blog posts, we will see advanced operations.


### Prerequisites


To follow along with this tutorial, you should have the following installed on your machine:


* Golang
* MySQL


**Installing the MySQL driver**


The first step is to install the MySQL driver for Golang. This driver lets us connect to a MySQL database from our Golang application. To install the MySQL driver, open a terminal and run the following command:



```
go get -u github.com/go-sql-driver/mysql

```

Here's the sample code. Take a minute and go through the code. Afterwards, we will discuss one line at a time.



```
package main

import (
    "database/sql"
    "fmt"
    "log"

    _ "github.com/go-sql-driver/mysql"
)

func main() {
    db, err := sql.Open("mysql", "username:password@tcp(127.0.0.1:3306)/testdb")
    defer db.Close()

    if err != nil {
        log.Fatal(err)
    }

    pingErr := db.Ping()
    if pingErr != nil {
        log.Fatal(pingErr)
    }

    fmt.Println("Connected!")

    var version string
    scanEerr := db.QueryRow("SELECT VERSION()").Scan(&version)

    if scanErr != nil {
        log.Fatal(scanErr)
    }

    fmt.Println(version)
}

```

### Let's understand one by one


* we need to connect to the MySQL database from our Golang application. To do this, we will use the `database/sql` package in the Go standard library. This package provides a common interface for working with different SQL databases.


	+ underscore alias `_` will import the package but will use it explicitly. It will be indirectly used for initialising the database drivers.



```
import (
  ...
  "database/sql"
  _ "github.com/go-sql-driver/mysql"
  ...
)

```

* Now, we need to open a connection to the MySQL database. We can do this by calling the [`sql.Open`](http://sql.Open)`()` function and passing in the MySQL driver name and a connection string.


	+ The first field is the name of the driver (`mysql` in this case)
	+ The second field is the [DSN](https://github.com/go-sql-driver/mysql#dsn-data-source-name) (Data source name)format which consists of username, password, protocol, address and name of the database, etc.



```
    db, err := sql.Open("mysql", "username:password@tcp(127.0.0.1:3306)/testdb")

```

* Ensure that the connection is properly closed when the application exits with the help of the following `defer` statement.



```
defer db.Close()

```

* Let's ping the database to verify that our program can connect to the database. If this function doesn't return any error (`nil`), then we can establish the connection successfully.



```
pingErr := db.Ping()

```

* The `QueryRow` the function executes a query that will return at most one row. The `Scan` function will store output from the matched row into the `version` variable.



```
    var version string
    scanErr := db.QueryRow("SELECT VERSION()").Scan(&version)

```

The output of the program will be as shown below:



```
$ go run main.go
Connected!
5.7.38

```

Thank you for reading. If you have any questions, do let me know in the comments.


I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


