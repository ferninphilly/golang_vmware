# Module Three Lab: Control Flow statements in GO

## Control flow

1. The IF statement:

```go
package main

import (
    "fmt"
    "math"
)

func sqrt(x float64) string {
    if x < 0 {
        return sqrt(-x) + "i"
    }
    return fmt.Sprint(math.Sqrt(x))
}

func main() {
    fmt.Println(sqrt(25), sqrt(-36))
}
```

2. The shortened "if" statement:

```go
package main

import (
    "fmt"
    "math"
)

func pow(x, n, lim float64) float64 {
    //Also- step 1: Establish the var ("v" in this case) and then use it in an IF statement- so 
    //v < lim is a boolean. If true return v else
    //Pay attention to the scope of "v" here
    if v := math.Pow(x, n); v < lim {
        return v
    } else {
        fmt.Printf("%g >= %g\n", v, lim)
    }
    // can't use v here, though
    return lim
}

func main() {
    fmt.Println(
        pow(3, 2, 10),
        pow(3, 3, 20),
    )
}
```

3. Logical operators:

```go
package main

import "fmt"

func main() {
   var a bool = true
   var b bool = false
   if ( a && b ) {
      fmt.Printf("Line 1 - Condition is true\n" )
   }
   if ( a || b ) {
      fmt.Printf("Line 2 - Condition is true\n" )
   }
   
   /* lets change the value of  a and b */
   a = false
   b = true
   if ( a && b ) {
      fmt.Printf("Line 3 - Condition is true\n" )
   } else {
      fmt.Printf("Line 3 - Condition is not true\n" )
   }
   if ( !(a && b) ) {
      fmt.Printf("Line 4 - Condition is true\n" )
   }
}
```

4. FOR loops:

```go
package main

import "fmt"

//Notice the breakdown of each of the three statements: INIT, CONDITION, POST
func main() {
    for i := 0; i < 10; i++ {
        fmt.Println(i)
    }
    fmt.Println("DONE!")
}
```

5. In GO there is no **while** loop; that, too- is **for**:

```go
package main

import "fmt"

func main() {
    sum := 1
    for sum < 1000 {
        sum += sum
        }
    fmt.Println(sum)
}

```


6. FOR loops with RANGE keyword:

```go
package main

import "fmt"

func main() {

    // Here we use `range` to sum the numbers in a slice.
    // Arrays work like this too.
    nums := []int{2, 3, 4}
    sum := 0
    for _, num := range nums {
        sum += num
    }
    fmt.Println("sum:", sum)

    // `range` on arrays and slices provides both the
    // index and value for each entry. Above we didn't
    // need the index, so we ignored it with the
    // blank identifier `_`. Sometimes we actually want
    // the indexes though.
    for i, num := range nums {
        if num == 3 {
            fmt.Println("index:", i)
        }
    }

    // `range` on map iterates over key/value pairs.
    kvs := map[string]string{"a": "apple", "b": "banana"}
    for k, v := range kvs {
        fmt.Printf("%s -> %s\n", k, v)
    }

    // `range` can also iterate over just the keys of a map.
    for k := range kvs {
        fmt.Println("key:", k)
    }

    // `range` on strings iterates over Unicode code
    // points. The first value is the starting byte index
    // of the `rune` and the second the `rune` itself.
    for i, c := range "go" {
        fmt.Println(i, c)
    }
}
```

7. The **switch** statements:

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    now := time.Now().Unix()
    mins := now % 2
    switch mins {
    case 0:
        fmt.Println("even")
    case 1:
        fmt.Println("odd")
    }
}
```

8. Multiple case values in the **switch** statement:

```go
package main

import "fmt"

func main() {
    score := 7
    switch score {
    case 0, 1, 3:
        fmt.Println("Terrible")
    case 4, 5:
        fmt.Println("Mediocre")
    case 6, 7:
        fmt.Println("Not bad")
    case 8, 9:
        fmt.Println("Almost perfect")
    case 10:
        fmt.Println("hmm did you cheat?")
    default:
        fmt.Println(score, " off the chart")
    }
}
```

9. Fallthrough statement:

```go

package main

import "fmt"

func main() {
    n := 4
    switch n {
        case 0:
            fmt.Println("is zero")
            fallthrough
        case 1:
            fmt.Println("is <= 1")
            fallthrough
        case 2:
            fmt.Println("is <= 2")
            fallthrough
        case 3:
            fmt.Println("is <= 3")
            fallthrough
        case 4:
            fmt.Println("is <= 4")
            fallthrough
        case 5:
            fmt.Println("is <= 5")
            fallthrough
        case 6:
            fmt.Println("is <= 6")
            fallthrough
        case 7:
            fmt.Println("is <= 7")
            fallthrough
        case 8:
            fmt.Println("is <= 8")
            fallthrough
        default:
            fmt.Println("Try again!")
        }
}
```

10. The **break** statement:

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    n := 1
    switch n {
    case 0:
        fmt.Println("is zero")
        fallthrough
    case 1:
        fmt.Println("<= 1")
        fallthrough
    case 2:
        fmt.Println("<= 2")
        fallthrough
    case 3:
        fmt.Println("<= 3")
        if time.Now().Unix()%2 == 0 {
            fmt.Println("It's an interesting time")
            break
        }
        fallthrough
    case 4:
        fmt.Println("<= 4")
        fallthrough
    case 5:
        fmt.Println("<= 5")
    }
}
```

11. Let's see how **break** acts in a __for__ loop:

