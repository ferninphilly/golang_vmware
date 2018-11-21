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

5. Now let's close queues:

```go
// In a [previous](range) example we saw how `for` and
// `range` provide iteration over basic data structures.
// We can also use this syntax to iterate over
// values received from a channel.

package main

import "fmt"

func main() {

    // We'll iterate over 2 values in the `queue` channel.
    queue := make(chan string, 2)
    queue <- "one"
    queue <- "two"
    close(queue)

    // This `range` iterates over each element as it's
    // received from `queue`. Because we `close`d the
    // channel above, the iteration terminates after
    // receiving the 2 elements.
    for elem := range queue {
        fmt.Println(elem)
    }
}

```

6. Remember how we said that channels are **blocking** functions? Let's demonstrate this. We're going to re-run our previous goroutine example without the __sleep__ portion to show how the reading from channels blocks (therefore allowing the __goroutine__ to finish running before moving on to close out **main**):

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

7. More practice with channels (simple):

```go
package main

import "fmt"
import "time"

// This is the function we'll run in a goroutine. The
// `done` channel will be used to notify another
// goroutine that this function's work is done.
func worker(done chan bool) {
    fmt.Print("working...")
    time.Sleep(time.Second)
    fmt.Println("done")

    // Send a value to notify that we're done.
    done <- true
}

func main() {

    // Start a worker goroutine, giving it the channel to
    // notify on.
    done := make(chan bool, 1)
    go worker(done)

    // Block until we receive a notification from the
    // worker on the channel.
    <-done
}
```

8. More practice with channels: 

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

9. Directional channels:

```go
// When using channels as function parameters, you can
// specify if a channel is meant to only send or receive
// values. This specificity increases the type-safety of
// the program.

package main

import "fmt"

// This `ping` function only accepts a channel for sending
// values. It would be a compile-time error to try to
// receive on this channel.
func ping(pings chan<- string, msg string) {
    pings <- msg
}

// The `pong` function accepts one channel for receives
// (`pings`) and a second for sends (`pongs`).
func pong(pings <-chan string, pongs chan<- string) {
    msg := <-pings
    pongs <- msg
}

func main() {
    pings := make(chan string, 1)
    pongs := make(chan string, 1)
    ping(pings, "passed message")
    pong(pings, pongs)
    fmt.Println(<-pongs)
}
```

10. A better example: relay race:

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    // Create an unbuffered channel
    baton := make(chan int)

    // First runner to his mark
    go Runner(baton)

    // Start the race
    baton <- 1

    // Give the runners time to race
    time.Sleep(500 * time.Millisecond)
}

func Runner(baton chan int) {
    var newRunner int

    // Wait to receive the baton
    runner := <-baton

    // Start running around the track
    fmt.Printf("Runner %d Running With Baton\n", runner)

    // New runner to the line
    if runner != 4 {
        newRunner = runner + 1
        fmt.Printf("Runner %d To The Line\n", newRunner)
        go Runner(baton)
    }

    // Running around the track
    time.Sleep(100 * time.Millisecond)

    // Is the race over
    if runner == 4 {
        fmt.Printf("Runner %d Finished, Race Over\n", runner)
        return
    }

    // Exchange the baton for the next runner
    fmt.Printf("Runner %d Exchange With Runner %d\n", runner, newRunner)
    baton <- newRunner
}
```

11. Select statement example:

```go
// Go's _select_ lets you wait on multiple channel
// operations. Combining goroutines and channels with
// select is a powerful feature of Go.

package main

import "time"
import "fmt"

