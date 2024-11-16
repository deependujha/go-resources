# Create and Run Go Project

## Create a Go Project

To create a new Go project, use the `go mod init <project-name>` command.

```bash
go mod init github.com/deependujha/my-go-project
```

This style of project name is recommended for Go projects.

This will create a new directory named `my-project` with the following structure:

```md
my-project/
├── go.mod
└── main.go
```

---

## Run the Project

```bash
go run .
```

- If you use `go run main.go`, it will only run the `main.go` file. If you're importing other files, you need to use the `go run .` command. Else, use `go run main.go helper.go ...` to run multiple files.

---

## Build the Project

```bash
go build .

# --- OR ---
go build -o my-project
```

---

## Install external packages

```bash
go get github.com/someuser/ext-go-package
```

- This will install the package in the `go.mod` file and `go.sum` file.

---

## Install external go executables binaries

```bash
go install github.com/someuser/ext-go-executable
```

---

## `go.mod` vs `go.sum`

| Aspect |	go.mod | 	go.sum |
| :--- | :--- | :--- |
| Purpose |	Tracks direct dependencies and Go version.| 	Verifies and locks exact versions and hashes. |
| Editable |	Yes (manually or by Go tools).| 	No (automatically maintained by Go tools). |
| Contents |	Module names and versions.| 	Module names, versions, and their checksums. |
| Scope |	Focuses on top-level dependencies.	| Includes transitive dependencies. |

**Best Practices**

- Commit both files to version control to ensure a consistent build environment.
- Regularly run `go mod tidy` to clean up unused dependencies and sync go.sum.
- Use go.sum as a security feature—verify that dependencies haven’t been altered.

---

## `go mod tidy` command

!!! info
    - Removes Unused Dependencies:
    - Adds Missing Dependencies:
    - Updates go.sum

- `go mod tidy` is a Go command used to clean up and optimize your project's dependency files (go.mod and go.sum).
- It ensures that your module's dependency list is accurate and minimal, reflecting only the libraries and versions you truly need.

---

## `go install`

To install a local binary package, use the `go install` command.

It installs the executable binary in the `$GOPATH/bin` directory. Mostly `~/go/bin` directory.

```bash
go install
```
