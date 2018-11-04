# Module Two Lab: Coding in the GO playground

## Primitive Types Lab

1. So we're going to start with creating variable types in Golang. Let's create one of each. Open up the _go playground_ and try typing (into the main function) the following `var myname = <yourname>` and then print that out: 

```go
package main

import "fmt"

func main() {
    var myname = "Fernando Pombeiro"
    fmt.Println(myname)
}
```

2. When using the **var** keyword we are using an __initializer__ that will default to the minimum value for that particular type. So let's do one for each primitive type keeping two outside of the function and one inside. Let's see what they initialize to:

```go
package main

import "fmt"

var c bool
var s string

func main() {
    var i int
    fmt.Println(i, c, s)
}
```

3. This has to do with the very important lesson on **intializers in Go** and **scope**. Let's declare some variables OUTSIDE of the main function. ALSO- notice that I am initializing multiple variables in one command (both _first_ and _last_ below are initialized in a single var declaration!). Type the following into your _go playground_:

```go
package main
import (
    "fmt"
)
var first, last = <your first name with quotes>, <your last name with quotes>
func main() {
    fmt.Println(first,last)
}
```

Note that we've initialized multiple variables with a single call.

4. Final sample (and this exercise will fail but please notice why)- we can both _explicitly_ declare the **type** AND simultaneously declare the variable:

```go
package main

import (
    "fmt"
)

func main() {
    var notanint string = "0"
    fmt.Println(notanint + 2)
}
```

5. _Within functions only_ we can also use the **:=** to declare variables

```go
package main

import "fmt"

func main() {
    a := "Fern is awesome"
    fmt.Printf(a)
}
```

6. Let's try some string replacements! Please reference back to the slide to see what each of the replacement types ("%") are achieving

```go
package main

import "fmt"

func main() {
    var i int = 2
    var f float64 = 3.1415
    var b bool = true
    var s string = "Fern is awesome"
    fmt.Printf("%d\n %v\n %v\n %X\n", i, f, b, s)
}
```

6. Let's look at various strings and types. Please note that to get the TYPE you have to repeat the variable that you are looking to get the type from...so in this case (obviously) it's string:

```go
package main

import "fmt"

func main() {
    truth := "Fern is awesome"
    yourname := "<insert your name>"
    fmt.Printf("%s\n", truth)
    fmt.Printf("%v\n", truth)
    fmt.Printf("%q\n", truth)
    fmt.Printf("You know who is awsesome? %s\n",yourname)
    fmt.Printf("Also- they know that %s is of type %T", yourname, yourname)
}
```

7. So for type here's an example from the intro to go:

```go
package main

import "fmt"

func main() {
    v := 42 // change me!
    fmt.Printf("v is of type %T\n", v)
}
```

8. Constants:

```go
package main

import "fmt"

const Best = "Samurai"

func main() {
    const Movie = "Cop"
    fmt.Println(Best,Movie,"is the best film ever to grace the cinematic screen")
}
```

9. Attempting to change a constant (should fail):

```go
package main

import "fmt"

const Best = "Samurai"

func main() {
    const Movie = "Cop"
    Movie := "Troll 2" 
    fmt.Println(Best,Movie,"is the best film ever to grace the cinematic screen")
}
```

10. Casting in Golang (please try removing the explicit casts after running once)

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    var x, y int = 3, 4
    pi := 3.1415
    fmt.Println(int(pi))
    var f float64 = math.Sqrt(float64(x*x + y*y))
    var z uint = uint(f)
    fmt.Println(x, y, z)
}
```

Practice questions:

1. Create a substitution that prints out "<Your name> is awesome!"

2. Within the main package create a function that adds 5 + 5 as integers and "5" + "5" to be "55"

3. Print out the "type" for pi, along with it's value (3.1415)

## Functions

1. Initial function:

```go
package main

import "fmt"

func mult(x, y int) int {
    return x * y
}

func main() {
    fmt.Println(mult(3,4))
}
```

2. Function returning multiple results:

```go
package main

import "fmt"

func swap(x, y string) (string, string) {
    return y, x
}

func main() {
    a, b := swap("Troll 2", "Samurai Cop")
    fmt.Println("The two best movies ever created, in order, are: ", a, " and ", b)
}
```

3. Naked returns. Please, for the love of all things holy **do not use these in production code**. It makes your code unnecessarily unreadable. That being said- here's how they work:

```go
package main

import "fmt"

func split(sum int) (x, y int) {
    x = sum * 4 / 9
    y = sum - x
    return
}

func main() {
    fmt.Println(split(17))
}
```

4. Variadic Functions (we'll get into "range" in controls in the next section):

```go
package main

import "fmt"

func sum(input ...int) int {
    total := 0

    for i := range input {
        total += input[i]
    }

    fmt.Println("sum was ", total)
    return total
}

func main() {
    sum(1, 2, 3, 4, 6)
}
```

5. We're going to return a function here to form a closure. Watch how we manipulate the variables here using an anonymous function:

```go
package main
import "fmt"
func keepDoubling() func() int {
    i := 1
    return func() int {
        i *= 2
        return i
    }
}

func main() {
    doubled := keepDoubling()
    //Move through the sequence
    fmt.Println(doubled())
    fmt.Println(doubled())
    fmt.Println(doubled())
    //Now start all over:
    newDouble := keepDoubling()
    fmt.Println(newDouble())
}
```

