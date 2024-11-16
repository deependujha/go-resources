# Maps in Go

## What is a Map?

A map is a collection of key-value pairs where each key is unique, and you can retrieve a value using its key.

!!! secondary "What about sets in Go?"
    Go doesn’t have a built-in set type. You can use a map with a value of `nil/boolean` to simulate a set.

---

## Declaring and Initializing Maps

### Declaring a Map

```go
var m map[string]int // Declares a map without initializing
fmt.Println(m)       // Output: map[]
fmt.Println(m == nil) // Output: true
```

### Initializing a Map

```go
// Using make
m := make(map[string]int)
fmt.Println(m) // Output: map[]

// Declaring and initializing in one step
m2 := map[string]int{
    "Alice": 25,
    "Bob":   30,
}
fmt.Println(m2) // Output: map[Alice:25 Bob:30]
```

---

## Basic Operations on Maps

### Adding or Updating Elements

```go
m := make(map[string]int)
m["Alice"] = 25 // Add a key-value pair
m["Bob"] = 30   // Add another pair
m["Alice"] = 26 // Update the value for the key "Alice"
fmt.Println(m)  // Output: map[Alice:26 Bob:30]
```

### Accessing Values

```go
age := m["Alice"]
fmt.Println(age) // Output: 26
```

### Checking if a Key Exists

```go
age, exists := m["Charlie"]
if exists {
    fmt.Println("Charlie’s age:", age)
} else {
    fmt.Println("Charlie not found") // Output: Charlie not found
}
```

### Deleting a Key

```go
delete(m, "Alice")
fmt.Println(m) // Output: map[Bob:30]
```

---

## Iterating Over a Map

```go
for key, value := range m {
    fmt.Printf("%s: %d\n", key, value)
}
```

---

## Length of a Map

```go
fmt.Println(len(m)) // Output: Number of key-value pairs
```

---

## Maps as Function Arguments

Maps are reference types, so passing them to a function doesn’t copy the map; it passes a reference.

```go
func updateMap(m map[string]int) {
    m["Bob"] = 35
}

m := map[string]int{"Bob": 30}
updateMap(m)
fmt.Println(m["Bob"]) // Output: 35
```

---

## Maps of Structs

```go
type Person struct {
    Name string
    Age  int
}

people := map[string]Person{
    "Alice": {Name: "Alice", Age: 25},
    "Bob":   {Name: "Bob", Age: 30},
}
fmt.Println(people["Alice"]) // Output: {Alice 25}
```

---

## Key Points

1. **Maps are reference types** and are automatically garbage collected.
2. Use `make` to initialize maps. Uninitialized maps will cause a runtime panic on writes.
3. Keys must be of a type that is comparable (e.g., numbers, strings, structs without slices or maps).
4. Maps are not safe for concurrent use; use synchronization primitives like `sync.Mutex` or `sync.Map` for concurrent scenarios.
