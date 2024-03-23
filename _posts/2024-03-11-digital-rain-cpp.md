---
layout: post
title: A Project in Modern C++
tags: cpp coding project
categories: demo
---

## Introduction

As part of the C++ Programming module in my final year at ATU Galway, I've been assigned to develop a modern C++ console application to execute my own interpretation of **Digital rain**. So what is digital rain?

Digital rain is the visual effect which was made famous when it was used for the  opening scene in the 1999 film “The Matrix”. The digital rain is displayed as a stream of green characters falling  vertically down the screen. This effect is still strongly linked to the film and is often used in films to represent types of computer processes, such as hacking. 
<img src="https://raw.githubusercontent.com/Deniscostello/digital-rain-cpp/main/docs/assets/images/Matrix-Rain.png" width="600" height="300">

*Figure 1*

## Software Design And Test
One key element I wanted in this project was good coding practices. I wanted to ensure this project was using modern C++ and Object-Oriented Programming (OOP).

### Object Oriented Programming (OOP)

The project has a class called **Droplet**. This class has five variables,
- Int x - for the x coordinate of the console, 
- Int y - for the y coordinate of the console,
- Int length - for the desired length, 
- Static int count - to increment on default constructor and decrement on destructor,
- Vector of characters raindrops - to store the characters that would be falling down the screen. 

This class also has five key functions,
- Raindrop – main function in Droplet,
- displayRain - print raindrops,
- GotoXY – go to x and y coordinate on console,
- FillDroplets – fill raindrops,
- SetTextColor – set the color of raindrop



<img src="https://raw.githubusercontent.com/Deniscostello/digital-rain-cpp/main/docs/assets/images/UML-Droplet.png" width="300" height="300"> 

*Figure 2*


Using OOP my code was faster and easier to execute, while maintaining a clear structure for the project. OOP allowed me to keep my C++ code DRY “Don’t Repeat Yourself".

### Test functions

As I wanted to keep my main code as clean as possible, I used **RunTest** as a global procedural function to run my initial test runs. **RunTest** calls my unit test for Droplet. Testing the default constructor, 1-argument constructor, 2-argument constructor, all-argument constructor, and the copy constructor.



## Algorithm 

### Main
When the project execution begins, main.cpp is the first to run. The first line in main is **RunTest** function. Once all the unit tests are complete the main thread is put in sleep mode for 2 seconds, this is so I can view the test results. After the delay the screen is cleared using *system(“cls”)*. Once the screen is cleared, the cursor visibility is set to false to ensure the cursor will not be visible when the raindrops begin. Using windows **GetStdHandle** API function to get the handle for the console. Then setting cursorInfo to type **CONSOLE_CURSOR_INFO**. Setting the cursor to false.  


<img src="https://raw.githubusercontent.com/Deniscostello/digital-rain-cpp/main/docs/assets/images/SetCursorFalse.png" width="600" height="100">

*Figure 3.1*


### Threads
This project uses multithreading to execute threads concurrently. Using a for loop, a new thread is added to a vector of threads. I create a variable called numThrreads which is used to determine the sizes of the vector. Looping through when i is less than numThreads. Using a new instance of my class **Droplet** dl. Each thread will execute the Raindrop function with its own copy of the dl object. Before creating a new thread, the current thread is paused for 100 milliseconds. It is used to create a timing between threads. 


<img src="https://raw.githubusercontent.com/Deniscostello/digital-rain-cpp/main/docs/assets/images/ThreadsImg.png" width="300" height="300"> 

*Figure 3.2*

### Droplet

#### *Raindrop*
Raindrop initially gets the width and height of the console. Using the windows **GetStdHandle** API function to get the handle of the console window. Getting the consoles screens dimensions using windows **CONSOLE_SCREEN_BUFFER_INFO** API function. Checking if GetConsoleScreenBufferInfo returns a nonzero value, if it does then the width is set to the number of spaces from right to left, and the height is set from bottom to top.

#### *Random*
I want both the size of the raindrops and where they fall from to be random. Initially I used the standard C++ rand() function, however after running the project a few times it was obvious that it wasn’t truly random. After speaking to my lecturer, Michelle Lynch, I found out to create a truly random sequence I would need to use the standard library < random > for it's **default_random_engine**  and the < chrono > to use time_since_epoch, “In computer science, the epoch time, also known as Unix time is considered the number of seconds since 1 January 1970” [1]. 


<img src="https://raw.githubusercontent.com/Deniscostello/digital-rain-cpp/main/docs/assets/images/Random engine.png" width="600" height="100">