func main() {

    // For our example we'll select across two channels.
    c1 := make(chan string)
    c2 := make(chan string)

    // Each channel will receive a value after some amount
    // of time, to simulate e.g. blocking RPC operations
    // executing in concurrent goroutines.
    go func() {
        time.Sleep(1 * time.Second)
        c1 <- "one"
    }()
    go func() {
        time.Sleep(2 * time.Second)
        c2 <- "two"
    }()

    // We'll use `select` to await both of these values
    // simultaneously, printing each one as it arrives.
    for i := 0; i < 2; i++ {
        select {
        case msg1 := <-c1:
            fmt.Println("received", msg1)
        case msg2 := <-c2:
            fmt.Println("received", msg2)
        }
    }
}
```

12. Select part 2: Which one will be printed out? WHY did it run like this? 

```go
package main

import (  
    "fmt"
    "time"
)

func server1(ch chan string) {  
    time.Sleep(6 * time.Second)
    ch <- "from server1"
}
func server2(ch chan string) {  
    time.Sleep(3 * time.Second)
    ch <- "from server2"

}
func main() {  
    output1 := make(chan string)
    output2 := make(chan string)
    go server1(output1)
    go server2(output2)
    select {
    case s1 := <-output1:
        fmt.Println(s1)
    case s2 := <-output2:
        fmt.Println(s2)
    }
}
```

13. SELECT with default:

```go
package main

import (  
    "fmt"
    "time"
)

func process(ch chan string) {  
    time.Sleep(10500 * time.Millisecond)
    ch <- "HERE'S JOHNNY!!!!"
}

func main() {  
    ch := make(chan string)
    go process(ch)
    for {
        time.Sleep(1000 * time.Millisecond) 
        /* So here we are creating an infinite loop that will run every second...and then perform a select statement. Because the function takes 10500 milliseconds it will not initially run. 
        So this will run 10.5 times before they sync up (10.5 seconds). */
        select {
        case v := <-ch:
            fmt.Println("BANG!! CRASH!!.... ", v)
            return
        default:
            fmt.Println("All work and no play makes Jack a Dull Boy")
        }
    }

}
```

14. Random select- these will run and output a random value:

```go
package main

import (  
    "fmt"
    "time"
)

func server1(ch chan string) {  
    ch <- "Iron Man Dies"
}
func server2(ch chan string) {  
    ch <- "Captain America Dies"

}
func main() {  
    output1 := make(chan string)
    output2 := make(chan string)
    go server1(output1)
    go server2(output2)
    time.Sleep(1 * time.Second)
    select {
    case s1 := <-output1:
        fmt.Println(s1)
    case s2 := <-output2:
        fmt.Println(s2)
    }
}

```

15. Buffered channels- simple example that works:

```go
package main

import (  
    "fmt"
)


func main() {  
    ch := make(chan string, 2)
    ch <- "Thanos"
    ch <- "Iron Man"
    fmt.Println(<- ch)
    fmt.Println(<- ch)
}

```

16. Let's take a close look at this one:

```go
package main

import (  
    "fmt"
    "time"
)

func write(ch chan int) {  
    for i := 0; i < 5; i++ {
        ch <- i
        fmt.Println("successfully wrote", i, "to ch")
    }
    close(ch)
}
func main() {  
    ch := make(chan int, 2)
    go write(ch)
    time.Sleep(2 * time.Second)
    for v := range ch {
        fmt.Println("read value", v,"from ch")
        time.Sleep(2 * time.Second)

    }
}
```

17. Capacity and lengths of channels:

```go
package main

import (
	"fmt"
)

func main() {
    mcu := make(chan string, 3)
    mcu <- "Spiderman"
    mcu <- "Thanos"
    mcu <- "Iron Man"
    fmt.Println("marvel cinematic universe can hold this many characters: ", cap(mcu))
    fmt.Println("marvel cinematic universe has this many characters: ", len(mcu))
    fmt.Println("Thanos kills this guy (reads from the mcu channel)", <-mcu)
    fmt.Println("new marvel cinematic universe is", len(mcu))
}

```

18. WaitGroups struct types:

```go
package main

import (  
    "fmt"
    "sync"
    "time"
)

func process(i int, wg *sync.WaitGroup) {  
    fmt.Println("started Goroutine ", i)
    time.Sleep(2 * time.Second)
    fmt.Printf("Goroutine %d ended\n", i)
    wg.Done()
}

