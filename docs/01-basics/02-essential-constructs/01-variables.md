# Variables in Go

## Variable Declaration & Initialization

```go
// declare a variable first (with type), and then initialize it
var name string
name = "Deependu Jha"

// OR (declare and initialize in one line)
var name string = "Deependu Jha"

// OR (declare and initialize in one line with walrus operator)
name := "Deependu Jha" // walrus operator (declare and initialize in one line)
```

---

## Variable Types

| Type | Description |
| :--- | :--- |
| `bool` | Boolean type, can be `true` or `false`. |
| `int` | Integer type, can be positive or negative. |
| `int8`, `int16`, `int32`, `int64` | Signed integer types, can be positive or negative. |
| `uint8`, `uint16`, `uint32`, `uint64` | Unsigned integer types, can only be positive. |
| `float32`, `float64` | Floating-point types, can be positive or negative. |
| `string` | String type, can be empty. |
| `[]byte` | Byte slice type, can be empty. |

---

## Print in Go

```go
import "fmt"

func main() {
    fmt.Print("Hello, World!\n") // print without newline
    fmt.Println("Hello, World!") // print with newline
    fmt.Printf("Hello, %s!", "World") // print with format
}
```

---

## Log in Go

```go
import "log"

func main() {
    log.Print("Hello, World!\n") // print without newline
    log.Println("Hello, World!") // print with newline
    log.Printf("Hello, %s!", "World") // print with format

    log.Fatal("Fatal error!") // logs and exits (os.exit(1))
    log.Fatalf("Fatal error! %s", "World") // logs and exits
    log.Panic("Panic error!") // logs and panics
}
```
