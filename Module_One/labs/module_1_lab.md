# Lab One: Basic setup

## Quick side note:

1. Before we start let's install git. Start up your virtual machine and log into it.

2. Once you're logged in go to the command line (if it's not already up) and run `sudo apt install git`

3. Now we have github installed.

## Structure of a GO application:

1. Let's create our WORKSPACE. 

2. Navigate to your mounted file: `ch /mnt/hgfs/go`

3. Do you see the "src" and "bin" folders there? Go into the **src** folder ( `cd src/` ). Currently there is just a **test** folder in there. We want to make MORE sub folders in there and save our data to various GIT repos. From within the current folder `mkdir github.com`

4. Run `ls`. We have created a github.com folder now which will house all of our various repositories. Change directory into that folder with a `cd github.com`

5. We are ready to create our project here. You can call this project whatever you'd like but for argument's sake let's call it __golang_practice__. SO...`mkdir golang_practice && cd golang_practice`

6. Quick check...run a `pwd` to make sure that you are in the **/mount/hgfs/go/src/github.com/golang_practice/**. Good? Keep going...

7. Now we're ready to begin! The next folder we want to create is our **cmd** folder. `mkdir cmd`
