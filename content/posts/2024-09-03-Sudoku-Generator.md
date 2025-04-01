---
title: Sudoku generator
summary: A program that generates a 9 by 9 sudoku game written in C#
date: 2024-09-03
tags: ["CSharp", "projects"]
---

## The problem

- Generate a valid (only one answer) 9x9 sudoku game.
    Also the rules of sudoku are the following:
    - No same number on one row or collumn.
    - No same number in the same 3x3 square of numbers

    For simplicity reasons I named the 3x3 square a quadrant. I don't 
    know if this is the official definition but it sounded right so I 
    stuck with it.

## Coordinates for our numbers

- Obviously, we'll need to represent the numbers in such a way that we have rows and collumns. We need a set of cartesian axis for our numbers so that they have some sorts of coordinates. I set out a class that does just that, plus more. Instead of only representing abstract coordinates, this class is the foundation of my application, containing also the number that is found at those coordonates and a boolean value to know if the coordinate is full or not.

## The 'Quad'

- After a trying a bunch of different methods to generate a sudoku practically without any type of brute forcing, I started thinking of a method that would try to 'correct' itself as the algorithm progresses. Shortly said, brute forcing.

At the core of my brute forcing algorithm is the Quadrant class. The quadrants are the ones that get reseted every time the program runs out of numbers to be put in a certain spot.

## The process scheme

- The problem I had while trying to generate a sudoku first-try so to say, without brute forcing, is that after a few quadrants generated (once even 8 of them), some previous values were put in such a way that a some cells in the current quadrant had no valid number to put there. So the program went into an infinite loop to try all 9 possible numbers again and again.

- With brute forcing, I added a new class, Sudoku, that has an array of 9 Quads. With the help of a `while` loop inside the Sudoku constructor, the Quadrants are constantly generated and monitored. If one Quad hits an impossible situation (as described above), all the Quadrants are reseted and the generation begins from the ground up. If all of the Quads are complete, the program is over and we have our sudoku.

- But how does it know when to reset it all and start over? This is where I think the code can get greatly improved as the current method is quite stupid. The Quadrant class has a static counter that increases everytime a Quadrant gets reseted. After a certain threshold, the program considers that it has run in an impossible situation and starts over from scratch. This I think is quite inefficient and greatly affects the time it needs to generate a sudoku.

![the program scheme](https://raw.githubusercontent.com/Gandalf789/Gandalf789.github.io/master/scheme.png)

## Current state 

- In its current state, the program can generate a 9 x 9 sudoku in a varying amount of time. Sometimes it takes less than a second, sometimes almost 40 seconds. But in all my time testing, albeit quite short, the program never exceeded the 1 minute mark. So despite its flaws, I think it's a pretty decent program.

- The code can be found [on my github](https://github.com/Gandalf789/Sudoku-Generator).

---

*T.*