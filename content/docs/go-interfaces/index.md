---
title: 'Go Interfaces'
date: 2024-08-08T19:27:37+10:00
weight: 3
---

```go
package main

import (
    "fmt"
    "math"
)

type geometry interface {
    area() float64
    perim() float64
}

type rect struct {
    width, height float64
}

type circle struct {
    radius float64
}

func (r rect) area() float64 {
    return r.width * r.height
}

func (r rect) perim() float64 {
    return 2*r.width + 2*r.height
}

func (c circle) area() float64 {
    return math.Pi * c.radius * c.radius
}

func (c circle) perim() float64 {
    return 2 * math.Pi * c.radius
}

func measure(g geometry) {
    fmt.Println(g)
    fmt.Println(g.area())
    fmt.Println(g.perim())
}

func main() {
    r := rect{width: 3, height: 4}
    c := circle{radius: 5}

    fmt.Println("Rectangle:")
    measure(r)
    fmt.Println("Circle:")
    measure(c)
}
```

Execute:

```
Rectangle:
{3 4}
12
14
Circle:
{5}
78.53981633974483
31.41592653589793
```
