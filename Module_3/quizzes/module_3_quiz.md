# This is the quiz section for module 3

## At this point you should be able to solve this one...so fork the repo and let's see what we can do!

1. Create a function that takes two inputs and checks if one is the square root of the other (hint: you need to use the "math" package). Return a message if the second input IS the square root of the first one.

2. Write a custom "is in array" function that returns a value if the input input is IN the array (second input). **Bonus:** Can you create a function that can take strings OR ints as inputs? (look at switch statements and interfaces)

3. Use a __switch__ statement combined with interface **Type casting** to create a function that returns every single input as a string

4. Create a **switch** statement function that determines if an input based on what second it currently is is **even** or **odd**

5. Create a function that does a **fallthrough** switch statement that takes a Star Wars film title as an input and then falls through for the rest of the films (episodes 1 through 8)

6. Create a switch function statement that assigns a grade based on range with an input as an int-- so for example "91" would be an "A" and 72 would be a "C"

7. Using the previous function, add in a **Label** that you **GOTO** when you get the Phantom menace. I'll leave it to you what function you want to do within that label (make lowercase, replace with "sucks")

8. We're going to create startships for **Star Trek**- We will have an **Enterprise** struct and a **Klingon Bird-of-Prey** struct. They will ALL havet the ability to **fire_photon_torpedoes()** and **warp_speed()** but ONLY the Klingon ship can **cloak()**. You should have a __state__ called "NbrOfTorpedoes". You should use **pointers** for the functions to the structs and an **interface** for things that all ships can do.

9. Now we're also going to need a function that keeps count of our photon torpedoes- so, using pointers, we're going to reduce the number of photon torpedoes with each call of **fire_photon_torpedoes**. 

10. Create a string that **looks** a lot like a JSON object (so- looks like this: `{ "Movies: }`). Put your three favorite movies in a list as separate objects with the key "title" and then the movie name. You will then add a key "Year made" with an INT for the year and "actors" with a list of actors from IMDB. Create a struct called "Movie". Now create a list of them. and unmarshal your data into that struct.

11. Once you have everything in the struct- print out your movie titles, actors, and the years made.

### For this next section you need to go to your vm or docker container

12. Create a function that returns the total area of a circle (pi * radius squared)Call the function something simple. Write a file next to this one using the proper ending of the filename to make it a test file. 

13. Write a test table in the test file and test to make sure that you get the correct area of the circle every time you run the function using five different inputs. Run the test

14. Add in a second function that benchmarks the first one (*b). How many nanoseconds is it taking? Run the benchmark test.

15. go and get the dep package. __go get__ 3-5 other packages and see if you can create an effective Gopkg.toml from them. Watch for the commit id. 


