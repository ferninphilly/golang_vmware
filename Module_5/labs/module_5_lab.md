# Module Five Lab: DevOps and Deployment in GO

## Command line applications in Docker

1. On your **host** machine verify that you have the correct directory structure built. You need to have the following built at a minimum:

```
|- $GOPATH
|-- src/
|----   github.com/
|------     <your username>
|-- bin/
|-- pkg/
```

2. Let's take a look at a quick __flag__ example for Command Line applications:

```go
// [_Command-line flags_](http://en.wikipedia.org/wiki/Command-line_interface#Command-line_option)
// are a common way to specify options for command-line
// programs. For example, in `wc -l` the `-l` is a
// command-line flag.

package main

// Go provides a `flag` package supporting basic
// command-line flag parsing. We'll use this package to
// implement our example command-line program.
import "flag"
import "fmt"

func main() {

    // Basic flag declarations are available for string,
    // integer, and boolean options. Here we declare a
    // string flag `word` with a default value `"foo"`
    // and a short description. This `flag.String` function
    // returns a string pointer (not a string value);
    // we'll see how to use this pointer below.
    wordPtr := flag.String("word", "foo", "a string")

    // This declares `numb` and `fork` flags, using a
    // similar approach to the `word` flag.
    numbPtr := flag.Int("numb", 42, "an int")
    boolPtr := flag.Bool("fork", false, "a bool")

    // It's also possible to declare an option that uses an
    // existing var declared elsewhere in the program.
    // Note that we need to pass in a pointer to the flag
    // declaration function.
    var svar string
    flag.StringVar(&svar, "svar", "bar", "a string var")

    // Once all flags are declared, call `flag.Parse()`
    // to execute the command-line parsing.
    flag.Parse()

    // Here we'll just dump out the parsed options and
    // any trailing positional arguments. Note that we
    // need to dereference the pointers with e.g. `*wordPtr`
    // to get the actual option values.
    fmt.Println("word:", *wordPtr)
    fmt.Println("numb:", *numbPtr)
    fmt.Println("fork:", *boolPtr)
    fmt.Println("svar:", svar)
    fmt.Println("tail:", flag.Args())
}
```

If you don't have **Docker** installed on your system- install it now. To install docker on ubuntu (or AWS ami):

2. On the command line: `sudo yum update -y` then `sudo yum install -y docker`

3. Once installed let's start 'er up! `sudo service docker start`

4. Because we're sick of using **sudo**: `sudo usermod -a -G docker ec2-user` (you probably have to reboot your system to have this work without sudo)

5. Now either __with__ or __without__ sudo: `docker pull golang`

6. So let's make sure we have our docker image: `docker images`

7. Assuming you have an image called "golang" in there- let's set the container to run on an infinite loop: