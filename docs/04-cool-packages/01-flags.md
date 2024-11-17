# `flag` Module in Go

The `flag` module in Go is used for parsing command-line arguments. It provides a way to define and use flags in your program.

---

## Basic Example

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	// Define flags
	name := flag.String("name", "World", "a name to say hello to")
	age := flag.Int("age", 0, "your age")
	isMember := flag.Bool("member", false, "are you a member?")
	
	// Parse the flags
	flag.Parse()

	// Access the values
	fmt.Printf("Hello, %s! Age: %d, Member: %t\n", *name, *age, *isMember) // we need to dereference pointers

	// Remaining command-line arguments
	fmt.Println("Other arguments:", flag.Args())
}
```

- Output

```bash
go run main.go -name=Deep -age=22 -member=true extra_arg
```

You would get:
```
Hello, Deep! Age: 23, Member: true
Other arguments: [extra_arg]
```

- The one weird thing is that, we need to dereference the pointers to get the actual values.

---

## Better flag usage

- We can use `flag.IntVar` instead of `flag.Int`. But, here we need to pass a pointer to the variable.

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	var worker int
	flag.IntVar(&worker, "worker", 0, "Number of worker")

	flag.Parse()

	fmt.Println("Number of workers is: ", worker)
}
```

---

## Help command

- we can use `-h` or `--help` to see auto-generated usage information:

```bash
go run . -h 
# or
go run . --help
```

---

## Flags with Structs (Good example)

```go
package main

import (
	"flag"
	"fmt"
)

type ServerConfig struct {
	port    int
	workers int
	env     string
}

func main() {
	conf := ServerConfig{}

	flag.IntVar(&conf.port, "port", 6969, "which port to run on")
	flag.IntVar(&conf.workers, "worker", 1, "number of workers to run")
	flag.StringVar(&conf.env, "env", "dev", "which mode server is in: dev | prod")

	flag.Parse()

	fmt.Printf("%+v\n", conf)
}
```

- Run it:

```bash
go run . -port=8080 -worker=2 -env=prod

# Output
{port:8080 workers:2 env:prod}
```
