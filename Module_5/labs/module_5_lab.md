# Module Five Lab: DevOps and Deployment in GO

## Command line applications in Docker

1. Let's take a look at a quick __flag__ example for Command Line applications:

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

2. Now we're going to look at creating our command line application: **nbrFiles!!**. Let's see how to create this. FIRST OFF- we need to  create  this file on our directory- so head over to your $GOPATH directory `cd $GOPATH`.

3. Let's create a simple file called **nbrFiles.go** with `touch nbrFiles.go`. Head in there and let's create our application (we'll go through this line by line):

```go
package main

import (
    "flag"
    "fmt"
    "io/ioutil"
    "log"
    "os"
)

func main() {
    cwd, err := os.Getwd()
    if err != nil {
        log.Fatal(err)
    }
    dir := flag.String("dir", cwd, "a string describing directory to check")
    flag.Parse()
    //Let's error catch!
    if string((*dir)[0]) != "/" {
        fmt.Println("Please enter a valid directory structure")
        panic("They didn't enter a valid directory structure!!")
    }
    files, _ := ioutil.ReadDir(*dir)
    fmt.Println(len(files))
}
```

4. Run this application a couple of times with `go run nbrFiles.go`. It should return a number equivalent to the number of files in the current working directory. Is it working? Hopefully! If it is...let's make this a permanent part of our OS! 

5. We're going to go through each of the ways that we can create binaries for this thing. __first__ we're going to just do a simple `go build` and create the binary in our home directory- so `go build nbrFiles.go`. Now do an `ls` on your current directory. Is there a new file there? Awesome!

6. Now that we have the file let's take her for a spin!:
    * `./nbrFiles` How many files in the current directory?
    * `./nbrFiles -dir=$HOME` How many files in your home directory?
    * `./nbrFiles -dir=IshouldFailHard!!` Did this fail?

7. Awesome! Assuming this has worked thus far let's take it to the next level! Let's make this able to work from **anywhere** on our OS. Run the following command: `go install nbrFiles.go`. Now cd into your home directory (or anywhere): `cd ~` and try running the command `nbrFiles`. Cool, huh?

### Creating a web application!

If you don't have **Docker** installed on your system- install it now. To install docker on ubuntu (or AWS ami):

8. On the command line: `sudo yum update -y` then `sudo yum install -y docker`

9. Once installed let's start 'er up! `sudo service docker start`

10. Because we're sick of using **sudo**: `sudo usermod -a -G docker ec2-user` (you probably have to reboot your system to have this work without sudo)

11. Now either __with__ or __without__ sudo: `docker pull golang`

12. So let's make sure we have our docker image: `docker images`

13. Assuming you have an image called "golang" in there- let's set the container to run on an infinite loop so that we can keep it alive and go in there and code in it: `docker run -td golang`

14. Check that your docker container is running: `docker ps`. Is it running? Cool! 

15. Now let's go in and look around: `docker exec -it golang /bin/bash`. Run an `ls`. See the files there? Good. Let's exit!

## Creating a functional web application in Golang!

16. First off let's review our GO directory structure:

```
|- $GOPATH
|-- src/
|----   github.com/
|------     <your username>
|-- bin/
|-- pkg/
```

17. Now we want to mount our files- so let's mount everything into our docker container and get started! So obviously the first thing we want to do is create a new directory that has the data we're after...so let's do that. Go to your **$GOPATH/src/** and `mkdir webapp` 

18. Under that make a **src** directory, a **bin** directory, and a **pkg** directory. This is, at the most basic level, the general setup for a go application. Remember what the inside of our **go** docker container looked like? Well- we want to mirror that here. 

19. Side note: RIGHT NOW we probably just need **built-in** packages...BUT- __what if you want to isolate your imported packages to just this particular app?__ WELL- there are quite a few ways to do this: one is to export a new $GOPATH variable (i.e: in the current session: `export GOPATH=~/go/src/webapp/` for **just this session**). I **know** that this is a lot to manage (and it is a great opportunity for an enterprising young coder to help manage this)- but at the moment it's kind of the wild west with this stuff. Another option is to just **go get** as CMD in the Dockerfile (as discussed above)

20. Okay- so let's use this code to create our webserver. **Bonus points to whoever can name the movie reference without googling!**:

```go
package main

import (
  "net/http"
  "strings"
)
//Notice that the writer is (the Response Writer) is not a pointer but the
//http.Request IS. Why is that? Response Writer is of type interface which
//implements a pointer further back to a non-exported STRUCT. We can't use 
//the non-exported struct directly- only through the interface here.
//The REQUEST is a struct that we want to point to and alter so the server
//can see it. If we created new structs every time- where would the server see them?
func psychlo(w http.ResponseWriter, r *http.Request) {
  message := r.URL.Path
  message = strings.TrimPrefix(message, "/")
  message = "Are you a man-animal or are you a psychlo " + message + "?"

  w.Write([]byte(message))
}

func main() {
  http.HandleFunc("/", psychlo)
  if err := http.ListenAndServe(":8080", nil); err != nil {
    panic(err)
  }
}
```

21. Now- to deploy this thing we'll need a **Dockerfile** ....obviously...so let's create one. Whereas we can put **docker-compose.yml** files in the **./deployment** folder in our application- an actual docker file has issues with __ADD__ and __COPY__ commands from a parent directory- so instead of sticking it in a directory folder we'll have to put it next to our application root. SO... change directories into your webapp root directory and `touch Dockerfile`

22. Now that the Dockerfile has been created- let's populate it! 

```
FROM golang
# create a working directory
WORKDIR /go/src/webapp
# add source code from go/src/webapp
ADD ./go/src/webapp src/webapp/
# run main.go
# Note where the default $GOPATH directory exists- it points to /go/- so we need to run from there when we use "go run"- hence the command here
CMD ["go", "run", "src/webapp/src/main.go"]
```

23. ...and let's test it! First we'll build it (from our root directory in golang_vmware repo): `docker build -t lroncontainer -f ./go/src/webapp/Dockerfile .`

24. And now let's run it (forwarding it to port 8080 and 6060 and removing extra layers): `docker run --rm -it -p 8080:8080 -p 6060:6060 lroncontainer`

25. Kind of optional BUT- it would be nice if we made code changes that were immediately reflected...so going forward I'm going to mount my volumes so when I change them on my host they automatically change on my Docker container. So to go into my docker container: `docker run --rm -it -p 8080:8080 -v $(pwd)/go/src/webapp:/go/src/webapp lroncontainer bash`

## Install some dependencies (management)

26. Now that we've got a very basic application running let's look at installing dependency management- for which we'll use the [dep](https://golang.github.io/dep/) package..which we'll install via our Dockerfile. SO- let's add two lines to our dockerfile and it will look like this:

```
FROM golang
# create a working directory
WORKDIR /go/src/webapp
# add source code
ADD ./go/src/webapp src/webapp/
# Add in dep- which will handle our dependencies going forward
RUN curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh
RUN dep init
RUN go get github.com/BurntSushi/toml
# run main.go
CMD ["go", "run", "src/webapp/cmd/main.go"]
```

27. Now rebuild (using no-cache because when I was testing this was giving me issues with dep init): `docker build --no-cache -t lroncontainer -f ./go/src/webapp/Dockerfile .`

28. And let's take a look inside. What's changed? `docker run --rm -it -p 8080:8080 -v $(pwd)/go/src/webapp:/go/src/webapp lroncontainer bash`

29. Cool! Dependency management covered! The next thing we're going to want to do is set up some configuration for our application! (Our app will need an api key. We will want to store that api key. NOW- I realize that there is probably a parameter store somewhere that you can use that encrypts this stuff BUT- let's assume that isn't __always__ the case). Let's use **toml** from [BurntSushi](https://github.com/BurntSushi/toml)

30. So let's add the toml package to our Dockerfile. Add this line: `RUN go get github.com/BurntSushi/toml`

31. Now...in order to demonstrate package management we're going to add a __config__ package to our **internal** directory in our application. So let's create an **internal/config** directory. 

32. NOTE that you should probably add __config*__ to your **.gitignore** file to prevent adding a bunch of passwords to, you know...git.

33. Okay- now we're going to create our package...so make a file in the **internal/config** directory called **config.go**. We want to put everything to create our configuration file into this package. Let's walk through how we're going to do this.... FIRST- we're going to create our package name and struct and import (remember we're using the **toml** package). Also NOTE the comments! This is important and :

```go
//Package config reads the toml file and puts config data into a struct.
package config

import (
    "log"
    "github.com/BurntSushi/toml"
)

//ConfigFile struct to contain the configuration information for our api
type ConfigFile struct {
    Moviekey string
    Owner    string
}
```

34. I want you guys to note the comments there. This is to help with **godoc**- which we'll go into later but please- keep the comments!! Okay- so next we want to create a function that reads the toml package. Let's add that. Let's review how this one works... 

```go
//CreateConfig Note that this function starts with a capital so...we can export it
func (c *ConfigFile) createConfig(filename string) {
    if _, err := toml.DecodeFile(filename, &c); err != nil {
        HandleError(err)
        return
    }
}
```

35. Finally let's set up a single function that will implement (create) our config file. NOTE that the function name is capitalized. 

```go
//LoadConfig loads the config file
func LoadConfig() ConfigFile {
    var cfg ConfigFile
    cfg.createConfig(CONFIGFILE)
    return cfg
}
```

36. So in the end our **config** file should look like this:

```go
package config

import (
    "log"

    "github.com/BurntSushi/toml"
)

const CONFIGFILE = "/go/src/webapp/internal/cfgfile/moviedb.toml"

type ConfigFile struct {
    Moviekey string
    Owner    string
}

//HandleError Generic Error Handler
func HandleError(e error) {
    if e != nil {
        log.Print(e)
    }
}

//CreateConfig Note that this function starts with a capital so...we can export it
func (c *ConfigFile) createConfig(filename string) {
    if _, err := toml.DecodeFile(filename, &c); err != nil {
        HandleError(err)
        return
    }
}

//LoadConfig loads the config file
func LoadConfig() ConfigFile {
    var cfg ConfigFile
    cfg.createConfig(CONFIGFILE)
    return cfg
}
```

37. Let's test our package! We'll start with __importing__ it into our __main.go__ folder...then we'll just see if we can print out the movie key. __Notice how we are doing a pseudonym for our config package__

```go
package main

import (
    "fmt"
    cg "webapp/internal/config"
)

func main() {
    cfg := cg.LoadConfig()
    fmt.Println("Here is the config: " + cfg.Moviekey)
}
```

38. Assuming that that worked...let's start work on our api ingest! I'm going to let you guys set up the next section (i.e: where do you think you put the package?). Let's talk through this- but I want you guys to do this one because the best way to learn GO is to code in GO. 

39. The next section should be done in the **quizzes** section but basically we're going to create a package that reads from the API based on inputs from the config files.

## GODOC

40. Let's go over one more thing which is really neat- the `godoc` command. Boot up your docker container but add in port forwarding to port 6060 (remember how to do this?). 

41. Head into your docker container (again- I don't want to repeat this- if you can try to remember how to do this) and, from inside your docker container, go to your command line and run this command: `godoc -http=:6060`. 

42. SO- in the top level section you see a section called "Packages". This should map to your **pkg** folder- so anything you have in there should be available.

## Multi-stage Docker Builds

43. So- we have now built a docker application....and we're ready to deploy it! Now- we're not going to use the web application we built (as I can't guarantee that everyone has completed the homework!) so instead we're going to deploy another, very simple application.

44. To test our knowledge- add a new project to your **go/src/** folder. Set it up so that it's ready  to be added to a docker container. Call it __deploymeapp__ and add the (hint, hint) **THREE** necessary files to the application!

45. Here's the code you can put in your **main.go** file at the root of your application:

```go
package main

import (
    // Import the gorilla/mux library we just installed
    "fmt"
    "net/http"

    "github.com/gorilla/mux"
)

func main() {
    // Declare a new router
    r := mux.NewRouter()

    // This is where the router is useful, it allows us to declare methods that
    // this path will be valid for
    r.HandleFunc("/", handler).Methods("GET")

    // We can then pass our router (after declaring all our routes) to this method
    // (where previously, we were leaving the secodn argument as nil)
    http.ListenAndServe(":8080", r)
    fmt.Println("Server is  up and running on localhost:8080!")
}

func handler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, `My name is Max.
    My world is fire. And blood.
    Once, I was a cop; a road warrior searching for a righteous cause.
    As the world fell, each of us in our own way was broken.
    It was hard to know who was more crazy.
    Me... or everyone else. Here they come again.
    Worming their way into the black matter of my brain.
    I told myself... they cannot touch me. They are all dead. 
    I am the one who runs from both the living and the dead.
    Hunted by scavengers. Haunted by those I could not protect.
    So I exist in this Wasteland.
    A man reduced to a single instinct: survive.
    -Mad Max`)
}
```

46. And here is the Dockerfile we can use for that:

```
FROM golang
# create a working directory
WORKDIR /go/src/deploymeapp
# add source code
ADD ./go/src/deploymeapp /go/src/deploymeapp/
# Add in dep- which will handle our dependencies going forward
RUN curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh
RUN go get github.com/gorilla/mux
RUN dep init
RUN dep ensure -update github.com/gorilla/mux
CMD ["go", "run", "src/main.go"]
```

47. Build out the docker image. Take it for a test run: `docker run --rm -it -p 8080:8080 -v $(pwd)/go/src/deploymeapp:/go/src/deploymeapp deployme bash` Working? Awesome! Let's get it up and running as a container. Exit out of there (`exit`) and go `docker run --rm -td -p 8080:8080 -v $(pwd)/go/src/deploymeapp:/go/src/deploymeapp deployme`. Then `docker ps`. See it? Awesome!

48. Let's check the size of the container: `docker ps -s --format "{{.Size}}"`

49. Okay...we love our application but we want something a **bit** easier to deploy. Remember our `go install` and `go build` commands from above? WELL- when we deploy wouldn't it make **more** sense to reduce our entire application to a **go binary** file and then use  **that** to deploy!! So let's do that! Here's what our new Dockerfile can look like:

```
FROM golang
# create a working directory
WORKDIR /go/src/deploymeapp/src/
RUN go get github.com/gorilla/mux
# I'm simplifying this code to JUST copy the main.go file into the root directory here
# The reason for this is  to simplify the next command
COPY ./go/src/deploymeapp/src/main.go .
# This is the GO BUILD command that will run relative to the WORKDIR above- so the main file
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main .

#Now we're going to build a second stage AFTER we've built our binary!
FROM alpine:latest  
WORKDIR /root/
COPY --from=0 /go/src/deploymeapp/src/ .
# If there are supporting things, like ca-certs- you would need to add them to this machine as well
# Now we just run the command from the binary!
CMD ["./main"]  
```

50. So- add that dockerfile and then let's build the most minimal image (**alpine latest** is pretty much bare metal).  Let's run this thing: `docker run -td -p 8080:8080 secondstage` 

51. Let's run a quick `docker ps -s` to see the size of the new container. I have a....pretty small container there running my webserver (oh yeah- and also- let's check to make sure that my binary is working- go to **localhost:8080**). Does Mad Max have anything to say to you?