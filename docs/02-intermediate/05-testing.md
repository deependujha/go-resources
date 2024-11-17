# Testing

### **1. Basic Testing in Go**

Go has a built-in testing framework in the `testing` package.

- Test needs to be in a file `*_test.go`.
- Test functions need to be prefixed with `Test`.

**Basic Test Example:**
```go
package main

import "testing"

// Function to be tested
func Add(a, b int) int {
    return a + b
}

// --- code in `add_test.go` file

// Test function
func TestAdd(t *testing.T) {
    result := Add(2, 3)
    expected := 5
    if result != expected {
        t.Errorf("Expected %d but got %d", expected, result)
    }
}
```
- The test function name must start with `Test` followed by the function being tested.
- `t *testing.T` is passed to the test function to report errors.
- `t.Errorf` is used to report a test failure.

**Run the tests:**
```sh
go test
```

---

### **2. Table-Driven Tests**

Table-driven tests are a Go idiom for testing different inputs and expected results in a concise and easy-to-maintain format.

```go
func TestAdd(t *testing.T) {
    tests := []struct {
        a, b     int
        expected int
    }{
        {2, 3, 5},
        {5, 7, 12},
        {0, 0, 0},
        {-1, 1, 0},
    }

    for _, tt := range tests {
        t.Run(fmt.Sprintf("%d+%d", tt.a, tt.b), func(t *testing.T) {
            result := Add(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Expected %d but got %d", tt.expected, result)
            }
        })
    }
}
```

---

### **3. Testing with Setup and Teardown**

You might need setup or teardown functions for initializing and cleaning up resources.

**Example with `testing.T` Setup:**
```go
package main

import (
    "fmt"
    "testing"
)

func setup() {
    fmt.Println("Setting up")
}

func teardown() {
    fmt.Println("Tearing down")
}

func TestAdd(t *testing.T) {
    setup()
    defer teardown() // Ensures teardown happens after test completes

    result := Add(2, 3)
    if result != 5 {
        t.Errorf("Expected 5 but got %d", result)
    }
}
```

---

### **4. Mocking in Go**

Go doesn’t have built-in mocking tools, but you can easily create mock implementations or use third-party libraries like **`github.com/stretchr/testify/mock`** for more complex mocking.

#### **Manual Mocking**

If you're testing a function that depends on an external interface or a database, you can create a manual mock.

**Example with Interface:**
```go
package main

import "testing"

// Define an interface
type Database interface {
    Save(data string) error
}

// Implement a mock struct
type MockDatabase struct{}

func (m *MockDatabase) Save(data string) error {
    if data == "" {
        return fmt.Errorf("empty data")
    }
    return nil
}

func TestSaveData(t *testing.T) {
    mockDB := &MockDatabase{}
    err := mockDB.Save("data")
    if err != nil {
        t.Errorf("Expected no error but got %v", err)
    }

    err = mockDB.Save("")
    if err == nil {
        t.Error("Expected error for empty data")
    }
}
```

---

#### **Mocking with `testify/mock`**

`testify` is a popular library for mocking and assertions. Install it via:
```sh
go get github.com/stretchr/testify
```

**Mock Example with `testify/mock`:**
```go
package main

import (
    "testing"
    "github.com/stretchr/testify/mock"
)

// Define an interface
type Database interface {
    Save(data string) error
}

// Define a mock struct using testify
type MockDatabase struct {
    mock.Mock
}

func (m *MockDatabase) Save(data string) error {
    args := m.Called(data)
    return args.Error(0)
}

// Test function using the mock
func TestSaveDataWithMock(t *testing.T) {
    mockDB := new(MockDatabase)
    mockDB.On("Save", "data").Return(nil) // Mock behavior
    mockDB.On("Save", "").Return(fmt.Errorf("empty data"))

    err := mockDB.Save("data")
    if err != nil {
        t.Errorf("Expected no error but got %v", err)
    }

    err = mockDB.Save("")
    if err == nil {
        t.Error("Expected error for empty data")
    }

    mockDB.AssertExpectations(t) // Assert all expected calls were made
}
```

`testify/mock` allows you to:
- Set expectations (`mockDB.On()`).
- Verify that expectations were met (`mockDB.AssertExpectations()`).

---

### **5. Benchmarking**

Go also supports benchmarking with the `testing` package using the `Benchmark` function.

**Example:**
```go
func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Add(2, 3)
    }
}
```

To run the benchmark:
```sh
go test -bench .
```

**Benchmarking Specific Functions:**
You can benchmark individual functions like this:
```sh
go test -bench=BenchmarkAdd
```

---

### **6. Code Coverage**

Go supports built-in code coverage.

**Run Tests with Coverage:**
```sh
go test -cover
```

**Show Detailed Coverage:**
```sh
go test -coverprofile=coverage.out
go tool cover -html=coverage.out
```

This will generate a nice HTML file showing the code coverage details.

---

### **7. Parallel Tests**

To run tests in parallel (useful for independent tests), you can use `t.Parallel()`.

```go
func TestAdd1(t *testing.T) {
    t.Parallel()  // Run this test in parallel with others
    result := Add(2, 3)
    if result != 5 {
        t.Errorf("Expected 5 but got %d", result)
    }
}

func TestAdd2(t *testing.T) {
    t.Parallel()
    result := Add(5, 7)
    if result != 12 {
        t.Errorf("Expected 12 but got %d", result)
    }
}
```

---

### **8. Test Suites (Multiple Tests for the Same Group)**

Test suites aren’t built-in, but you can group tests logically by creating a helper function for repeated setup or assertions.

**Test Suite Example:**
```go
package main

import "testing"

func setupSuite() {
    // Setup code for the suite
}

func teardownSuite() {
    // Cleanup code for the suite
}

func TestSuite(t *testing.T) {
    setupSuite()
    defer teardownSuite()

    t.Run("TestCase1", TestAdd1)
    t.Run("TestCase2", TestAdd2)
}
```

---

### **9. Additional Testing Tools**

- **Test coverage tools**: Like `go-coverage` for more advanced code coverage analysis.
- **GoMock**: Another popular mocking library (install via `go get github.com/golang/mock/gomock`).
- **Testify Assertions**: It’s useful for easy assertions:
  ```go
  assert.Equal(t, expected, actual)
  assert.NoError(t, err)
  ```

---

### **10. Summary of Testing Workflow**

1. **Write a function** to be tested.
2. **Write a test function** that checks the behavior.
3. **Use assertions** to check that the result matches expectations.
4. **Mock external dependencies** if needed.
5. **Use `t.Parallel()`** for concurrent testing.
6. **Benchmark** critical parts of your code to ensure performance.
7. **Run tests** using `go test`.
