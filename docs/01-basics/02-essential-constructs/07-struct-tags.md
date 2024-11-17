# Struct Tags

Struct tags are small pieces of metadata attached to fields of a struct that provide instructions to other Go code that works with the struct.

## Basic Usage

- Go struct tags are annotations that appear after the type in a Go struct declaration.
- Each tag is composed of short strings associated with some corresponding value.
- A struct tag looks like this, with the tag offset with backtick ` characters.

```go
type User struct {
	Name string `example:"name"`
}
```

- Other Go code is then capable of examining these structs and extracting the values assigned to specific keys it requests.
- Struct tags have no effect on the operation of your code without additional code that examines them.

---

## Encoding json

- Struct tags can be used to encode JSON.
- The `json` tag is used to specify the name of the field when encoding to JSON.
- It can also be used to denote the field as private, which will exclude it from encoding.
- Or, omit this field in json if the value is empty.

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"os"
	"time"
)

type User struct {
	Name          string    `json:"name"`
	Password      string    `json:"-"` // ignore private field
	PreferredFish []string  `json:"preferredFish, omitempty"` // omit if empty
	CreatedAt     time.Time `json:"createdAt"`
}

func main() {
	u := &User{
		Name:      "Sammy the Shark",
		Password:  "fisharegreat",
		CreatedAt: time.Now(),
	}

	out, err := json.MarshalIndent(u, "", "  ")
	if err != nil {
		log.Println(err)
		os.Exit(1)
	}

	fmt.Println(string(out))
}
```
