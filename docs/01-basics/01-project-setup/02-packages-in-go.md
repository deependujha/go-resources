# Packages in Go

A **package** in Go is a way to organize code and promote reusability. Every Go program is made up of **at least one package**, and the `main` package is required to create an executable.

!!! success "Keep in mind"
    Each directory in a Go project should have the same package name.

    ```md
    myproject/
      myproject.go # package myproject
      dir_1/
        a1.go # package alpha
        a2.go # package alpha (same package name)
      dir_2/
        b1.go # package beta
        b2.go # package beta (same package name)
        dir_3/
          c1.go # package gamma
          c2.go # package gamma (same package name)
    ```

---

## Declaring and Using Packages

### Declaring a Package
Every Go file starts with a `package` declaration:
```go
package main
```

### Importing Packages
Use the `import` keyword to include packages:
```go
import "fmt"
```

For multiple imports:
```go
import (
	"fmt"
	"math"
)
```

---

## Creating and Using Custom Packages

### Create a Package
1. Create a new directory (e.g., `mypackage`).
2. Add a `.go` file with the package name:
   ```go
   // mypackage/mypackage.go
   package mypackage

   import "fmt"

   func SayHello(name string) {
       fmt.Printf("Hello, %s!\n", name)
   }
   ```

### Use the Custom Package
1. In the main program, import your package:
   ```go
   // main.go
   package main

   import (
       "mypackage"
   )

   func main() {
       mypackage.SayHello("Deep")
   }
   ```

2. Run the program:
   ```bash
   go run .
   ```

!!! warning
    The below command will not work, as all the executables have not been mentioned.
    ```bash
    go run main.go
    ```
    You can either use: `go run main.go mypkg/mypkg.go` or better `go run .`

---

## Package Visibility: Exported vs. Unexported

- **Exported identifiers** (functions, variables, constants, types) **start with an uppercase letter**.
  - Example: `SayHello` (accessible outside the package).
- **Unexported identifiers** start with a lowercase letter.
  - Example: `helperFunc` (accessible only within the same package).

### Example
```go
// mypackage/mypackage.go
package mypackage

import "fmt"

// Exported
func SayHello(name string) {
	fmt.Printf("Hello, %s!\n", name)
}

// Unexported
func helperFunc() {
	fmt.Println("This is a helper function")
}
```

The function `helperFunc` cannot be used outside `mypackage`.

---

## Package Initialization

You can define an **`init` function** in a package. This function runs automatically when the package is imported.

### Example
```go
package mypackage

import "fmt"

func init() {
	fmt.Println("mypackage initialized!")
}
```

When the package is imported, the `init` function runs before any other code in the package.

---

## Go Modules and Packages

Go modules make package management simple:
1. Initialize a module:
   ```bash
   go mod init myproject
   ```
2. Go automatically manages dependencies when you import third-party packages.

### Example with Third-Party Packages

Install a package (e.g., `github.com/spf13/cobra`):
```bash
go get github.com/spf13/cobra
```

Import and use it in your code:
```go
import "github.com/spf13/cobra"
```

---

## Best Practices

1. **Keep Packages Cohesive**:
   Each package should have a single responsibility.

2. **Avoid Circular Dependencies**:
   Packages should not depend on each other in a circular manner.

3. **Use `internal` for Private Packages**:
   - The `internal` directory restricts the use of its packages to the parent module.
   - Example:
     ```
     project/
       internal/
         helper/
           helper.go
       main.go
     ```
   - The `helper` package can only be used by code inside the `project` module.

4. **Document Your Package**:
   Add comments to exported functions, constants, and types. This helps tools like `godoc` generate documentation.

---

## Common Go Standard Library Packages

| Package  | Description                                       |
|----------|---------------------------------------------------|
| `fmt`    | Formatting input/output.                         |
| `os`     | OS-level functionality (files, environment vars).|
| `time`   | Time and date manipulation.                      |
| `net/http` | HTTP client and server.                        |
| `io`     | Input/output primitives.                         |
| `math`   | Mathematical functions and constants.            |
| `sync`   | Synchronization primitives like mutexes.         |
| `context`| Managing request contexts (e.g., cancellations). |
