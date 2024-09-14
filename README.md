# Go resources

## Basic Go features

- Go pass arguments to file

```go
for i, val := range os.Args{
        fmt.Println!("i", i, "; val", val)
    }
```

--

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
