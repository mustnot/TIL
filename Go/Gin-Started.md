# Started Go Webframework - Gin

> [gin-gonic/gin](https://github.com/gin-gonic/gin) 를 참고하여 간단하게 정리한 내용

<br>

## Installation

Go command to install Gin

```bash
$ go get -u github.com/gin-gonic/gin
```

Import Code : 

```go
import (
	"net/http"

	"github.com/gin-gonic/gin"
)
```

<br>

## Quick start

**example.go**

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	router := gin.Default()
	router.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})
	router.Run()
}
```

```bash
$ go run example.go
```

공식 문서에는 `r := gin.Default()`로 되어 있는데, `r`이라는 단어만 보았을 때, 알아보기 어려운 점이 있어서 router로 수정하였다. `router`의 역할은 Flask로 따져보았을 때, `app = Flask(__name__)`과 같은 의미로 생각된다.



## Methods

일반적으로 제공되는 HTTP 메소드는 모두 지원하며 다음과 같은 방식으로 지원한다. 의외로 가독성이 뛰어난데, 프로젝트의 크기가 커질수록 라인이 길어지는 점은 감안해야할 것 같다. MSA에서는 빠르게 개발될 수 있을 것 같다.

`router.[METHOD]([ROUTE], [FUNC(c *gin.Context)])` 형태의 작성 방식을 띄고 있다.

```go
func main() {
	// Creates a gin router with default middleware:
	// logger and recovery (crash-free) middleware
	router := gin.Default()

	router.GET("/someGet", getting)
	router.POST("/somePost", posting)
	router.PUT("/somePut", putting)
	router.DELETE("/someDelete", deleting)
	router.PATCH("/somePatch", patching)
	router.HEAD("/someHead", head)
	router.OPTIONS("/someOptions", options)

	// By default it serves on :8080 unless a
	// PORT environment variable was defined.
	router.Run()
	// router.Run(":3000") for a hard coded port
}
```

<br>

## Practice

```go
package main

import (
	"net/http"

	"github.com/gin-gonic/gin"
)

func hello(c *gin.Context) {
	name := c.Param("name")
	c.String(http.StatusOK, "Hello %s", name)
}

func main() {
	router := gin.Default()
	router.GET("/user/:name", hello)
	router.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})
	router.Run() // listen and serve on 0.0.0.0:8080 (for windows "localhost:8080")
}
```





















