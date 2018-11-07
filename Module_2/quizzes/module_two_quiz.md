# Module Two Quiz

## The following challenges are intended to test and re-enforce what we've been going over so far. Ideally they should force you to think about how you code. The challenges for this section can be completed in the [go_playground](https://play.golang.org/)

1. Create a basic "Hello <name>" command line application that prints out your name (or someone else's name) using string substitution.

2. Now take that same and add in "...and I am <# of years> old" (you can lie about your age). Repeat for everyone in your group.

3. Print out the binary representation of "You are tearing me apaht Lisa!"

4. Cast pi as an integer

5. How do we use a function name to decide whether it can be exported? 

6. In your virtual Machine- create a package (outside of main) that calculates the Fibonacci sequence and import it using skills from Module one

7. **GO GET** a package that hits a Chuck Norris api and returns facts about Norris.

8. Import that package into main and print out Chuck Norris facts as part of your application

9. Create a list of your favorite films and print them (we haven't gone through the **for** loop yet so instead print them out by index...in order)

10. Using DEFER and PANIC- create a function that panics if the word "Klingon" is entered (as a string) into the function. Have the panic say "KLINGONS DECLOAKING!!". Make sure it fails.

11. Now add in a DEFER call to recover from that before you println.

12. Create a function that takes in the array of your favorite films and splits it into three separate slices and prints them out.

13. Create a function that makes three empty slices based on lengths that you input (the inputs should decide the length)

14. Create a map that lists a your top five favorite films with key being film name and value being the year it came out and print it out.

15. Make a map that lists a film as the key and then an inner map that also lists all of the characters-as-keys and actors-as-values

16. Create two structs: 
    * One will be called "jedi" and have fields of "name" (string), "good" (bool), weapon string, powerLevel int
    * The other will be called "fighter" and have the same fields

17. Instantiate your struct assigning names and fields to each of those. The variable name for each of those should be the character names (ex: "darth := jedi{}" or "han := fighter{}")

18. Create methods for your struct that capitalizes the name. Create a second method (called ouch) that subtracts points from powerLevel (I leave it to you. BONUS: create multiple methods that subtract points based on what they are hit by)

19. Create a "Weapons" method for jedi and one for fighter (taking that struct as the input). Have the jedi "Weapons" method be "FWOOOOSH" (for a lightsaber, get it?) and the second "Weapons Method" for the fighter be "Pew! Pew!"

20. Make an interface for these methods, instantiate it, and have some Star Wars among your characters.