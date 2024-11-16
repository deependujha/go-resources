# Functions in Go

- Functions are **`first-class citizens`**, meaning they `can be passed around like any other variable`.

## Basic Function Definition

In Go, a function is defined using the `func` keyword. It can take parameters and return values.

```go
func main(){
    a := 10
    b := 20
    fmt.Println(add(a, b))
}

func add(a, b int) int {
    return a + b
}
```

- if multiple parameters are same, just comma separate them and in the end mention datatype

---

## Multiple return values

```go
func swap(a, b int) (int, int) {
    return b, a
}

func main(){
    x, y := swap(1, 2)
    fmt.Println(x, y) // Output: 2 1
}
```

!!! info "`err != nil` concept"
    This multi-return feature is heavily used by Gofers for error handling.

    - First value is the success value, second value is the error value.
    - If the function returns an error, the second value will be non-nil.
    - If the function returns a value, the second value will be nil.

    ```go
    func divide(a, b int) (int, error) {
        if b == 0 {
            return 0, errors.New("division by zero")
        }
        return a / b, nil
    }

    func main(){
        x, err := divide(10, 2)
        if err != nil {  // error occurred. Hnadle it.
            fmt.Println(err)
        } else {
            fmt.Println(x)
        }
    }
    ```

---

## Named Return Values

```go
func divide(a, b int) (result int, err error) {
    if b == 0 {
        err = fmt.Errorf("division by zero")
        return
    }
    result = a / b
    return
}
```

---

## Variadic Functions

- A variadic function allows you to pass a variable number of arguments of a specific type.

```go
func sum(nums ...int) int {
    total := 0
    for _, num := range nums {
        total += num
    }
    return total
}

func main() {
    result := sum(1, 2, 3, 4, 5)
    fmt.Println(result) // Output: 15
}
```

---

## Defer, Panic and Recover

### Defer

- executes a statement just before the completion of the function
- if multiple defer statements are present, they are executed in reverse order (stack - LIFO)
- used as cleanup code, e.g., closing a file, releasing a lock, etc.

```go
// output: counting, 3, 2, 1

func defer_in_func() {
    defer fmt.Println("1")
    defer fmt.Println("2")
    defer fmt.Println("3")

    fmt.Println("counting")
}
```

### Panic

- Panic is a built-in function that stops the ordinary flow of control and begins panicking.
- When the function F calls panic, execution of F stops, any deferred functions in F are executed normally, and then F returns to its caller.
- To the caller, F then behaves like a call to panic. The process continues up the stack until all functions in the current goroutine have returned, at which point the program crashes.
- `Panics can be initiated by invoking panic directly. They can also be caused by runtime errors, such as out-of-bounds array accesses`.

```go
func main() {
    fmt.Println("hello")
    defer fmt.Println("deferred print")
    panic("panicked")
    fmt.Println("world")
}
```

### Recover

- Recover is a built-in function that regains control of a panicking goroutine.
- Recover is only useful inside deferred functions.
- During normal execution, a call to recover will return nil and have no other effect.
- If the current goroutine is panicking, a call to recover will capture the value given to panic and resume normal execution.

```go
package main

import "fmt"

func main() {
    f()
    fmt.Println("Returned normally from f.")
}

func f() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered in f", r)
        }
    }()
    fmt.Println("Calling g.")
    g(0)
    fmt.Println("Returned normally from g.")
}

func g(i int) {
    if i > 3 {
        fmt.Println("Panicking!")
        panic(fmt.Sprintf("%v", i))
    }
    defer fmt.Println("Defer in g", i)
    fmt.Println("Printing in g", i)
    g(i + 1)
}
```

---

## functions as arguments

- You can assign functions to variables, pass functions as arguments, and return them from other functions.

```go
func multiply(a, b int) int {
    return a * b
}

func applyOperation(a, b int, op func(int, int) int) int {
    return op(a, b)
}

func main(){
    result := applyOperation(5, 3, multiply)
    fmt.Println(result) // Output: 15
}
```

!!! success "`closure`/python type decorator functions in go"
    We can use this feature as decorator function in go.

    ```go
    func add(a, b int) int {
        return a + b
    }

    func fix_first_argument(a int, op func(int, int) int) func(int) int {
        return func(b int) int{
            return op(a, b)
        }
    }

    func main(){
        add_one := fix_first_argument(1, add)
        result := add_one(5)
        fmt.Println(result) // Output: 6
    }
    ```

---

## Closure in Go

A closure is a function value that references variables from outside its body.

- The function may access and assign to the referenced variables; in this sense the function is "bound" to the variables.

```go
package main

import "fmt"

func adder() func(int) int {
    sum := 0
    return func(x int) int {
        sum += x
        return sum
    }
}

func main() {
    pos, neg := adder(), adder()
    for i := 0; i < 10; i++ {
        fmt.Println(pos(i), neg(-2*i))
    }
}
```

---

## Anonymous function in Go

- a function that doesn’t have a name

```go
package main
import "fmt"

func main() {
    // Anonymous function (defined and called immediately)
    func() {
        fmt.Println("Welcome! to GeeksforGeeks")
    }()
}
```

- passing arguments to anonymous function

```go
package main
import "fmt"

func main() {
    // Passing arguments in anonymous function
    func(ele string) {
        fmt.Println(ele)
    }("GeeksforGeeks")
}
```

- Assigning anonymous function to a Variable

```go
package main
import "fmt"

func main() {
    // Assigning an anonymous function to a variable
    value := func() {
        fmt.Println("Welcome! to GeeksforGeeks")
    }
    value()
}
```

- passing anonymous function as function argument

```go
package main
import "fmt"

// Passing anonymous function as an argument
func GFG(i func(p, q string) string) {
    fmt.Println(i("Geeks", "for"))
}
func main() {
    value := func(p, q string) string {
        return p + q + "Geeks"
    }
    GFG(value)
}
```
