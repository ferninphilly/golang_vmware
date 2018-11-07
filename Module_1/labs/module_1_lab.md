# Lab One: Basic setup

## Quick side note:

1. Before we start let's install git. Start up your virtual machine and log into it.

2. Once you're logged in go to the command line (if it's not already up) and run `sudo apt install git`

3. Now we have github installed and we're ready to `git init` in the next section. 

## Structure of a GO application:

1. Let's create our WORKSPACE. 

2. Navigate to your mounted file: `ch /mnt/hgfs/go`

3. Do you see the "src" and "bin" folders there? Go into the **src** folder ( `cd src/` ). Currently there is just a **test** folder in there. We want to make MORE sub folders in there and save our data to various GIT repos. From within the current folder `mkdir github.com`

4. Run `ls`. We have created a github.com folder now which will house all of our various repositories. Change directory into that folder with a `cd github.com`

5. We are ready to create our project now. You can call this project whatever you'd like but for argument's sake let's call it __golang_practice__. SO...`cd /mnt/hgfs/go/ && mkdir golang_practice && cd golang_practice`

6. **NOTE**: IF YOU WANT to practice making your own repo follow the steps [here](https://help.github.com/articles/create-a-repo/)...but that's outside of the scope of this class! In the short term let's just run `git init` from within our **golang_practice**

7. Quick check...run a `pwd` to make sure that you are in the **/mount/hgfs/go/src/github.com/golang_practice/**. Good? Keep going...

8. Now we're ready to begin! The next folder we want to create is our **cmd** folder. `mkdir cmd`. We'll go over the "Command" folder in the presentation

9. Now let's create our **internal** folder. This is where we're going to put the __vast majority__ of our application code. It will be a sub folder as well...so we'll need to do a `mkdir -p internal/app/practiceapp/` and a `mkdir -p internal/pkg`

10. Create a **pkg** directory at the top level...so relatively simply: `mkdir -p pkg`

## Downloading packages

1. Okay- so now let's start by getting a package- the equivalent of `pip install`. Let's start with a prettify package...specifically this [one](https://godoc.org/github.com/kr/pretty)

2. Run `go get github.com/kr/pretty`. Where did it put the package? 

3. So now that we have an understanding of how this works...let's see how to actually __use__ this wonderful package that we've just downloaded! So to do that we need to create a **main.go** file.

4. Where does the **main.go** file go? (hint: `cd /mnt/hgfs/go/src/golang_practice/cmd && touch main.go`)

5. Now switch over to your preferred coding gui on your host system and open **main.go**. Put the following in to the first line of that file: `package main`

This module is complete!
