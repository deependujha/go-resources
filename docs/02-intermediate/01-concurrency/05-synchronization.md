### **Synchronization in Go**

Synchronization ensures that goroutines coordinate properly and avoid conflicts, especially when accessing shared resources. Go provides multiple mechanisms for synchronization:

---

### **1. sync.Mutex**
`sync.Mutex` is used to lock and unlock critical sections to ensure only one goroutine accesses shared data at a time.

#### **Example: Mutex for Shared Counter**
```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var mu sync.Mutex
	counter := 0

	var wg sync.WaitGroup
	const numGoroutines = 5

	for i := 0; i < numGoroutines; i++ {
		wg.Add(1)
		go func(id int) {
			defer wg.Done()
			mu.Lock()
			counter++
			fmt.Printf("Goroutine %d incremented counter to %d\n", id, counter)
			mu.Unlock()
		}(i)
	}

	wg.Wait()
	fmt.Println("Final Counter:", counter)
}
```

---

### **2. sync.RWMutex**
`sync.RWMutex` provides separate locks for reading and writing:
- Multiple readers can acquire the lock simultaneously.
- Only one writer can acquire the lock, blocking readers and other writers.

#### **Example: RWMutex for Read and Write**
```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var mu sync.RWMutex
	data := make(map[int]string)

	var wg sync.WaitGroup

	// Writer
	wg.Add(1)
	go func() {
		defer wg.Done()
		mu.Lock()
		data[1] = "GoLang"
		fmt.Println("Write: Added GoLang")
		mu.Unlock()
	}()

	// Reader
	wg.Add(1)
	go func() {
		defer wg.Done()
		mu.RLock()
		fmt.Println("Read: Value for key 1 is", data[1])
		mu.RUnlock()
	}()

	wg.Wait()
}
```

---

### **3. sync.WaitGroup**
`sync.WaitGroup` waits for multiple goroutines to complete.

#### **Example: Using WaitGroup**
```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func worker(id int, wg *sync.WaitGroup) {
	defer wg.Done() // Decrement counter when goroutine finishes
	fmt.Printf("Worker %d starting\n", id)
	time.Sleep(time.Second) // Simulate work
	fmt.Printf("Worker %d done\n", id)
}

func main() {
	var wg sync.WaitGroup

	for i := 1; i <= 3; i++ {
		wg.Add(1) // Increment counter for each goroutine
		go worker(i, &wg)
	}

	wg.Wait() // Block until all workers are done
	fmt.Println("All workers finished")
}
```

---

### **4. sync.Cond**
`sync.Cond` allows goroutines to signal each other. Useful for scenarios like producer-consumer.

#### **Example: Using sync.Cond**
```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	var mu sync.Mutex
	cond := sync.NewCond(&mu)
	queue := make([]int, 0)

	const maxQueueSize = 3

	// Consumer
	go func() {
		for {
			mu.Lock()
			for len(queue) == 0 {
				cond.Wait() // Wait until there's something to consume
			}
			item := queue[0]
			queue = queue[1:]
			fmt.Println("Consumed:", item)
			cond.Signal() // Notify producers
			mu.Unlock()
		}
	}()

	// Producer
	go func() {
		for i := 1; i <= 5; i++ {
			mu.Lock()
			for len(queue) >= maxQueueSize {
				cond.Wait() // Wait until there's space
			}
			queue = append(queue, i)
			fmt.Println("Produced:", i)
			cond.Signal() // Notify consumers
			mu.Unlock()
			time.Sleep(time.Millisecond * 500)
		}
	}()

	// Let the program run for a while
	time.Sleep(time.Second * 5)
}
```

---

### **5. sync.Once**
`sync.Once` ensures that a piece of code runs only once, no matter how many goroutines invoke it.

#### **Example: Using sync.Once**
```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var once sync.Once

	initFunc := func() {
		fmt.Println("Initialization done")
	}

	for i := 0; i < 3; i++ {
		go func(id int) {
			fmt.Printf("Goroutine %d calling init\n", id)
			once.Do(initFunc) // Only the first call runs initFunc
		}(i)
	}

	// Wait for goroutines to complete
	time.Sleep(time.Second)
}
```

---

### **6. Atomic Operations**
The `sync/atomic` package provides low-level primitives for atomic operations like incrementing or swapping values without locks.

#### **Example: Atomic Counter**
```go
package main

import (
	"fmt"
	"sync/atomic"
)

func main() {
	var counter int64

	const numGoroutines = 10
	done := make(chan bool, numGoroutines)

	for i := 0; i < numGoroutines; i++ {
		go func() {
			for j := 0; j < 1000; j++ {
				atomic.AddInt64(&counter, 1) // Atomically increment counter
			}
			done <- true
		}()
	}

	for i := 0; i < numGoroutines; i++ {
		<-done
	}

	fmt.Println("Final Counter:", counter)
}
```

---

### **7. Channel-Based Synchronization**
Channels are a high-level and idiomatic way to synchronize goroutines.

#### **Example: Using Channels for Synchronization**
```go
package main

import "fmt"

func worker(done chan bool) {
	fmt.Println("Working...")
	done <- true // Notify main goroutine
}

func main() {
	done := make(chan bool)
	go worker(done)

	<-done // Wait for worker to finish
	fmt.Println("Worker finished")
}
```

---

### **When to Use What**
| **Scenario**                     | **Synchronization Mechanism**    |
|-----------------------------------|----------------------------------|
| Mutual exclusion (read/write)     | `sync.Mutex`, `sync.RWMutex`     |
| Waiting for multiple goroutines   | `sync.WaitGroup`                 |
| Triggering once-only execution    | `sync.Once`                      |
| Fine-grained atomic updates       | `sync/atomic`                    |
| Coordinating producer/consumer    | `sync.Cond`, Channels            |

---

### **Key Takeaways**
- Use **channels** for idiomatic Go synchronization.
- Use **mutexes** for critical sections when channels are not suitable.
- Avoid over-synchronizing; keep designs simple and focused on performance.
