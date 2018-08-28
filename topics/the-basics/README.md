
# The Basics

## Hello World

```go
package main

import (
	"fmt"
)

func main() {
	fmt.Println("Hello, World!")
}
```

Every `.go` file must list which package it's a part of. This name is generally
the same as the repository / directory name, but for compilation to create a
binary (not just a library), you must define a file as being in `package main`,
and for that file to have a `main()` function.

The import statement is bringing in the `fmt` package from the Go standard
library, which implements functions that deal with formatted I/O.  The `main()`
function is the entry point for when the compiled binary file gets run, and it
uses the `Println` function from the `fmt` package to write to STDOUT.

## Types

Go is strongly and statically typed: all values have a specific type.

* Booleans: a boolean truth value denoted by the predeclared constants `true`
  and `false`
* Numerics: sets of integer or floating-point numbers of given sizes
* Strings: an immutable set of string values, which is a sequence of bytes
* Errors: interface technically but so important in go it's worth calling out imo
* Arrays: numbered sequence of elements of a single type; length is part of the
  type
* Slices: window view of a specific underlying array
* Maps: has table with specific types for keys and values
* Pointers: pointers to an address in memory
* Structs: a sequence of fields with a name and a type
* Channels: communication across concurrent functions by sending typed values

Booleans, numerics and strings are occasionally referred to as built-in types,
whereas arrays, slices, maps, pointers, structs and channels may be referred to
as "reference types," so-called because the "value" of a reference type is a
header value which contains a reference to the underlying data. This makes them
cheap to copy.

In the workshop today, we'll only be focussing on structs and channels, as well
as the basic types (booleans, numerics and strings).

## Basic Types

```go
b1 := true         // type is bool
b2 := false

n1 := 123          // int
n2 := 123.456      // float32/64
n3 := 1e10         // float32/64
n4 := uint8(123)   // uint
n5 := float32(123) // float32

s1 := `Raw string literal`
s2 := "Interpreted string literal"
```
## Reference Types

### Slices and Arrays
Arrays in Go are fixed length and of a single type.
Rather surprisingly for a lot of go newbies they are value type not reference type so when you pass it to a function or assign it to a new value a new copy is made.
```go
package main

import "fmt"

func main() {  
	//type and size definition are part of the array type
    a := [...]string{"USA", "China", "India", "Germany", "France"}
    b := a // a copy of a is assigned to b
    b[0] = "Singapore"
    fmt.Println("a is ", a)
    fmt.Println("b is ", b) 
}
```
Slices are pretty similar to Arrays but the size can be changed (sort of)
```go
//No size specified in the definition
var x []float64
//The above is a nil instance so we should 'make' the instance

//creates a slice with a size and capacity of 5
x := make([]float64, 5)
//creates a slice with a size 5 but capacity of 10
//slices are backed by an array and cannot exceed them
//but can be smaller
x := make([]float64, 5, 10)

```
Another way to create a slice is with the [low:high] syntax
```go
arr := [5]float64{1,2,3,4,5}
//low is the index the new slice starts from. High is not included
x := arr[0:5]
//you can omit either value if you want. The below are all equivalent
x := arr[:5]
x := arr[0:]
x := arr[:]
```

