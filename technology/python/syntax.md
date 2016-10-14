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

