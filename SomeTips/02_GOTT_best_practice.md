# 1. GOTT(Golang on the Toilet)

- [1. GOTT(Golang on the Toilet)](#1-gottgolang-on-the-toilet)
  - [1.1. Readable](#11-readable)
    - [1.1.1. if else and happy path](#111-if-else-and-happy-path)
      - [1.1.1.1. avoid else return](#1111-avoid-else-return)
      - [1.1.1.2. Keep happy path unindented](#1112-keep-happy-path-unindented)
      - [1.1.1.3. Remove unnecessary else](#1113-remove-unnecessary-else)
    - [1.1.2. init()](#112-init)
      - [1.1.2.1. what is init()](#1121-what-is-init)
      - [1.1.2.2. Downside of init()](#1122-downside-of-init)
      - [1.1.2.3. Good practices](#1123-good-practices)
      - [1.1.2.4. when init() is required](#1124-when-init-is-required)
    - [1.1.3. comments](#113-comments)
  - [1.2. Robust](#12-robust)
    - [1.2.1. panic](#121-panic)
    - [1.2.2. error](#122-error)
  - [1.3. Efficient](#13-efficient)
    - [1.3.1. Pointer](#131-pointer)
    - [1.3.2. Deprecation](#132-deprecation)

## 1.1. Readable

### 1.1.1. if else and happy path

#### 1.1.1.1. avoid else return

```golang
// Bad example
func absoluteNumber(x int) int {
    if x >=0 {
        return x
    } else {
        return -x
    }

}
```

#### 1.1.1.2. Keep happy path unindented

Happy path is the route that execution will take if everything goes well

```golang
// good example
func aFunc() error {
    if err := doSomething(); err != nil {
        return err
    }
    if err := doAnotherthing(); err != nil {
        return err
    }
    return nul  // happy path
}
```

#### 1.1.1.3. Remove unnecessary else

```golang
// bad example
var a int 
if flag {
    a = 1
} else {
    a = -1
}

// good example
a := -1
if flag {
    a = 1
}
```

### 1.1.2. init()

#### 1.1.2.1. what is init()

* init() is a pre-defined function.
* init() containsa piece of code whichrunsbefore most things.
* In detail, execution order:
  * 1.Initialize all the imported packages;
  * 2.Initialize all the variable declarations in thispackage;
  * 3.Run init().

#### 1.1.2.2. Downside of init()

* init() makescode less readable.
* init() brings unexpected side-effect when import a package.

#### 1.1.2.3. Good practices

```golang
// Declare public initialization functions, and let function caller to explicitly initialize variables in their code

// client.go
package client
func Init() {
    Client = buildNewClient()
}

// server.go
func main() {
    client.Init()
}
```

```golang
var _default = createVar()

func createVar() {
    return MyVar{...}
}
```

#### 1.1.2.4. when init() is required

* Be completely deterministic
* Avoid accessing or manipulating global or environmental state
* Avoid depending on the ordering of other init() functions
* Avoid I/O calls

### 1.1.3. comments

* Comment is critical to code readability
* Block comment`/* */`(for big comments, and magic parameters).
* Line comments `//` (for small comments).
* The user needs to understand what the package, function does instead of how it is implemented. 
* So the comment should focus on WHAT instead of HOW.

## 1.2. Robust

### 1.2.1. panic

Panic is a runtime exception, and we can trigger a panic by calling panic()function.

When the panic keyword is encountered, execution order:
    1.Execution for the current function stops.
    2.Any function called with defer keyword is executed.
    3.The program execution is terminated.

Normally, there are two use cases for panic:
    1.An unrecoverable error where the application cannot simply continue its execution.
    2.A developer error.

About handle panic:
    * Use "defer" function with "recover" logic.
    * Put the "defer"function at the beginning of the target function. 
    * Multiple "defer" are executed in the reverse order of declaration.

### 1.2.2. error

```golang
// Error is just an interface with a single method
type error interface {
    Error() string
}
```

Sometimes it's useful to have more than just a string in error in structured form.
To create a custom error we need a structure which implements `Error()` and `Unwrap()`

```golang
type QueryError struct {
    Query string
    Err error
}

func (e *QueryError) Error() string { return e.Query + ": " + e.Err.Error() }

func (e *QueryError) Unwrap() error { return e.Err }
```

Add context to make debug easier

```golang
func foo() error {
    var err error
    ...
    if err != nil {
        return fmt.Errof("context: %w", err)
    }
    return nil
}
```

Check if errors match

```golang
import io

func main() {
    err := io.EOF
    fmt.Println(errors.Is(err, io.EOF)) // true
    err = fmt.Errorf("context: %w", io.EOF)
    fmt.Println(errors.Is(err, io.EOF)) // true
}
```

Casting

```go
func main() {
    _, err := os.Open("non-existing")
    var pathError *fs.PathError
    fmr.Println(errors.As(err, &pathError)) // true
    
    err2 := fmt.Errorf("context: %w", err)
    fmr.Println(errors.As(err2, &pathError)) // tru
}
```

## 1.3. Efficient

### 1.3.1. Pointer

Pointer is just a variable that holds an address of another variable.

When to use pointer:
    * Themethod/functionmodifiesits receiver/arguments.
    * Avery large amount of data.
    * If all other methods are using pointer receiver (code consistency).

When not to use pointer:
    * point to an interface.
    * The method/function does not modifyits receiver/arguments.
    * Asmall amount of data.

* By default Go passes struct by value.
* Do not take the address of a loop variable directly.

```go
// Bad
func main() {
    x := map[int]string{1: "a", 2: "b", 3: "c"}
    r := make([]*string, 0, len(x))
    for _, v := range x {
        r = append(r, &v)
    }

    // Print out c, c, c
    for _, v := range r {
        fmt.Println(*v)
    }
}
```

```go
// Good
func main() {
    x := map[int]string{1: "a", 2: "b", 3: "c"}
    r := make([]*string, 0, len(x))
    for _, v := range x {
        c :=v
        r = append(r, &c)
    }

    // Print out a, b, c
    for _, v := range r {
        fmt.Println(*v)
    }
}
```

### 1.3.2. Deprecation

Code deprecationis to use new code to replace old code.

* Add unit test for the deprecated function. 
* Call the new function in the deprecated function to ensure the behaviors are the same.

* Start the deprecation mark with "Deprecated:"
* Provide detailed migration information.
* Add a blank line before the "Deprecated:".

```go
// LegacyFunc does some amazing things, but no one could understand
//
// Deprecated: use CoolFunc instead

func LegacyFunc() {}
```
