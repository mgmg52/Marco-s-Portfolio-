## Exercise 1
> **Please write a program which asks the user to type in an upper limit. <br>
The program then prints out numbers so that each subsequent number is the previous one doubled, starting from the number 1.<br>
That is, the program prints out powers of two in order.<br>
The execution of the program finishes when the next number to be printed would be greater than the limit set by the user.<br>No numbers greater than the limit should be printed.**

**My Solution:**

```python
limit = int(input("Upper limit: "))
n = 1
while n <= limit:
    print(n)
    n *= 2
```
