# Concurrency in Go

## What is Concurrency?

Concurrency refers to the ability of a program to handle multiple tasks at the same time. In Go, this is primarily achieved using **goroutines** and **channels**.

---

### **1. Goroutines**

Goroutines are lightweight threads managed by the Go runtime. They are fundamental to concurrency in Go.

- **How to create a Goroutine:**

A goroutine is created using the `go` keyword followed by a function call. 

```go
go myFunction() // Runs concurrently with the rest of the program
```

```go
package main

import (
    "fmt"
    "time"
)

func printNumbers() {
    for i := 1; i <= 5; i++ {
        fmt.Println(i)
        time.Sleep(time.Second)
    }
}

func main() {
    go printNumbers() // Start the function in a goroutine

    // Wait for goroutine to finish, 
    //else it will exit and thread will be killed
    time.Sleep(6 * time.Second) 
}
```

---

### **2. WaitGroup**

A `sync.WaitGroup` is used to wait for a collection of goroutines to finish executing.

```go
package main

import (
    "fmt"
    "sync"
)

func printMessage(i int, wg *sync.WaitGroup) {
    defer wg.Done()
    fmt.Printf("Message %d\n", i)
}

func main() {
    var wg sync.WaitGroup

    // Start 5 goroutines
    for i := 1; i <= 5; i++ {
        wg.Add(1)
        go printMessage(i, &wg)
    }

    // Wait for all goroutines to finish
    wg.Wait()
}
```

---

### **3. Channels**

Channels are used for communication between goroutines. They allow data to be passed between concurrent tasks safely.

- Once msg has been read from channel, it will be garbage collected.
- If multiple goroutines are reading from the same channel, it can be read by any one of them only.

- **Declaring a channel**:

```go
ch := make(chan int) // Unbuffered channel for integers
```

- **Sending data to a channel**:

```go
ch <- 42 // Send 42 to channel
```

- **Receiving data from a channel**:

```go
value := <-ch // Receive data from channel
```

```go
package main

import "fmt"

func sendData(ch chan int) { // running in goroutine
    ch <- 42
}

func main() {
    ch := make(chan int)
    go sendData(ch) // Run sendData in a goroutine
    receivedValue := <-ch // Receive value from channel (blocking)
    fmt.Println(receivedValue) // Prints: 42
}
```

- **Buffered Channels**:

- A buffered channel allows for asynchronous sending and receiving.
- Channels with a buffer size can hold a specified number of elements before blocking.
- Even `goroutine` get blocked when channel is full (buffered channel)/ channel value is not read (in case of unbuffered channel)

```go
ch := make(chan int, 2) // Create a buffered channel with capacity of 2
ch <- 1
ch <- 2
fmt.Println(<-ch) // Prints 1
fmt.Println(<-ch) // Prints 2
```

---

### **4. Select Statement**

The `select` statement allows you to wait on multiple channel operations. It’s like a `switch` but for channels.

```go
package main

import (
    "fmt"
    "time"
)

func sendData(ch chan string) {
    time.Sleep(time.Second)
    ch <- "Hello from goroutine"
}

func main() {
    ch := make(chan string)
    go sendData(ch) // Run sendData in a goroutine

    select {
    case msg := <-ch:
        fmt.Println(msg) // Prints: Hello from goroutine
    case <-time.After(2 * time.Second):
        fmt.Println("Timeout")
    }
}
```

A better example, infinitely waiting for response from multiple channels, and if any time `quit channel` sends a message, it will exit the program.

```go
package main

import "fmt"

func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c <- x:
			x, y = y, x+y
		case <-quit:
			fmt.Println("quit")
			return
		}
	}
}

func main() {
	c := make(chan int)
	quit := make(chan int)
	go func() {
		for i := 0; i < 10; i++ {
			fmt.Println(<-c)
		}
		quit <- 0
	}()
	fibonacci(c, quit)
}
```

---

### **5. Worker Pool**

A worker pool is a pattern where you create a fixed number of workers (goroutines) that process tasks from a shared channel.

**Steps**:

- Create a channel for tasks.
- Start a fixed number of worker goroutines.
- Workers listen on the task channel and process the tasks.

!!! info "Keep in mind"
    - Once msg has been read from channel, it will be garbage collected.
    - If multiple goroutines are reading from the same channel, it can be read by any one of them only.

```go
package main

import "fmt"

// Task to be processed
type Task struct {
    id int
}

// Worker function
func worker(id int, tasks chan Task, done chan bool) {
    for task := range tasks { // this range will keep reading from channel until it is closed
        fmt.Printf("Worker %d is processing task %d\n", id, task.id)
    }
    done <- true
}

func main() {
    tasks := make(chan Task, 10)
    done := make(chan bool)

    // Create 3 workers
    for i := 1; i <= 3; i++ {
        go worker(i, tasks, done)
    }

    // Send tasks to workers
    for i := 1; i <= 5; i++ {
        tasks <- Task{id: i}
    }

    // Close task channel
    close(tasks)

    // Wait for workers to finish
    for i := 1; i <= 3; i++ {
        <-done
    }
}
```

---

### **6. Mutex (Mutual Exclusion)**

Mutexes are used for synchronizing access to shared resources to avoid race conditions.

```go
package main

import (
    "fmt"
    "sync"
)

var counter int
var mu sync.Mutex

func increment() {
    mu.Lock()   // Lock the mutex
    counter++   // Critical section
    mu.Unlock() // Unlock the mutex
}

func main() {
    for i := 0; i < 1000; i++ {
        go increment()
    }

    // Wait before printing the counter value
    fmt.Println(counter) // Prints the final value of counter
}
```

---

### Summary of Concurrency Topics

- **Goroutines**: Lightweight threads managed by the Go runtime.
- **Channels**: Communication between goroutines.
- **Select**: Wait on multiple channel operations.
- **Worker Pool**: Using multiple goroutines to process tasks.
- **Mutexes**: Synchronization primitives for mutual exclusion.
- **WaitGroup**: Wait for multiple goroutines to finish.
