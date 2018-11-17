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

7. Assuming you have an image called "golang" in there- let's set the container to run on an infinite loop so that we can keep it alive and go in there and code in it: `docker run -td golang`

8. Check that your docker container is running: `docker ps`. Is it running? Cool! 

9. Now let's go in and look around: `docker exec -it golang /bin/bash`. Run an `ls`. See the files there? Good. Let's exit!

## Creating a functional web application in Golang!

10. Now we want to mount our files- so let's mount everything into our docker container and get started! So obviously the first thing we want to do is create a new directory that has the data we're after...so let's do that. Go to your **$GOPATH/src/** and `mkdir webapp` 

11. Under that make a **cmd** directory. This will hold our **main.go** file. Go ahead and create that as well. Please note (Java folks especially!)- **Please do not create a src folder here!**. I realize that there is a **src/** folder in the default golang docker container. It's perfectly fine to have a **src/** at your home root directory but I would encourage you guys NOT to have it in every app. It's not necessary at the application level (this is an opinion- and some will differ...but it's my class sooo....) 

12. Side note: RIGHT NOW we probably just need **built-in** packages...BUT- __what if you want to isolate your imported packages to just this particular app?__ WELL- there are quite a few ways to do this: one is to export a new $GOPATH variable (i.e: in the current session: `export GOPATH=~/go/src/webapp/` for **just this session**). I **know** that this is a lot to manage (and it is a great opportunity for an enterprising young coder to help manage this)- but at the moment it's kind of the wild west with this stuff. Another option is to just **go get** as CMD in the Dockerfile (as discussed above)

13. Okay- so let's use this code to create our webserver. **Bonus points to whoever can name the movie reference without googling!**:

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

14. Now- to deploy this thing we'll need a **Dockerfile** ....obviously...so let's create one. Whereas we can put **docker-compose.yml** files in the **./deployment** folder in our application- an actual docker file has issues with __ADD__ and __COPY__ commands from a parent directory- so instead of sticking it in a directory folder we'll have to put it next to our application root. SO... change directories into your webapp directory and `touch Dockerfile`

15. Now that the Dockerfile has been created- let's populate it! 

```
FROM golang
# create a working directory
WORKDIR /go/src/webapp
# add source code
ADD ./go/src/webapp src/webapp/
# run main.go
CMD ["go", "run", "src/webapp/cmd/main.go"]
```

16. ...and let's test it! First we'll build it (from our root directory in golang_vmware repo): `docker build -t lroncontainer -f ./go/src/webapp/Dockerfile .`

17. And now let's run it (forwarding it to port 8080 and 6060 and removing extra layers): `docker run --rm -it -p 8080:8080 -p 6060:6060 lroncontainer`

18. Kind of optional BUT- it would be nice if we made code changes that were immediately reflected...so going forward I'm going to mount my volumes so when I change them on my host they automatically change on my Docker container. So to go into my docker container: `docker run --rm -it -p 8080:8080 -v $(pwd)/go/src/webapp:/go/src/webapp lroncontainer bash`

## Install some dependencies (management)

19. Now that we've got a very basic application running let's look at installing dependency management- for which we'll use the [dep](https://golang.github.io/dep/) package..which we'll install via our Dockerfile. SO- let's add two lines to our dockerfile and it will look like this:

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

20. Now rebuild (using no-cache because when I was testing this was giving me issues with dep init): `docker build --no-cache -t lroncontainer -f ./go/src/webapp/Dockerfile .`

21. And let's take a look inside. What's changed? `docker run --rm -it -p 8080:8080 -v $(pwd)/go/src/webapp:/go/src/webapp lroncontainer bash`

22. Cool! Dependency management covered! The next thing we're going to want to do is set up some configuration for our application! (Our app will need an api key. We will want to store that api key. NOW- I realize that there is probably a parameter store somewhere that you can use that encrypts this stuff BUT- let's assume that isn't __always__ the case). Let's use **toml** from [BurntSushi](https://github.com/BurntSushi/toml)

23. So let's add the toml package to our Dockerfile. Add this line: `RUN go get github.com/BurntSushi/toml`

24. Now...in order to demonstrate package management we're going to add a __config__ package to our **internal** directory in our application. So let's create an **internal/config** directory. 

25. NOTE that you should probably add __config*__ to your **.gitignore** file to prevent adding a bunch of passwords to, you know...git.

26. Okay- now we're going to create our package...so make a file in the **internal/config** directory called **config.go**. We want to put everything to create our configuration file into this package. Let's walk through how we're going to do this.... FIRST- we're going to create our package name and struct and import (remember we're using the **toml** package). Also NOTE the comments! This is important and :

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

27. I want you guys to note the comments there. This is to help with **godoc**- which we'll go into later but please- keep the comments!! Okay- so next we want to create a function that reads the toml package. Let's add that. Let's review how this one works... 

```go
//CreateConfig Note that this function starts with a capital so...we can export it
func (c *ConfigFile) createConfig(filename string) {
    if _, err := toml.DecodeFile(filename, &c); err != nil {
        HandleError(err)
        return
    }
}
```

28. Finally let's set up a single function that will implement (create) our config file. NOTE that the function name is capitalized. 

```go
//LoadConfig loads the config file
func LoadConfig() ConfigFile {
    var cfg ConfigFile
    cfg.createConfig(CONFIGFILE)
    return cfg
}
```

29. So in the end our **config** file should look like this:

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

29. Let's test our package! We'll start with __importing__ it into our __main.go__ folder...then we'll just see if we can print out the movie key. __Notice how we are doing a pseudonym for our config package__

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

30. Assuming that that worked...let's start work on our api ingest! I'm going to let you guys set up the next section (i.e: where do you think you put the package?)

31. The next section should be done in the **quizzes** section but basically we're going to create a package that reads from the API based on inputs from the config files.

32. Once we have the application done we should build it...
