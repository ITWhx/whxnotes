#### 标识符长度

```go
type Person struct {
	Name string
	Age  int
}

// AverageAge returns the average age of people.
func AverageAge(people []Person) int {
	if len(people) == 0 {
		return 0
	}
   //分段
	var count, sum int
	for _, p := range people {
		sum += p.Age
		count += 1
	}
   //分段
	return sum / count
}
```

*  `p`在执行函数期间存在时间很短 。 如果要了解`p`的作用只需阅读两行代码。 
* 对函数逻辑使用空行分段分段 



#### 上下文是关键

```go
for index := 0; index < len(s); index++ {
	//
}
```

```go
for i := 0; i < len(s); i++ {
	//
}
```

* 第二个好， 前者的额外冗长性(指`index`)几乎没有增加对于程序的理解。 
* 但是以下，第二个更加具备可读性。

```go
func (s *SNMP) Fetch(oid []int, index int) (int, error)
```

```go
func (s *SNMP) Fetch(o []int, i int) (int, error)
```



####  不要用变量类型命名你的变量

```go
var usersMap map[string]*User
```

*  如果`users`的描述性都不够用，那么`usersMap`也不会。 

 此建议也适用于函数参数。 

```go
type Config struct {
	//
}

func WriteConfig(w io.Writer, config *Config)
```

*  命名`*Config`参数`config`是多余的。 我们知道它是`*Config`类型。 

