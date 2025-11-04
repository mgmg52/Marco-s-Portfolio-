## Powers of two
> **Please write a program which asks the user to type in an upper limit.**  
> **The program then prints out numbers so that each subsequent number is the previous one doubled, starting from the number 1.**  
> **That is, the program prints out powers of two in order.**  
> **The execution of the program finishes when the next number to be printed would be greater than the limit set by the user.**  
> **No numbers greater than the limit should be printed.**


**My Solution:**

```python
limit = int(input("Upper limit: "))
n = 1
while n <= limit:
    print(n)
    n *= 2
```


## Powers of base n
> **Please change the program from the previous exercise so that the user gets to input also the base which is multiplied (in the previous program the base was always 2). <br>
Please don't use the value True as the condition of your while loop in this exercise!**

**My Solution:**

```python
limit = int(input("Upper limit: "))
base = int (input("Base:"))
n = 1
while n <= limit:
    print(n)
    n *= base
```


## The sum of consecutive numbers
> **Please write a program which asks the user to type in a limit. The program then calculates the sum of consecutive numbers (1 + 2 + 3 + ...) until the sum is at least equal to the limit set by the user.**

**My Solution:**

```python
limit = int(input("Limit: "))
number = 1
sum = 1
while sum < limit:
    number += 1
    sum += number
print(sum)
```


## Substrings
> **Please write a program which asks the user to type in a string. The program then prints out all the substrings which begin with the first character, from the shortest to the longest.**

**My Solution:**

```python
s = input("Please type in a string: ")
i = 1
while i <= len(s):
    print(s[:i])
    i += 1
```


## First letters of words
> **Please write a program which asks the user to type in a sentence. The program then prints out the first letter of each word in the sentence, each letter on a separate line.**

**My Solution:**

```python
sentence = input("Please type in a sentence: ")
sentence = " " + sentence
index = 1

while index < len(sentence):
    if sentence[index-1] == " " and sentence[index] != " ":
        print(sentence[index])
    index += 1
```


## Flip the pairs
> **Please write a program which asks the user to type in a number. <br>
The program then prints out all the positive integer values from 1 up to the number.<br>
However, the order of the numbers is changed so that each pair or numbers is flipped. That is, 2 comes before 1, 4 before 3 and so forth.**

**My Solution:**

```python
n = int(input("Number: "))
i = 1
while i <= n:
    if i + 1 <= n:
        print(i + 1)
        print(i)
        i += 2
    else:
        print(i)   
        i += 1
```
