# Module Four Lab: GoRoutines and Concurrency

## Go Routines

1. First try at a GoRoutine:

```go
package main

import (  
    "fmt"
)

func troll2() {  
    fmt.Println("They ate her!! And now they're gonna eat me!!") //Why didn't this print?
}
func main() {  
    go troll2()
    fmt.Println("NOOOOOOOOOOOO!!!")
}
```

2. Let's fix number one to give the original goRoutine time to run:

```go
package main

import (  
    "fmt"
    "time"
)

func troll2() {  
    fmt.Println("They ate her!! And now they're gonna eat me!!")
}
func main() {  
    go troll2()
    time.Sleep(1 * time.Second)
    fmt.Println("NOOOOOOOOOOOO!!!")
}
```

3. Now let's run multiple goRoutines concurrently and see how they output:

```go
package main

import (  
    "fmt"
    "time"
)

func numbers(stoptime int) {  
    for i := 1; i <= 5; i++ {
        time.Sleep(stoptime * time.Millisecond)
        fmt.Printf("%d ", i)
    }
}
func alphabets(stoptime int) {  
    for i := 'a'; i <= 'e'; i++ {
        time.Sleep(stoptime * time.Millisecond)
        fmt.Printf("%c ", i)
    }
}
func main() {  
    go numbers(250)
    go alphabets(400)
    time.Sleep(3000 * time.Millisecond)
    fmt.Println("main terminated")
}

```

4. Let's use **channels** here to hold and communicate values from different functions. Here we're going to split an array up between two **goroutines** (you have to imagine that this array was a lot bigger than it actually is):

```go
package main

import "fmt"

func sum(s []int, c chan int) {
    sum := 0
    for _, v := range s {
        sum += v
    }
    c <- sum // send sum to c
}

func main() {
    s := []int{7, 2, 8, -9, 4, 0}
    c := make(chan int)
    go sum(s[:len(s)/2], c) //goroutine passes value to c
    go sum(s[len(s)/2:], c) //other goroutine passes value
    x, y := <-c, <-c // receive from c
```

5. Remember how we said that channels are **blocking** functions? Let's demonstrate this. We're going to re-run our previous goroutine example without the __sleep__ portion to show how the reading from channels blocks (therefore allowing the __goroutine__ to finish running before moving on to close out **main**):

```go
package main

import (  
    "fmt"
)

func troll2(blocker chan bool) {  
    fmt.Println("They ate her!! And now they're gonna eat me!!")
    blocker <- true //writing something to the channel
}
func main() {  
    blocker := make(chan bool)
    go troll2(blocker)
    //time.Sleep(1 * time.Second) Don't need that anymore!
    <-blocker //We don't ACTUALLY need to put this write to a variable
    fmt.Println("NOOOOOOOOOOOO!!!")
}
```

6. More practice with channels: 

```go
package main

import (
    "fmt"
)

func calcSquares(number int, squareop chan int) {
    sum := 0
    for number != 0 { //shorthand for a loop
        digit := number % 10
        sum += digit * digit
        number /= 10
    }
    squareop <- sum //write the sum to the square operator
}

func calcCubes(number int, cubeop chan int) {
    sum := 0
    for number != 0 {
        digit := number % 10
        sum += digit * digit * digit
        number /= 10
    }
    cubeop <- sum
}

func main() {
    number := 589
    sqrch := make(chan int)
    cubech := make(chan int)
    go calcSquares(number, sqrch)
    go calcCubes(number, cubech)
    squares, cubes := <-sqrch, <-cubech
    fmt.Println("Final output", squares+cubes)
}

```