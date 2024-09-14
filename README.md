# Go resources

## Basic Go features

<details>
  <summary>Go pass arguments to file</summary>

```go
for i, val := range os.Args{
        fmt.Println!("i", i, "; val", val)
    }
```
</details>

<details>
  <summary>Yield in go</summary>

```go
func YieldFunction() <-chan int {
    ch := make(chan int)
    go func() {
        defer close(ch)
        for i := 0; i < 10; i++ {
            ch <- i // Yield data to the consumer
        }
    }()
    return ch
}

func main() {
    data := YieldFunction()
    for val := range data {
        // Process data concurrently
        fmt.Println(val)
    }
}
```
</details>

---

## Cool CLI projects

- [Melkey Go Blueprint](https://github.com/Melkeydev/go-blueprint)

---

## libraries

- [Go dotenv](https://pkg.go.dev/github.com/lpernett/godotenv)
- [Make http server or request endpoint (CRUD) to external URL](https://pkg.go.dev/net/http)
- [URL parsing](https://pkg.go.dev/net/url)
- [Handling JSON in Go](https://pkg.go.dev/encoding/json)
- 

---

## CLI

- [Cobra](https://github.com/spf13/cobra) - [User guide](https://github.com/spf13/cobra/blob/main/site/content/user_guide.md)
- [Cobra CLI](https://github.com/spf13/cobra-cli/tree/main)
- [Viper (read cfg files)](https://github.com/spf13/viper)
- [Tview (CLI UI)](https://github.com/rivo/tview)
- [Physics based animation in CLI](https://github.com/charmbracelet/harmonica/tree/master)
- [CLI UI components](https://github.com/charmbracelet/bubbles)
- [Bubble tea](https://github.com/charmbracelet/bubbletea)
- [LipGloss](https://github.com/charmbracelet/lipgloss)

---

## Publish Go package (cli/ library)

- [Publish a module docs](https://go.dev/doc/modules/publishing)
- [Deep-Go repository (publish go pkg with CI/CD)](https://github.com/deependujha/deep-go)