Both the slice and array can be iterated over in an easy way as below using the the range keyword
```go
for key, val := range x {
	//key for slice/array is the index
	fmt.Println(key,val)
}
```
Inbuilt functions exist to allow you to grow a slice (append) or use a copy function which allows you to shrink a function (low:high could do that too)
[Great explanation of slices and arrays](https://www.golang-book.com/books/intro/6)

### Maps
Sometimes known as a hash/hashtable or dictionary a map is an associative datatype. That means you associate a value with a unique key.

```go
//map[KeyType]ValueType where keyType is comparable 
//(not slices/maps/funcs)
//ValueType can even be another map 
var m map[string]int
//nil map behaves ok when reading but writing to above panics
//use this to initialize the object so you can write to it
m = make(map[string]int)
//setting a value
m["route"] = 66
//getting a value type 1. A missing value returns a zero value
i := m["route"]
//a 2 value assignment is available with a bool to indicate if exists
i, ok := m["route"]
//you can also range over a map like you do a slice/array
//Iteration order over a map IS NOT GUARANTEED
```
[More detail here](https://blog.golang.org/go-maps-in-action)

### Pointers
A pointer holds the memory address of a value.
The type  `*T`  is a pointer to a  `T`  value. Its zero value is  `nil`

The  `&`  operator generates a pointer to its operand.
```go
i := 42
p = &i

//we can generate a new pointer as well like this
xPtr := new(int)
```
The  `*`  operator denotes the pointer's underlying value.
```go
fmt.Println(*p) // read i through the pointer p
*p = 21         // set i through the pointer p
```
This is known as "dereferencing" or "indirecting".
Pointers give us an ability to pass by *reference* as well. A new copy of the pointer is created but it still points to same memory.


### Channels
We'll cover these in more detail further in the course
## Variable Declaration

```go
var x T         // Variable x of type T with a zero value
var x T = v     // Variable x of type T with value v
var x = v       // Variable x with value v, implicit typing

x := v          // Short variable declaration (type inferred)
x, y := v1, v2  // Double declaration (similar with var)

make(T)         // make takes a type T, which must be a slice,
                // map or channel type, optionally followed by
                // a type-specific list of expressions
```

You can also use `new(T)`, which allocates zeroed storage for a new value of
type T and returns its address.

## Zero Values

Every type has a zero value. For the Rubyists, note that a boolean, numeric or
string can never be `nil`.

```go
0     // numeric
false // boolean
""    // string
nil   // pointer, channel, func,
      // interface, map, or slice
```

## Structs

A struct is a type which contains a collection of named fields. The `type`
keyword introduces a new type. It's followed by the name of the type
(`rectangle`, in the following example), the keyword `struct` to indicate that
we are defining a struct type and a list of fields inside of curly braces. Each
field has a name and a type.

```go
type rectangle struct {
	width  int //lower case name means private variable
	height int
	Name string //this would be exported
}

r1 := rectangle{1, 2}       // New rectangle with w + h
r1.width = 3                // Set width to a new value
fmt.Printf("Width = %d; Height = %d\n", r1.width, r1.height)

var r2 rectangle            // w=0, h=0 (int zero values)
r4 := rectangle{}           // w=0, h=0
r3 := rectangle{height: 1}  // w=0, h=1
```

## Functions

A function is an independent section of code that maps zero or more input
parameters to zero or more output parameters. In Go, they are values like any
other value, and can be passed around as any other value.

```go
func f1() {}                // Simple function definition
func f2(s string, i int) {} // Function that accepts two args
func f3(s1, s2 string) {}   // Two args of the same type
func f4(s ...string) {}     // Variadic function

func f5() int {             // Return type declaration
	return 42
}

//RETURN VALUES CAN BE DISCARDED AS WELL. ALL FOLLOWING ARE VALID
//A,B := F6()
//_,B := F6() - Go won't compile if you have an unused value
//A,_ := F6()
func f6() (int, string) {   // Multiple return values
	return 42, "foo"
}

FUNC F7() (maxspeed int){      //Named return value
```

* Variadic Function: [playground][vf]

Functions are values in Go. You can set them as instance variables on objects or pass them in as arguments into functions
```go
import (
	"fmt"
	"math"
)

//accepts a func which takes two floats and returns one
//this function also returns a single float too
func compute(fn func(float64, float64) float64) float64 {
	//result of the function is returned
	return fn(3, 4)
}

func main() {
	//go offers support for anonymous functions
	hypot := func(x, y float64) float64 {
		return math.Sqrt(x*x + y*y)
	}
	fmt.Println(hypot(5, 12))
	//passing in our locale function
	fmt.Println(compute(hypot))
	//passing in an inbuilt function which matches the signature
	fmt.Println(compute(math.Pow))
}
```
 Link: [playground][fv]


## Errors

Go does exception handling differently by not handling exceptions. Rather than exceptions for things like missing file etc and a traditional try catch block style you may be used to in other languages Go uses errors. Errors are defined by any type which implements the following interface
```go
type error interface {  
    Error() string  
}
```
That means it's nilable as well so when a function returns an error type you'll often see (or have already heard this lamented) people checking the return from functions
```go
//it's expected that the error is the right most returned value
val, err := doThatImportantThing()
if err != nil {
	fmt.Println("OH SHIT. SO THIS IS HOW WE DIE")
}
```
This is mentioned in more detail by Rob Pike in the [Go Blog](https://blog.golang.org/errors-are-values)

What if you want to handle certain exception types? How would that work here? 
``` go
func main() {  
    result, err := divide(1.0, 0.0)  
    if err != nil {  
        switch err.(type) {  
        case *ErrZeroDivision:  
            fmt.Println(err.Error())  
        default:  
            fmt.Println("What the h* just happened?")  
        }  
    }

    fmt.Println(result)  
}

func divide(a, b float64) (float64, error) {  
    if b == 0.0 {  
        return 0.0, NewErrZeroDivision("Cannot divide by zero")  
    }

    return a / b, nil  
}
```
Should help describe this. We can use types and check for them (ohh thats a week 2 spoiler :o ) 
[Playground link](https://play.golang.org/p/vATgyXkWIt) and credit to [Sebastian Dahlgren](https://medium.com/@sebdah/go-best-practices-error-handling-2d15e1f0c5ee)

This is one of the first things we're covering which feel weird and a bit different. It may take a bit of getting used to. Some people still hate this.

### Panic
Exceptions do happen though. Things like a nil pointer or invalid memory address or stack overflow etc cause a a panic in Go. Panics are serious and impact the flow of control of your program essentially. You can throw them yourself but the rule of thumb seems to be that it should be when

 - there's a programmer error (wrong type)
 - Unrecoverable error. Required config or dep not there and unable to run

 Let's look at an example

```go
package main

import "fmt"

func main() {
    f()
    fmt.Println("Returned normally from f.")
}

func f() {
	//defer is like a finally call and is also used 
	//for expensive resources and operations
    defer func() {
	    //recover is close to a catch for an exception
        if r := recover(); r != nil {
            fmt.Println("Recovered in f", r)
        }
    }()
    fmt.Println("Calling g.")
    g(0)
    fmt.Println("Returned normally from g.")
}

func g(i int) {
    if i > 3 {
        fmt.Println("Panicking!")
        //similar to throw new exception really
        panic(fmt.Sprintf("%v", i))
    }
    defer fmt.Println("Defer in g", i)
    fmt.Println("Printing in g", i)
    g(i + 1)
}
```
[Definition of the types here](https://blog.golang.org/defer-panic-and-recover)
# Exercise

> Declare a struct type to maintain information about a person.  Declare a
> function that creates new values of your type.  Call this function from main
> and display the value.

* Exercise template: [source][ts] / [playground][tp]
* Example solution: [source][ss] / [playground][sp]

[ts]: exercises/basics/template/basics.go
[tp]: http://play.golang.org/p/ta6oFzjgwn
[ss]: exercises/basics/solution/basics.go
[sp]: http://play.golang.org/p/xTcpaKL4KG
[vf]: https://play.golang.org/p/sWII7ikLpjL
[fv]: https://tour.golang.org/moretypes/24 
