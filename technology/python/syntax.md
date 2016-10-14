Syntax
======


## Comments
```
>>> # This is a comment
>>> # print("Hello World!")
... 
>>>
```


## Keywords and Identifiers

    False      class      finally    is         return
    None       continue   for        lambda     try
    True       def        from       nonlocal   while
    and        del        global     not        with
    as         elif       if         or         yield
    assert     else       import     pass
    break      except     in         raise


## Variables and Datatypes
```
>>> a = 12
>>> type(a)
<class 'int'>
```

```
>>> a = 1.0
>>> type(a)
<class 'float'>
```

```
>>> a = "Hello World"
>>> type(a)
<class 'str'>
```

### Strings
```
>>> 'World'
'World'
>>> "World"
'World'
>>> 'World\'s Best'
"World's Best"
>>> "World's Best"
"World's Best"
```

### Read User Input
```
>>> number = int(input("Enter a number: "))
Enter a number: 5
>>> print(number)
5
```
```
name = str(input("Enter your name: "))
Enter your name: root
>>> print(name)
```

### Multiple Assignments
```
>>> a, b = 4, 5
>>> a
4
>>> b
5
```

```
>>> a, b = b, a
>>> a
5
>>> b
4
```

### Operators

  * Mathematical  
    +, -, /, %, *
  * Logical  
    and, or
  * Relational  
    <, <=, >, >=, ==, !=


#### Expressions
```
a = 9
b = 12
c = 3
x = a - b / 3 + c * 2 - 1
y = a - b / (3 + c) * (2 - 1)
z = a - (b / (3 + c) * 2) - 1
print("X = ", x)
print("Y = ", y)
print("Z = ", z)
```

#### Type Conversion

    float(string) -> float value
    int(string) -> integer value
    str(integer) -> string representation
    str(float) -> string representation

```
>>> a = 8.126768
>>> str(a)
'8.126768'
```


## Print and string formatting

```
>>> name = 'Yongyuan'
>>> country = 'China'
>>> print("%s is from %s" % (name, country))
Yongyuan is from China
>>> print("{0} is from {1}".format(name, country))
Yongyuan is from China
>>> print("{} is from {}".format(name, country))
Yongyuan is from China
>>> print("{} is from {}".format(name))
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: tuple index out of range
```


## Conditions and Control Flow
```
if expression:
    do this
elif expression:
    do this
else: 
    do this
```


## Do's and dont's

```
if x == True:
    do not this  
 
if x == False:
    do not this
```

```
if x:
    do this
 
if not x:
    do this
```

## Looping


### While Loop

```
while condition:
    statement1
    statement2
```

```
>>> # Print 0-10
>>> n = 0 
>>> while n < 11:
...     print(n)
...     n += 1
```


### For Loop

```
for iterating_var in sequence:
    statement
>>> for i in range(0, 11):  # Print 0-10
...     print(i)
```

```
>>> sum = 0
>>> n = 10
>>> for i in range(1, n):
...     sum += i
...
>>> print(sum)
45
```


### Break

```
>>> word = "Python2 Programming"
>>> for letter in word:
...     if letter == "2":
...         break
...     print(letter)
...
P
y
t
h
o
n
```

### Continue
```
>>> word = "Python3 Programming"
>>> for letter in word:
...     if letter == "3":
...         continue
...     print(letter)
...
P
y
t
h
o
n
 
P
r
.
```


## Data structures

  * List
  * Tuple
  * Dictionary
  * Set
  * String


### Lists

Supports multiple datatypes

```
>>> list = [1, 2, "hello", 1.0]
>>> list
[1, 2, 'hello', 1.0]
```


### List Operations
```
>>> a = [23, 45, 1, -3434, 43624356, 234]
>>> a.append(45)
>>> a
[23, 45, 1, -3434, 43624356, 234, 45]
```

```
>>> a.insert(0, 111) # Inserts 111 at 0th Position
>>> a
[111, 1, 23, 45, 1, -3434, 43624356, 234, 45]
>>> a.count(45)
2
>>> a.remove(234)
>>> a
[111, 1, 23, 45, 1, -3434, 43624356, 45]
```