func main() {  
    no := 3
    var wg sync.WaitGroup
    //So we've spawned THREE goRoutines here. All three should be going.
    for i := 0; i < no; i++ {
        wg.Add(1)
        go process(i, &wg) //We need to pass the address here or we will be
        //making multiple copies of wg.
    }
    wg.Wait() //By calling this we are going to wait until ALL goroutines are done.
    fmt.Println("All go routines finished executing")
}
```

19. Basic worker pools:

```go
// In this example we'll look at how to implement
// a _worker pool_ using goroutines and channels.

package main

import "fmt"
import "time"

// Here's the worker, of which we'll run several
// concurrent instances. These workers will receive
// work on the `jobs` channel and send the corresponding
// results on `results`. We'll sleep a second per job to
// simulate an expensive task.
func worker(id int, jobs <-chan int, results chan<- int) {
    for j := range jobs {
        fmt.Println("worker", id, "started  job", j)
        time.Sleep(time.Second)
        fmt.Println("worker", id, "finished job", j)
        results <- j * 2
    }
}

func main() {

    // In order to use our pool of workers we need to send
    // them work and collect their results. We make 2
    // channels for this.
    jobs := make(chan int, 100)
    results := make(chan int, 100)

    // This starts up 3 workers, initially blocked
    // because there are no jobs yet.
    for w := 1; w <= 3; w++ {
        go worker(w, jobs, results)
    }

    // Here we send 5 `jobs` and then `close` that
    // channel to indicate that's all the work we have.
    for j := 1; j <= 5; j++ {
        jobs <- j
    }
    close(jobs)

    // Finally we collect all the results of the work.
    for a := 1; a <= 5; a++ {
        <-results
    }
}

