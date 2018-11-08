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

25. JSON unmarshalling/marshalling example at the atomic level:

```go
// Go offers built-in support for JSON encoding and
// decoding, including to and from built-in and custom
// data types.

package main

import "encoding/json"
import "fmt"
import "os"

func main() {

    // First we'll look at encoding basic data types to
    // JSON strings. Here are some examples for atomic
    // values.
    bolB, _ := json.Marshal(true)
    fmt.Println(string(bolB))

    intB, _ := json.Marshal(1)
    fmt.Println(string(intB))

    fltB, _ := json.Marshal(2.34)
    fmt.Println(string(fltB))

    strB, _ := json.Marshal("gopher")
    fmt.Println(string(strB))

    // And here are some for slices and maps, which encode
    // to JSON arrays and objects as you'd expect.
    slcD := []string{"apple", "peach", "pear"}
    slcB, _ := json.Marshal(slcD)
    fmt.Println(string(slcB))

    mapD := map[string]int{"apple": 5, "lettuce": 7}
    mapB, _ := json.Marshal(mapD)
    fmt.Println(string(mapB))
}
```

26. Now let's unmarshal them into structs:

```go
package main

import "encoding/json"
import "fmt"
import "os"

// We'll use these two structs to demonstrate encoding and
// decoding of custom types below.
type response1 struct {
    Page   int
    Fruits []string
}
type response2 struct {
    Page   int      `json:"page"`
    Fruits []string `json:"fruits"`
}

func main() {

    // The JSON package can automatically encode your
    // custom data types. It will only include exported
    // fields in the encoded output and will by default
    // use those names as the JSON keys.
    res1D := &response1{
        Page:   1,
        Fruits: []string{"apple", "peach", "pear"}}
    res1B, _ := json.Marshal(res1D)
    fmt.Println(string(res1B))

    // You can use tags on struct field declarations
    // to customize the encoded JSON key names. Check the
    // definition of `response2` above to see an example
    // of such tags.
    res2D := &response2{
        Page:   1,
        Fruits: []string{"apple", "peach", "pear"}}
    res2B, _ := json.Marshal(res2D)
    fmt.Println(string(res2B))

    // Now let's look at decoding JSON data into Go
    // values. Here's an example for a generic data
    // structure.
    byt := []byte(`{"num":6.13,"strs":["a","b"]}`)

    // We need to provide a variable where the JSON
    // package can put the decoded data. This
    // `map[string]interface{}` will hold a map of strings
    // to arbitrary data types.
    var dat map[string]interface{}

    // Here's the actual decoding, and a check for
    // associated errors.
    if err := json.Unmarshal(byt, &dat); err != nil {
        panic(err)
    }
    fmt.Println(dat)

    // In order to use the values in the decoded map,
    // we'll need to cast them to their appropriate type.
    // For example here we cast the value in `num` to
    // the expected `float64` type.
    num := dat["num"].(float64)
    fmt.Println(num)

    // Accessing nested data requires a series of
    // casts.
    strs := dat["strs"].([]interface{})
    str1 := strs[0].(string)
    fmt.Println(str1)

    // We can also decode JSON into custom data types.
    // This has the advantages of adding additional
    // type-safety to our programs and eliminating the
    // need for type assertions when accessing the decoded
    // data.
    str := `{"page": 1, "fruits": ["apple", "peach"]}`
    res := response2{}
    json.Unmarshal([]byte(str), &res)
    fmt.Println(res)
    fmt.Println(res.Fruits[0])

    // In the examples above we always used bytes and
    // strings as intermediates between the data and
    // JSON representation on standard out. We can also
    // stream JSON encodings directly to `os.Writer`s like
    // `os.Stdout` or even HTTP response bodies.
    enc := json.NewEncoder(os.Stdout)
    d := map[string]int{"apple": 5, "lettuce": 7}
    enc.Encode(d)
}
```

27. BUT what about when you don't know the type of the JSON return??

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Foo struct {
	A int                    `json:"a"`
	B int                    `json:"b"`
	X map[string]interface{} `json:"-"` // Rest of the fields should go here.
}

func main() {
    s := `{"a":1, "b":2, "x":1, "y":1}`
    f := Foo{}
    if err := json.Unmarshal([]byte(s), &f); err != nil {
        panic(err)
    }

    if err := json.Unmarshal([]byte(s), &f.X); err != nil {
        panic(err)
    }
    delete(f.X, "a")
    delete(f.X, "b")

    fmt.Printf("%+v", f)
}

```

28. JSON unmarshal into a MAP- more expensive than a struct BUT- **extremely** useful for unknown JSON types:

```go
package main

