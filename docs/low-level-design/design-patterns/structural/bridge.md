# Bridge

The **bridge** design pattern allows for large classes or sets of closely related classes to be split into two separate hierarchies - abstraction and implementation - which can be developed independently of each other.




// REMOVE
- problem: you have a program that has two dimensions of things you want to implement 
    - example a program that represents shapes
        1. need to implement all different types of shapes (square, circle)
        2. foreach you also have to implement different colors (red, blue)
    - do this in one giant shape class is bad becuase you would have to do...
        1. red square
        2. blue squre
        3. red circle
        4. blue circle
- Solution: instead split up implementations...
    - have 1 class shape (implement: circle square functions)
    - have 1 class color (implement: red blue functions)
    - have shape class contain a color class instance
    - shape class functions can now reference color class functions
    - shape and color has some basic core functions (getsize, get color etc)
    - to implement more shapes we inherit from shape class
    - to impkement more color we inherit form color class
    - making the combos (red square) means we create a square subclass which takes in a red subclass in its constructor.

- Definition of implementation and abstraction are not code related
    - abstraction (what are the base functions we want to see out of an object)
    - implementation (code logic needed to help build base functions listed in abstraction)
    - tying it to shape example... 
        - abstractions would be the different shapes
        - implementation is the different possible colors

- Questions: 
    - is bridge pattern 1 to 1, (abstract implemnt) i dont think so
    - if its not 1 to 1, does it connect through chaining?
    - chaining multiple dimensions seems clutter as well..

## Problem

## Solution

## Structure

## Bridge Example

### Non Bridge Example

### Bridge Example


