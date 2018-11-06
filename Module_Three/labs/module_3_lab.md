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
