# Arrays and Slices in Go

## Arrays

An array in Go is a fixed-size collection of elements of the same type.

### Declaring and Initializing Arrays

```go
var arr [5]int // Declares an array of size 5 with zero values
fmt.Println(arr) // Output: [0 0 0 0 0]

arr[0] = 10 // Assign value to an index
fmt.Println(arr[0]) // Output: 10

// Declare and initialize an array in one step
arr2 := [3]string{"Go", "Python", "Java"}
fmt.Println(arr2) // Output: [Go Python Java]

// Use `...` to let the compiler infer the size
arr3 := [...]int{1, 2, 3, 4}
fmt.Println(arr3) // Output: [1 2 3 4]
```

---

## Slices

A slice is a dynamically-sized, more flexible version of an array. It's a view into an underlying array.

### Declaring and Initializing Slices

```go
// Create a slice directly
slice := []int{1, 2, 3, 4}
fmt.Println(slice) // Output: [1 2 3 4]

// Create a slice from an array
arr := [5]int{10, 20, 30, 40, 50}
slice2 := arr[1:4] // Slices from index 1 to 3 (4 is excluded)
fmt.Println(slice2) // Output: [20 30 40]

// Create a slice with `make`
slice3 := make([]int, 3) // Creates a slice of length 3
fmt.Println(slice3) // Output: [0 0 0]
```

---

## Slice Operations

### Append Elements to a Slice

```go
slice := []int{1, 2, 3}
slice = append(slice, 4, 5)
fmt.Println(slice) // Output: [1 2 3 4 5]
```

### Copy a Slice

```go
src := []int{1, 2, 3}
dest := make([]int, len(src))
copy(dest, src)
fmt.Println(dest) // Output: [1 2 3]
```

### Reslicing

```go
slice := []int{1, 2, 3, 4, 5}
reslice := slice[1:4]
fmt.Println(reslice) // Output: [2 3 4]
```

---

## Useful Built-in Functions

### `len`

Returns the length of an array or slice.

```go
slice := []int{1, 2, 3, 4}
fmt.Println(len(slice)) // Output: 4
```

### `cap`

Returns the capacity of a slice (the maximum number of elements it can hold without reallocation).

```go
slice := make([]int, 3, 5) // Length 3, Capacity 5
fmt.Println(cap(slice)) // Output: 5
```

---

## Multidimensional Arrays and Slices

### Arrays

```go
matrix := [2][2]int{{1, 2}, {3, 4}}
fmt.Println(matrix) // Output: [[1 2] [3 4]]
```

### Slices

```go
matrix := [][]int{
    {1, 2},
    {3, 4},
}
fmt.Println(matrix) // Output: [[1 2] [3 4]]
```

---

## Actual way to use 2d array in go

- There's no easy way to use 2d array in go.
- We need to create a slice of slices.

```go
package main

import "fmt"

func main() {
	my_matrix := make([][]int, 3)
	for i := range my_matrix {
		my_matrix[i] = make([]int, 3)
	}

	fill_the_matrix(my_matrix)

	// print matrix
	print_matrix(my_matrix)
}

// fill in the matrix values
func fill_the_matrix(mat [][]int) {
	r := len(mat)
	c := len(mat[0])

	for i := 0; i < r; i++ {
		for j := 0; j < c; j++ {
			mat[i][j] = r*i + j + 1
		}
	}
}

func print_matrix(mat [][]int) {
	r := len(mat)
	c := len(mat[0])

	for i := 0; i < r; i++ {
		for j := 0; j < c; j++ {
			fmt.Print(mat[i][j], " ")
		}
		fmt.Println()
	}
}
```

---

## Important points

!!! warning "Some important points"
    - Any dimensional array in go is internally a 1-D array only.
    - A slice is a descriptor for a contiguous segment of an underlying array and provides access to a numbered sequence of elements from that array.
    - So, if we modify the underlying array, the slice will reflect the change.
    - And, if we modify the slice, the underlying array will reflect the change.

    ```go
        arr := [3]int{1,2,3}
        sli := arr[1:2]

        fmt.Println("arr: ", arr) // [1 2 3]
        fmt.Println("slice: ", sli) // [2]

        sli[0] = 5
        fmt.Println("arr: ", arr) // [1 5 3]
        fmt.Println("slice: ", sli) //[5]

        arr[1] = 9
        fmt.Println("arr: ", arr) // [1 9 3]
        fmt.Println("slice: ", sli) // [9]
    ```