*Figure 3.3*


For the length of the raindrop, it will be anywhere from 5 and the randomly generated distY. Where distY returns a random number between 0 and 10. Therefore the vector of characters raindrops, can be any between 5 and 15 characters in length. After the random numbers are generated, the **FillDroplets** function is called.


#### *FillDroplets*
The **FillDroplets** function adds characters to the raindrops vector. It initially puts a blank character at the beginning of raindrops. Loops for the length set by the random engine in **Raindrop**. As there is a blank character added to raindrops before the loop, raindrops will always be greater than length by 1. Each iteration of the loop I use the random engine again. This time I return a random integer from 32 to 63. This two ranges were inputted by me. 


<img src="https://raw.githubusercontent.com/Deniscostello/digital-rain-cpp/main/docs/assets/images/ASCII Table.jpeg" width="450" height="300">

*Figure 3.4*

When I first saw Figure 3.4 above the second column really stood out to me, so I wanted my digital rain to print only the characters in the second column (32 and 63) in Figure 3.4. Using type casting I converted the randomly chosen integer from the engine to its corresponding character in the ASCII table. Then add that character to the raindrops. After the raindrops vector is full the **displayRain** function is called.

#### *displayRain*
This function takes in an integer, this integer is the random integer from distX which gives the x coordinate for the raindrops. The function has a for loop which loops through the size of raindrops. As this project uses threads, each thread uses the same resource to print the characters. Therefore, I need a mutex to lock the std::cout resource. Using std::mutex to protect the resource. Once  mutex is locked I call my GotoXY function. 

GotoXY takes in the randomly selected X coordinate and the Y coordinate. And sets the XY coordinates of the cursor to the variables given, using the windows **SetConsoleCursorPosition** API function. 


Inside displayRain an if statement checks if index i is modulus of 3, if it is I call SetTextColor function which uses windows **SetConsoleTextAttribute** API function to set the color to green, otherwise it will set the color to light green. 

Before printing the character in the raindrops vector, I check if the Y coordinate is less than 29 (the console window height). At the end of the for loop I increment the y variable by 1 to move the raindrops down, creating the drop by drop falling effect. After the for loop the y variable is set to the current value of y minus the length generated by the random engine. As previously stated above, raindrops is greater than length by 1 because of pushing the blank character before looping through the size of length. Meaning when displayRain is called again in the while(1) the Y coordinate will be moved down by one from the previous loop. This creates the raindrop falling effect. I added a 100 millisecond delay in the while(1) loop as I found it more visual appealing.
 
#### *End Of Main*
Once all threads are created, each thread in the vector of threads are joined by using the .join(). Once all threads are joined, main exits the for each loop and the program ends.


## Modern C++

As C was the base coding language in my first few years in college,  I wanted to ensure that I only used C++ for this project. Utilizing features like smart pointers, range-based for loops, classes. Also for its easy to understand / readability. Using standard library iostream header library to output the rain onto the command line.  Also < chrono > header library for timing and < thread > for concurrent execution, allowing more than one rain drop to fall.

## Problem Solving

Once I got a single raindrop to fall down the screen I knew my next step was to create threads. So I looked up how to create threads online and added code found from cplusplus reference [2] to create two threads. However, the output was not how I expected the code to behave. The raindrops would print in a column together and skip some characters in raindrops. After doing more research I found out that both threads were using the same resource to print and I would need to use the standard library std::mutex so that only one thread could have access to the resource at one time. 

Once I sorted out the mutex  issues, another one happened, the raindrops were printing from left to right instead of top to bottom. Using the debugger I was able to find out that the y coordinate was being set to very big negative integers. Thanks to the debugger I was able to see it was a problem with the y coordinate. So I looked at where I was setting y, and I noticed I didn’t have it set in my copy constructor. Once I set y=0 in my copy constructor the issue was fixed. In a way, I’m glad it came up because it helped me become more comfortable using the debugger.



## References

[1] "What is the Epoch Time?". [Online]. medium.com. Available:https://medium.com/@kavindaperera25/what-is-the-epoch-time-f46c94e7a6e7#:~:text=In%20computer%20science%2C%20the%20epoch%20time%2C%20also%20known%20as%20Unix,time%20is%20measured%20in%20seconds. Accessed: Mar 21, 2024.


[2] "Thread". [Online]. cplusplus.com. Available: https://cplusplus.com/reference/thread/thread/. Accessed: Mar 22, 2024.
