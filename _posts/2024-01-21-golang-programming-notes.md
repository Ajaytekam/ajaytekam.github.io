---   
title: GoLang Programming Basics Notes 
author: ajaytekam   
date: 2024-01-21 12:45:00 +0530   
img_path: /assets/posts/20240121/ 
categories: [GoLang, Automation]
tags: [Coding, DevOps]
image:
  path: golang.png 
---    

## Contents 

- [Program Structure](#program-structure)    
- [Running and Compilation](#running-and-compilation)    
- [Package Imports](#package-imports)    
- [Creating Custom Packages](#creating-custom-packages)   
- [Variables](#variables)    
- [Basic Data Types](#basic-data-types)    
- [User Input Output with fmt](#user-input-output-with-fmt)     
- [Array and Slices](#array-and-slices)  
- [Loops in Go](#loops-in-go)    
- [Conditional Statement](#conditional-statement)  
- [Functions](#functions)   
- [Structure](#structure)    
- [Pointers](#pointers)    
- [Command Line Arguments](#command-line-arguments)  
- [Concurrency in Go](#concurrency-in-go)     
    - [Creating Goroutine](#creating-goroutine)    
    - [Channels](#channels) 
    - [Channel Direction](#channel-direction)   
    - [Select](#select)    
    - [Buffered Channels](#buffered-channels)     
    - [Using sync module to wait for goroutines](#using-sync-module-to-wait-for-goroutines)     
- [Interfaces in Go](#interfaces-in-go)   
- [Maps](#maps)    
- [Errors in GoLang](#errors-in-golang)    
- [Reading/writing to a file](#readingwriting-to-a-file)  


# Program Structure    

* Go Program is made up of packages. Packages are equivalent of modules in python.    
* The program execution starts by running main package.  
* Packages are imported using `import` keyword.    

Example Program: 

```go  
package main

import "fmt"

func main() {
    fmt.Println("Hello from GoLang")
}
```  

### Structure of Program   

* `package main` imports a package main. Go packages are similar to libraries or modules in other language, a package contains one or more go source file. The main package needs to be imported by every go program, because every program starts running in package `main`.  
* `import "fmt"` imports the package ΓÇ£fmtΓÇ¥ into the sample program for using the function `Println`. The package `fmt` comes from the Go standard library.    

__Running go Program :__  

```bash  
go run main.go  
```  

At above program :

* `Println()` function from fmt package.   
* In every go program always main function will execute.  

# Running and Compilation   

To run the program `go run source_file`   

```bash   
go run prg1.go  
```  

To compile a binary `go build source_file`  

```bash  
go build prg1.go
```  

Changing output binary name  

```bash  
go build -o prg1 prg1.go  
```  

Above command builds executable file `hello`. To compile without debug symbols use below command    

```shell   
go build -ldflags '-w -s' prg1.go  
```  

Compiling for Cross Architecture, for arm 

```shell 
GOOS=linux GOARCH=arm GOARM=6 go build -o armhello hello.go
```  

For amd64  

```shell 
GOOS=linux GOARCH=amd64 go build -o hello hello.go  
```  

# Package Imports    

Packages are imported using `import` keyword.   

```go 
import "fmt"
import "math"
```  

Multiple packages can be imported using bracket.   

```go  
import (
    "fmt", 
    "math"
)
```   

Functions imported from packages starts with Capital letter.    

```go   
fmt.Println("Hello world")
fmt.Scan(&var)
```  

List of go packages can be found here : [https://pkg.go.dev/std](https://pkg.go.dev/std)    

# Variables  

There are three ways to defined variable in go  

* __Method 01__  

```go 
// defining variables 
var userName string
var age int 

// assigning values 
userName = "Ajay"
age = 28
```  

* __Method 02__ Using Type Interference 

With `:=`  

```go  
userName := "Ajay"
age := 28
``` 

The variables type is inferred from the value on the right hand side.  With `var name = value` expression  

```go   
var userName = "Ajay"
var age = 28
```  

* Constant variable  

```go  
const testVar int = 50  
```  

or  

```go  
const testVar = "Ajay"
```  

# Creating Custom Packages  

## Creating Go Project   

```bash   
go mod init exmple/hello
```  

* `exmple/hello` is path of the project/code.  
* `go.mod` file lists the specific versions of the dependencies that your project uses.  
* It is good idea to put the github repo url in project example: 

```bash  
go mod init github.com/ajaytekam/gotest
```  

## Example of Creating Go Packege   

* Create a go project  

```bash  
go mod init github.com/ajaytekam/gotest
```  

* Create two files   

```go    
// func01.go

package gotest 

import "fmt"

func SayHello01() {
  fmt.Println("Hello world, from func01.go")
}
```  

```go    
// func02.go

package gotest

import "fmt"

func SayHello02() {
  fmt.Println("Hello world This message from func02.go")
}
```  

* There are three total files  

```  
func01.go
func02.go
go.mod
```  

Now push the code into github repo  

## Using the package in program  

* Creating a project  

```bash    
go mod init example/hello
```  

* Create a file with below code  

```go  
// main.go
package main

import "github.com/Ajaytekam/gotest"
```  

* Now run the below command to import the package from github, by doing this the auto-complete function in your editor (LSP) will pickup the defined functions in gotest package  

```bash  
go mod tidy 
```  

where `tidy` flag used to Adds missing and removes unused modules and dependencies from the go.   

* Now add this code into `main.go`   

```go  
// main.go

package main

import "github.com/Ajaytekam/gotest"

func main() {
  gotest.SayHello01()
  gotest.SayHello02()
}
```  

Run the application `go run .`   

### Creating Binary    

To create binary run command `go build .`   

### Installing the Binary   

To install the binary in default go binary location run `go install .`    

### Remove Install Binary  

You have to remove the installed binary manually. The defult path for the go binary is `~/.go/bin` in linux and `~/.go/bin` in linux.   

# Basic Data Types    

Some basics data types are :  

* bool : for boolean values.  
* string : for string values.  
* int : for signed integers. It comes with specific sizes int8, int16, int32, int64.  
* uint : for unsigned int numbers. uint8, uint16, uint32, unit64, uintptr.  
* byte : alias for uint8  
* rune : alias for int32. Represents a unicode char.  
* float32 and float64 : for floating point numbers.  
* complex64 and complex128 : for complex numbers.  

For golang datatype visit : [https://golangbyexample.com/all-data-types-in-golang-with-examples/](https://golangbyexample.com/all-data-types-in-golang-with-examples/)   

# User Input/Output with fmt

__Printing Data/Messages :__  

* `fmt.Println()` : prints with appending line termination.   

```go  
fmt.Println("Hello world")
```  

Example of printing variable with fmt.Println()  

```go  
package main

import "fmt"

func main() {
    var userName = "Ajay"
    var age = 28
    var occup = "Cloud Engineer"

    fmt.Println("My name is", userName, ".I am", age, "years old.")
    fmt.Println("And i work as a", occup)
}
```  

* `fmt.Printf()` : We can use format string to prints variables. Some basics format string are %v which prints variable value, %T which prints variable type and `%t` which prints only boolean value (true/false).  

|Verbs|Description|  
|---|---|  
|%d|decimal integer|  
|%x,%o,%b|integer in hexadecimal,ocatal,binary|  
|%f,%g,%e|floating point number|  
|%t|boolean:true or false|   
|%c|rune(unicode code point)|  
|%s|string|   
|%q|quoted string ΓÇ£abcΓÇ¥ or rune ΓÇ£cΓÇ¥|   
|%v|any value in a natural format|   
|%T|type of any value|   
|%%|literal percent sign(no operand)|    

For more details check `fmt` documentation.   

__User Input :__  

* `fmt.Scan()` : takes user input and store it into variable. It takes variable address (pointer) as an argument `&variable_name`.  

```go  
package main

import "fmt" 

func main() {
    // declaring vars 
    var userName string
    var age int 

    // taking user input
    fmt.Printf("Name: ")
    fmt.Scan(&userName)
    fmt.Printf("Age: ")
    fmt.Scan(&age)

    fmt.Printf("My name is %v, i am %v years old\n", userName, age)
}
```  

Documentation for fmt package can be found here : [https://pkg.go.dev/fmt](https://pkg.go.dev/fmt)   

# Array and Slices    

__Array__  

Creating array: 

* Method 1  

```go  
var VARIABLE_NAME [SIZE]TYPE
```  

Example: 

```go 
var numbers [5]int
```  

* Method 2  

```go  
var VARIABLE_NAME  = [SIZE]TYPE{Value1, value2, ...value_N}   

// or 

VARIABLE_NAME := [SIZE]TYPE{Value1, value2, ...value_N}   
```  

Example:  

```go  
var nameLists  = [5]string{"john", "david", "peter", "samay", "rammy"}

// or 

nameLists := [5]string{"john", "david", "peter", "samay", "rammy"}
```  

Accessing array elements:  

```go  
fmt.Println(nameLists)
// Output: [john david peter samay rammy]

fmt.Println(nameLists[0])
// Output: john

fmt.Println(nameLists[2])
// Output: peter

fmt.Println(nameLists[4])
// Output: rammy

fmt.Println(nameLists[1:3])
// Output: [david peter]

fmt.Println(nameLists[2:4])
// Output: [peter samay]

fmt.Println(nameLists[3:])
// Output: [samay rammy]

fmt.Println(nameLists[4:])
// Output: [rammy]
```

* `len` : Return lenth of array.    

```go  
fmt.Priontln(len(myArray))
```

* `cap` : Return total capacity of an array.  

```go  
fmt.Println(cap(myArray))
```  

* `range` : Used to iterate through array.  

```go  
for var_1, var2 := range Array_Name {
    fmt.println("Index :", var_1, "| value:", var_2)
}
```  

Where var_1 contains index of element and var_2 contains element value. Example :  

```go  
package main

import "fmt"

func main() {
    var numx [5]int
    numx[0] = 10
    numx[1] = 20
    numx[2] = 30
    numx[3] = 40
    numx[4] = 50

    for i, j := range numx {
        fmt.Println("Index:", i, "| Value:", j)
    }
}
```  

Output :

```bash  
Index: 0 | Value: 10
Index: 1 | Value: 20
Index: 2 | Value: 30
Index: 3 | Value: 40
Index: 4 | Value: 50
```  

To print just values with range we can provide an empty var `_` in place of index  

```go  
for _, var2 := range Array_Name {
    fmt.println("Index :", var_1, "| value:", var_2)
}  
```  

Example :

```go  
package main

import "fmt"

func main() {
    var numx [5]int
    numx[0] = 10  
    numx[1] = 20
    numx[2] = 30
    numx[3] = 40
    numx[4] = 50

    for _, j := range numx {
        fmt.Println("Value:", j)
    }   

}
```   

Output:  

```bash  
Value: 10
Value: 20
Value: 30
Value: 40
Value: 50
```  

__Slice__  

Slice is a dynamic view of an array. Slice donΓÇÖt store anything by themselves, they reference an array. If we change something via the slice, the array is modified.  

Creating slice :  

```go  
// var VARIABLE_NAME []TYPE
var nameLists []string

// var VARIABLE_NAME = []TYPE{value1, value2, ...value_N}   
var numbers  = []int{10, 20, 30}   

// VARIABLE_NAME := []TYPE{value1, value2, ...value_N}   
fnumbers := []float32{10.23, 300.023}
```  

Example:  

```go  
package main

import "fmt"  

func main() { 
    var nameLists []string
    nameLists = append(nameLists, "John")
    nameLists = append(nameLists, "Mike")
    nameLists = append(nameLists, "Sammy")
    fmt.Println(nameLists)

    var numbers  = []int{10, 20, 30}
    numbers = append(numbers, 40)
    numbers = append(numbers, 50)
    fmt.Println(numbers)

    fnumbers  := []float32{10.23, 300.023}
    fnumbers = append(fnumbers, 12.23)
    fnumbers = append(fnumbers, 800.23)
    fmt.Println(fnumbers)
}
```  

# Loops in Go  

There is only for loop is available in Go. Syntax :   

```go   
for [condition | (initialization; condition; increment) | range ]
{
    statement(s)
}
```  

Example :

```go  
package main 

import "fmt"

func main() {
    message := "Hello World"

    for i := 0; i < 5 ; i++ {
        fmt.Println(message)
    }
}
```  

__Using Ranges__ : Ranges are used to iterate through variables such as array, slice and maps. Range basically returns two values index and value. Syntax :  

```go  
for var_1, var2 := range Array_Name {
	fmt.println("Index :", var_1, "| value:", var_2)
}
```  

Where var_1 contains index of element and var_2 contains element value. Example :  

```go    
package main

import "fmt"

func main() {
    numx := []int{10,20,30,40,50}  

    for i, j := range numx {
        fmt.Println("Index:", i, "| Value:", j)
    }   

}
```  

Output :

```bash 
Index: 0 | Value: 10
Index: 1 | Value: 20
Index: 2 | Value: 30
Index: 3 | Value: 40
Index: 4 | Value: 50
```  

To print just values with range we can provide an empty var _ in place of index  

```go  
for _, var2 := range Array_Name {
    fmt.println("Index :", var_1, "| value:", var_2)
}
```  

Example :

```go  
package main

import "fmt"

func main() {
    numx := []int{10,20,30,40,50}

    for _, j := range numx {
        fmt.Println("Value:", j)
    }

}
```  

Output:  

```bash 
Value: 10
Value: 20
Value: 30
Value: 40
Value: 50
```  

# Conditional Statement    

__if statement :__  

```go  
if condition {
    statment(s)
}
```  

__if-else statement :__   

```go  
if condition {
    statment(s)
} else {
    statment(s)
}
```   

__if-else-if statement :__   

```go  
if condition {
    statment(s)
} else if {
    statment(s)
}
```

Example:  

```go    
package main

import "fmt"

func main() {
    var age int
    fmt.Printf("Enter your age: ")
    fmt.Scan(&age)
    if age < 18 {
        fmt.Println("You are a miner")
    } else if age >= 18 && age < 40 {
        fmt.Println("You are an Adult")
    } else if age > 40 {
       fmt.Println("You are an Adult but in 40s or so, Take care of your Health!!")
    }
}  
```   

__Switch Case :__  

Syntax :

```go  
switch expression {
    case x:
        // code statement
    case y:
        // code statement
    case z:
        // code statement
    default:
        // code statement
}
```  

Example :

```go  
package main

import "fmt"

func main() {
    var ch string

    fmt.Printf("Enter your choice [Y|N]: ")
    fmt.Scan(&ch)

    switch ch {
        case "N":
            fmt.Println("You selected No")
        case "Y":
            fmt.Println("You selected Yes")
        default:
            fmt.Println("Did not understand your choice!!")
    }
}  
```  

Output 

```bash  
$ go run main.go
Enter your choice [Y|N]: Y
You selected Yes

$ go run main2.go
Enter your choice [Y|N]: y
Did not understand your choice!!
```  

Another example:  

```go 
package main

import "fmt"

func main() {
    var ch string

    fmt.Printf("Enter your choice [Y|N]: ")
    fmt.Scan(&ch)

    switch ch {
        case "N":
        case "n":
            fmt.Println("You selected No")
        case "Y":
        case "y":
            fmt.Println("You selected Yes")
        default:
            fmt.Println("Did not understand your choice!!")
    }
}  
```  

Output:  

```bash  
$ go run main.go
Enter your choice [Y|N]: y
You selected Yes

$ go run main.go
Enter your choice [Y|N]: n
You selected No
```  

# Functions

Function syntax in Go

```go  
func function_name([parameter list]) [return_types] {

    statements....
}
```  

* `function_name` : function should be named in camelCase.    
* `Arguments` : A function can take zero or more arguments. For two or more consecutive arguments with the same type, you can define the type on the back of the last argument.  
* `Return Types` : A function can return zero or more values. If returns more than one values, you need to cover them with parentheses.   

Example 1 :  

```go  
func testFunc() string {
    return "Hello World"
}

func main() {
    fmt.Println(testFunc())
}  
``` 

Example 2 : 

```go   
func testFunc(x string) string {
    return "Hello "+x
}

func main() {
    fmt.Println(testFunc("Ajay"))
}  
```   

Example 3 : 

```go  
func testFunc(x string) (msg string) {
    msg = "Hello " + x
    return 
}  
   
func main() {
    fmt.Println(testFunc("Ajay"))
}   
```  

Example 4 :  

```go  
func testFunc(x, y string, z int) (string, string, int) {
    a := "First String" + " " + x
    b := "Second String" + " " + y
    c := 20+z
    return a, b, c
}  
   
func main() {
    str1, str2, num := testFunc("Hello", "World", 10)

    fmt.Println(str1)
    fmt.Println(str2)
    fmt.Println(num)
}  
``` 

* `Exported/Unexported Functions` : Functions can be exported if first letter of their name is capitalized and unexported if first letter of function name is small.    
* `Anonymous Function` : Anonymous function can be declared inside a function and execute immediately after decaling it. The anonymous function can access the scope of its parents.   

```go  
package main  

import "fmt"  

func main() {
    
    myFunc := func(x int) int {
        return x*x
    }
    
    a := myFunc(3)
    b := myFunc(6)
    c := myFunc(9)

    fmt.Printf("a: %v\nb: %v\nc: %v\n", a, b, c)
}  
```  

# Structure   

structure is a user-defined datatype which contains one or more element of the same or different type.

__Defining a structure :__  

```go  
type structureName struct {
    variable_1 variable_type
    variable_2 variable_type
    ...
    variable_3 variable_type
}
```  

__Declaring a variable :__  

Once a structure type is defined, it can be used to declare variables of that type using following syntax :  

```go  
var variable_name structureName  
```

Example :

```go  
package main 

import "fmt"  

// defining structure 
type details struct {
    name string
    age int
    occup string
}

func main() {
    
    // declaring structure variable 
    var std1 details 

    // assigning values 
    std1.name = "John snow"
    std1.age = 20
    std1.occup = "Student"

    // printing values 
    fmt.Println("Name:", std1.name)
    fmt.Println("Age:", std1.age)
    fmt.Println("Occupation:", std1.occup)
}
```   

We can also create a variable and assign values to it :  

```go  
variableName := structureNam{"Value1", "Value2", ..."Value_N"}
```  

Example :

```go  
package main 

import "fmt"  

// defining structure 
type details struct {
    name string
    age int
    occup string
}

func main() {
    
    // declaring structure variable 
    // and assigning values 
    std1 := details{"John snow",20, "Student"}

    // printing values 
    fmt.Println("Name:", std1.name)
    fmt.Println("Age:", std1.age)
    fmt.Println("Occupation:", std1.occup)
}
```  

Struct can be also instantiate using the new keyword as well as using pointer address operators.  

```go  
package main 

import "fmt"  

// defining structure 
type details struct {
    name string
    age int
    occup string
}

func main() {
    
    // initiate struct using new keyword  
    std1 := new(details)

    std1.name = "John snow"
    std1.age = 20 
    std1.occup = "Student"

    // printing values 
    fmt.Println("Name:", std1.name)
    fmt.Println("Age:", std1.age)
    fmt.Println("Occupation:", std1.occup)

    // initiate struct using pointer & operator 
    var std2 = &details{"John Doe", 30, "IT Administrator"}

    // printing values 
    fmt.Println("Name:", std2.name)
    fmt.Println("Age:", std2.age)
    fmt.Println("Occupation:", std2.occup)
}
```  

__Go Methods :__    

Methods are special type of functions which works on the variable of a certain type called receiver. A receiver is usually a struct or an alias type or a pointer pointing to an instance of a struct or an alias.  

The struct and sets of methods (called method-sets) can be used to mimic object-oriented programming equivalent of class. The receiver is the part of the method, so two methods of the same name can exist on different types.  

Syntax:  

```go  
func (recv receiver_type) methodName(parameter_list) (return_value_list)  {
    statement(s)
}
```   

Example 1:  

```go  
package main 

import "fmt"  

// defining structure 
type details struct {
    name string
    age int
    occup string
}

func (dt details) Display() {
    fmt.Println("Name:", dt.name)
    fmt.Println("Age:", dt.age)
    fmt.Println("Occupation:", dt.occup)
}

func main() { 
    st1 := details{"John Doe", 30, "IT Administrator"}
    st1.Display()
}
```  

Example 2:  

```go  
package main 

import "fmt"  

// defining structure 
type specs struct {
    height int
    width int
    depth int
}

func (dt specs) Calculate() int {
    return dt.height*dt.width*dt.depth
}

func main() { 
    st1 := specs{10, 20, 30}
    fmt.Println(st1.Calculate())
}
```  

# Pointers   

Go-Lang supports pointers. Pointer declaration

```go  
var pointer_name *var-type
```

or

```go  
var pointer_name = &variable
```  

Assigning address of a variable to pointer variable  using `&` operator, similar to C language  

```go  
var value = 10
var *ptr int
ptr = &value
```  

Now by using *pointer_name the value is accessed and by using just the pointer name only address can be accessed. Example

```go  
package main 

import "fmt"  

func main() { 
    var value = 20
    var num_p *int
    num_p = &value
    fmt.Println("Value:", *num_p)
    fmt.Println("Addess:", num_p)
    fmt.Printf("%T\n\n", num_p)

    name := "Don Joe"
    var ptr = &name
    fmt.Println("Value:", *ptr)
    fmt.Println("Addess:", ptr)
    fmt.Printf("%T\n", ptr)
}
```   

Output: 

```bash  
Value: 20
Addess: 0xc0000b8000
*int

Value: Don Joe
Addess: 0xc00009e210
*string
```  

Note :

* Go does not support pointer arithmetic.
* Functions/methods accept both variables and pointers.
* Pass pointers when function/method needs to modify the parameter.
* When a variable is passed, the function/method gets a copy and the original copy is not modified. With pointers the underlying value is modified.  

# Command Line Arguments  

Command line arguments can be accessed using os packages.

```go  
os.Args[INDEX]  
```  

where index starts from 0. Code example :  

```go    
package main

import (
    "fmt"
    "os"
)

func main() {
    ArgsLen := len(os.Args)
    fmt.Println("Number of Length:", ArgsLen)
    for i:=0; i<ArgsLen;i++ {
        fmt.Printf("%v Argument: %v\n", i+1,os.Args[i])
    }   
}
```   

Output:   

```bash  
$ go run main1.go 
Number of Length: 1
1 Argument: /tmp/go-build2920941665/b001/exe/main1

go run main1.go Hello world
Number of Length: 3
1 Argument: /tmp/go-build1528893215/b001/exe/main1
2 Argument: Hello
3 Argument: world
```  

With compiled binary  

```bash 
$ go build main1.go 
$ ./main1 Hello world
Number of Length: 3
1 Argument: ./main1
2 Argument: Hello
3 Argument: world
```  

# Concurrency in Go  

* Concurrency is implemented in golang using Goroutines. 
* A Goroutine is a function or method which executes independently and simultaneously in connection with any other Goroutines present in your program.  
* Some important points are :
    - Goroutines are like light-weighted thread but the cost or resources needed for goroutines is very small as compared to the thread.    
    - Every program contains at least a single Goroutine and that Goroutine is known as the main Goroutine.    
    - All the Goroutines are working under the main Goroutines if the main Goroutine terminated, then all the goroutine present in the program also terminated.    
    - Goroutine always works in the background.  

## Creating Goroutine 

A goroutine is created by using a keyword `go` followed by a function name. Example :  

```go  
package main 

import (
    "fmt"
    "time"
)

func printT(tNo int, t int, delay int) {
    for i:=0;i<t;i++ {
        fmt.Println("ThreadNo:", tNo, "| Delay:", delay, "seconds | Counter:",i)
        time.Sleep(time.Second * time.Duration(delay))
    }
}

func main() {    
    go printT(1, 5, 3)    
    go printT(2, 6, 2)    
    go printT(3, 7, 1)    
    var str string
    fmt.Scanln(&str)
}
```  

As we can see that the function printT is called using go routines.   

GO routines with anonymous functions.  

```go  
package main

import (
    "fmt"
    "time"
)

func main() {

    go func() {
        for i:=0;i<6;i++{
            fmt.Println("01: Hello world")
            time.Sleep(time.Second*1)    
        }
    }()    


    go func() {
        for i:=0;i<3;i++{
            fmt.Println("02: Hello Test")
            time.Sleep(time.Second*2)    
        }
    }()    

    var str string
    fmt.Scanln(&str)
}
```   

## Channels   

Channels enables the communication between Goroutines, which allows them to synchronize their execution according to the requirements. channel (`chan`) is a go datatype which basically pass data between goroutines.  

Channel variable is declared using  

```go  
var c chan Datatype = make(chan Datatype)
```  

Example :

```go    
var c chan string = make(chan string)

// or

c := make(chan string)
```   

```go   
var c chan int = make(chan int)

// or

c := make(chan int)
```    

Now the above variable will be passed to each thread and then the data will be send and received using these variables. An Example program   

```go   
package main

import (
    "fmt"
    "time"
)

func SayHello(c chan string) {
    for i := 0;;i++ {
        c <- "Hello world"
    }
}

zgfunc printMsg(c chan string) {
    for {
        msg := <- c
        fmt.Println(msg)
        time.Sleep(time.Second*1)
    }
}

func main() {
    var c chan string = make(chan string)
    // or you can use 'c := make(chan string)'
    go SayHello(c)
    go printMsg(c)

    var str string
    fmt.Scanln(&str)
}
```    

* The above program is an example of a goroutine channel, where channel variable c is declared, then it is passed to both goroutine functions. 
* Now when SayHello function execute it will send string data ΓÇ£Hello worldΓÇ¥ into the channel via channel variable `c`, and we can see in another goroutine function printMsg the channel variable c store the channel data into  the string variable msg and the message gets printed in next line.  
* And after 1 second of delay it will receive next message through channel.   
* Also note that this channel is synchronized, means for 1 second delay the function SayHello have to wait and after that it will resume in its execution.    

## Channel Direction

In golang channels are by-default bi-directional, but you can also create unidirectional channels. For example to send data to the channel  

```go   
c chan<- string
```

To receive data to the channel  

```go    
c <-chan string  
```

Example Program :

```go    
package main   

import (
    "fmt" 
    "time"
)

// Only send data to channel 
func SayHello(c chan<- string) {
    for i := 0;;i++ {
        c <- "Hello world"
    }
}

// Only receive data to channel 
func printMsg(c <-chan string) {
    for {
        msg := <- c
        fmt.Println(msg)
        time.Sleep(time.Second*1)
    }
}

func main() {
    var c chan string = make(chan string) 
    // or you can use 'c := make(chan string)'
    go SayHello(c)
    go printMsg(c)

    var str string
    fmt.Scanln(&str)
}
```  

Another example of channels  

```go  
package main

import (
    "fmt"
)

func SetMsg(ch1 chan<- string) {
// defining ch1 to send data
    ch1 <- "Hello world"
}

func GetMsg(ch1 <-chan string, ch2 chan<- string) {
// defining ch1 to receive data and ch2 to send data
    msg := <- ch1
    ch2 <- msg
}

func main() {
    // declaring channels with buffered capacity of 1
    ch1 := make(chan string, 1)
    ch2 := make(chan string, 1)

    SetMsg(ch1)
    GetMsg(ch1,ch2)
    fmt.Println(<-ch2)
}
```  

## Select  

Select statement lets user wait for multiple channel operations. It basically works like switch statement for channels. Example  

```go  
package main 

import (
    "fmt"
    "time" 
)

func main() {
    ch1 := make(chan string)
    ch2 := make(chan string)

    go func(){
        for{
            ch1 <- "Message from Func1"
            time.Sleep(1*time.Second)
        }
    }()


    go func(){
        for{
            ch2 <- "Message from Func2"
            time.Sleep(2*time.Second)
        }
    }()

    for { 
        select {
            case msg1 := <- ch1:
                fmt.Println(msg1)
            case msg2 := <- ch2:
                fmt.Println(msg2)
        }
    }
} 
```  

we can also implement timeout option with   

```go  
for {
    select {
        case msg1 := <- ch1:
            fmt.Println(msg1)
        case msg2 := <- ch2:
            fmt.Println(msg2)
        case <- time.After(2*time.Second):
            fmt.Println("No messages received: Time out")
    }
}
```  

There is also a default option is available in select, but it keep invoked continuously,    

```go   
for {
    select {
        case msg1 := <- ch1:
            fmt.Println(msg1)
        case msg2 := <- ch2:
            fmt.Println(msg2)
        default:
            fmt.Println("No data received")
    }
}
```  

## Buffered Channels  

* By-default channels are unbuffered, means they only accepts sends (chan <-) if there are corresponding receive (<- chan), which are ready to receive the send values, otherwise channel will be blocked.   
* To circumvent that, buffered channels can be used, it will allows to accept a limited number of values without a corresponding receiver for those values. 
* Buffered channel are blocked only when the buffer is full.   
* Similarly receiving from a buffered channel are blocked only when the buffer will be empty.  

Creating Buffered channel

```go   
ch := make(chan_type, capacity)
```  

The capacity of buffered channel will be greater then 0, because unbuffered channel has capacity of 0.

Example :  

```go  
package main 

import (
    "fmt"
)

func main() {
    // channel with capacity 2 
    ch := make(chan string, 2)
    
    ch <- "Message 1"
    ch <- "Message 2"

    // receiving/priting both messages
    fmt.Println(<-ch)
    fmt.Println(<-ch)
}
```  

Example 2 :  

```go  
package main

import (
    "fmt"
    "time"
)

func SetMessage(ch chan<- string, msg string) {
    for i:=0;i<5;i++ {
        ch <- msg
    }
}

func main() {
    ch := make(chan string, 10)

    go SetMessage(ch, "Message One")
    go SetMessage(ch, "Message Two")

    // wait for 5 second
    time.Sleep(5*time.Second)
    // close the channel
    close(ch)

    for v := range ch {
        fmt.Println(v)
        time.Sleep(100*time.Millisecond)
    }
}
```  

Example 3 : 

```go  
package main

import (
    "fmt"
    "time"
)

func AddMsg(ch chan<- string, msg string) {
    for i:=0;i<5;i++ {
        ch <- msg
    }   
    close(ch)
}

func main() {
    ch := make(chan string, 5)   
    go AddMsg(ch, "Hello World")
    time.Sleep(2*time.Second) 
    for msg :=  range ch {
        fmt.Println(msg)
    } 
}
```  

## Using sync module to wait for goroutines   

First create a waitgroup variable

```go  
var wg sync.WaitGroup
```  

Then add number of goroutines you want to wait for

```go     
wg.Add(2)
```   

* Now provide each goroutines with wg pointer and after ending of each goroutine set `wg.Done()` which basically decrements the number of goroutines (which is in this case 2). 
* Now on main function put `wg.Wait()` to wait for all the goroutines. 
* Now look the example project.   

```go  
package main

import (
    "fmt"
    "time"
    "sync"
)

func Print(wg *sync.WaitGroup, tm int, msg string) {
    time.Sleep(time.Duration(tm) * time.Millisecond)
    fmt.Println(msg)
    wg.Done()
}

func main() {
    var wg sync.WaitGroup
    wg.Add(3)
    go Print(&wg, 1000, "Hello world From 1")
    go Print(&wg, 2000, "Hello world From 2")
    go Print(&wg, 3000, "Hello world From 3")
    wg.Wait()
    fmt.Println("All routines completed")
}
```  

# Interfaces in Go  

Interfaces are named collections of method signatures as well as custom type.   

* [https://gobyexample.com/interfaces](https://gobyexample.com/interfaces)  
* [https://golangbyexample.com/interface-in-golang/](https://golangbyexample.com/interface-in-golang/)  
* [https://jordanorelli.com/post/32665860244/how-to-use-interfaces-in-go](https://jordanorelli.com/post/32665860244/how-to-use-interfaces-in-go)  

# Maps  

Map is similar to hashes or dictionary. Creating an empty map :  

```go   
var mymap = make(map[key-type]int)
```  

Example :

```go   
var mymap = make(map[string]int)
```   

Example :   

```go   
package main

import "fmt"

func main() {
    mymap := make(map[string]int)
    mymap["num1"] = 200
    mymap["num2"] = 210
    mymap["num3"] = 220
    mymap["num4"] = 230

    // print all maps
    fmt.Println("Print all values : ")
    fmt.Println(mymap)

    // print key values
    fmt.Println("\nPrint only keys:values pair : ")
    for key, val := range mymap {
        fmt.Println(key, "=>", val)
    }

    // print only keys
    fmt.Println("\nPrint only keys : ")
    for key,_ := range mymap {
        fmt.Println(key)
    }

    // print only values
    fmt.Println("\nPrint only values : ")
    for _,val := range mymap {
        fmt.Println(val)
    }
}
```  

Output :   

```bash    
Print all values :
map[num1:200 num2:210 num3:220 num4:230]

Print only keys:values pair :
num1 => 200
num2 => 210
num3 => 220
num4 => 230

Print only keys :
num1
num2
num3
num4

Print only values :
200
210
220
230
```   

# Errors in GoLang    

Most functions and methods returns at-least one error value (or nil), so we can use that to show errors. Example :   

```go   
package main

import (
    "fmt"
    "os"
)

func main() {
    myfile, err := os.Open("file.txt")
    if err != nil {
        fmt.Println("[!] Error : ", err)
        return
    }
}
```   

Output :  

```bash  
[!] Error :  open file.txt: no such file or directory
```  

It can also be implemented like this

```go   
func main() {
    if _, err := os.Open("file.txt"); err != nil {
        fmt.Println("[!] Error : ", err)
        return
    }
}
```  

## Using fmt.Errorf   

```go  
package main

import (
    "fmt"
    "math"
)

func Sqrt(x float64) (float64, error) {

    if x < 0 {
        return 0, fmt.Errorf("cannot Sqrt negative number: %v", float64(x))
    }

    return math.Sqrt(x), nil
}

func main() {
    fmt.Println(Sqrt(2))
    fmt.Println(Sqrt(-2))
}
```  

Output :

```bash    
1.4142135623730951 <nil>
0 cannot Sqrt negative number: -2
```  

As we can see the Sqrt return 2 values. We can use error check to get only values.

```go   
package main

import (
    "fmt"
    "math"
)

func Sqrt(x float64) (float64, error) {

    if x < 0 {
        return 0, fmt.Errorf("cannot Sqrt negative number: %v", float64(x))
    }

    return math.Sqrt(x), nil
}

func main() {
    res1, err := Sqrt(2)
    if err != nil {
        fmt.Println(err)
    } else {
        fmt.Println(res1)
    }

    res2, err := Sqrt(-2)
    if err != nil {
        fmt.Println(err)
    } else {
        fmt.Println(res2)
    }
}
```  

Output :  

```bash   
1.4142135623730951
cannot Sqrt negative number: -2  
```  

We can also create a function to handle error instead of checking it each time with function call

```go  
package main

import (
    "fmt"
    "math"
)

func checkError(err error) {
    if err != nil {
        fmt.Println(err)
    }

}

func Sqrt(x float64) (float64, error) {

    if x < 0 {
        return 0, fmt.Errorf("cannot Sqrt negative number: %v", float64(x))
    }

    return math.Sqrt(x), nil
}

func main() {
    res1, err := Sqrt(2)
    checkError(err)
    if res1 != 0 {fmt.Println(res1)}

    res2, err := Sqrt(-2)
    checkError(err)
    if res2 != 0 {fmt.Println(res1)}
}
```  

Output :

```bash  
1.4142135623730951
cannot Sqrt negative number: -2  
```  

References: 

* Implementing Errors on custom package : [https://progressivecoder.com/a-guide-to-basic-error-handling-in-golang/](#https://progressivecoder.com/a-guide-to-basic-error-handling-in-golang/)    
* Example of built-in error with struct : [https://gobyexample.com/errors](#https://gobyexample.com/errors)     

# Reading/writing to a file  

* [https://www.educative.io/edpresso/file-reading-in-golang](#https://www.educative.io/edpresso/file-reading-in-golang)   
* [https://www.linode.com/docs/guides/creating-reading-and-writing-files-in-go-a-tutorial/](#https://www.linode.com/docs/guides/creating-reading-and-writing-files-in-go-a-tutorial/)    
