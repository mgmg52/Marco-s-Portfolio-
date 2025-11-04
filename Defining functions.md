## Mean
> **Please write a function named mean, which takes three integer arguments. The function should print out the arithmetic mean of the three arguments.**

## My Solution: ##

```python
def mean(x,y,z):
    print((x + y + z)/3)
# Testing the function
if __name__ == "__main__":
    mean(5,3,1)
    mean (10,1,1)
```


## Print many times
> **Please write a function named print_many_times(text, times), which takes a string and an integer as arguments. The integer argument specifies how many times the string argument should be printed out.**

## My Solution: ##

```python
def print_many_times(text, times):
    i = 1
    while i <= times:
        print(text)
        i += 1
# Testing the function
if __name__ == "__main__":
    print_many_times("python", 5)
```


## Chessboard
> **Please write a function named chessboard, which prints out a chessboard made out of ones and zeroes. The function takes an integer argument, which specifies the length of the side of the board.**

## My Solution: ##

```python
def chessboard(size):
    i = 0
    while i < size:
        if i % 2 == 0:
            row = "10"*size
        else:
            row = "01"*size
        print(row[0:size])
        i += 1
# Testing the function
if __name__ == "__main__":
    chessboard(6)
```


## Line
> **Please write a function named line, which takes two arguments: an integer and a string. <BR>
The function prints out a line of text, the length of which is specified by the first argument.<br>
The character used to draw the line should be the first character in the second argument. <BR>
If the second argument is an empty string, the line should consist of stars.**

## My Solution: ##

```python
def line(n, s):
    ch = s[0] if s else " "
    if ch.isspace():
        print("*" * n)
    else:
        print(ch * n)
# Testing the function
if __name__ == "__main__":
    line(7, "%")
```


## A box of hashes
> **Please write a function named box_of_hashes, which prints out a rectangle of hash characters. The function takes one argument, which specifies the height of the rectangle. The rectangle should be ten characters wide.**

>**The function should call the function line from the exercise above for the actual printing out. Copy your solution to that exercise above the code for this exercise. Please don't change anything in your line function.**

## My Solution: ##

```python
def line(n, s):
    ch = s[0] if s else " "
    if ch.isspace():
        print("*" * n)
    else:
        print(ch * n)

def box_of_hashes(height):
    i = 0
    while i < height:
        line(10, "#")   
        i += 1
#Testing the function
if __name__ == "__main__":
    box_of_hashes(5)
```


## A square of hashes
> **Please write a function named square_of_hashes, which draws a square of hash characters. The function takes one argument, which determines the length of the side of the square.**

>**The function should call the function line from the exercise above for the actual printing out. Copy your solution to that exercise above the code for this exercise. Please don't change anything in the line function.**

## My Solution: ##

```python
def line(n, s):
    ch = s[0] if s else " "       
    if ch.isspace():               
        print("*" * n)
    else:
        print(ch * n)

def square_of_hashes(size):
    i = 1
    while i <= size:
     line(size, "#")
     i += 1
# Testing the function
if __name__ == "__main__":
    square_of_hashes(5)
    square_of_hashes(3)
```


## A shape
> **Please write a function named shape, which takes four arguments. The first two parameters specify a triangle, as above, and the character used to draw it. The first parameter also specifies the width of a rectangle, while the third parameter specifies its height. The fourth parameter specifies the filler character of the rectangle. The function prints first the triangle, and then the rectangle below it.**

>**The function should call the function line from the exercise above for the actual printing out. Copy your solution to that exercise above the code for this exercise. Please don't change anything in the line function.**

## My Solution: ##

```python
def line(n, s):
    ch = s[0] if s else " "     
    if ch.isspace():              
        print("*" * n)
    else:
        print(ch * n)

def shape (wt, n1, hr, n2):
    i = 1
    while i <= wt:
        line (i, n1)
        i += 1
    b = 1
    while b <= hr:
        line (wt, n2)
        b += 1
# Testing the function
if __name__ == "__main__":
    shape (5, "x", 3, "*")
```


## First, second and last words
> **Please write three functions: first_word, second_word and last_word. Each function takes a string argument.**

>**As their names imply, the functions return either the first, the second or the last word in the sentence they receive as their string argument.**

>**In each case you may assume the argument string contains at least two separate words, and all words are separated by exactly one space character. There will be no spaces in the beginning or at the end of the argument strings.**

## My Solution: ##

```python
def first_word(a):
    return a.split()[0]

def second_word(b):
    return b.split()[1]

def last_word(c):
    parts = c.split()        
    return parts[-1] 
#Testing the function
if __name__ == "__main__":
    sentence = "once upon a time there was a programmer"
    print(first_word(sentence))
    print(second_word(sentence))
    print(last_word(sentence))
```
