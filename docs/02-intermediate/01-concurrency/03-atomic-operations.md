# **Why Use Atomic Operations?**

Atomic operations provide:

- **Thread-safety** without needing explicit locks (`sync.Mutex`), improving performance.
- **Low-level primitives** to manipulate memory directly, ensuring correctness in concurrent environments.
- **Efficient updates** to variables, avoiding the overhead of context switching caused by locking.

---

### **Commonly Used Atomic Functions**

Here are some key atomic functions provided by the `sync/atomic` package:

1. **Atomic Load**: Safely reads a value.
   - `LoadInt32`, `LoadInt64`, `LoadUint32`, `LoadUint64`, `LoadPointer`

2. **Atomic Store**: Safely writes a value.
   - `StoreInt32`, `StoreInt64`, `StoreUint32`, `StoreUint64`, `StorePointer`

3. **Atomic Swap**: Replaces a value atomically and returns the old value.
   - `SwapInt32`, `SwapInt64`, `SwapUint32`, `SwapUint64`, `SwapPointer`

4. **Atomic Add**: Safely increments or decrements a value.
   - `AddInt32`, `AddInt64`, `AddUint32`, `AddUint64`

5. **Compare-And-Swap (CAS)**: Compares a value and swaps it only if it matches the expected value.
   - `CompareAndSwapInt32`, `CompareAndSwapInt64`, `CompareAndSwapUint32`, `CompareAndSwapUint64`, `CompareAndSwapPointer`

---

### **Examples of Atomic Operations**

#### **1. Atomic Counter**
Incrementing a shared counter across multiple goroutines safely:

```go
package main

import (
	"fmt"
	"sync"
	"sync/atomic"
)

func main() {
	var counter int64 // Shared counter
	var wg sync.WaitGroup

	// Launch 10 goroutines, each incrementing the counter 100 times
	for i := 0; i < 10; i++ {
		wg.Add(1)
		go func() {
			defer wg.Done()
			for j := 0; j < 100; j++ {
				atomic.AddInt64(&counter, 1)
			}
		}()
	}

	wg.Wait()
	fmt.Println("Final Counter:", counter) // Should print 1000
}
```

---

#### **2. Compare-And-Swap (CAS)**
Ensures atomicity when setting a variable conditionally:

```go
package main

import (
	"fmt"
	"sync/atomic"
)

func main() {
	var value int32 = 42

	// Compare and swap value only if it matches expected value
	swapped := atomic.CompareAndSwapInt32(&value, 42, 100)
	fmt.Println("Swapped:", swapped)  // true
	fmt.Println("Value:", value)      // 100

	// Fails because the current value is no longer 42
	swapped = atomic.CompareAndSwapInt32(&value, 42, 200)
	fmt.Println("Swapped:", swapped)  // false
	fmt.Println("Value:", value)      // 100
}
```

---

#### **3. Atomic Swap**
Replace a value and retrieve the old one atomically:

```go
package main

import (
	"fmt"
	"sync/atomic"
)

func main() {
	var value int64 = 10

	oldValue := atomic.SwapInt64(&value, 50)
	fmt.Println("Old Value:", oldValue) // 10
	fmt.Println("New Value:", value)    // 50
}
```

---

#### **4. Atomic Load and Store**
Safely read and write shared variables:

```go
package main

import (
	"fmt"
	"sync/atomic"
)

func main() {
	var value int64 = 42

	// Load the current value
	current := atomic.LoadInt64(&value)
	fmt.Println("Loaded Value:", current) // 42

	// Store a new value
	atomic.StoreInt64(&value, 99)
	fmt.Println("Updated Value:", value)  // 99
}
```

---

### **When to Use Atomic Operations**
1. **Simple Counters or Flags**: Use atomic operations for counters, toggles, or status flags in a lightweight way.
2. **Performance-Sensitive Scenarios**: If locks are slowing down performance, atomic operations provide a fast, lock-free alternative.
3. **Shared State Updates**: Safely update shared data between goroutines when full synchronization is not needed.

---

### **When NOT to Use Atomic Operations**
1. **Complex Data Structures**: Atomic operations work only on individual variables. For more complex data, consider using `sync.Mutex` or `sync.RWMutex`.
2. **Read-Heavy Operations**: Use `sync.RWMutex` if reads significantly outweigh writes, as atomic operations don’t optimize for reads.

---

### **Advanced Use Cases**
1. **Atomic Value**: Use `sync/atomic.Value` to store and load any value (not just numeric types) safely.
   - Example:
   ```go
   package main

   import (
       "fmt"
       "sync/atomic"
   )

   func main() {
       var value atomic.Value

       value.Store("Hello, Go!")
       fmt.Println("Loaded Value:", value.Load()) // Hello, Go!
   }
   ```

2. **Building Lock-Free Data Structures**: Atomic primitives are the building blocks for lock-free data structures, like queues or stacks, but they require deeper understanding and careful design.

---

### Summary

!!! quote ""
    - Atomic operations in Go are lightweight, efficient, and ideal for managing simple shared states in concurrent programs.
    - While they are not a replacement for higher-level synchronization primitives (like mutexes), they are indispensable for performance-critical or low-level concurrency tasks.
    - Mastering these will strengthen your understanding of how Go handles memory and synchronization under the hood.
