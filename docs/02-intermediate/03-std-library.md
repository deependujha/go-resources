# Go Standard Library

### **1. os (File Operations, Environment Variables)**

**File Operations:**

- `os.Open(name string) (*os.File, error)` – Opens a file for reading.
- `os.Create(name string) (*os.File, error)` – Creates a new file for writing.
- `file.Close()` – Closes the opened file.
- `ioutil.ReadFile(filename string) ([]byte, error)` – Reads the entire content of the file into a byte slice.
- `os.Remove(name string) error` – Deletes the specified file.
- `os.Stat(name string) (os.FileInfo, error)` – Retrieves file information like size, permissions, etc.

**Environment Variables:**

- `os.Getenv(key string) string` – Gets the value of the environment variable.
- `os.Setenv(key, value string) error` – Sets the value of the environment variable.
- `os.Environ()` – Returns a list of all environment variables.

---

### **2. net/http (Building HTTP Servers, Clients)**

**HTTP Server:**

- `http.HandleFunc(pattern string, handler func(http.ResponseWriter, *http.Request))` – Register a handler function for the given pattern.
- `http.ListenAndServe(addr string, handler http.Handler) error` – Starts an HTTP server at the specified address.
- Example:
  ```go
  http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello, world!")
  })
  log.Fatal(http.ListenAndServe(":8080", nil))
  ```

**HTTP Client:**

- `http.Get(url string) (*http.Response, error)` – Sends a GET request.
- `http.Post(url, contentType string, body io.Reader) (*http.Response, error)` – Sends a POST request.
- `http.NewRequest(method, url string, body io.Reader) (*http.Request, error)` – Creates a new HTTP request (useful for custom headers, etc.).
- Example:
  ```go
  resp, err := http.Get("https://example.com")
  if err != nil {
      log.Fatal(err)
  }
  defer resp.Body.Close()
  ```

---

### **3. encoding/json (JSON Marshalling/Unmarshalling)**

**Marshalling (Go struct to JSON):**

- `json.Marshal(v interface{}) ([]byte, error)` – Converts Go values to JSON.
- Example:
  ```go
  person := struct {
      Name string
      Age  int
  }{"Alice", 30}
  jsonData, _ := json.Marshal(person)
  ```

**Unmarshalling (JSON to Go struct):**

- `json.Unmarshal(data []byte, v interface{}) error` – Converts JSON back to Go values.
- Example:
  ```go
  jsonStr := `{"Name":"Alice","Age":30}`
  var person struct {
      Name string
      Age  int
  }
  json.Unmarshal([]byte(jsonStr), &person)
  ```

---

### **4. strconv (Type Conversions)**

**String to Int/Float:**

- `strconv.Atoi(s string) (int, error)` – Converts string to integer.
- `strconv.ParseInt(s string, base, bitSize int) (int64, error)` – Converts string to integer with specified base and bit size.
- `strconv.ParseFloat(s string, bitSize int) (float64, error)` – Converts string to float.
- Example:
  ```go
  num, _ := strconv.Atoi("42")
  ```

**Int/Float to String:**

- `strconv.Itoa(i int) string` – Converts integer to string.
- `strconv.FormatFloat(f float64, fmt byte, prec, bitSize int) string` – Converts float to string.
- Example:
  ```go
  numStr := strconv.Itoa(42)
  ```

---

### **5. time (Time Manipulation)**

**Current Time:**

- `time.Now()` – Returns the current local time.
- Example:
  ```go
  t := time.Now()
  fmt.Println(t)
  ```

**Formatting Time:**

- `t.Format(layout string) string` – Formats the time based on layout.
- Layout example: `2006-01-02 15:04:05`
- Example:
  ```go
  fmt.Println(t.Format("2006-01-02 15:04:05"))
  ```

**Sleeping:**

- `time.Sleep(duration time.Duration)` – Pauses execution for the specified duration.
- Example:
  ```go
  time.Sleep(2 * time.Second)
  ```

**Time Parsing:**

- `time.Parse(layout, value string) (Time, error)` – Parses a string into time.
- Example:
  ```go
  parsedTime, _ := time.Parse("2006-01-02", "2024-11-17")
  ```

**Duration:**

- `time.Duration` – Represents elapsed time.
- `time.Second`, `time.Minute` etc., are constants representing durations.
