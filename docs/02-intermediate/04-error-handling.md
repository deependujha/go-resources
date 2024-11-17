# **Error Handling in Go**

Go doesn't have exceptions. Instead, it uses **multiple return values**, typically returning an error as the second value from a function.

---

### **1. Error Type**

Go's built-in `error` type is an interface:
```go
type error interface {
    Error() string
}
```
This means any type that implements the `Error()` method satisfies the `error` interface.

---

### **2. Common Error Handling Pattern**

Go functions often return two values: the result and an error. If the error is `nil`, the result is valid. If not, handle the error.

**Basic Example:**
```go
result, err := someFunction()
if err != nil {
    fmt.Println("Error:", err)
    return
}
// Process result
```

**Function Signature with Error:**
```go
func someFunction() (string, error) {
    // Do something, return an error if needed
    if failureCondition {
        return "", errors.New("something went wrong")
    }
    return "success", nil
}
```

---

### **3. Creating Custom Errors**

You can create custom error types by defining a new struct that implements the `error` interface.

**Example:**
```go
type CustomError struct {
    Code int
    Msg  string
}

func (e *CustomError) Error() string {
    return fmt.Sprintf("Error %d: %s", e.Code, e.Msg)
}

func someFunction() error {
    return &CustomError{Code: 404, Msg: "Not Found"}
}

err := someFunction()
if err != nil {
    fmt.Println(err)  // Prints: Error 404: Not Found
}
```

---

### **4. Handling Multiple Errors**

You can chain errors or wrap them with additional context using `fmt.Errorf` or `errors.Wrap` (from external libraries like `github.com/pkg/errors`).

**Wrapping Errors:**
```go
if err != nil {
    return fmt.Errorf("failed to process request: %w", err)
}
```
The `%w` format verb allows you to wrap the original error to retain its context.

**Unwrapping Errors (Go 1.13+):**
```go
if errors.Is(err, someSpecificError) {
    // Handle specific error
}
```

---

### **5. Error Checking:**

- **Nil Check:** Always check for `nil` before proceeding.
```go
if err != nil {
    // Handle error
}
```

- **Error Assertion:** If the error is of a specific type, you can use type assertion.
```go
if customErr, ok := err.(*CustomError); ok {
    fmt.Println("Custom Error:", customErr.Code)
}
```

---

### **6. Best Practices:**

- **Early Return:** If an error occurs, return early to avoid deeper nested logic.
- **Error Messages:** Provide clear, descriptive messages for easier debugging.
- **Avoid Silent Failures:** Don’t ignore errors unless absolutely necessary (e.g., if an error is truly non-critical).

---

### **7. Example:**

```go
package main

import (
    "errors"
    "fmt"
)

func riskyFunction() (int, error) {
    return 0, errors.New("something went wrong")
}

func main() {
    result, err := riskyFunction()
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Println("Result:", result)
}
```

Output:
```
Error: something went wrong
```
