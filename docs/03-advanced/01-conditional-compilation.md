# Conditional Compilation in Go

We can run different code based on the operating system or architecture using **conditional compilation**.

## Running on Different Operating Systems

```md
my_project/
├── main.go
└── helper_darwin.go
└── helper_linux.go
```

If we can a function that is defined in both `helper_darwin.go` and `helper_linux.go`, and we run the program on a different operating system, the function will be called from the correct file.

---

## Running on Different Architectures

Go provides us with build tags to run different code based on the architecture.

```md
my_project/
├── main.go
└── helper_mac_m1.go
└── helper_mac_intel.go
```

- `helper_mac_m1.go` file

```go
//+build darwin,arm64

package main

import "fmt"

func checker_func(){
	fmt.Println("running on mac silicon")
}
```

- `helper_mac_intel.go` file

```go
//+build darwin,!arm64

package main

import "fmt"

func checker_func(){
	fmt.Println("running on mac intel")
}
```

---

## New `build tag`

Many new codebases also use `build tag` to run different code based on the operating system or architecture.

```go
//go:build darwin && arm64

package main

import "fmt"

func checker_func(){
	fmt.Println("running on mac silicon")
}
```