```

20. Worker Pools- step by step. FIRST- create two structs:

```go
type Job struct {  
    id       int
    randomno int
}
type Result struct {  
    job         Job
    sumofdigits int
}
//Notice that we're using NESTED structs here. 
//THIS is how you do nested structs. 
//THIS is how you DON'T do nested structs:
/*
type Configuration struct {
    Val   string
    Proxy struct {
        Address string
        Port    string
    }
}
Just make a new struct!
*/
```

21. Now we're going to create our channels that will write to these structs. Remember how we stated that channels could be of __any type__? Well...

```go
var jobs = make(chan Job, 10)  
var results = make(chan Result, 10)  
```

22. Now we're going to create the function that actually **does** a thing. If you want to take the time to understand it- by all means! But really- this could be __literally anything__- from an abstraction point just consider that this is a function that does-a-thing:

```go
func digits(number int) int {  
    sum := 0
    no := number
    for no != 0 {
        digit := no % 10
        sum += digit
        no /= 10
    }
    time.Sleep(2 * time.Second)
    return sum
}
```

23. NOW- back to this- let's write a function that creates a goRoutine. So what's going on here? 

```go
func worker(wg *sync.WaitGroup) {  
    for job := range jobs {
        output := Result{job, digits(job.randomno)}
        results <- output
    }
    wg.Done()
}
```

We're creating a WaitGroup....and saying: okay- the worker is going to take the waitgroup as an input, then range through all of the **jobs** in the **jobs** channel (defined above to be length 10). The output will be written to the **results** channel as type RESULT (the name of the struct). You see- **digits** is the task that the worker is __actually__ performing.

24. The next thing we want to do is create a **worker pool** (that group of eager, waiting workers waiting for tasks):

```go
func createWorkerPool(noOfWorkers int) {  
    var wg sync.WaitGroup
    for i := 0; i < noOfWorkers; i++ {
        wg.Add(1)
        go worker(&wg)
    }
    wg.Wait()
    close(results)
}
```

So what we're doing here is taking the number of workers that we want to create as a parameter. It will call on the workgroup to ADD 1 **before** creating the goRoutine to add to the waitgroup counter. Finally it calls **wg.Wait()** to allow all of the workers to be created. Once this is done it **closes** the results channel as nothing else will need to be written there.

25. Okay! So we've craeted our worker pool. Now we need to allocate jobs to each of our workers:

```go
func allocate(noOfJobs int) {  
    for i := 0; i < noOfJobs; i++ {
        randomno := rand.Intn(999)
        job := Job{i, randomno}
        jobs <- job
    }
    close(jobs)
}
```

SO- the **allocate** function takes the number of jobs we want to create as the input parameter and assigns a random number (up to 998). It then takes a job struct with the job number and random number and assigns it to the **jobs** channel...it then closes the jobs channel after assigning all jobs.

26. Finally we want to create a **result** function that basically just prints the output from the **results** channel. To use this we're going to create a function that takes another channel with a BOOLEAN type in and writes to that:

```go
func result(done chan bool) {  
    for result := range results { //results is the channel that holds our results
        fmt.Printf("Job id %d, input random no %d , sum of digits %d\n", result.job.id, result.job.randomno, result.sumofdigits)
    }
    done <- true
}
```

27. Finally we are going to create our **main** function measuring time:

```go
func main() {  
    startTime := time.Now()
    noOfJobs := 100 //Obvious
    go allocate(noOfJobs)
    done := make(chan bool)
    go result(done)
    noOfWorkers := 10
    createWorkerPool(noOfWorkers)
    <-done
    endTime := time.Now()
    diff := endTime.Sub(startTime)
    fmt.Println("total time taken ", diff.Seconds(), "seconds")
}
```

Okay- so we start the execution time and then calculate total time taken...then display. We set the number of jobs to 100 and allocate them. We then assign 10 workers to teh process, finish the process, and print out the time. Put all of these into your main package then run it. Check out the time!!

28. NOW- switch from 10 to 20 workers. Run again and take a look at the time. What happened? 

29. Let's create a program with a race condition. **Please note here: you need to run this program on your local machine. Please DO NOT try to run this in the go playground as that is deterministic. Run locally!**

```go
package main  
import (  
    "fmt"
    "sync"
    )
var x  = 0  
func increment(wg *sync.WaitGroup) {  
    x = x + 1
    wg.Done()
}
func main() {  
    var w sync.WaitGroup
    for i := 0; i < 1000; i++ {
        w.Add(1)        
        go increment(&w)
    }
    w.Wait()
    fmt.Println("final value of x", x)
}
```

30. Now let's fix the race condition using a mutex (imagine that "x" here is database and you want to make sure that you don't have multiple inserts and updates happening simultaneously):

```go
package main  
import (  
    "fmt"
    "sync"
    )
var x  = 0  
func increment(wg *sync.WaitGroup, m *sync.Mutex) {  
    m.Lock()
    x = x + 1
    m.Unlock()
    wg.Done()   
}
func main() {  
    var w sync.WaitGroup
    var m sync.Mutex
    for i := 0; i < 1000; i++ {
        w.Add(1)        
        go increment(&w, &m) //It's essential to pass the address of the mutex so that you don't generate a thousand copies of the mutex- which will cause the race condition to still occur.
    }
    w.Wait()
    fmt.Println("final value of x", x)
}
```

31. Now let's look at how to solve this issue with a channel instead:

```go
package main  
import (  
    "fmt"
    "sync"
    )
var x  = 0  
func increment(wg *sync.WaitGroup, ch chan bool) {  
    ch <- true
    x = x + 1
    <- ch
    wg.Done()   
}
func main() {  
    var w sync.WaitGroup
    ch := make(chan bool, 1) //BECAUSE this is a channel with a buffer of ONE- so it will BLOCK ALL OTHER GOROUTINES TRYING TO WRITE TO THE CHANNEL.
    //Now follow that logic through- all other goroutines are stopped until the channel empties...SO- only one goRoutine can work at a time.
    for i := 0; i < 1000; i++ {
        w.Add(1)        
        go increment(&w, ch)
    }
    w.Wait()
    fmt.Println("final value of x", x)
}
```

32. Rate limiting in Golang example:

```go
// <em>[Rate limiting](http://en.wikipedia.org/wiki/Rate_limiting)</em>
// is an important mechanism for controlling resource
// utilization and maintaining quality of service. Go
// elegantly supports rate limiting with goroutines,
// channels, and [tickers](tickers).