import (
	"encoding/json"
	"fmt"
)

func main() {

	json_bytes := []byte(`
		{
			"Name":"Death Bed: the Bed that Eats",
			"Category": "Cult",
			"Reviews":["Terrible","Awful", "Hilarious"],
			"Score":4
		}
	`)

	var masnun map[string]interface{}
	err := json.Unmarshal(json_bytes, &masnun)
	if err != nil {
		panic(err)
	}

	fmt.Println(masnun["Name"], masnun["Category"], masnun["Reviews"], masnun["Score"])

}
```

29. So this will fail- but see if you can make it work using type casting somehow:

```go
package main

import (
	"encoding/json"
	"fmt"
	"strings"
)

func main() {

	json_bytes := []byte(`
		{
			"Name":"Troll 2",
            "Category": ["Comedy?", "Horror?"]
            "Score": 128
		}
	`)

	var masnun map[string]interface{}
	err := json.Unmarshal(json_bytes, &masnun)
	if err != nil {
		panic(err)
	}

	var filmcat string
	filmcat = masnun["Category"][0]

	fmt.Println(strings.ToUpper(filmcat))

}
```

30. The answer to the previous question- see where to place this: 

```go
//We're going to cast category as an interface and then...
catlist = masnun["Category"].([]interface{})
//We're going to cast each part as a string
filmcat = catlist[0].(string)
fmt.Prinln("Here is the film category: ", filmcat)
//Do you see how interfaces can KIND OF take the place of generics? 
```

31. Testing! Let's create our test here:
```go
package main

func Sum(x int, y int) int {
    return x + y
}

func main() {
    Sum(5, 5)
}
```

32. Now- normally we would write these unit tests in another file...but for now let's keep it in the same file- so add this to your package:

```go
import "testing" 

func TestSum(t *testing.T) { //Notice the package reference here
    total := Sum(5, 5)
    if total != 10 {
       t.Errorf("Sum was incorrect, got: %d, want: %d.", total, 10)
    }
}
```

33. Test Tables- how to test multiple possibilities in one go!

```go
package main

import "testing"

func TestSum(t *testing.T) {
	tables := []struct {
		x int
		y int
		n int
	}{
		{1, 1, 2},
		{1, 2, 3},
		{2, 2, 4},
		{5, 2, 7},
	}

	for _, table := range tables {
		total := Sum(table.x, table.y)
		if total != table.n {
			t.Errorf("Sum of (%d+%d) was incorrect, got: %d, want: %d.", table.x, table.y, total, table.n)
		}
	}
}
```

34. More on test tables:

```go
package main

// Fib returns the nth number in the Fibonacci series.
func Fib(n int) int {
        if n < 2 {
                return n
        }
        return Fib(n-1) + Fib(n-2)
}

type fibTest struct {
        n        int
        expected int
}

var fibTests = []fibTest {
        {1, 1}, {2, 1}, {3, 2}, {4, 3}, {5, 5}, {6, 8}, {7, 13},
}

func TestFib(t *testing.T) {
    for _, tt := range fibTests {
            actual := Fib(tt.n)
            if actual != tt.expected {
                    t.Errorf("Fib(%d): expected %d, actual %d", tt.n, tt.expected, actual)
            }
    }
}

func main() {
    fmt.Println("We have to use s=this function so...")
    a := Fib(10)
}
```

35. Let's try benchmarking. Add this function to the one above:

```go

func BenchmarkFib10(b *testing.B) { //See how testing is now testing.B??
        // run the Fib function b.N times
        for n := 0; n < b.N; n++ {
                Fib(10)
        }
}
```

36. Now try running the benchmark: `go test -bench=.`. How many nanoseconds?

37. We expect nanoseconds to increase exponentially as we go up SO...add this:

```go
func benchmarkFib(i int, b *testing.B) {
        for n := 0; n < b.N; n++ {
                Fib(i)
        }
}

func BenchmarkFib1(b *testing.B)  { benchmarkFib(1, b) }
func BenchmarkFib2(b *testing.B)  { benchmarkFib(2, b) }
func BenchmarkFib3(b *testing.B)  { benchmarkFib(3, b) }
func BenchmarkFib10(b *testing.B) { benchmarkFib(10, b) }
func BenchmarkFib20(b *testing.B) { benchmarkFib(20, b) }
func BenchmarkFib40(b *testing.B) { benchmarkFib(40, b) }
```

and run again- without the tests, so: `go test -run=XXX -bench=.`
What do you expect to see here? (Hopefully, as this is a recursive function, your report goes up with time)