```
>>> b = [45, 56, 90]
>>> a.append(b)
>>> a [45, 43624356, -3434, 1, 45, 23, 1, 111, [45, 56, 90]]
>>> a[-1]
>>> [45, 56, 90]
>>> a.extend(b) #To add the values of b not the b itself
>>> a
[45, 43624356, -3434, 1, 45, 23, 1, 111, [45, 56, 90], 45, 56, 90]
>>> a[-1]
90
>>> a.remove(b)
>>> a
[45, 43624356, -3434, 1, 45, 23, 1, 111, 45, 56, 90]
>>> a.sort()
>>> a
[-3434, 1, 1, 23, 45, 45, 45, 56, 90, 111, 43624356]
```


### Lists Operations (Stack and Queue)

```
>>> a = [1, 2, 3, 4, 5, 6]
>>> a
[1, 2, 3, 4, 5, 6]
>>> a.pop() # Pop Operation
6
>>> a.pop()
5
>>> a.pop()
4
>>> a
[1, 2, 3]
>>> a.append(34) # Push Operation
>>> a
[1, 2, 3, 34]
>>> a.append(1)
>>> a
[1, 2, 3, 34, 1]
```

### Lists Comprehensions
```
>>> a = [1, 2, 3]
>>> [x ** 2 for x in a]
[1, 4, 9]
```

```
>>> z = [x + 1 for x in [x ** 2 for x in a]]
>>> z
[2, 5, 10]
```

### Tuple

```
>>> a = 'Fedora', 'Debian', 10, 12
>>> a
('Fedora', 'Debian', 10, 12)
>>> a[1]
'Debian'
>>> for x in a:
...     print(x, end=' ')
...
Fedora Debian 10 12
>>> type(a)
<class 'tuple'>
>>> len(a)
4
```


#### Immutable (Cannot modify any value)

```
>>> a = (1, 2, 3, 4)
>>> del a[0]
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
TypeError: 'tuple' object doesn't support item deletion
```


#### Tuple Packing & Unpacking
```
>>> a = (1, 2, 3) # Packing
>>> (first, second, third) = a # Unpacking
>>> first
1
>>> second
2
>>> third
3
>>> (first, second, third) = (1, 2, 3) # Another way
```


### Dictionary

```
>>> data = {'quentin':'Fedora', 'diwen':'Debian', 'steve':'Mac'}
>>> data
{'quentin': 'Fedora', 'steve': 'Mac', 'diwen': 'Debian'}
>>> data['steve']
'Mac'
>>> data['gerard'] = 'Fedora'
>>> data
{'gerard': 'Fedora', 'quentin': 'Fedora', 'steve': 'Mac', 'diwen': 'Debian'}
>>> del data['steve']
>>> data
{'gerard': 'Fedora', 'quentin': 'Fedora', 'diwen': 'Debian'}
```


#### Loop through Dict

```
>>> for x, y in data.items():
...     print("%s uses %s" % (x, y))
...
gerard uses Fedora
quentin uses Fedora
diwen uses Debian
```


### Sets

```
>>> a = set('abcthabcjwethddda')
>>> a
{'a', 'c', 'b', 'e', 'd', 'h', 'j', 't', 'w'}
>>> a.add('p')
>>> a
{'a', 'c', 'b', 'e', 'd', 'h', 'j', 'q', 'p', 't', 'w'}
```


### Slicing


#### List
```
>>> a = [1, 2, 3, 4, 5, 6, 7]
>>> a[1:4]
[2, 3, 4]
>>> a[:-1]
[1, 2, 3, 4, 5, 6]
>>> a[::-1]
[7, 6, 5, 4, 3, 2, 1] # Reverse
```

#### Tuple

```
>>> a = (1, 2, 3, 4, 5, 6, 7)
>>> a[1:4]
(2, 3, 4)
>>> a[::-1]
(7, 6, 5, 4, 3, 2, 1)
```


## String

```
>>> a = 'Hello World'
>>> a[0]
'H'
>>> a[5]
' '
>>> a[1:]
'ello World'
>>> a[::-1] # Reverse
'dlroW olleH'
```

```
>>> s = "I am Dutch"
>>> s
I am Dutch'
>>> s = "Here is a line \
... split in two lines"
>>> s
'Here is a line split in two lines
```

```
>>> s = """ This is a
... multiline string, so you can
... write many lines"""
>>> print(s)
This is a
multiline string, so you can
write many lines
```


### Operations