package main

import "time"
import "fmt"

func main() {

    // First we'll look at basic rate limiting. Suppose
    // we want to limit our handling of incoming requests.
    // We'll serve these requests off a channel of the
    // same name.
    requests := make(chan int, 5)
    for i := 1; i <= 5; i++ {
        requests <- i
    }
    close(requests)

    // This `limiter` channel will receive a value
    // every 200 milliseconds. This is the regulator in
    // our rate limiting scheme.
    limiter := time.Tick(200 * time.Millisecond)

    // By blocking on a receive from the `limiter` channel
    // before serving each request, we limit ourselves to
    // 1 request every 200 milliseconds.
    for req := range requests {
        <-limiter
        fmt.Println("request", req, time.Now())
    }

    // We may want to allow short bursts of requests in
    // our rate limiting scheme while preserving the
    // overall rate limit. We can accomplish this by
    // buffering our limiter channel. This `burstyLimiter`
    // channel will allow bursts of up to 3 events.
    burstyLimiter := make(chan time.Time, 3)

    // Fill up the channel to represent allowed bursting.
    for i := 0; i < 3; i++ {
        burstyLimiter <- time.Now()
    }

    // Every 200 milliseconds we'll try to add a new
    // value to `burstyLimiter`, up to its limit of 3.
    go func() {
        for t := range time.Tick(200 * time.Millisecond) {
            burstyLimiter <- t
        }
    }()

    // Now simulate 5 more incoming requests. The first
    // 3 of these will benefit from the burst capability
    // of `burstyLimiter`.
    burstyRequests := make(chan int, 5)
    for i := 1; i <= 5; i++ {
        burstyRequests <- i
    }
    close(burstyRequests)
    for req := range burstyRequests {
        <-burstyLimiter
        fmt.Println("request", req, time.Now())
    }
}
```

33. Atomic counters in GO (instead of MUTEXES). What we're doing here is spinning up 100 go routines- each of which execute for a random number of iterations. At each new iteration they increment a shared int and then sleep for a microsecond. SO- each goroutine adds it's number of iterations to a shared counters...and as each iteration increments by ONE we would expect iterations to equal counters, right? Let's see what happens...

```go
    package main
     
    import (
            "fmt"
            "math/rand"
            "sync"
            "time"
            "sync/atomic"
    )
     
    var sharedint uint64
    var sharedcount uint64
    var wg sync.WaitGroup
     
    func readwrite(n int) {
            defer wg.Done() // idiomatic way to indicate  we are done
            iterations := rand.Int() % 1000 + 1000
            atomic.AddUint64(&sharedcount, uint64(iterations))
     
            for i := 1; i <= iterations; i++ {
                    sharedint++
                    time.Sleep(time.Microsecond)
            }
    }
     
    func main() {
            for i := 1; i <= 100; i++ {
                    wg.Add(1)
                    go readwrite(i)
            }
    		// Wait for all goroutines to finish
            wg.Wait()
            fmt.Println("value of shared int =", sharedint)
            fmt.Println("value of shared count =", sharedcount)
    }
```

So what happened here? Well- basically it's the normal race condition- BECAUSE nothing was locking the incremented variable while the goRoutines were working on it there were issues with multiple goRoutines hitting it at the same time. NOW- let's set an atomic counter:

34. Replace the **sharedint++** variable above with this: `atomic.AddUint64(&sharedint,1)` and run again. How's it look now? 
