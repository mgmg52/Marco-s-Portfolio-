## Powers of two
> Please write a program which asks the user to type in an upper limit. <br>
The program then prints out numbers so that each subsequent number is the previous one doubled, starting from the number 1.<br>
That is, the program prints out powers of two in order.<br>
The execution of the program finishes when the next number to be printed would be greater than the limit set by the user.<br>No numbers greater than the limit should be printed.

**My Solution:**

```python
limit = int(input("Upper limit: "))
n = 1
while n <= limit:
    print(n)
    n *= 2
```


## Powers of base n
> Please change the program from the previous exercise so that the user gets to input also the base which is multiplied (in the previous program the base was always 2). <br>
Please don't use the value True as the condition of your while loop in this exercise!

**My Solution:**

```python
limit = int(input("Upper limit: "))
base = int (input("Base:"))
n = 1
while n <= limit:
    print(n)
    n *= base
```
