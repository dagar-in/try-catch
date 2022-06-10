# Try | Catch | Finally in Go

Experiment in Golang that tries to bring the exception behavior of Java/Python/C++ to Golang using the same syntax.  

## Approach

1. We need `Try`, `Catch` and `Finally` methods.
2. We need a `Throw()` method for re-throwing exceptions.
3. It needs to be stateless so it could be nested and used across many threads.  

## API examples  

---

### 1. Simple `panic()` inside `Try`  

Unfortunately we have to include a `Finally` before a `Catch`. I have tried to find a way to avoid it, but looks impossible. Anyway, the behavior and order of call is exactly the same than Java or Python.  

```go
import (
 "fmt"
 "github.com/dagar-in/try-catch"
)

func main() {
 try.This(func() {
  panic("my panic")

 }).Finally(func() {
  fmt.Println("this must be printed after the catch")

 }).Catch(func(e try.E) {
  // Print crash
  fmt.Println(e)
 })
}
```  

#### 2. `Finally` is optional  

```go
import (
 "fmt"
 "github.com/dagar-in/try-catch"
)

func main() {
 var obj interface{}
 obj = 2
 try.This(func() {
  // this operation will panic because obj is an integer
  text := obj.(string)
  fmt.Println(text)

 }).Catch(func(e try.E) {
  // Print crash
  fmt.Println(e)
 })
}
```  

#### 3. Rethrowing  

```go
import (
 "fmt"
 "github.com/dagar-in/try-catch"
)

func main() {
 try.This(func() {
  panic("my panic")

 }).Finally(func() {
  fmt.Println("this must be printed after the catch")

 }).Catch(func(_ try.E) {
  fmt.Println("exception catched") // print
  try.Throw()                      // rethrow current exception!!
 })
}
```  

#### 4. Nested  

```go
package main

import (
 "fmt"
 "github.com/dagar-in/try-catch"
)

func main() {
 try.This(func() {
  try.This(func() {
   panic("my panic")

  }).Catch(func(e try.E) {
   fmt.Println("fixing stuff") // print
   try.Throw()                 // rethrow current exception!!
  })

 }).Catch(func(e try.E) {
  // print
  fmt.Println(e)
 })
 fmt.Println("hey")
}
```  

Response  

```
fixing stuff
my panic
hey
```  
