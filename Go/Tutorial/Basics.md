> [Go Bootcamp](http://www.golangbootcamp.com/) 을 보고 공부한 내용을 정리한 글입니다.

<br>

## Variables & inferred typing

### Variable

`var` 는 가변형 변수로 처음 지정된 값이 아니더라도 중간에 언제든지 변경 가능한 변수로 `var [변수명] [타입] = [값]` 순으로 작성한다. 읽기 쉬운 형태를 띈다고 생각된다.

예시 :

```go
var (
  name string = "Jay"
  age int = 40
  location string = "Earth"
)
```

실행 예시 :

```go
package main

import "fmt"

var (
	name     string
	age      int
	location string
)

func main() {
	name, location := "Jay", "Earth"
	age := 40
    fmt.Printf("%s (%d) of %s", name, age, location) # Printf : format print
}
```

```bash
$ go run main.go
Jay (40) of Earth
```

<br>

### Constant

상수(Constant)는 값이 변경될 수 있는 Variable과 달리 한 번 설정된 값은 다시 변경될 수 없는 특성을 가지고 있다. `var` 와 형식은 비슷하나 Type을 지정하지 않아도 된다. Go 언어의 특징 중 하나인 타입을 추론할 수 있기 때문에 문자열로 입력하던 정수형으로 입력하던 형식을 추론하여 지정해준다.

예시 :

```go
const name = "Jay"
```

`const` 는 한 번 설정된 이후에는 변경할 수 없는 특징으로 예시와 같이 name이라는 상수에 Jay라는 값을 이미 입력했다면 다시 name을 변경하거나 입력하면 `cannot assign to name` 에러가 발생한다.

실행 예시 :

```go
package main

import "fmt"

const name = "Jay"

func main() {
    name = "Not Jay"
    fmt.Println("name:", name)
}
```

```bash
$ go run main.go
# command-line-arguments
./main.go:8:10: cannot assign to name
```

