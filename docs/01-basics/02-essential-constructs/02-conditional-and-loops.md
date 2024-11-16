# Conditional and Loops in Go

## Conditional Statements (if, else if, else)

```go
num := 10
num_mod_3 := num % 3

if num_mod_3 == 0 {
    fmt.Println("Divisible by 3")
} else if num_mod_3 == 1 {
    fmt.Println("Remainder 1 when divided by 3")
} else {
    fmt.Println("Remainder 2 when divided by 3")
}
```

!!! warning "`else if` and `else` should immediately follow preceeding `}`"
    below code will not compile. `Go’s syntax enforces clean code structure`.
    ```go
    if num_mod_3 == 0 {
        fmt.Println("Divisible by 3")
    }
    else if num_mod_3 == 1 {
        fmt.Println("Remainder 1 when divided by 3")
    }
    else {
        fmt.Println("Remainder 2 when divided by 3")
    }
    ```

---

## Switch Statement

- Go only runs the selected case, not all the cases that follow.
- In effect, the break statement that is needed at the end of each case in those languages is provided automatically in Go.
- Another important difference is that Go's switch cases need not be constants, and the values involved need not be integers.

```go
package main

import (
	"fmt"
	"runtime"
)

func main() {
	fmt.Print("Go runs on ")
	switch os := runtime.GOOS; os {
	case "darwin":
		fmt.Println("OS X.")
	case "linux":
		fmt.Println("Linux.")
	default:
		// freebsd, openbsd,
		// plan9, windows...
		fmt.Printf("%s.\n", os)
	}
}
```

---

## Loops

```go
for i := 0; i < 10; i++ {
    fmt.Println(i)
}
```

---

## `for` loop with `range`

- range form of the for loop iterates over a **slice** or **map**

```go
var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}

for i, v := range pow {
    fmt.Printf("2**%d = %d\n", i, v)
}
```

!!! info "performance penalty"
    - range on a slice passes the copy of the value (not a reference) by default, this affects performance for large slices or complex data types
    - In cases where performance is critical, you might consider using an index-based loop to avoid the copy.

---

## `for` loop with `break`

```go
for i := 0; i < 10; i++ {
    if i == 5 {
        break
    }
    fmt.Println(i)
}
```

---

## `for` loop with `continue`

```go
for i := 0; i < 10; i++ {
    if i == 5 {
        continue
    }
    fmt.Println(i)
}
```

---

## Infinite Loop

```go
for {
    fmt.Println("Infinite loop")
}
```
