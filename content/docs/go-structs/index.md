---
title: 'Go Struct'
date: 2024-08-10T19:27:37+10:00
weight: 3
---

## Struct

### Declaration

```go
type person struct {
    name string
    age int
    pet string
}
```

### Definition

```go
var bob person
```

or

```go
bob := person{}
```
or

```go
bob := person{
    "Bob",
    40,
    "cat",
}
```
or

```go
bob := person{
    name: "Bob",
    age: 40,
    pet: "cat",
}
```

### Access

```go
bob.name = "Bob"
fmt.Println(bob.name)
```

## Anonimous Struct

You can also declare that a variable implements a struct type without first giving the struct type a name. This is called an anonymous struct and can only be instantiated once.

```go
var person struct {
    name string
    age int
    pet string
}
```

```go
person.name = "Bob"
person.age = 40
person.pet = "cat"
```
or

```go
person := struct {
    name string
    age int
    pet string
}{
    name: "Bob",
    age: 40,
}
```

You might wonder when it’s useful to have a data type that’s associated only with a single instance. Anonymous structs are handy in two common situations:

1. The first is when you translate external data into a struct or a struct into external data (like JSON or Protocol Buffers). This is called *unmarshaling* and *marshaling* data, respectively.
2. Writing tests is another place where anonymous structs pop up.

## Composition

Composition is similar to OOP languages inheritance. While Go doesn’t have inheritance, it encourages code reuse via built-in support for composition and promotion.

```go
package main

import "fmt"

type employee struct {
	name    string
	age     uint8
	address
}

type address struct {
	street string
	city  string
}

var employee1 employee = employee{"John Doe", 43, address{"Main Street", "London"}}

func main() {
	fmt.Println(employee1.name, employee1.age, employee1.street, employee1.city)
}
```

The above code will print: `John Doe 43 Main Street London`


## Methods & Pointers

Methods are functions on user defined types like structs. They are the equivalent to class methods in OOP languages. Method declarations look like function declarations, with one addition: the `receiver` specification. The receiver appears between the keyword *func* and the name of the method.

Pointers are better explained at the [pointers page]( {{<ref "/docs/go-pointers">}}), but in the example below you see how they work together with methods.

```go
type Employee struct {
    name    string
    age     int
}

func (e Employee) updateName(newName string) {
    e.name = newName
    fmt.Println(e.name)  // It will print Bob
}

func (e *Employee) updateNamePointer(newName string) {
    e.name = newName
    fmt.Println(e.name)  // It will print Bob
}

func main() {
    employee1 := Employee {
        name:   "Alice",
        age:    38,
    }

    employee1.updateName("Bob") // The name will not be updated to Bob. It will remain Alice
    employee1.updateNamePointer("Bob") // The name will be updated to Bob.

    fmt.Println("Employee Name:", employee1.name)
    fmt.Println("Employee Age:", employee1.age)
}
```
