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

You can also declare that a variable implements a struct type without first giving the struct type a name. This is called an anonymous struct:

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

```go
package main

import "fmt"

type character struct {
	name         string
	strength     uint8
	dextery      uint8
	intelligence uint8
	inventory
}

type inventory struct {
	weapon string
	armor  string
}

var human character = character{"Human", 10, 13, 8, inventory{"sword", "chain mail"}}

func main() {
	fmt.Println(human.name, human.strength, human.weapon, human.armor)
}
```

The above code will print: `Human 10 sword chain mail`


## Pointers

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