```go
package main

import "fmt"

func main() {
    i := 0
    for { //since there are no checks, this is an infinite loop
        if i >= 3 { break } //break out of this for loop when this condition is met
        fmt.Println("Value of i is:", i)
        i++;
    }
    fmt.Println("A statement just after for loop.") 
}
```

12. Now let's look at **continue**:

```go
package main

import "fmt"

func main() {
    
    //a continue within this loop will bring back execution to the beginning of the loop.  Checks and increments in for loop will be executed.

    for i := 0; i<21 ; i++ { //control comes back here when there is a ‘continue’ within this for block
        if i%2 == 0 { 
            continue //if it is an even number, go back to beginning of for loop
        }
        fmt.Println("Odd:", i)  //execution will reach here only when i%2 is not 0, and therefore it is odd
    }
}
```

13. So for labels- they can be used along with **continue** or **break** statements:

```go
package main

import "fmt"

func main() {
    guestList := []string{"bill", "jill", "joan"}
    arrived := []string{"sally", "jill", "joan"}

CheckList:
    for _, guest := range guestList {
        for _, person := range arrived {
            fmt.Printf("Guest[%s] Person[%s]\n", guest, person)

            if person == guest {
                fmt.Printf("Let %s In\n", person)
                continue CheckList
            }
        }
    }
}
```

14. We can also use labels with **break** statements:

```go
package main

import "fmt"

func main() {
    SwitchStatement:
        switch 1 {
        case 1:
            fmt.Println(1)
            for i := 0; i < 10; i++ {
                break SwitchStatement
            }
            fmt.Println(2)
        }
        fmt.Println(3)
}
```

15. We can also use them with **goto** (sigh):

```go
package main

import "fmt"

func main() {
   /* local variable definition */
   var a int = 10

   /* do loop execution */
   LOOP: for a < 20 {
      if a == 15 {
         /* skip the iteration */
         a = a + 1
         goto LOOP
      }
      fmt.Printf("value of a: %d\n", a)
      a++     
   }  
}
```

16. Pointers- generated and more:

```go
package main

import "fmt"

func main() {
    i, j := 42, 2701
    p := &i         // point to i
    fmt.Println(*p) // read i through the pointer
    *p = 21         // set i through the pointer
    fmt.Println(i)  // see the new value of i
    p = &j         // point to j
    *p = *p / 37   // divide j through the pointer
    fmt.Println(j) // see the new value of j
}
```

17. More on pointers: does "x" become zero- like we want it to- here? 

```go
import "fmt"
func zero(x int) {
  x = 0
}
func main() {
  x := 5
  zero(x)
  fmt.Println(x)
}
```

18. So let's say we desperately WANTED to make x zero...well:

```go
func zero(xPtr *int) {
  *xPtr = 0 //Store the incoming value to whatever xPtr is pointing at
}
func main() {
  x := 5
  zero(&x) //We are going to modify the value by pointing at it- and pass in a pointer
  fmt.Println(x) 
}
```

19. We can also use the built in "new" function to create pointers:

```go
//This example is a bit silly because you'll rarely use pointers for built-in types...but whatever- it's an example
func one(xPtr *int) {
  *xPtr = 1
}
func main() {
  xPtr := new(int) //Creates a pointer to the INT type
  one(xPtr)
  fmt.Println(*xPtr) // x is 1
}
```

20. Here's another pointer exercise to demonstrate how we work with them:

```go
package main

import "fmt"

func reset(value int) {
	value = 0
}

func resetPtr(value *int) {
	*value = 0
}

func main() {
	value := 1
	fmt.Println("initial", value)

	reset(value)
	fmt.Println("after reset", value)

	resetPtr(&value)
	fmt.Println("after resetPtr", value)
```

21. Pointer to a struct:

```go
package main

import "fmt"

type myType struct {
	value int
}

func (mt myType) reset() {
	mt.value = 0
}

func (mt *myType) resetPtr() {
	mt.value = 0
}

func main() {
	mt := myType{
		value: 1,
	}
	fmt.Println("initial", mt.value)

	mt.reset()
	fmt.Println("after reset", mt.value)

	mt.resetPtr()
	fmt.Println("after resetPtr", mt.value)
}
```

22. Last pointer section:

```go
package main
import "fmt"

func main() {
    var a = 7.98
    var p = &a
    var pp = &p

    fmt.Println("a = ", a)
    fmt.Println("address of a = ", &a)

    fmt.Println("p = ", p)
    fmt.Println("address of p = ", &p)

    fmt.Println("pp = ", pp)

    // Dereferencing a pointer to pointer
    fmt.Println("*pp = ", *pp)
    fmt.Println("**pp = ", **pp)
}
```

23. Embedding structs:

```go
package main
import "fmt"

type Ball struct {
    Radius   int
    Material string
}
type Football struct {
    Ball
}
func (b Ball) Bounce() {
    fmt.Printf("Bouncing ball %+v\n", b)
}

func main() {
    fb := Football{}
    fmt.Printf("fb = %+v\n", fb)
    fb := Football{Ball{Radius: 5, Material: "leather"}}
    fmt.Printf("fb = %+v\n", fb)
    fb.Bounce() //OR fb.Ball.Bounce()

}
```

24. Anonymous structs:

```go
package main

import (  
    "fmt"
)

func main() {  
    emp3 := struct {
        firstName, lastName string
        age, salary         int
    }{
        firstName: "Andreah",
        lastName:  "Nikola",
        age:       31,
        salary:    5000,
    }

    fmt.Println("Employee 3", emp3)
}

```