```
>>> s = "gerard braad"
>>> s.title()
'Gerard Braad'
>>> z = s.upper()
>>> z
'GERARD BRAAD'
>>> z.lower()
'gerard braad'
​>>> s = "123"
>>> s.isdigit()
True
>>> s = "Fedora24"
>>> s.isdigit()
False
```


### Split Method

```
>>> s = "Let's learn Python3"
>>> s.split(" ")
["Let's", 'learn', 'Python3']
```

``` 
>>> s.split()
["Let's", 'learn', 'Python3']
```

```
>>> s = "This is: ME"
>>> s.split(":")
['This is', ' ME']
```


### Join Method

```
>>> s = "We all love Python."
>>> s.split(".")
['We all love Python', '']
```

```
>>> "3".join(s.split("."))
'We all love Python3'
```


### Strip Method

```
>>> s = "    abc  "
>>> s.strip()
'abc'
```

```
>>> # Strip from left hand and right hand size
>>> s = "www.fedoraproject.org"
>>> s.lstrip("www.")
'fedoraproject.org'
```

```
>>> s.rstrip(".org")
'www.fedoraproject'
```


## Enumerate

```
>>> for i, j in enumerate(['a', 'b', 'c']):
...     print(i, j)
...
0 a
1 b
2 c
```


## Functions

```
def functionname(params):
    statement1
    statement2
```

```
>>> def sum(a, b):
...     return a + b
 
>>> res = sum(2, 3)
>>> res
5
```


### Check Palindrome

```
>>> def check_palindrome(word):
...     rev_word = word[::-1]
...     if word == rev_word:
...         print("Palindrome")
...     else:
...         print("Not Palindrome")
...
>>> check_palindrome("madam")
Palindrome
>>> check_palindrome("python3")
Not Palindrome
```


### Default Arguments

```
>>> def test(a , b=-99):
...     if a > b:
...         return True
...     else:
...         return False
```

```
>>> test(12, 23)
False
>>> test(12)
True
```


### Keyword Arguments

```
>>> def func(a, b=5, c=10):
...     print('a is', a, 'and b is', b, 'and c is', c)
...
>>> func(12)
a is 12 and b is 5 and c is 10
>>> func(12, 24)
a is 12 and b is 24 and c is 10
>>> func(12, c = 25)
a is 12 and b is 5 and c is 25
>>> func(a=12)
a is 12 and b is 5 and c is 10
```

Note: Can not have non-keyword argument after a keyword based argument



## File Handling

    r - Open with read only mode
    w - Open with write mode
    a - Open with append mode


### Reading File

```
>>> f = open('sample.txt', 'r')
>>> f.read()
"Hello World.\nLet's learn Python3.\n"
>>> f.close()
>>> f = open('sample.txt', 'r')
>>> f.readline()
'Hello World.\n'
>>> f.readline()
"Let's learn Python3.\n"
>>> f.close()
>>> f = open('sample.txt', 'r')
>>> f.readlines()
['Hello World.\n', "Let's learn Python3.\n"]
>>> f.close()
```


### Loop through Lines

```
>>> f = open('sample.txt', 'r')
>>> for i in f:
...     if 'Python' in i:
...         print(i)
...    f.close()

```

    Let's learn Python3.
    


### Write and Append

```
>>> f = open('test.txt', 'w') # Write
>>> f.write("Hello world")
>>> f.close()
>>> f = open('test.txt', 'r')
>>> f.read()
'Hello world'
>>> f.close()
>>> f = open('test.txt', 'a') # Append
>>> f.write(" Python')
>>>f.close()
>>> f = open('test.txt', 'r')
>>> f.read()
"Hello world Python"
>>>f.close()
```


#### Using with Statement
```
>>> with open('sample.txt', 'r') as f:
...     f.read()
...
"Hello World.\nLet's learn Python3.\n"
```

```
>>> with open('sample.txt', 'r') as f:
...     for i in f:
...         if 'Python' in i:
...             print(i)
...
Let's learn Python3.
```


## Modules

   * import module 
   * from module import something
   * from module import *



`math.py`
```
def square(x):
    return x * x
```

`result.py`
```
from math import square
print(square(5))
```


### `__name__` and `__main__`

`math.py`
```
def square(x):
    return x * x
if __name__ == '__main__':
    print("Result: for square(5) == %d " % square(5))
```

`result.py`
```
import math
print(math.square(5))
```
