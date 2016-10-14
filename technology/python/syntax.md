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


### Immutable (Cannot modify any value)

```
>>> a = (1, 2, 3, 4)
>>> del a[0]
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
TypeError: 'tuple' object doesn't support item deletion
```


### Tuple Packing & Unpacking
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

