# **Context in Go**

The `context` package in Go provides a way to manage deadlines, cancellation signals, and other request-scoped values across API boundaries. It is widely used for **concurrent programming** and **graceful termination** of goroutines.

---

## **Key Features of Context**

- **Cancellation Propagation**: Cancels all derived contexts when the parent context is canceled.
- **Timeouts and Deadlines**: Automatically cancel operations after a specified time.
- **Request-Scoped Data**: Pass values (like user IDs, session tokens) across API calls.

---

## **Creating Contexts**
There are four main ways to create contexts:

### **`context.Background()`**
- It’s an empty context and acts as the root of any context tree.
- Typically used at the **top level** (e.g., in `main()` or request handlers).

```go
ctx := context.Background()
```

### **`context.TODO()`**
- Used when you are unsure which context to use.
- A placeholder to indicate that you plan to use a proper context later.

```go
ctx := context.TODO()
```

### **`context.WithCancel(parentContext)`**
- Derives a new context that can be explicitly canceled.

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	// Create a context with cancel
	ctx, cancel := context.WithCancel(context.Background())

	go func(ctx context.Context) {
		for {
			select {
			case <-ctx.Done():
				fmt.Println("Goroutine exiting...")
				return
			default:
				fmt.Println("Goroutine running...")
				time.Sleep(500 * time.Millisecond)
			}
		}
	}(ctx)

	time.Sleep(2 * time.Second)
	cancel() // Cancel the context
	time.Sleep(1 * time.Second)
}
```

---

### **`context.WithTimeout(parentContext, duration)`**
- Creates a context that cancels itself after a specified timeout.

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	// Create a context with a timeout of 2 seconds
	ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
	defer cancel() // Always call cancel to release resources

	select {
	case <-time.After(3 * time.Second):
		fmt.Println("Operation completed")
	case <-ctx.Done():
		fmt.Println("Timeout occurred:", ctx.Err())
	}
}
```

---

### **`context.WithDeadline(parentContext, deadlineTime)`**
- Similar to `WithTimeout`, but specifies an exact **deadline** instead of a duration.

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	// Set a specific deadline
	deadline := time.Now().Add(2 * time.Second)
	ctx, cancel := context.WithDeadline(context.Background(), deadline)
	defer cancel()

	select {
	case <-time.After(3 * time.Second):
		fmt.Println("Operation completed")
	case <-ctx.Done():
		fmt.Println("Deadline exceeded:", ctx.Err())
	}
}
```

---

## **Passing Values in Context**
- Use `context.WithValue(parentContext, key, value)` to pass data along with the context.
- Not intended for frequently accessed data; use it sparingly.

```go
package main

import (
	"context"
	"fmt"
)

func main() {
	// Create a context with a value
	ctx := context.WithValue(context.Background(), "userID", 42)

	// Pass the context to a function
	process(ctx)
}

func process(ctx context.Context) {
	userID := ctx.Value("userID")
	if userID != nil {
		fmt.Println("User ID:", userID)
	} else {
		fmt.Println("No user ID found")
	}
}
```

---

## **Key Functions in Context**

| Function                 | Description                                                                 |
|--------------------------|-----------------------------------------------------------------------------|
| `ctx.Done()`             | Returns a channel that is closed when the context is canceled or times out. |
| `ctx.Err()`              | Returns an error indicating why the context was canceled (e.g., `canceled` or `deadline exceeded`). |
| `ctx.Value(key)`         | Retrieves the value associated with the context for a given key.            |

---

## **Using Context in Goroutines**

Contexts are especially useful for managing goroutines, ensuring they are terminated when no longer needed.

### **Example: Goroutine with Timeout**
```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
	defer cancel()

	go longRunningTask(ctx)

	// Wait for context to timeout
	time.Sleep(4 * time.Second)
	fmt.Println("Main function exiting")
}

func longRunningTask(ctx context.Context) {
	for {
		select {
		case <-ctx.Done():
			fmt.Println("Task canceled:", ctx.Err())
			return
		default:
			fmt.Println("Working...")
			time.Sleep(1 * time.Second)
		}
	}
}
```

---

## **Best Practices with Context**
1. **Always Cancel Contexts**:
   - Call `cancel()` for `WithCancel`, `WithTimeout`, and `WithDeadline` to avoid resource leaks.

2. **Pass Context Explicitly**:
   - Pass the context as the first argument to functions that need it.

3. **Avoid Passing Context Across Tiers**:
   - Context should not be passed to multiple layers unnecessarily.

4. **Do Not Store Context in Structs**:
   - Pass it explicitly to each function instead.

5. **Minimal Use of `WithValue`**:
   - Use for request-scoped data; avoid for large or frequently accessed data.

---

## **Advanced Topics**
1. **Rate Limiting with Context**:
   Use `time.Ticker` in conjunction with `ctx.Done()` to limit the rate of operations.

2. **Buffered Channels with Context**:
   Allow goroutines to process a limited number of tasks, with graceful shutdown.

3. **Context with HTTP Handlers**:
   Use `ctx` to propagate deadlines, timeouts, or cancellation in web servers.

---

### **Example: HTTP Server with Context**
```go
package main

import (
	"context"
	"fmt"
	"net/http"
	"time"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		ctx, cancel := context.WithTimeout(r.Context(), 2*time.Second)
		defer cancel()

		done := make(chan bool)
		go func() {
			// Simulate processing
			time.Sleep(3 * time.Second)
			done <- true
		}()

		select {
		case <-ctx.Done():
			http.Error(w, "Request timed out", http.StatusRequestTimeout)
		case <-done:
			fmt.Fprintln(w, "Request processed successfully")
		}
	})

	fmt.Println("Server starting on :8080")
	http.ListenAndServe(":8080", nil)
}
```
