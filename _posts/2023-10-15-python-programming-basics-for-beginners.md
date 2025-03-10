---   
title: Python Programming Basics for Beginners
author: ajaytekam   
date: 2023-10-15 14:57:00 +0530   
img_path: /assets/posts/20231015/ 
categories: [Python, Coding]
tags: [Coding]
image:
  path: python.jpg  
---    

## Contents 

-    [Simple Python Code Example](#simple-python-code-sample)
-    [Taking input from User](#taking-input-from-user)
-    [Functions](#functions)
-    [Main Function like Structure](#main-function-like-structure)
-    [Simple try-except Block](#simple-try-except-block)
-    [Python Command-line Arguments](#python-command-line-arguments)
-    [File IO](#file-io)
-    [Data types in Python](#data-types-in-python)
-    [TypeCasting in Python](#typecasting-in-python)
-    [Variable Operators](#variable-operators)
-    [Looping and Control Structures](#looping-and-control-structures)
-    [String Regular Expressions](#string-regular-expressions)
-    [MultiThreading](#multithreading)
-    [Multiprocessing](#multiprocessing)   
-    [SubProcess Module : Running External Programs](#subprocess-module--running-external-programs)   
-    [Object Oriented Programming : Classes and OPP Concepts](#object-oriented-programming--classes-and-opp-concepts)
-    [File and Directory handling](#file-and-directory-handling)
-    [Python Modules](#python-modules)
-    [Python Packages](#python-packages)
-    [Socket Controls, Servers and Clients Web Servers and Client scripting](#socket-controls-servers-and-clients-web-servers-and-client-scripting)
-    [Logging module : Creating/Generating logs in python](#logging-module-creating-generating-logs-in-python)
-    [Writing plugins in Python](#writing-plugins-in-python)
-    [Python Tips and Tricks](#python-tips-and-tricks)
    - [Passing n number or arguments to a method as list(), dict() in python](#passing-n-number-or-arguments-to-a-method-as-list-dict-in-python--)
    - [One-Liner code for reading from a file and store the lines of text in a list](#one-liner-code-for-reading-from-a-file-and-store-the-lines-of-text-in-a-list)
    - [Make graceful exit from a running multithreaded program using signal module](#make-graceful-exit-from-a-running-multithreaded-program-using-signal-module)



Simple python Code Sample
-------------------------

```python
#!/usr/bin/python3

print("Hello world This is fucking Test.")
```

Taking input from User
----------------------

```python
#!/usr/bin/python3

name = input("Name : ")
print("Your name is : %s" % name)
```

Functions
---------

```python
def function_name(arguments....):
	// function statements
	// function statements
```
Example :
```python
#!/usr/bin/python3

def printMsg1(msg):
    print("Your message is : ", msg)


def printMsg2(msg1, msg2):
    print("First message is : ", msg1)
    print("Second message is : ", msg2)

printMsg1("Hello world")
printMsg2("Test101", "Test102")
```

Main Function like Structure
----------------------------

```python
if __name__ == "__main__":
	// statement of main function
	// ....
```
Example :
```python
#!/usr/bin/python3

def addnum(x, y):
    return x+y

def mulnum(x, y):
    return x*y;

def main():
    print(addnum(50, 50))
    print(mulnum(10, 10))

if __name__ == "__main__":
    main()
```

Simple try-except Block
-----------------------

```python
try:
	// statements
except:
	// statements
finally:
	// statemnets
```
where `finally` block is optional. Example :
```python
#!/usr/bin/python3

try:
    print(x)
except:
    print("Code execution Failed")
    print("Something Error on above code.")
finally:
    print("This is the code of final block")
```
Bulit-in Custom Error handling library. Example 1 :
```python
#!/usr/bin/python3

try:
    print(10/0)
except ZeroDivisionError:
    print("Divide by Zero Detected.")
```
Example 2 :
```python
#!/usr/bin/python3

try:
    print(x)
except NameError:
    print("variable x is not defined.")
```
Some of the common execptions are :
* **IOError** : If the file cannot be opened.
* **ImportError** : If python cannot find the module.
* **ValueError** : Raised when a built-in operation or function receives an argument that has the right type but an inappropriate value.
* **KeyboardInterrupt** : Raised when the user hits the interrupt key (normally Control-C or Delete).
* **EOFError** : Raised when one of the built-in functions (input() or raw_input()) hits an end-of-file condition (EOF) without reading any data.

https://docs.python.org/3/library/exceptions.html#Exception

Python Command-line Arguments
-----------------------------

```python
#!/usr/bin/python3

import sys

if len(sys.argv) == 1:
    print("No arguments supplied.?")
    sys.exit(0)

# prints the whole arguments
print(sys.argv[1:])

# prints the single arguments one by one

print("Program Name : ",sys.argv[0])
print("Fist Arguments : ",sys.argv[1])
print("Second Arguments : ",sys.argv[2])
```
`sys.argv[0]` denotes name of the program, and `sys.argv[1:]` represents whole arguments. Output :
```console
$ ./ex7.py 1st 2nd

['1st', '2nd']
Program Name :  ./ex7.py
Fist Arguments :  1st
Second Arguments :  2nd
```
**Using `getopt` library :**
The basic syntax of `optget` is as follows :
```python
getopt.getopt(args, shortopts, longopts=[])
```
where :
* `args` are the arguments to be passed.
* `shortopts` is the options this script accepts.
* `longopts` is the list of String parameters this function accepts which should be supported.

```python
#!/usr/bin/python3

import sys
import getopt

def main():
    opts, args = getopt.getopt(sys.argv[1:], "hi:o:", ["ifile=", "ofile="])
    for opt, arg in opts:
        if opt == '-h':
            print(sys.argv[0]," -i <inputFile> -o <outputFile>")
            sys.exit(0)
        elif opt in ('-i', '--ifile'):
            infile = arg
            print("Input File : ", infile)
        elif opt in ('-o', '--ofile'):
            outfile = arg
            print("Output File : ", outfile)

if __name__ == "__main__":
    main()
```
Output :
```console
$ ./ex6.py -i InputFile.txt -o OutputFile

Input File :  InputFile.txt
Output File :  OutputFile
```
Using `longopts` options :
```console
$ ./ex6.py --ifile=InputFile.txt --ofile=OutputFile

Input File :  InputFile.txt
Output File :  OutputFile
```
Where at `shortopts` options which is setted `hi:o:`, `h` is for help without any argvalue (because `:` is not putted there), and `i:` means `-i` has a value to set which is input file value, similarly `o:` means output value `-o` used to set output value. For `longopts` we have setted options `ifile=` and `ofile=` which is used as `--ifile=FileName` and `--ofile=OutPutFile`.

**argparse Library** :

basic Use :
```python
#!/usr/bin/python3

import argparse


def main():
    parser = argparse.ArgumentParser()
    parser.parse_args()


if __name__ == "__main__":
    main()
```
Output :
```console
age: ex8.py [-h]

optional arguments:
  -h, --help  show this help message and exit
```
basically it creates `-h\--help` option by default.

* **Positional Argument** :

Example of positional arguments
```python
#!/usr/bin/python3

import argparse

def main():
    parser = argparse.ArgumentParser()
    parser.add_argument("echo", help="print the supplied string")
    args = parser.parse_args()
    print(args.echo)

if __name__ == "__main__":
    main()
```
Output :
```console
$ age: ex8.py [-h] echo

positional arguments:
  echo        print the supplied string

optional arguments:
  -h, --help  show this help message and exit

$ ./ex8.py "Hello world"
Hello world
```
Example 2:
```python
#!/usr/bin/python3

import argparse

def main():
    parser = argparse.ArgumentParser()
    parser.add_argument("square", help="return the square root of given number", type=int, )
    args = parser.parse_args()
    print(args.square**2)

if __name__ == "__main__":
    main()
```
Output :
```console
$ ./ex8.py 6

36
```
* **Optional arguments** :

Example :
```python
#!/usr/bin/python3

import argparse

def main():
    parser = argparse.ArgumentParser()
    parser.add_argument("--file", help="Filename to perform operations on")
    args = parser.parse_args()
    print(args.file)

if __name__ == "__main__":
    main()
```

Output :
```console
$  ./ex8.py -h

usage: ex8.py [-h] [--file FILE]

optional arguments:
  -h, --help   show this help message and exit
  --file FILE  Filename to perform operations on

$ ./ex8.py --file data.txt
data.txt
```
**Short Option**
```python
#!/usr/bin/python3

import argparse

def main():
    parser = argparse.ArgumentParser()
    parser.add_argument("-f", "--file", help="Filename to perform operations on")
    args = parser.parse_args()
    print(args.file)

if __name__ == "__main__":
    main()
```
Output :
```console
$ ./ex8.py -h

optional arguments:
  -h, --help            show this help message and exit
  -f FILE, --file FILE  Filename to perform operations on

$ ./ex8.py -f data.txt
data.txt
```
**action tag** :  It is used to store true/false value on an option, for example if its given as argument then the value is true otherwise false. Example :
```python
#!/usr/bin/python3

import argparse

def main():
    parser = argparse.ArgumentParser()
    parser.add_argument("-v", "--verbose", help="return the square root of given number", action="store_true")
    args = parser.parse_args()
    if args.verbose:
        print("verbose mode is ON")

if __name__ == "__main__":
    main()
```
Output :
```console
$ ./ex8.py --verbose
verbose mode is ON

$ ./ex8.py -v
verbose mode is ON
```
Combination of Positional and Optional Arguments :
```python
#!/usr/bin/python3

import argparse

def main():
    parser = argparse.ArgumentParser()
    parser.add_argument("square", type=int, help="display a square of a given number")
    parser.add_argument("-v", "--verbosity", type=int, help="increase output verbosity")
    args = parser.parse_args()
    answer = args.square**2
    if args.verbosity == 2:
        print("the square of {} equals {}".format(args.square, answer))
    elif args.verbosity == 1:
        print("{}^2 == {}".format(args.square, answer))
    else:
        print(answer)

if __name__ == "__main__":
    main()
```
Output :
```console
$ ./ex9.py 6
36

$ ./ex9.py 6 -v 1
6^2 == 36

$ ./ex9.py 6 -v 2
the square of 6 equals 36
```

#### Python Subparsers

Subparser allow for different arguments to be permitted based on the command/option being run. It shows more options based on the given command/option. Now lets see code example :

```python
#!/usr/bin/python3

import argparse

def main():
    # start coding from here
    parser = argparse.ArgumentParser()
    subparser = parser.add_subparsers(dest='Module')
    SubDomainEnum = subparser.add_parser('SubDomainEnum')
    PortScan = subparser.add_parser('PortScan')
    SubDomainEnum.add_argument('--config', help="", type=str, required=True)
    SubDomainEnum.add_argument('--domain', help="", type=str, required=True)
    PortScan.add_argument('--domainlist', help="", type=str, required=True)
    PortScan.add_argument('--ports', help="", type=str, required=True)
    args = parser.parse_args()
    if args.Module == 'SubDomainEnum':
        print('Config File: ', args.config)
        print('Domain Name: ', args.domain)
    elif args.Module == 'PortScan':
        print('DomainList: ', args.domainlist)
        print('Port Numbers : ', args.ports)
    else:
        print(" Github Action Reconnaissance Framework\n")
        print(" Available templates modules : \n")
        print(" [*] SubdomainEnum => Tools used: amass, commonspeak2, massdns, dnsgen")
        print(" [*] PortScan=> Tools used: masscan")

if __name__ == "__main__":
    main()
```

Output :

```shell

```

Link : https://docs.python.org/2/howto/argparse.html

File IO
-------

**Opening File** :
```python
file_object = open(<file-name>, <access-mode>, <buffering>)
```
access modes : `r, rb, rb+, w, wb, wb+, a, ab, a+, ab+`

Example :

Open a file :
```python
file = open("file.txt", "r")
```

**Closing File** :

Syntax :
```python
file_object.close();
```
Example :
```python
#!/usr/bin/python3

file = open("file.txt", "r")
file.close()
```

**Reading from file** :

read() method is used to read from opened file. syntax :
```python
file_object.read([number_of_bytes]);
```
where the argument "number_of_bytes" is optional, and it determines how much byte you want to read from beginning. And if it is missing then it try to read whole file. Example :
```python
#!/usr/bin/python3

file = open("file.txt", "r")
file.read(20)
file.close

file = open("file.txt", "r")
file.read()
file.close
```

**Writing in file** :

write() method is used to write string in an opend file. Syntax :
```python
file_object.write(String);
```
Also note that you have to open the file in "w" access mode. Example :
```python
#!/usr/bin/python3

str = "Hello world This is test string"

file = open("file.txt", "w")
file.write()
```
at here you can also use various access modes.



**Reading/Writing a Binary file** :

Binary files can not be read/write as normal ascii characters. The Binary files can range from image files like JPEGs or GIFs, audio files like MP3s or binary document formats like Word or PDF, executable files etc. To read/write binary file we have to open it in ( rb, rb+, wb, wb+) modes. For example reading an image filei and writing it to another file (making a copy of it).
```python
#!/usr/bin/python3

file1 = open("img1.jpg", "rb");
data = file1.read()
file1.close()

file2 = open("img1_copy.jpg", "wb")
file2.write(data)
file2.close()
```

Data types in Python
--------------------

Since python is loosely typed language,  then we don't need to define the data type within variable while declaring it. Python has following built-in data types :

* **Numeric Types :** int, float, complex
* **String Type :** str
* **Sqeuence Types :** list, tuple, range
* **Mapping Types :** dict
* **Set Types :** set, frozenset
* **Boolean Types :** bool
* **Binary Types :** bytes, bytearray, memoryview

**type()** function : used to check the variable data type.


**Numeric Type :**

* **int** : used for integer numbers.
* **float** : for floating point numbers.
* **complex** : complex numbers contains both real numbers and imaginary parts. Example : 2.14j, 12x, 4r etc.

```python
#!/usr/bin/python3

# integer
num1 = 10

# float
num2 = 14.34

# complex numbers
cmp = 12.4j

print("num1 : %d | type : %s" % (num1, type(num1)))
print("num2 : %.2f | type : %s" % (num2, type(num2)))
print("num2 : %s | type : %s" % (cmp, type(cmp)))
```

**String Type :**

* **str** : Defining String variable. Example :

```python
#!/usr/bin/python3

str1 = "This is first second"
print(str1)

str2 = 'This is first second'
print(str2)

# prints str1 two times *2
print(str1*2)

# concatenate two strings
print(str1+str2)

print(type(str1))
```

**Sqeuence Types :**

* **list** : It is similar to an array, but it contains different datatypes. Defining list :

```python
# empty list
List = []

# list with values
List = [1, 2, 100, "Strin1", "String2", 220]
```
Printing List
```python
#!/usr/bin/python3

List = [1, 2, 100, "Strin1", "String2", 220]

# with for loop
for i in List:
	print(i)

# with while loop
i = 0
length = len(List)
while i < length:
	print(List[i])
	i+=1

# with enumerate()
for i, val in enumerate(List):
	print(i, " : ",val)
```
Operations on list:

1. list.append(element) : append element on end of list.
2. list.insert(index, element) : Insert element on given index.
3. list.extend(list) : add content of another list into given list.
4. list.remove(element) : remove given element from the list.
5. list.pop() : remove last element from list.
6. list.pop(index) : remove given index element from list.
7. list.sort() : sort the list.
8. list.reverse() : reverse the list.

Example :
```python
#!/usr/bin/python

lst1 = [1, 2, 3, 4, 5]
lst2 = [100, 200, 300, 400, 500]

print(lst1)
print(lst2)

lst1.insert(0, 10)
print(lst1)

lst2.append(600)
print(lst2)

lst1.extend(lst2)
print(lst1)

lst1.remove(10)
print(lst1)

lst1.pop()
print(lst1)

lst1.pop(5)
print(lst1)

lst1.sort()
print(lst1)

lst1.reverse()
print(lst1)
```
Nested list :
```python
#!/usr/bin/python

list = [1, 2, 3, 4, [5.1, 5.2, 5.3, 5.4, 5.5], 6, 7, 8, 9]

for i in list:
	print(i)

for i in list[4]:
	print(i)
```

* **tuple** : tuples are similer to list, except it is read-only structure, means we can't modify the size and value of the items of a tuple. The element of tuple is encolsed by parentheses '()'. Example :


```python
#!/usr/bin/python

tpl = (10, 20, 30, 40, 50, "Hello", "world")

print(tpl[0])
print(tpl[1])
print(tpl[2])


# prints 1st and second elements
print(tpl[0:3])

# prints from 5th element to last element
print(tpl[4:])

# prints everything before 5th element
print(tpl[:4])
```
Converting list into tupple
```python
#!/usr/bin/python

lst = [100, 200, 300, 400, 500]
print(lst)

tpl = tuple(lst)
print(tpl)
```
* **range** : returns a sequence of numbers, starting from 0 by default, and increments by 1 (by default), and ends at a given number. Syntax :

```python
range(start, stop, step)
```
Example :
```python
#!/usr/bin/python

# prints 1 to 10
for x in range(1, 10):
	print(x)

# print only even number
for x in range(2, 10, 2):
	print(x)
```

* **Mapping Types :** dictonary

Dictonary is key:value pair data structure where elements are saparated by `,` and whole elements enclosed by `{}`. It is similer like HashMap in java. In dictonary the key must be uniq, and duplication of values are allowed. Example of dictonary :
```python
#!/usr/bin/python

dict = {"Name" : "Ajay", "Age":26, "Education":"MCA", "Nationality":"Indian", "Height":5.8}

print(dict)

print(dict["Name"])
print(dict["Age"])
print(dict["Height"])

# updating value
dict["Age"] = 27
print(dict["Age"])

# removing all elements from dictonary
dict.clear()

# deleting the dictonary
del dict
```
Also note that the key must be string, numbers or tuples. Other then that are not allowed.

Methods in Dictonary :

1. dict.clear() : clear all key:values from dictonary.
2. dict.copy() : create a copy of dictonary.
```python
dict_copy = dict.copy()
```
3. fromkeys(key_list, value) : Take input as keys or list/tuple of keys and create new dictonary with the single value assigned in all the kays.

 ```python
keys = (1, 2, 3, 4, 5)
mydict = dict.fromkeys(keys, 0)
print(mydict)
```
4. dict.get(key) : return the value of specified key.
5. dict.items() : returns a list containing a tuple for each key value pair.
6. dict.keys() : return all the keys.
7. dict.values() : returns a list of all the values in the dictionary.
8. dict.pop(key) : remove the element of specified key.
9. dict.update({key:value}) : add a new key:value pair on dictonary.
```python
#!/usr/bin/python
dict = {1:"one", 2:"two", 3:"three", 4:"four", 5:"five"}
dict.get(5))
dict.items()
dict.keys()
dict.values();
dict.pop(5)
dict.update({5:"five"})
```

* **Set Types :**

**set** : It is an un-ordered collection data type that is iterable, mutable and has no public elements. Syntax :
```python
# declaring sets
# first method
set1 = {"string1", "string2", "string3", "string4"}

# second method
set2 = set(["string1", "string2", "string3", "string4"])

# iterating through
for i in set1:
	print(i)
```
Methods used within the set :
1. add() : add an element to the set.
2. remove() : remove an specified element from the set.
3. pop() : remove a random element from the set.
3. union() : returns a set containing a union from two sets.
4. update() : returns a set containing a union from two sets and also store it.
4. difference() : returns the difference of sets.
5. clear() : clear all the elements of the set.

Example code for the concept :
```python
#!/usr/bin/python

myset = {1, 2, 3, 4, 5, "String1", "String2", "String3"}

myset.remove()
print(myset)

myset.pop()
print(myset)

myset.add("String3")
print(myset)

set01 = {"Hello", "World", "This", "is", "Test"}
set02 = {"World", "This", "is", "Test"}

# it just prints the all unique elements
myset.union(set01, set02)

# it stores all the unique elements to myset
myset.update(set01, set02)

set01.difference(set02)

set01.clear()
set02.clear()
myset.clear()
```
for more methods : [Click Here](https://www.w3schools.com/python/python_ref_set.asp)


 **frozenset** : frozenset is immutable, means its can not changed. Example :
```python
#!/usr/bin/python

# an empty frozenset
fs1 = frozenset()
print(fs1)

# frozenset from a set
fs2 = frozenset({"string1", "string2", "string3"})
print(fs2)

# from array
fs3 = frozenset([1, 2, 3, 4, 5])
print(fs3)

# from dictonary
dict = {"Name":"Don Joe", "Age":26, "Sex":"Male"}
dict_keys = frozenset(dict)
print(dict_keys)
```
* **Boolean Types :** boolan values are represented with "True" and "False". Example :

```python
#!/usr/bin/python
bl = False
type(bl)
bl = True
type(bl)
```

* **Binary Types :**

**bytes** Defined as bytes. Syntax :
```python
# 1st method
bt1 = b"Put_the_data_here"

# 2nd method
bt2 = bytes(b"data_here")
```
**bytearray** : Defined as array of bytes. Sytax :
```python
btarr = bytearray(10)
```
**memoryview** : shows the memory address of variable. Syntax :
```python
mv = memoryview(b"HelloString")
```
Example :
```python
#!/usr/bin/python

# byte
bt = b"TestString"
print(bt)

# bytearray
btarr = bytearray(100)
print(btarr)

# memoryview
mv = memoryview(b"HelloString")
print(mv)
```

TypeCasting in Python
---------------------

* **Explicit Conversion** :

The syntax is as follows :
```python
(required_datatype)(expression)
```
Example :
```python
#!/usr/bin/python

# string to integer
str = "120"
num = (int)(str)
type(num)

# string to float
str = "12.3"
num = (float)(str)
type(num)
```
* **Using Built-in Function** :

**int(str, base)** : Converts string to integer within given base (at here base represents the string is in hexadecimal, binary, decimal or octal). Where base are 2(for binary), 8(for octal), 10(for decimal), 16(for hexadecimal). Example :
```python
#!/usr/bin/python

# from binary
str = "1010"
num = int(str, 2)
print(num)
type(num)

# from octal
str = "12"
num = int(str, 8);
print(num)
type(num)

# from hexadecimal
str = "12AB"
num = int(str, 16)
print(num)
type(num)

# from decimal
str = "1000"
num = int(str, 10)
print(num)
type(num)
```

Variable Operators
------------------

**1. Arithmetic operators** :

| Operators | Desc |
|-----------|------|
| Addition ( + ) | add two strings or numbers |
| Subtraction ( - ) | subtract two numbers |
| Multiplication ( * ) | multiply two numbers |
| Division ( / ) | divide two numbers |
| Modulus ( % ) | divide two numbers and return remainder |
|Exponent ( ** ) | perform exponential (power) calculation |
| Floor Division ( // ) | Divide teo numbers and return result as integer (discard the demical numbers) |

Example :
```python
#!/usr/bin/python

n1 = 200
n2 = 20
n3 = 15

# addition
print(n1+n2)

# subtraction
print(n1-n2)

# multiplication
print(n1*n2)

# division
print(n1/n2)

# modulus
print(n1 % n3)

# exponent
print(2**4)

# floor modulus
print(20//3)
```

**2. Comparison Operators** :

These operators are similar to other programming languages :

| Comparison Operator |
|---------------------|
| Equal to "==" |
| Not equal to "!=" or "<>" |
| Less then "<" |
| Greater then ">" |
| Less then or equal to "<=" |
| Greater then or equal to ">=" |

**3. Assignment operator** :

| Operator | Example | equivalent to |
|--------|---------|---------------|
| =      | a = b   |               |
| +=     | a += b  | a = a + b |
| -=    | a -= b | a = a - b |
| *= | a *= b | a = a * b |
| /= | a /= b | a = a / b |
| %= | a %= b | a = a % b |
| //= | a // b | a = a // b |

**4. Bitwise Operator** :

It is used to perform bitwise operations.

| Operator | Description |
|----------|-------------|
| &  | Bitwise And |
| | | Bitwise OR |
| ^ | Bitwise XOR |
| ~ | Bitwise Not |

Example :
```python
#!/usr/bin/python

b1 = "1100" # 12 in binary
b2 = "1010" # 10 in binary

n1 = int(b1, 2)
n2 = int(b2, 2)

# bitwise and
print(n1 & n2)

# bitwise or
print(n1 | n2)

# bitwise xor
print(n1 ^ n2)

# bitwise not
print(~n1)
print(~n2)
```

**5. Logical Operator** :

| Operator | Example | Description |
|----------|-------------|----------|
| AND/and  | a AND b | Return true if both operand is true |
| OR/or | a OR b | Return true if one of the operand is true |
| NOT/not | Not(a AND b) | Reverse the logical state of its operands |

**6. Membership Operator** :

membership operators test for membership in a sequence, such as strings, lists, or tuples. there are two operators :

| Operator | Description |
|----------|-------------|
|  in  | Return true if it finds a variable in the specified sequence and otherwise false. |
| not in | Return to true if it does not finds a variable in the specified sequence and otherwise false. |

Example :
```python

list1 = [1, 2, 3, 4, 5, 6, 10]
list2 = ["Hello", "World", "String1", "String2"]
str = "TestString101"

# in operator
print(5 in list1)
print(10 in list1)
print(11 in list1)
print("Hello" in list2)
print("Hey" in list2)
print("H" in list2[0])
print("101" in str)

# not in operator
print(9 not in list1)
print(10 not in list1)
print(8 not in list1)

print("Test" not in list2)
print("String1" not in list2)
print("101" not in str)
print("X" not in str)
print("H" not in list2[0])
print("X" not in list2[0])
```

**7. Identity Operators** :

`is` and `is not` are the identity operators both are used to check if two values are located on the same part of the memory. Two variables that are equal does not imply that they are identical.

| Operator | Description |
|----------|-------------|
| is | True if the operands are identical |
| is not | True if the operands are not identical |

Example :
```python
#!/usr/bin/python

n1 = 10
n2 = 10

print(n1 is n2)

str1 = "HelloWorld"
str2 = "HelloWorld"

print(str1 is str2)

print(n1 is str1)
print(n1 is not str1)
print(str1 is not str2)
```

Looping and Control Structures
------------------------------

**Looping** :

* while loop :

Syntax :
```python
while expression:
	statement..
	statement..
```
Examle :

```python
#!/usr/bin/python

# Example1
c = 0
while (c < 9):
	print("Hello World")
	c+=1
```
Infinite Loop :
```python
#!/usr/bin/python

while 1:
	name = input("Enter Name")
	print(name)
```
To break the loop press "Ctrl + c".

Using `else` condition with while loop :
```python
#!/usr/bin/python
passwd= ""
while passwd != "1234":
	passwd = input("Enter Password")
else:
	print("Correct Password!")
```

* for loop :

Syntax :
```python
for iterating_var in sequence:
	statement()
```
Example :
```python
#!/usr/bin/python

list = [1, 2, 3, 4, 5]
dict = {"Name":"Ajay", "Age":26, "Occupation":"Coding"}
tpl = (10, 20, 30, 40)
set_v = {"Hello", "World", "This", "is", "set"}

print("Looping list :")
for item in list:
    print(item)

print()
print("Looping dictonary :")
for key in dict:
    print(key, "->", dict[key])


print()
print("Looping Touple :")
for item in tpl:
    print(item)


print()
print("Looping set :")
for item in set_v:
    print(item)


print()
print("Looping a simple string :")
for ch in "Hello":
    print(ch)
```
Using ranges with for loop :
```python
#!/usr/bin/python

for i in range(1, 10):
    print(i)

print()

for i in range(0, 10, 2):
    print(i)
```
We can also use them with nested loop.

**Control Structure** :

* if-else statement :

Syntax :
```python
if expression:
	statement....
else:
	statement....
```
Example :
```python
#!/usr/bin/python

# if statement
str = "Hello"
if (str == "Hello"):
    print("The string value is 'Hello'")

# if-else statement
num = 9
if (num < 10):
    print("num is less then 10.")
else:
    print("num is greater then or equal to 10.")
```

* if-elif-else statement :

Syntax :
```python
if expression:
	statement....
elif expression:
	statement....
else:
	statement....
```
Example :
```python
#!/usr/bin/python

age = int(input("Enter your age : "))

if age < 18:
    print("You are under-age")
elif (age > 18 and age < 40):
    print("You are an Adult")
elif (age > 40 and age< 60):
    print("You are in your 40's or 50's")
else:
    print("You are senior citizen")
```

String Regular Expressions
--------------------------

In python the regular expression are implemented using `re` module. The modules defines several functions and methods to work with regex. Some of the methods are as follows :

1. re.findall()
2. re.split()
3. re.sub()
4. re.subn()
5. re.search()

**re.findall()** : Returns all the mached values as an arry of strings. Example Program :
```python
#!/usr/bin/python

import re

str1 = "Hello world 20. This 1234 is test 587.675"
str2 = "openwall test string footest lets play"
str3 = "12.34 67.34 QUICK TEST 101"

pt1 = "[0-9]"

result = re.findall("[0-9]", str1);
print(result) # return list of single digits


result = re.findall("[0-9]+", str1);
print(result) # returns list of numebrs

result = re.findall("[0-9]+\.[0-9]+", str3)
print(result) # returns list of floating point numbers

result = re.findall("\d+\.\d+", str3)
print(result) # returns list of floating point numbers


result = re.findall(r"\b[Tt]\w+", str1)
print(result) # returns list of words that starts with 'T/t'

result = re.findall(r"\w*is\b", str1)
print(result) # returns list of words that end with is 'is'


result = re.findall(r"\w*e\w*", str2)
print(result) # returns list of words that contains the letter 'e'
```
Output :
```shell
['2', '0', '1', '2', '3', '4', '5', '8', '7', '6', '7', '5']
['20', '1234', '587', '675']
['12.34', '67.34']
['12.34', '67.34']
['This', 'test']
['This', 'is']
['openwall', 'test', 'footest', 'lets']
```

> Note : The 'r' in front tells Python the expression is a raw string. In a raw string, escape sequences are not parsed. For example, '\n' is a new line whereas r'\n' means two characters: a backslash \ followed by n. As we know backlash `\` is used to escape various characters including all metacharacters. However, using r prefix makes `\` treat as a normal character.

* **re.split()** : The split() method splits the string into multiple parts from given pattern and return a list of splitted strings. Example :
```python
#!/usr/bin/python
import re
str1 = "hello world this is test string"
str2 = "This is 1 of the 3 test hello4me 12 test2string"
str3 = "Hello world, this is str1, this is str2, Hello again"
result = re.split("\s", str1)
print(result) # split based on whilespace
result = re.split("\d+", str2)
print(result) # split based on numbers
result = re.split(",", str3)
print(result) # split based on comma ','
```
Output :
```shell
['hello', 'world', 'this', 'is', 'test', 'string']
['This is ', ' of the ', ' test hello', 'me ', ' test', 'string']
['Hello world', ' this is str1', ' this is str2', ' Hello again']
```
Setting number of maxsplit, for example split the string only at the first occurrence
```python
#!/usr/bin/python
import re
str1 = "hello world this is test string"
result = re.split("\s", str1, 1)
print(result) # split first two matches
result = re.split("\s", str1, 2)
print(result) # split first three  matches
result = re.split("\s", str1, 3)
print(result) # split first three  matches
```
Output :
```shell
['hello', 'world this is test string']
['hello', 'world', 'this is test string']
['hello', 'world', 'this', 'is test string']
```

* **re.sub()** : The sub() function replaces the matched pattern with replacement_string. Example :
```python
#!/usr/bin/python
import re
str1 = "Hello world This is test string"
result = re.sub("\s", "*", str1)
print(result) # replaces whitespace with *
name = input("Enter Name : ")
result = re.sub("world", name, str1)
print(result)
```
Output :
```shell
Hello*world*This*is*test*string
Enter Name : ajay
Hello ajay This is test string
```
There is also a count parameter by which we can control the number of replacement. Example :
```python
#!/usr/bin/python
import re
str1 = "Hello world This is test string"
result = re.sub("\s", "*", str1, 2)
print(result) # replaces first two whitespace with `*`
```
Output :
```shell
Hello*world*This is test string
```
* **re.subn()** : It is similar to the sub() function excepts it returns a tuple of two items which contains the new string and number of substitution made.
```python
#!/usr/bin/python
import re
str1 = "Hello world This is test string"
result = re.subn("\s", "*", str1)
print(result) # replaces whitespace with *
print("New String : ", result[0])
print("Number of Substitution made : ", result[1])
```
Output :
```shell
('Hello*world*This*is*test*string', 5)
New String :  Hello*world*This*is*test*string
Number of Substitution made :  5
```

* **re.search()** : This method search for a match, and returns the matched object, if there is a match, also note that only first the first occurrence of the match will be returned, and if there is no matche found, then the value 'None; is returned. Example :

```python
#!/usr/bin/python

import re
str1 = "Hello world 12 This is test string"
str2 = "Hello world again"

result = re.search("\d+", str1);
if result:
    print("Numerical value exists in str1")
else:
    print("Numerical value dose not exists in str1")

if re.search("\d+", str2):
    print("Numerical value exists in str2")
else:
    print("Numerical value dose not exists in str2")
```
Output :
```shell
Numerical value exists in str1
Numerical value dose not exists in str2
```

### Match object

Match is an object containing information about the search and the result. The Properties of match object is as follows :

* match.group() : returns the matched objects as string.
* match.group(num) : returns the matched objects as indexed list.
* match.groups() : returns the matched objects as tuple.
* match.start() : returns the starting index of matched object.
* match.end() : returns the ending index of the matched object.
* match.span() : returns both starting and ending index of matched object.
* match.re = returns the regex pattern.
* match.string = returns the original string.

Example :
```python
#!/usr/bin/python

import re

str1 = "Hello world Python is cool"

pattern = "Python\s(\w*\s*)*"

m = re.search(pattern, str1)
print("m.group() : ", m.group())
print("m.start() : ", m.start())
print("m.end : ", m.end())
print("m.span() : ", m.span())
print("m.re : ", m.re)
print("m.string : ", m.string)
```
Output :
```shell
m.group() :  Python is cool
m.start() :  12
m.end :  26
m.span() :  (12, 26)
m.re :  re.compile('Python\\s(\\w*\\s*)*')
m.string :  Hello world Python is cool
```
Example2 Program :
```python
#!/usr/bin/python

import re

str1 = "Hello worlld this is test"
pattern = "(\w{3,4}) (\w{1,2}) (\w{3,4})"

m = re.search(pattern, str1)
print("m.group() : ", m.group())
print("m.groups() : ", m.groups())
print("m.start() : ", m.start())
print("m.end : ", m.end())
print("m.span() : ", m.span())
print("m.re : ", m.re)
print("m.string : ", m.string)
```
Output :
```shell
m.group() :  this is test
m.groups() :  ('this', 'is', 'test')
m.start() :  13
m.end :  25
m.span() :  (13, 25)
m.re :  re.compile('(\\w{3,4}) (\\w{1,2}) (\\w{3,4})')
m.string :  Hello worlld this is test
```
At the above code we divided the regex pattern between three groups `(regex)` so in that case we can use match.groups() property {it basically returns matched object as a tuple}.

MultiThreading
--------------

A thread is a sequence of such instructions within a program that can be executed independently of other code, and Multithreading is a technique which allows a CPU to execute multiple threads at the same time. These threads can execute individually while sharing their process resources.

The main advantage of Multi-Threading over single process execution is as follows :

* Multiple threads within a process share the same data space with the main thread and can therefore share information or communicate with each other more easily than if they were separate processes.

* Threads sometimes called light-weight processes and they do not require much memory overhead; they are cheaper than processes.

* Threads can be pre-empted (interrupted).

* Threads can temporarily be put on hold (also known as sleeping) while other threads are running - this is called yielding.

### MultiThreading Within Python :

Threre are two modules to implements multithreading in Python :

* thread    ( deprecated in python3/ Only available on python2 )
* threading ( Available on both python 2 and 3)

In this tutorial we are only going to look at threading module, because it is supported by both versions of python.

#### threading module :

**Creating a thread :**

Syntax :
```python
threading.Thread(target=FunctionName, args=(argument_list...,))
```
It returns a Thread object by which we can start that thread and also perform various opertaions on that Thead. The arguments are :

* `target` is the callable function which we want to run as a thread
* `args` is a tupple of containing arguments for the callable function, and also remember to put a `,` (comma) at the end of the args tupple.

There's also some other arguments are available :
```python
threading.Thread(group=GroupName, target=FunctionName, name=ThreadName, args=(argument_list...,), kwargs={})
```
The other arguments are :

* `group` used to set the thread group name.
* `name` used to set the Thead name.
* `kwargs` is a dictionary of keyword arguments for the target invocation.

Now lets see an examples.

Example1 :
```python
#!/usr/bin/python

import threading

def worker(id):
    print("Worker : ", id, " | Started")
    print("Worker : ", id, " | Finished")


if __name__ == "__main__":
    for i in range(3):
        t = threading.Thread(target=worker, args=(i,))
        t.start()
```
Output :
```shell
('Worker : ', 0, ' | Started')
 ('Worker : ', 1, ' | Started')
('Worker : ', 1, ' | Finished')
('Worker : ', 0, ' | Finished')
 ('Worker : ', 2, ' | Started')
('Worker : ', 2, ' | Finished')
```
Example2:
```python
#!/usr/bin/python

import threading
import time
def worker(id):
    print("Worker : ", id, " | Started")
    time.sleep(2)
    print("Worker : ", id, " | Finished")


if __name__ == "__main__":
    for i in range(1, 10):
        t = threading.Thread(target=worker, args=(i,))
        t.start()
```
Output :
```shell
('Worker : ', 1, ' | Started')
 ('Worker : ', 2, ' | Started'('Worker : ', 3)
, ' | Started')
 ('Worker : ', 4, ' | Started')
('Worker : ', 5, ' | Started')
('Worker : ', 6, ' | Started')
 ('Worker : ', 7('Worker : ', 8, ' | Started', ' | Started')
)
('Worker : ', 9, ' | Started')
('Worker : ', 1, ' | Finished')
('Worker : ', 2, ' | Finished')
('Worker : ', 6(('Worker : '(, 8'Work(er : ''Worker : , '7, , 5, ' | Finished')
 'Worker : '' | Finished', ' | Finished')
, 4, )
, ' | Finished')' | Finished')
(
'Worker : ', 3, ' | Finished')
('Worker : ', 9, ' | Finished')
```

Some of the additional methods provided by threading module are :

* threading.activeCount() ΓêÆ Returns the number of thread objects that are active.
* threading.currentThread() ΓêÆ Returns the number of thread objects in the caller's thread control.
* threading.enumerate() ΓêÆ Returns a list of all thread objects that are currently active.

At the above program there is a delay of 2 seconds. also note that the output is a mess, because the print() function of the worker method is not synchronised among the threads. Now to print the message in order we have to synchromize the print() function. There are two methods to do that :

* **Lock()** : first we create an object of lock then use methods acquire() and release() for synchronization. Example Program :

```python
#!/usr/bin/python

import threading
import time

lock = threading.Lock()

def worker(id):
    lock.acquire()
    print("Worker : ", id, " | Started")
    lock.release()
    time.sleep(2)
    lock.acquire()
    print("Worker : ", id, " | Finished")
    lock.release()


if __name__ == "__main__":
    for i in range(1, 10):
        t = threading.Thread(target=worker, args=(i,))
        t.start()
```
Output :
```shell
('Worker : ', 1, ' | Started')
('Worker : ', 2, ' | Started')
('Worker : ', 3, ' | Started')
('Worker : ', 4, ' | Started')
('Worker : ', 5, ' | Started')
('Worker : ', 6, ' | Started')
('Worker : ', 7, ' | Started')
('Worker : ', 8, ' | Started')
('Worker : ', 9, ' | Started')

('Worker : ', 3, ' | Finished')
('Worker : ', 2, ' | Finished')
('Worker : ', 1, ' | Finished')
('Worker : ', 9, ' | Finished')
('Worker : ', 4, ' | Finished')
('Worker : ', 5, ' | Finished')
('Worker : ', 6, ' | Finished')
('Worker : ', 8, ' | Finished')
('Worker : ', 7, ' | Finished')
```
* **Sempphore()** : First we create object and note set the `theading.Sempahore(1)`, then use acquire() and release() for synchronization. Example Program :

```python
#!/usr/bin/python

import threading
import time

screenLock = threading.Semaphore(value=1)

def worker(id):
    screenLock.acquire()
    print("Worker : ", id, " | Started")
    screenLock.release()
    time.sleep(2)
    screenLock.acquire()
    print("Worker : ", id, " | Finished")
    screenLock.release()


if __name__ == "__main__":
    for i in range(1, 10):
        t = threading.Thread(target=worker, args=(i,))
        t.start()
```
Output :
```shell
('Worker : ', 1, ' | Started')
('Worker : ', 2, ' | Started')
('Worker : ', 4, ' | Started')
('Worker : ', 3, ' | Started')
('Worker : ', 5, ' | Started')
('Worker : ', 6, ' | Started')
('Worker : ', 7, ' | Started')
('Worker : ', 8, ' | Started')
('Worker : ', 9, ' | Started')
('Worker : ', 1, ' | Finished')
('Worker : ', 2, ' | Finished')
('Worker : ', 3, ' | Finished')
('Worker : ', 4, ' | Finished')
('Worker : ', 9, ' | Finished')
('Worker : ', 5, ' | Finished')
('Worker : ', 8, ' | Finished')
('Worker : ', 6, ' | Finished')
('Worker : ', 7, ' | Finished')
```

Some of the useful methods of Thread class is as follows :

* **join()** : The join() waits for thread to terminate. For example lets look at the below code :

```python
#!/usr/bin/python

import threading
import time
lst = []

def worker(x, y):
    time.sleep(1)
    for i in range(x, y):
        lst.append(i)

if __name__ == "__main__":
    t1 = threading.Thread(target=worker, args=(1,5,))
    t1.start()
    t2 = threading.Thread(target=worker, args=(5,10,))
    t2.start()
    print(lst)
```
Output :
```shell
[]
```
As we can see at the above example that the mai thread (main function) prints the lst values before the completion of two threads, so the main thread must be paused until all the thread complete their jobs, which can be done by join() method. Example :
```python
#!/usr/bin/python

import threading
import time
lst = []

def worker(x, y):
    time.sleep(1)
    for i in range(x, y):
        lst.append(i)

if __name__ == "__main__":
    t1 = threading.Thread(target=worker, args=(1,5,))
    t1.start()
    t1.join()
    t2 = threading.Thread(target=worker, args=(5,10,))
    t2.start()
    t2.join()
    print(lst)
```
Output :
```shell
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```
We can also pause main thread for a certain amount of time `join(time[])`. Example :
```python
#!/usr/bin/python

import threading
import time
lst = []

def worker(x, y):
    time.sleep(1)
    for i in range(x, y):
        lst.append(i)

if __name__ == "__main__":
    t = threading.Thread(target=worker, args=(1,10,))
    t.start()
    t.join(1.5)
    print(lst)
```
Output :
```shell
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```
It stops the main thread for 1.5 seconds.

* **isAlive()** : The isAlive() method checks weather a thread is still executing. If the thread is currently active then the method isAlive() returns True; otherwise, it returns False.

```python
#!/usr/bin/python

import threading
import time
lst = []

def worker(x, y):
    time.sleep(1)
    for i in range(x, y):
        lst.append(i)

if __name__ == "__main__":
    t = threading.Thread(target=worker, args=(1,10,))
    t.start()
    print("Therad status : ", t.isAlive())
    t.join(1.5)
    print("Therad status : ", t.isAlive())
    print(lst)
```
Output :
```shell
Therad status :  True
Therad status :  False
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```
* **getName()** : The getName() method returns the name of thread.
* **setName()** : sets the name of of thread.

```python
#!/usr/bin/python

import threading
import time

def worker(i):
    print(threading.currentThread().getName(), " : Starting..")
    time.sleep(i)
    print(threading.currentThread().getName(), " : Exiting..")


if __name__ == "__main__":
    t1 = threading.Thread(target=worker, name="Thread-1", args=(1, ))
    t2 = threading.Thread(target=worker, name="Thread-2", args=(2, ))
    t3 = threading.Thread(target=worker, args=(3, ))
    t3.setName("Thread-3");
    t1.start()
    t2.start()
    t3.start()
```
Output :
```shell
Thread-1  : Starting..
Thread-2  : Starting..
Thread-3  : Starting..
Thread-1  : Exiting..
Thread-2  : Exiting..
Thread-3  : Exiting..
```

#### Creating a Thread class with theading module :

The procedure to create a subclass of Thead clase with threading module are as follows :

* Define a new subclass of thread class
* override the __init__(self, args....) method (constructor) to add additional arguments, and also override the `Thread` class constructor `threading.Thread.__init__(self)`.
* Then override the `run(self, [,args])` method to implement what the thread should do when started (Basically we put all the code here or call the functions/methods from here which we want to run as a thread).

After of creation of subclass we can create an instenace of that an start a new thread by invoking the start() method. Example code :
```python
#!/usr/bin/python

import threading
import time

class myThread(threading.Thread):
    def __init__(self, name, ct):
        threading.Thread.__init__(self)
        self.name = name
        self.ct = ct
    def run(self):
        print("%s : Starting" % self.name)
        time.sleep(self.ct)
        print("%s : Finished" % self.name)

if __name__ == "__main__":
    t1 = myThread("Thread-1", 1)
    t2 = myThread("Thread-2", 2)
    t1.start()
    t2.start()
```
Output :
```shell
Thread-1 : Starting
Thread-2 : Starting
Thread-1 : Finished
Thread-2 : Finished
```
At above code we simply put the code for thread on the run() function.

Example2 :
```python
#!/usr/bin/python

import threading
import time

class myThread(threading.Thread):
    def __init__(self, tID, name, delay):
        threading.Thread.__init__(self)
        self.tID = tID
        self.name = name
        self.delay = delay
    def run(self):
        print("Starting ", self.name)
        print_time(self.tID, self.name, 5, self.delay)
        print("Exiting ", self.name)

def print_time(threadID, threadName, counter, delay):
    while counter:
        time.sleep(delay)
        print("ThreadID : %d | %s: %s" % (threadID, threadName, time.ctime(time.time())))
        counter -= 1

if __name__ == "__main__":
    t1 = myThread(1, "Thread-1", 1)
    t2 = myThread(2, "Thread-2", 2)
    t1.start()
    t2.start()
    print("Exiting Main Thread")
```
Output :
```shell
Starting  Thread-1
Starting  Thread-2
Exiting Main Thread
ThreadID : 1 | Thread-1: Wed Mar 25 01:33:57 2020
ThreadID : 2 | Thread-2: Wed Mar 25 01:33:58 2020
ThreadID : 1 | Thread-1: Wed Mar 25 01:33:58 2020
ThreadID : 1 | Thread-1: Wed Mar 25 01:33:59 2020
ThreadID : 1 | Thread-1: Wed Mar 25 01:34:00 2020
ThreadID : 2 | Thread-2: Wed Mar 25 01:34:00 2020
ThreadID : 1 | Thread-1: Wed Mar 25 01:34:01 2020
Exiting  Thread-1
ThreadID : 2 | Thread-2: Wed Mar 25 01:34:02 2020
ThreadID : 2 | Thread-2: Wed Mar 25 01:34:04 2020
ThreadID : 2 | Thread-2: Wed Mar 25 01:34:06 2020
Exiting  Thread-2
```
At above code the function print_time() is called within the run() method.

**Synchronization of Threads** : We can use similar methods as we used previously which are by using `Lock()` or `Semaphore()` method. Example :
```python
#!/usr/bin/python

import threading
import time

class myThread(threading.Thread):
    def __init__(self, tID, name, delay):
        threading.Thread.__init__(self)
        self.tID = tID
        self.name = name
        self.delay = delay
    def run(self):
        print("Starting ", self.name)
        threadLock.acquire()
        print_time(self.tID, self.name, 3, self.delay)
        threadLock.release()
        print("Exiting ", self.name)

def print_time(threadID, threadName, counter, delay):
    while counter:
        time.sleep(delay)
        print("ThreadID : %d | %s: %s" % (threadID, threadName, time.ctime(time.time())))
        counter -= 1

if __name__ == "__main__":
    threadLock = threading.Lock()
    threads = []                    # empty thread list
    for t in range(1, 4):           # create thread objects and adds them to threads[]
        td = myThread(t, "Thread-"+str(t), 1)
        threads.append(td)
    for t in threads:               # start threads and join() them with main thread
        t.start()
        t.join()
    print("Exiting Main Thread")
```
Output :
```shell
Starting  Thread-1
ThreadID : 1 | Thread-1: Wed Mar 25 02:12:31 2020
ThreadID : 1 | Thread-1: Wed Mar 25 02:12:32 2020
ThreadID : 1 | Thread-1: Wed Mar 25 02:12:33 2020
Exiting  Thread-1
Starting  Thread-2
ThreadID : 2 | Thread-2: Wed Mar 25 02:12:34 2020
ThreadID : 2 | Thread-2: Wed Mar 25 02:12:35 2020
ThreadID : 2 | Thread-2: Wed Mar 25 02:12:36 2020
Exiting  Thread-2
Starting  Thread-3
ThreadID : 3 | Thread-3: Wed Mar 25 02:12:37 2020
ThreadID : 3 | Thread-3: Wed Mar 25 02:12:38 2020
ThreadID : 3 | Thread-3: Wed Mar 25 02:12:39 2020
Exiting  Thread-3
Exiting Main Thread
```
Although the above type of synchronization is anot ideal because it does not execute threads simultaniously, so we need to implement the Lock into print_time() function. Now lets see another example where items from a Queue are removed from multiple threads symultaniously. Some Basics of Queue Data structure :

The Queue module allows to create a new queue object that can hold a specific number of items. There are following methods to control the Queue :

* get() : The get() removes and returns an item from the queue.

* put() : The put adds item to a queue.

* qsize() : The qsize() returns the number of items that are currently in the queue.

* empty() : The empty( ) returns True if queue is empty; otherwise, False.

* full() : the full() returns True if queue is full; otherwise, False.

Example Code :
```python
#!/usr/bin/python

import threading
import time
import queue

exitFlag = 0

class myThread(threading.Thread):
    def __init__(self, name, que):
        threading.Thread.__init__(self)
        self.name = name
        self.que = que
    def run(self):
        print("Starting ", self.name)
        process_data(self.name, self.que)
        print("Exiting ", self.name)

def process_data(threadName, que):
    while not exitFlag:
        queueLock.acquire()
        if not Wqueue.empty():
            data = que.get()
            queueLock.release()
            print("%s processing \"%s\"" % (threadName, data))
        else:
            queueLock.release()
        time.sleep(1)

if __name__ == "__main__":
    queueLock = threading.Lock()
    itemlst = ["One", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine", "Ten"]
    threads = []
    Wqueue = queue.Queue(10)
    for t in range(1, 4):       # create a thread list from 1 to 3
        td = myThread("Thread-"+str(t), Wqueue)
        td.start()
        threads.append(td)


    queueLock.acquire()
    for word in itemlst:       # add data into Queue
        Wqueue.put(word)
    queueLock.release()

    while not Wqueue.empty():  # check if quque is empty or not
        pass

    exitFlag = 1

    for t in threads:          # join the threads with main thread
        t.join()

    print("Exiting Main Thread")
```
Output :
```shell
Starting  Thread-1
Starting  Thread-2
Starting  Thread-3
Thread-1 processing One
Thread-3 processing Two
Thread-2 processing Three
Thread-1 processing Four
Thread-3 processing Five
Thread-2 processing Six
Thread-1 processing Seven
Thread-3 processing Eight
Thread-2 processing Nine
Thread-1 processing Ten
Exiting  Thread-3
Exiting  Thread-2
Exiting  Thread-1
Exiting Main Thread
```

Multiprocessing
---------------

Tutorial : https://www.journaldev.com/15631/python-multiprocessing-example#python-multiprocessing-pool

Code Example :

```python
#!/usr/bin/python3

import multiprocessing
import subprocess
import re

hosts = ["google.com", "facebook.com", "nmap.org", "yahoo.com"]

def pingMe(host):
    try:
        result = subprocess.run(['ping', '-c', '1', host], stdout=subprocess.PIPE)
        regex = "\d{1,3}.\d{1,3}.\d{1,3}.\d{1,3}"
        ip_addr = re.findall(regex, result.stdout.decode('utf-8'))
        print(host+": "+ip_addr[0])
    except subprocess.CalledProcessError as e:
        print(e.output)

def main():
    # start coding from here
    pool = multiprocessing.Pool(4)
    try:
        pool.map_async(pingMe, hosts).get()
    except:
        pass

if __name__ == "__main__":
    main()
```

SubProcess Module : Running External Programs
---------------------------------------------
The subprocess module is a powerful part of Python standard library that lets programmer to use external programs and inspect their outputs easily.

### subprocess.run()

In this tutorial we are going to look at the `subprocess.run()` function. The documentation of function can be found [here](https://docs.python.org/3/library/subprocess.html#subprocess.run).

Syntax :

```shell
import subprocess
result = subprocess.run(command_to_execute, options...)
```

Some of the options are :

* `input` : Allows to pass data to the `stdin` of the subprocess.
* `capture_output=True` : Ensures that result.stdout and result.stderr are filled in with the corresponding output from the external program.
* `text=True` : Encode result.stdout and result.stderr from bytes to strings.
* `shell=True` : execute command with `/bin/sh -c 'command'`.
* `timeout=5` : Set timeout for command.
* `check=True` : Raise an exception if the external program returns non-zero exit code.
* `stdout` : Redirect output into an IO stream.
* `stderr` : Redirect error into an IO stream.

Example 1 :

```shell
subprocess.run('ping -c2 google.com', shell=True)

PING google.com (142.250.192.46) 56(84) bytes of data.
64 bytes from bom12s15-in-f14.1e100.net (142.250.192.46): icmp_seq=1 ttl=118 time=55.4 ms
64 bytes from bom12s15-in-f14.1e100.net (142.250.192.46): icmp_seq=2 ttl=118 time=54.7 ms
```

At above ping command is executed on a shell environment.

Example 2 :

```shell
result = subprocess.run('ping -c2 google.com', shell=True, capture_output=True, text=True)
```

Now the `result.stdout` returns the output and `result.stderr` returns errors if there are any.

```shell
print(result.stdout)

PING google.com (142.250.183.14) 56(84) bytes of data.
64 bytes from bom07s30-in-f14.1e100.net (142.250.183.14): icmp_seq=1 ttl=117 time=53.8 ms
64 bytes from bom07s30-in-f14.1e100.net (142.250.183.14): icmp_seq=2 ttl=117 time=53.5 ms

--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 53.484/53.663/53.842/0.179 ms
```

Now running an erroneous command to check `stdout.stderr` output

```shell
result = subprocess.run('ping -c google.com', shell=True, capture_output=True, text=True)

print(result.stderr)
ping: invalid argument: 'google.com'
```

We can also use `result.check_returncode()` method to check the error message

At the above instance the result.stdout will be empty.


Now if we add `check=True` option to above statement then it directly shows the error message

```shell
result = subprocess.run('ping -c google.com', shell=True, capture_output=True, text=True, check=True)

---------------------------------------------------------------------------
CalledProcessError                        Traceback (most recent call last)
<ipython-input-16-eb26f4b2912a> in <module>
----> 1 result = subprocess.run('ping -c google.com', shell=True, capture_output=True, text=True, check=True)

/usr/lib/python3.8/subprocess.py in run(input, capture_output, timeout, check, *popenargs, **kwargs)
    510         retcode = process.poll()
    511         if check and retcode:
--> 512             raise CalledProcessError(retcode, process.args,
    513                                      output=stdout, stderr=stderr)
    514     return CompletedProcess(process.args, retcode, stdout, stderr)

CalledProcessError: Command 'ping -c google.com' returned non-zero exit status 1.
```

Example 2 :

```shell
result = subprocess.run([sys.executable, '-c', 'print("hello world")'], capture_output=True, text=True, check=True)

result.stdout
'hello world\n'
```

At above sys.executable represents `/usr/bin/python3` binary.

Using `input` option

```shell
result = subprocess.run([sys.executable, '-c', 'import sys;print(sys.stdin.read())'], input=b"Hello world", check=True)
Hello world
```

Using `stdout` option :

We can write output data into a file by using stdout=file_descriptor

```shell
with open('op_data.txt', 'w') as FD:
    subprocess.run([''], text=True, stdout=FD)
```

we can also redirect output into /dev/null

```shell
subprocess.run(['nmap', '-sP', '192.168.1.0/24'], text=True, stdout=subprocess.DEVNULL)
```

Using `timeout` option :

```shell
subprocess.run(['nmap', '-sP', '192.168.1.0/24'], text=True, timeout=10)
```

### subprocess.check_output()

Run command with arguments and return its output.

Syntax :

```shell
import subprocess
result = subprocess.check_output(command_to_execute, options...)
```

Some of the options are :

* `input` : Allows to pass data to the `stdin` of the subprocess.
* `stderr` : Redirect error into an IO stream.
* `capture_output=True` : Ensures that result.stdout and result.stderr are filled in with the corresponding output from the external program.
* `text=True` : Encode result.stdout and result.stderr from bytes to strings.
* `shell=True` : execute command with `/bin/sh -c 'command'`.
* `timeout=5` : Set timeout for command.
* `check=True` : Raise an exception if the external program returns non-zero exit code.

Example :

```shell
subprocess.check_output(['nmap', '-sP', '192.168.1.0/24'], text=True, stderr=subprocess.STDOUT)
```

### subprocess.call()

### subprocess.Popen()


Object Oriented Programming : Classes and OPP Concepts
------------------------------------------------------

#### Class Basics :

Python also support Object Oriented proigramming. In python everything is an object with their properties and methods, and all these concept can be implemeted using class.

**Defining a Class :**

A can can be created by using class `keyword`
```python
class TestClass:
	# put code inside class
	pass
```
Above code defined class `TestClass` which does not have any code, and the we put `pass` statement to avoid getting any error.

**__init__(self) method(Constructor) in Python :**

The init(self) method in python is similar to constructor method of C++ and Java language, which is used to initialize the variable values at the time of object creation of a class. The __init__() function is called automatically every time the class is being used to create a new object. It is implemented as follows :
```python
class TestClass:
	def __init__(self, arg1, arg2....argN):
		self.arg1 = arg1
		self.arg2 = arg2
		...
		...
		self.argN = argN
```

> **"self" Parameter :** The self parameter is a reference to the current instance of the class, and is used to access variables that belongs to the class. Although it does not have to be named self , you can call it whatever you like, but it has to be the first parameter of any function in the class.

**Creating Object Instance** :

Syntax :
```python
ObjectName = ClassName()
```
Example Program :
```python
#!/usr/bin/python

class TestClass:
    def __init__(self, name):
        self.name = name

    def print_msg(self):
        print("Name : ", self.name)

if __name__ == "__main__":
    ob = TestClass("Ajay kumar")
    ob.print_msg()
```
Output :
```shell
Name :  Ajay kumar
```

**Class Variables**

A class has two types of variables :

1. class variables  : class variables are variables whose value is assigned in the class. It is like a static variable which is shared among all the instances of a class.
2. instance variable : an instance variable is a variable which is unique for all the instances of a class. Instance variables are variables whose value is assigned inside a constructor or method with `self` keyword.

Example :
```python
class TestClass:
	# class variable
	c_name = "TestClass"
	details = "This is TestDemp Class"

	def __init__(self, name, age):
		# instance variable
		self.name = name
		self.age = age
```
The class variables can be directly accessed without instansiating any objects. Example :
```python
#!/usr/bin/python

class TestClass:
    # class variable
    c_name = "TestClass"
    details = "This is TestDemp Class"

    def __init__(self, name, age):
        # instance variable
        self.name = name
        self.age = age

if __name__ == "__main__":
    print("Class Variables:\n")
    print("Class Name : ", TestClass.c_name)
    print("Class Details : ", TestClass.details)

    ob = TestClass("Ajay", 26)

    print("\nInstance Variables:\n")
    print("My name is %s and i am %d years old." % (ob.name, ob.age))
```
Output :
```shell
Class Variables:

Class Name :  TestClass
Class Details :  This is TestDemp Class

Instance Variables:

My name is Ajay and i am 26 years old.
```

**Class Methods :**

The methods are defined as follows :
```python
#!/usr/bin/python

class TestClass:
    def __init__(self, num1, num2):
        self.num1 = num1
        self.num2 = num2

    def add(self):
        print("num1 + num2 = ", self.num1 + self.num2)

    def mul(self):
        print("num1 * num2 = ", self.num1 * self.num2)

    def sub(self):
        print("num1 + num2 = ", self.num1 - self.num2)


if __name__ == "__main__":
    ob = TestClass(50, 20)
    ob.add()
    ob.sub()
    ob.mul()
```
Outout :
```shell
num1 + num2 =  70
num1 + num2 =  30
num1 * num2 =  1000
```
We can also supply the arguments into the methods. Example :
```python
#!/usr/bin/python

class TestClass:
    def __init__(self, name):
        self.name = name

    def message(self, msg):
        print("Name : ", self.name)
        print("MSG  : ", msg)

if __name__ == "__main__":
    ob = TestClass("Ajay")
    ob.message("Hello world, this is my messasge.")
```
Output :
```shell
Name :  Ajay
MSG  :  Hello world, this is my messasge.
```
Also note that at class method message(), to access argument `msg` we don't need to use `self` keyword.

**Modify/Changing objects properties** :

Syntax :
```python
object.property = new_value
```
Example Code :
```python
#!/usr/bin/python

class TestClass:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def print_data(self):
        print("Name : ", self.name)
        print("Age  : ", self.age)

if __name__ == "__main__":
    ob = TestClass("Ajay", 26)
    ob.print_data()

    # Changing Parameter
    ob.name = "John"
    ob.age = 30

    print("\nChanged values :\n")
    ob.print_data()
```
Output :
```shell
Name :  Ajay
Age  :  26

Changed values :

Name :  John
Age  :  30
```

**Deleting Object Property :**

Syntax :
```python
del object.property
```
Delete objects
```python
del object
```
Example :
```python
# delete object's properties
del ob.name

# delete object
del ob
```

#### Class Inheritence :

Inheritance enables to create a class which can inherit and use properties and methods of another class.
```
   Base_Class
	   ^
	   |
       |
       |
  Child_Class
```
Base Class : The class which properties and methods are inherited. It is also known as parent class.
Child Class : which inherites the properties and methods from Base class. For example we have a parent class name `Base` with two properties and a method `printB()` :
```python
class Base:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def printB(self):
        print(self.name)
        print(self.age)
```
Now we want to inherit its properties and methods on a new class named `Child`, and to do that the code would be :
```python
class Child(Base):
    def __init__(self, name, age, addr):
        Base.__init__(self, name, age)
        self.addr = addr

    def printC(self):
        print(self.name)
        print(self.age)
        print(self.addr)
```
As we can see that the points are :

* Put the parent class name on the definition of child class in parenthesis
```python
class Child(Base):
```
* We have to get all the parameter values into the child's __init__() method and inside the __init__() method we have to supply the parameters required by `Base()` class (which is in this case name and age).
```python
def __init__(self, name, age, addr):
	Base.__init__(self, name, age)   <----- Calling Base Class
	self.addr = addr
```
Full code of example is :

```python
#!/usr/bin/python

class Base:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def printB(self):
        print(self.name)
        print(self.age)

class Child(Base):
    def __init__(self, name, age, addr):
        Base.__init__(self, name, age)
        self.addr = addr

    def printC(self):
        print(self.name)
        print(self.age)
        print(self.addr)

if __name__ == "__main__":
    ob1 = Base("Ajay", 26)
    ob1.printB()

    print()

    ob2 = Child("john", 30, "Raipur, India")
    ob2.printC()
```
Output :
```shell
Ajay
26

john
30
Raipur, India
```

**Super function :**

We can also use super() function to inherit a class. It is basically used within the place of base class name to call the base class __init__() {constructor} function. Now in that case the Child class will look like this :
```python
class Child(Base):
    def __init__(self, name, age, addr):
        super().__init__(name, age)
        self.addr = addr
```
As we can see in __init__() function of child class we can replace base class name with `super()` keyword.
```python
#!/usr/bin/python

class Base:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def printB(self):
        print(self.name)
        print(self.age)

class Child(Base):
    def __init__(self, name, age, addr):
        super().__init__(name, age)
        self.addr = addr

    def printC(self):
        print(self.name)
        print(self.age)
        print(self.addr)

if __name__ == "__main__":
    ob1 = Base("Ajay", 26)
    ob1.printB()

    print()

    ob2 = Child("john", 30, "Raipur, India")
    ob2.printC()
```

**Overriding Properties:**
```python
#!/usr/bin/python

class Base:
    def __init__(self, name, age):
        self.name = name
        self.age = age

class Child(Base):
    def __init__(self, name, age, addr):
        Base.__init__(self, name, age)
        self.age = 20
        self.addr = addr

    def printD(self):
        print(self.name)
        print(self.age)
        print(self.addr)


if __name__ == "__main__":
    ob = Child("john", 30, "Raipur, India")
    ob.printD()
```
Output :
```shell
john
20
Raipur, India
```
**Overriding Methods:**
```python
#!/usr/bin/python

class Base:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def printD(self):
        print(self.name)
        print(self.age)


class Child(Base):
    def __init__(self, name, age, addr):
        Base.__init__(self, name, age)
        self.addr = addr

    def printD(self):
        print(self.name)
        print(self.age)
        print(self.addr)


if __name__ == "__main__":
    ob = Child("john", 30, "Raipur, India")
    ob.printD()
```
Output :
```shell
john
30
Raipur, India
```
**Composition in Python :**

In composition, we do not inherit from the base class but establish relationships between classes through the use of instance variables that are references to other objects. To achieve composition you can instantiate other objects in the class and then use those instances. For example :
```python
class Base:
    def __init__(self, name, age):
        self.name = name
        self.age = age

class Child:
	def __init__(self, name, age, addr)
		self.base = Base(name, age)
		self.addr = addr
```
As we can see in the `Child` class we create an innstantiate Base class with an instance variable `self.base`
```
self.base = Base(name, age)
```
Now we can access all the properties of Base class by
```
self.base.name
self.base.age
```
and to access any of Base class methods
```
self.base.Method()
```
Example Program :
```python
#!/usr/bin/python

class Base:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def printB(self):
        print(self.name)
        print(self.age)

class Child():
    def __init__(self, name, age, addr):
        self.base = Base(name, age)
        self.addr = addr

    def printC(self):
        self.base.printB()
        print(self.addr)

if __name__ == "__main__":
    ob1 = Base("Ajay", 26)
    ob1.printB()

    print()

    ob2 = Child("john", 30, "Raipur, India")
    ob2.printC()
```
Output :
```shell
Ajay
26

john
30
Raipur, India
```

File and Directory handling
---------------------------

#### os module :

The os module provides various methods to handling directories and files. Some methods are as follows :

* getcwd() : get the current working directory.
```shell
>>> import os
>>> os.getcwd()
'/tmp/test'
```
* os.chdir() : Change the currnet working directory.
```shell
>>> os.chdir("/home/")
>>> os.getcwd()
'/home'
```
* os.listdir() : List all the directories and files inside a directory.
```shell
>>> os.listdir()
['venv']
>>> os.listdir("/home/ajay/Documents")
['Courses', 'New_Books', 'links.md', 'temp', 'Files.zip']
```
* os.mkdir() : Create a new directory.
```shell
>>> os.listdir()
['venv']
>>> os.mkdir("testdir")
>>> os.listdir()
['venv', 'testdir']
```
* os.makedirs() : Create recursive directory structures.
```shell
>>> os.makedirs("test1/test2/test3")
```
* os.rename() : Renaming a directory or a file.
```shell
s.listdir()
['venv', 'testdir']
>>> os.rename("testdir", "newdir")
>>> os.listdir()
['newdir', 'venv']
```
* os.remove() : Remove a file.
* os.rmdir() : Remove a directory.
```shell
>>> os.listdir()
['newdir', 'file.txt', 'venv']
>>> os.remove("file.txt")
>>> os.rmdir("newdir")
>>> os.listdir()
['venv']
>>>
```
* os.removedirs() : Remove directories recursively. Also note that the directories needs to be empty.
```shell
>>> os.removedirs("test1/test2/test3")
```

Some other important methods of os module are :

* os.system("command") : executes system commands.
* os.uname() : Returns system information.
* os.getpid() : Returns the process id.

#### shutil module :

* rmtree() : It is used to remove non-empty directories. It is in the `shutil` modules.
```shell
>>> os.listdir("testdir")
['file3', 'file1', 'file2', 'test2']
>>> import shutil
>>> os.listdir("testdir")
['file3', 'file1', 'file2', 'test2']
>>> import shutil
>>> shutil.rmtree("testdir")
```
* shutil.copyfile(source, destination) : used to copy file.
```shell
>>> shutil.copyfile("file.txt", "filecopy.txt")
```
* shutil.move(source, destination) : Moves files from source to destincation.
```shell
>>> shutil.move("test1/file.txt", "test2/")
```
* shutil.copytree(source, destination) : Copy files recurcively from source directory to destination directory.
```shell
>>> shutil.copytree("test2/", "test2_copy")
```
The above command basically copy all the files and folders of "test2" into a new directory "test2_copy" and also note that if test2_copy is previously created then it throws error.

Python Modules
--------------

A module is nothing but a python file containing statements, functions and class definitions which can be latter imported and used by another python programs by using `import` statement. In other words module is like a code library which can be included/imported by another python applications.

**Creating a test module :**

File : testmodule.py
```python
def Add(a, b):
    return a+b

def Mul(a, b):
    return a*b

def display(data):
    print(data)
```

Now to include testmodule.py into another program the `import` keyword is used. Example :
```
#!/usr/bin/python3

import testmodule

def main():
    num1 = testmodule.Add(10, 20)
    num2 = testmodule.Mul(10,20)

    testmodule.display(num1)
    testmodule.display(num2)


if __name__ == "__main__":
    main()
```
Output :
```shell
30
200
```
To use functions are use as :
```python
module_name.fuction_name()
```
We can also define variables in modules. Example :

File: testmod.py
```python
year = {"month":12, "days":365, "weeks":52}
current_year = 2020
months = ["Jan","Fab","Mar","April","May","June","July","Aug","Sept","Oct","Nov","Dec"]
```
Program :
```python
#!/usr/bin/python

import testmod

def main():
    print(testmod.current_year)
    print("Number of Days : ", testmod.year["days"])
    print("Number of Months : ", testmod.year["month"])

    for i in testmod.months:
        print(i)

if __name__ == "__main__":
    main()
```
Output :
```shell
2020
Number of Days :  365
Number of Months :  12
Jan
Fab
Mar
April
May
June
July
Aug
Sept
Oct
Nov
Dec
```

**Import modules with renaming :**

We can also rename module at import statement. For example :
```python
!/usr/bin/python3

import testmodule as tm

def main():
    num1 = tm.Add(10, 20)
    num2 = tm.Mul(10,20)

    tm.display(num1)
    tm.display(num2)


if __name__ == "__main__":
    main()
```
At above program we rename testmodule as `tm`, and now we can access all its functionalities by simply `tm.function_name()`.

**Import From Module :**

With `import from` statement we can include only selected functions, Syntax :
```python
from module_name import function_name
```
Example :
```python
#!/usr/bin/python3

from testmodule import Add

def main():
    num = Add(10, 20)
    print(num)


if __name__ == "__main__":
    main()
```
Note : we can directly use the function here.

**from module import (All) :**

To import all functions from a module use `*` asterisc sign after import. Example :
```python
#!/usr/bin/python3

from testmodule import *

def main():
    num1 = Add(10, 20)
    num2 = Mul(10, 20)
    display(num1)
    display(num2)

if __name__ == "__main__":
    main()
```
By the technique we can call all the methods by just their names.

**dir() function :**

The dir() function will return all the methods and variables defined inside a module. It can also be used within user defined modules. Example :
```
>>> import os, testmodule, testmod
>>> dir(testmodule)
['Add', 'Mul', '__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'display']
>>> dir(testmod)
['__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'current_year', 'months', 'year']
>>> dir(os)
['CLD_CONTINUED', 'CLD_DUMPED', 'CLD_EXITED', 'CLD_TRAPPED', 'DirEntry', 'EX_CANTCREAT', 'EX_CONFIG', 'EX_DATAERR', 'EX_IOERR', 'EX_NOHOST', 'stat_float_times', 'stat_result', 'statvfs', 'statvfs_result', 'strerror', 'supports_bytes_environ', 'supports_dir_fd', 'supports_effective_ids', 'supports_fd', 'supports_follow_symlinks', 'symlink', 'sync', 'sys', 'sysconf', 'sysconf_names', 'system', 'tcgetpgrp', 'tcsetpgrp', 'terminal_size', 'times', 'times_result', 'truncate', 'ttyname', 'umask', 'uname', 'uname_result', 'unlink', 'unsetenv', 'urandom', 'utime', 'wait', 'wait3', 'wait4', 'waitid', 'waitid_result', 'waitpid', 'walk', 'write', 'writev']
```

**help() Method :** :

The help() method will all the methods, variables of a module in an ordered and arranged manner. It also works with user-created modules. Example :
```python
>>> import os
>>> help(os)

Help on module os:

NAME
    os - OS routines for NT or Posix depending on what system we're on.

FILE
    /usr/lib/python2.7/os.py

MODULE DOCS
    https://docs.python.org/library/os

DESCRIPTION
    This exports:
      - all functions from posix, nt, os2, or ce, e.g. unlink, stat, etc.
      - os.path is one of the modules posixpath, or ntpath
      - os.name is 'posix', 'nt', 'os2', 'ce' or 'riscos'
      - os.curdir is a string representing the current directory ('.' or ':')
      - os.pardir is a string representing the parent directory ('..' or '::')
      - os.sep is the (or a most common) pathname separator ('/' or ':' or '\\')
      - os.extsep is the extension separator ('.' or '/')
      - os.altsep is the alternate pathname separator (None or '/')
      - os.pathsep is the component separator used in $PATH etc
      - os.linesep is the line separator in text files ('\r' or '\n' or '\r\n')
      - os.defpath is the default search path for executables
```

```python
>>> import testmodule
>>> help(testmodule)
Help on module testmodule:

NAME
    testmodule

FILE
    /tmp/test/testmodule.py

FUNCTIONS
    Add(a, b)

    Mul(a, b)

    display(data)
```

**Locating modules :**

When you import a module, the python interpreter searches for the module in the following sequences :
* Current directory
* Next it searches moduels for the standard path (sys.path), which is as follows `sys.path`.
```python
>>> import sys
>>> print(sys.path)
['', '/usr/lib/python2.7', '/usr/lib/python2.7/plat-x86_64-linux-gnu', '/usr/lib/python2.7/lib-tk', '/usr/lib/python2.7/lib-old', '/usr/lib/python2.7/lib-dynload', '/home/ajay/.local/lib/python2.7/site-packages', '/usr/local/lib/python2.7/dist-packages', '/usr/lib/python2.7/dist-packages']
```

**namespaces and scopingi :**

* What is namespace ?

As we know variables are names (identifiers) that map to objects. A namespace is a dictionary of variable names (keys) and their corresponding objects (values). A Python statement can access variables in a local namespace and in the global namespace. Each function has its own local namespace.

Python makes educated guesses on whether variables are local or global. It assumes that any variable assigned a value in a function is local. Therefore, in order to assign a value to a global variable within a function, you must first use the global statement. The statement `global VarName` tells Python that VarName is a global variable. Python stops searching the local namespace for the variable. Example :
```python
#!/usr/bin/python3

# globale variable
val = 1000

def local():
    # works on local variable `val`
    val = 1
    val = val + 1

def globl():
    # works on global variable `val`
    global val
    val = val + 1

def main():

    local()
    print(val)
    globl()
    print(val)


if __name__ == "__main__":
    main()
```
Output :
```shell
1000
1001
```

Python Packages
---------------

Packages are namespaces which contain multiple packages and modules themselves. It is basically a hierarchical file directory structure which consists multiple modules and subpackages in it.

**Structure of Package** :

Each package in Python is a directory which must contain a special file called `__init__.py`. This file can be empty, and it indicates that the directory it contains is a Python package, so it can be imported the same way a module can be imported. Also if there is a sub-package insde a package then there is another `__init__.py` file inside that sub-package folder which belongs to that sub-package. The structure of package is as follows :
```
PACKAGE
  |
  |
  |---->  __init__.py
  |
  |---->  module1.py
  |
  |---->  module2.py
  |
  |---->  module3.py
```
Package with Sub-Packages :
 ```
PACKAGE
  |
  |
  |---->  __init__.py
  |
  |---->  module.py
  |
  |---->  SUB_PACKAGE1 --------------|
  |                                  |----> __init__.py
  |---->  SUB_PACKAGE2               |
               |                     |----> module1.py
               |----> __init__.py    |
               |                     |----> module2.py
               |----> module1.py
               |
               |----> module2.py
```
Now lets take an example of Auto-driving Car :
```
Auto_Driven_Car
  |
  |
  |----> __init__.py
  |
  |----> start_engine.py
  |
  |----> stop_engine.py
  |
  |----> Drive --|
  |              |----> __init__.py
  |              |
  |              |----> accelerate.py
  |              |
  |              |----> break.py
  |              |
  |              |----> steer.py
  |
  |----> Sensors --|
  |                |----> __init__.py
  |                |
  |                |----> Cemera.py
  |                |
  |                |----> nightVision.py
  |                |
  |                |----> Radar.py
  |
  |----> Directions --|
                      |----> __init__.py
                      |
                      |----> GPS.py
                      |
                      |----> RoadMaps.py
```

**__init__.py file :**

To consider directory a package it must contains `__init__.py` file. This file can be left empty but we generally place the initialization code for that package in this file. Now lets see an example of Package. The package structure is as follows :
```
 Calc                      // directory
   |---> __init__.py       // file
   |
   |---> Math.py           // file
   |
   |---> Display.py        // file
```
`File: __init__.py`
```python

```
File: Math.py
```python
def Add(a, b):
    print("%d + %d = %d" %(a,b,a+b))

def Mul(a, b):
    print("%d * %d = %d" %(a,b,a*b))

def Sub(a, b):
    print("%d - %d = %d" %(b,a, b-a))
```
File: Display.py
```python
def printD1(data):
    print("===========")
    print(data)
    print("===========")

def printD2(data):
    print("+=+=+=+=+=+=+")
    print(data)
    print("+=+=+=+=+=+=+")
```
**Example 1**

Note that at here __init__.py is empty. Example code of using above package :
```python
#!/usr/bin/python3

import Calc.Math
import Calc.Display

def main():
    # start coding here
    Calc.Math.Add(10, 20)
    Calc.Math.Mul(10, 20)
    Calc.Math.Sub(10, 20)
    Calc.Display.printD1("Hello World")
    Calc.Display.printD2("Hello World")


if __name__ == "__main__":
    main()
```
Output :
```shell
10 + 20 = 30
10 * 20 = 200
20 - 10 = 10
===========
Hello World
===========
+=+=+=+=+=+=+
Hello World
+=+=+=+=+=+=+
```
At above program we only import `Calc`.


**Example 2**

Now lets modify `__init__.py` :
`File: __ini__.py`
```python
import Calc.Math as M
import Calc.Display as D
```
Program code :
```python
#!/usr/bin/python3

import Calc

def main():
    # start coding here
    Calc.M.Add(10, 20)
    Calc.M.Mul(10, 20)
    Calc.M.Sub(10, 20)
    Calc.D.printD1("Hello World")
    Calc.D.printD2("Hello World")


if __name__ == "__main__":
    main()
```
Output :
```shell
10 + 20 = 30
10 * 20 = 200
20 - 10 = 10
===========
Hello World
===========
+=+=+=+=+=+=+
Hello World
+=+=+=+=+=+=+
```

**Example 3**

`File: __init__.py`
```python
from Calc.Math import *
from Calc.Display import *
```
Program Code :
```python
i#!/usr/bin/python3

import Calc

def main():
    # start coding here
    Calc.Add(10, 20)
    Calc.Mul(10, 20)
    Calc.Sub(10, 20)
    Calc.printD1("Hello World")
    Calc.printD2("Hello World")


if __name__ == "__main__":
    main()
```
Output :
```shell
10 + 20 = 30
10 * 20 = 200
20 - 10 = 10
===========
Hello World
===========
+=+=+=+=+=+=+
Hello World
+=+=+=+=+=+=+
```

Socket Controls, Servers and Clients Web Servers and Client scripting
---------------------------------------------------------------------

#### Socket Programming :

Socket programming is a way of connecting two nodes on a network to communicate with each other. Sockets are interior endpoints built for sending and receiving data. A single network will have two sockets, one for each communicating device or program. These sockets are a combination of an IP address and a Port.

**Socket APIs in Python :**

The `socket` module provides an interface to implements Sockets in python. Some of the inportant methods provided by the module are as follows :

* socket() : Used to create sockets (required on both server as well as client ends to create sockets).
* bind() : Used to bind to the address that is specified as a parameter.
* listen() : enables the server to accept connections.
* accept() : used to accept a connection. It returns a pair of values (conn, address) where conn is a new socket object for sending or receiving data and address is the address of the socket present at the other end of the connection.
* conect() : used to connect to a remote address specified as the parameter.
* connect_ex() : similar to connect(), but return an error indicator instead of raising an exception.
* send() : send data to the socket. The socket must be connected to a remote host.
* recv() : Receive data from the socket. The return value is a bytes object representing the data received.
* close() : used to mark the socket as closed.

Thses socket apis directly maps to their C counterparts system calls.

**Creating A Socket Server :**

The below diagram shows which methods are used on which stages :

```
      ----------                     ----------
      | Server |                     | Client |
      ----------                     ----------
          ||                             ||
          \/                             \/
    socket.socket()                 socket.socket()
          ||                             ||
          \/                             ||
     socket.bind()                       ||
          ||                             ||
          \/                             ||
    socket.listen()                      ||
          ||                             ||
          \/                             ||
    socket.accept()                      ||
          ||                             \/
          ||<--------------------- socket.connect()
          ||                             ||
          \/                             \/
|-> socket.recv() <---------------- socket.send() <---|
|         ||                             ||           |
|         ||                             ||           |
|         \/                             \/           |
|-- socket.send() ----------------> socket.recv() ----|
          ||                             ||
          \/                             \/
    socket.recv() <--------------- socket.close()
          ||
          ||
          \/
    socket.close()
```

Now lets look at the code of a simple server program :
**File: server.py**
```python
#!/usr/bin/python

import socket

ip = "127.0.0.1"
port = 1234

def main():
    srv = socket.socket(socket.AF_INET, socket.SOCK_STREAM)    # create a TCP socket
    srv.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)  # make the port number reusable
    print("Socket created.")
    srv.bind((ip, port))                                       # bind host address and port together
    print(f"Socket binded to {ip}:{port}")
    srv.listen(5)                                              # configure how many client server
    print("Listening...")                                      # can listen simultaniously

    while True:                                # Loop through the each new connection
        conn, addr = srv.accept()              # accept new connection
        print()
        print(f"Connected to {addr}")
        while True:                            # loop for receing and sending data to the
            data = conn.recv(1024).decode()    # connected client until client disconnected
            data =  data.rstrip("\n")          # by forcefully quiting or by sending empty
			if not data:                       # line
                break
            print(f"{addr[0]}:> {data}")
            data = input(":> ")                # server takes input and send it to the
            data += '\n'                       # client
            conn.send(data.encode())           # the decode() and encode() method is used to
        print(f"{addr} disconnected\n")        # decode/encode the data into bytes
        conn.close()

if __name__ == "__main__":
    main()
```

The `socket(socket.AF_INET, socket.SOCK_STREAM)` method has two arguments first one is `AF_INET` which refers to Address from the Internet and it requires a pair of (host, port) where the host can either be a URL of some particular website or its address and the port number is an integer. `SOCK_STREAM` is used to create TCP Protocols. For UDP protocols `SOCK_DGRAM` is used. Use :
```shell
$ python server.py
Socket created.
Socket binded to 127.0.0.1:1234
Listening...

Connected to ('127.0.0.1', 46018)
127.0.0.1:> Hello
:> Hello friend
127.0.0.1:> hi
:> hi again
127.0.0.1:> bye
:> bye bye
('127.0.0.1', 46018) disconnected

^C
```
For client i am using `nc`
```shell
$ nc 127.0.0.1 1234
Hello
Hello friend
hi
hi again
bye
^C
```

Writing A CLient :
**File: client.py**
```python
#!/usr/bin/python

import socket

serv_ip = "127.0.0.1"
serv_port = 1234

def main():
    try:
        clts = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        clts.connect((serv_ip, serv_port))
        print("Connected to server")
        print("Hit Return to exit")
        while 1:
            data = input(":> ").rstrip("\n")
            if not data:
                clts.close()
                break
            clts.sendall(data.encode())
            r_data = clts.recv(1024)
            r_data = r_data.decode().rstrip("\n")
            print(r_data)
    except:
        print("Server Down..")
        print("can't connect to the server..!!")


if __name__ == "__main__":
    main()
```
Output :
```shell
$ python client.py
Connected to server
Hit Return to exit
:> Hello
hello user
:> bye
bye bye
:>
```

**Multi-Threaded Server :**

**File: Tserver.py**
```python
#!/usr/bin/python

import socket
import threading

host = "127.0.0.1"
port = 1234
lock = threading.Lock()         # To lock other threads while
                                # admin finishes chat with current thead

def conn_handler(conn, addr, thread_id):        # method runs on thread

    print(f"[Thread_id : T{thread_id}] Connected to {addr[0]} ")
    while True:
        data = conn.recv(1024).decode()
        data =  data.rstrip("\n")
        if not data:
            conn.close()
            lock.release()      # release the thread lock
            break
        print(f"{addr[0]}:> {data}")
        data = input(":> ")
        data += '\n'
        conn.send(data.encode())
    print(f"[Thread_id : T{thread_id}] {addr[0]} disconnected.\n")


def main():
    srv = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    srv.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    print("Socket created.")
    srv.bind((host, port))
    print(f"Socket binded to {host}:{port}")
    srv.listen(5)
    print("Listening...")
    thread_id = 0
    print()
    print("Waitingh for Client..")

    while True:
        thread_id = thread_id + 1
        conn, addr = srv.accept()
        try:
            lock.acquire()      # acquire theadlock
            t = threading.Thread(target=conn_handler, args=(conn, addr, thread_id,))
            t.start()
        except:
            lock.release()      # release the lock if there is any error
            print(f"Error: Unable to start thread [T{thread_id}]\n")
            conn.close()
    srv.close()

if __name__ == "__main__":
    main()
```
Output :
```shell
$ python Tserver.py
Socket created.
Socket binded to 127.0.0.1:1234
Listening...

Waitingh for Client..
[Thread_id : T1] Connected to 127.0.0.1
127.0.0.1:> hello
:> hello user
[Thread_id : T1] 127.0.0.1 disconnected.

[Thread_id : T2] Connected to 127.0.0.1
127.0.0.1:> Hello again admin
:> hello user
127.0.0.1:> bye admin
:> bye bye
[Thread_id : T2] 127.0.0.1 disconnected.
```

Above code perform full chat with the users, so it is not the effective way to use threading here, but we can modify the server code to just connect with the client, send data once, then disconnect with the client, by that way we can better utilize the threading module. New program :
```python
#!/usr/bin/python

import socket
import threading

host = "127.0.0.1"
port = 1234
lock = threading.Lock()         # To lock other threads while
                                # admin finishes chat with current thead

def conn_handler(conn, addr, thread_id):        # method runs on thread

    print(f"[Thread_id : T{thread_id}] Connected to {addr[0]} ")
    data = conn.recv(1024).decode()
    data =  data.rstrip("\n")
    print(f"Data From {addr[0]}:> {data}")
    data = "Hello User " + addr[0] + ", from the Python Web Server\n";
    conn.send(data.encode())
    conn.close()
    lock.release()      # release the thread lock
    print(f"[Thread_id : T{thread_id}] {addr[0]} disconnected.\n")


def main():
    srv = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    srv.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    print("Socket created.")
    srv.bind((host, port))
    print(f"Socket binded to {host}:{port}")
    srv.listen(5)
    print("Listening...")
    thread_id = 0
    print()
    print("Waitingh for Client..")

    while True:
        thread_id = thread_id + 1
        conn, addr = srv.accept()
        try:
            lock.acquire()      # acquire theadlock
            t = threading.Thread(target=conn_handler, args=(conn, addr, thread_id,))
            t.start()
        except:
            lock.release()      # release the lock if there is any error
            print(f"Error: Unable to start thread [T{thread_id}]\n")
            conn.close()
    srv.close()

if __name__ == "__main__":
    main()
```
Output :
```shell
$ python Tserver.py
Socket created.
Socket binded to 127.0.0.1:1234
Listening...

Waitingh for Client..
[Thread_id : T1] Connected to 127.0.0.1
Data From 127.0.0.1:> Hello admin
[Thread_id : T1] 127.0.0.1 disconnected.

[Thread_id : T2] Connected to 127.0.0.1
Data From 127.0.0.1:> Hello admin
[Thread_id : T2] 127.0.0.1 disconnected.

[Thread_id : T3] Connected to 127.0.0.1
Data From 127.0.0.1:> Hello admin
[Thread_id : T3] 127.0.0.1 disconnected.

[Thread_id : T4] Connected to 127.0.0.1
Data From 127.0.0.1:> Hello admin
[Thread_id : T4] 127.0.0.1 disconnected.

[Thread_id : T5] Connected to 127.0.0.1
Data From 127.0.0.1:> Hello admin
[Thread_id : T5] 127.0.0.1 disconnected.

[Thread_id : T6] Connected to 127.0.0.1
Data From 127.0.0.1:> Hello admin
[Thread_id : T6] 127.0.0.1 disconnected.

[Thread_id : T7] Connected to 127.0.0.1
Data From 127.0.0.1:> Hello admin
[Thread_id : T7] 127.0.0.1 disconnected.

[Thread_id : T8] Connected to 127.0.0.1
Data From 127.0.0.1:> Hello admin
[Thread_id : T8] 127.0.0.1 disconnected.

[Thread_id : T9] Connected to 127.0.0.1
Data From 127.0.0.1:> Hello admin
[Thread_id : T9] 127.0.0.1 disconnected.

[Thread_id : T10] Connected to 127.0.0.1
Data From 127.0.0.1:> Hello admin
[Thread_id : T10] 127.0.0.1 disconnected.

[Thread_id : T11] Connected to 127.0.0.1
Data From 127.0.0.1:> Hello admin
[Thread_id : T11] 127.0.0.1 disconnected.
```
To send multiple request simultaniously, use below shell commands, and also replies from the server can be seen
```shell
$ while [ $x -le 10 ]
> do
> printf "Hello admin" | nc 127.0.0.1 1234
> x=$((x+1))
> done
Hello User 127.0.0.1, from the Python Web Server
Hello User 127.0.0.1, from the Python Web Server
Hello User 127.0.0.1, from the Python Web Server
Hello User 127.0.0.1, from the Python Web Server
Hello User 127.0.0.1, from the Python Web Server
Hello User 127.0.0.1, from the Python Web Server
Hello User 127.0.0.1, from the Python Web Server
Hello User 127.0.0.1, from the Python Web Server
Hello User 127.0.0.1, from the Python Web Server
Hello User 127.0.0.1, from the Python Web Server
Hello User 127.0.0.1, from the Python Web Server
```

#### Client-side scripting (with urllib3 and requests modules):

**urllib3 module :**

urllib3 is a powerfull module for client side scripting. Example of how to use :
```python
import urllib3
http = urllib3.PoolManager()  # instantiate PoolManager()
```
If you need to make requests to multiple hosts, then PoolManager takes care of maintaining your pools with diferent hosts.

Methods in urllib3 :

* http.request('METHOD', 'URL') : used to send request.
* resp.status : return status code.
* resp.data: return data from server.
* resp.geturl() : return url.
* resp.info() : return response header
* resp.headers[] : return response header, similar to resp.info()

A simple example :
```python
#!/usr/bin/python

import urllib3
http = urllib3.PoolManager()

def main():
    url = 'http://sec-art.net/robots.txt'
    resp = http.request('GET', url)

    print("URL: ", resp.geturl())
    print()
    print("Status Code: ", resp.status)
    print()
    print("HTTP Headers: ")
    headData = resp.info()
    # loop for printing headers
    for key in headData:
        print(f"{key} : {headData[key]}")
    print()
    print("Data : ", resp.data)

if __name__ == "__main__":
    main()
```
output :
```shell
URL:  http://www.sec-art.net/robots.txt

Status Code:  200

HTTP Headers:
Content-Type : text/plain; charset=UTF-8
Expires : Thu, 09 Apr 2020 10:23:58 GMT
Date : Thu, 09 Apr 2020 10:23:58 GMT
Cache-Control : private, max-age=86400
Last-Modified : Mon, 06 Apr 2020 19:23:31 GMT
X-Content-Type-Options : nosniff
X-XSS-Protection : 1; mode=block
Server : GSE
Accept-Ranges : none
Vary : Accept-Encoding
Transfer-Encoding : chunked

Data :  b'User-agent: Mediapartners-Google\nDisallow: \n\nUser-agent: *\nDisallow: /search\nAllow: /\n\nSitemap: http://www.sec-art.net/sitemap.xml\n\n'
```

Sending GET request with parameters :
```python
#!/usr/bin/python

import urllib3
http = urllib3.PoolManager()

def main():
    url = 'http://requestbin.net/r/1iw1w781'
    params = {'name':'ajay', 'age':26} # get parameters in dictonary form

    resp = http.request('GET', url, fields=params)
    print("Request sent..")
    print(f"Status Code : {resp.status}")

if __name__ == "__main__":
    main()
```
Output :
```shell
Request sent..
Status Code : 200
```
We can also test this with httpbin service, which returns all the paramters and other information in JSON format.
```python
#!/usr/bin/python

import urllib3
http = urllib3.PoolManager()

def main():
    url = 'https://httpbin.org/get'
    params = {'name':'ajay', 'age':26} # get parameters in dictonary form

    resp = http.request('GET', url, fields=params)
    print("Request sent..")
    print(f"Status Code : {resp.status}")
    print("Returned Data : "),
    print(resp.data.decode('utf-8'))
if __name__ == "__main__":
    main()
```
Output :
```shell
Request sent..
Status Code : 200
Returned Data :
{
  "args": {
    "age": "26",
    "name": "ajay"
  },
  "headers": {
    "Accept-Encoding": "identity",
    "Host": "httpbin.org",
    "X-Amzn-Trace-Id": "Root=1-5e8f0fbe-9cf8fc00bf9bc4009f633e00"
  },
  "origin": "192.168.1.10",
  "url": "https://httpbin.org/get?name=ajay&age=26"
}
```

Sending post request :

It is similar to get method, just change method 'GET' to 'POST' in `http.request()`
```python
#!/usr/bin/python

import urllib3
http = urllib3.PoolManager()

def main():
    url = 'http://requestbin.net/r/1iw1w781'
    params = {'name':'ajay', 'age':26} # get parameters in dictonary form

    resp = http.request('POST', url, fields=params)
    print("Request sent..")
    print(f"Status Code : {resp.status}")

if __name__ == "__main__":
    main()
```
Output :
```shell
Request sent..
Status Code : 200
```

Sending request with HTTPS :

For this we have to use certifi module, so first install it by `pip install certifi`. The certifi bundle contains carefully curated collection of Root Certificates for validating the trustworthiness of SSL certificates
```python
import certifi
print(certifi.where())
```
the built-in function `certifi.where()` reference the installed certificate authority (CA) bundle.
```python
#!/usr/bin/python

import urllib3
import certifi
http = urllib3.PoolManager(ca_certs=certifi.where())

def main():
    url = 'https://httpbin.org/get'
    params = {'name':'ajay', 'age':26} # get parameters in dictonary form

    resp = http.request('GET', url, fields=params)
    print("Request sent..")
    print(f"Status Code : {resp.status}")
    print("Returned Data : "),
    print(resp.data.decode('utf-8'))
if __name__ == "__main__":
    main()
```

Sending JSON request :

```python
#!/usr/bin/python

import urllib3
import certifi
import json
http = urllib3.PoolManager(ca_certs=certifi.where())

def main():
    url = 'http://httpbin.org/post'

    # js data
    js_dat = {'name':'ajay', 'age':26, 'city':'Raipur'}
    encoded_data = json.dumps(js_dat).encode('utf-8')


    resp = http.request(
            'POST',
            url,
            body=encoded_data,
            headers={'Contest-Type':'application/json'})


    print("Request sent..")
    print(f"Status Code : {resp.status}")
    print("Returned Data : "),
    ret_data = resp.data.decode('utf-8')
    print(ret_data)
    print("Only JSON part : \n")
    print(json.loads(ret_data)['json'])


if __name__ == "__main__":
    main()
```
Output :
```Output :
Request sent..
Status Code : 200
Returned Data :
{
  "args": {},
  "data": "{\"name\": \"ajay\", \"age\": 26, \"city\": \"Raipur\"}",
  "files": {},
  "form": {},
  "headers": {
    "Accept-Encoding": "identity",
    "Content-Length": "45",
    "Contest-Type": "application/json",
    "Host": "httpbin.org",
    "X-Amzn-Trace-Id": "Root=1-5e8f1ef6-a7efa03dc91c41287781ef84"
  },
  "json": {
    "age": 26,
    "city": "Raipur",
    "name": "ajay"
  },
  "origin": "122.170.210.49",
  "url": "http://httpbin.org/post"
}

Only JSON part :

{'age': 26, 'city': 'Raipur', 'name': 'ajay'}
```

Enable Redirection :

To enable redirects use :
```python
resp = http.request('GET', url, redirect=True)
```

Downlaod Binary Data :
```python
#!/usr/bin/python

import urllib3
import certifi
import json
http = urllib3.PoolManager(ca_certs=certifi.where())

def main():
    url = 'https://www.google.com/images/branding/googlelogo/1x/googlelogo_color_272x92dp.png'
    resp = http.request('GET', url)

    with open('image.png', 'wb') as f:
        f.write(resp.data)

    print("Image downloaded.")

if __name__ == "__main__":
    main()
```
Output :
```shell
Image downloaded
```

Downlaod Streaming Data :

Chunked transfer encoding is a streaming data transfer mechanism available since HTTP 1.1. In chunked
transfer encoding, the data stream is divided into a series of non-overlapping chunks.

The chunks are sent out and received independently of one another. Each chunk is preceded by its size in bytes.

Setting preload_content to False means that urllib3 will stream the response content. The stream() method
iterates over chunks of the response content. When streaming, we should call release_conn() to release the http connection back to the connection pool so that it can be re-used.
```python
#!/usr/bin/python

import urllib3
import certifi

http = urllib3.PoolManager(ca_certs=certifi.where())

def main():
    url = 'https://www.oreilly.com/programming/free/files/a-whirlwind-tour-of-python.pdf'
    filename = url.split('/')[-1]

    resp = http.request(
            'GET',
            url,
            preload_content=False)

    print(f"Downloading File : {filename}")
    with open(filename, 'wb') as f:
        for chunk in resp.stream(1024):
            f.write(chunk)

    resp.release_conn()

    print("File downloaded.")

if __name__ == "__main__":
    main()
```
Output :
```shell
Downloading File : a-whirlwind-tour-of-python.pdf
File downloaded.
```
At the above code :

* In http.request() method `preload_content=False` is added, which enable the streaming.
* The for loop
```python
for chunk in resp.stream(1024):
	f.write(chunk)
```
will iterate over the chunks of data and save them to a file.

* The `resp.release_conn()` will release the connection.

**requests module :**

In python the requests module is the most easient and user-freiendly way to send requests to a web server. It also takes care of connection pulling by itself.

Methods used within the requests library are :

* requests.get(url) : send GET request to the url.
* requests.post(url) : send post request to the url.
* requests.status_code : return status code.
* requests.encoding : to check encoding property.
* requests.url : return url.
* requests.text : shows the response body if it is text (parse it as unicode).
* requests.content : show the reponse body.
* requests.headers : show all the headers as key->value pairs.
* requests.cookies : show all cookies.
* requests.headers['field_name'] : Returns individual header field.

We can also use different HTTP request methods, Some of them are :

* request.put(url)
* request.delete(url)
* request.delete(url)
* request.head(url)
* request.options(url)

**Sending GET requests :**

```shell
>>> url =  "http://requestbin.net/r/y6kqbay6"
>>> re = requests.get(url)
>>> re.status_code
200
>>> re.encoding
'utf-8'
>>> re.url
'http://requestbin.net/r/y6kqbay6'
>>> re.content
b'ip:122.170.210.49\n'
>>> re.text
'ip:122.170.210.49\n'
>>> for key in re.headers:
...     print(f"{key} : {re.headers[key]}")
...
Date : Fri, 10 Apr 2020 05:35:18 GMT
Content-Type : text/html; charset=utf-8
Transfer-Encoding : chunked
Connection : keep-alive
Set-Cookie : __cfduid=d33a1b8f72a9104a755a54b012ea6929e1586496918; expires=Sun, 10-May-20 05:35:18 GMT; path=/; domain=.requestbin.net; HttpOnly; SameSite=Lax
Sponsored-By : http://requestbin.net
Via : 1.1 vegur
CF-Cache-Status : DYNAMIC
Server : cloudflare
CF-RAY : 581a1a8ddc57eaf4-LAX
Content-Encoding : gzip
>>> re.cookies
<RequestsCookieJar[Cookie(version=0, name='__cfduid', value='d33a1b8f72a9104a755a54b012ea6929e1586496918', port=None, port_specified=False, domain='.requestbin.net',
domain_specified=True, domain_initial_dot=True, path='/', path_specified=True, secure=False, expires=1589088918, discard=False, comment=None, comment_url=None,
rest={'HttpOnly': None, 'SameSite': 'Lax'}, rfc2109=False)]>
```
Sending GET requests with parameters :
```python
#!/usr/bin/python3

import requests

def main():
    url = "http://requestbin.net/r/y6kqbay6"
    data = {'name' : 'Frank Castle', 'Age' : '40', 'Occupation' : 'Self-Employed'}
    re = requests.get(url, params=data)
    print(f"Status Code : {re.status_code}")

if __name__ == "__main__":
    main()
```
Output :
```shell
Status Code : 200
```

**Sending POST requests :**
```python
#!/usr/bin/python3

import requests

def main():
    url = "http://requestbin.net/r/y6kqbay6"
    data = {'name' : 'Frank Castle', 'Age' : '40', 'Occupation' : 'Self-Employed'}
    re = requests.post(url, data)
    print(f"Status Code : {re.status_code}")

if __name__ == "__main__":
    main()
```
Output :
```shell
Status Code : 200
```
**Sending Custom Headers :**
```python
#!/usr/bin/python3

import requests

def main():
    url = "http://requestbin.net/r/y6kqbay6"
    headers = {'Custom1': 'test101', 'Custom2': 'exploit', 'Custom3': 'hello_world'}
    re = requests.post(url, headers=headers)
    print(f"Status Code : {re.status_code}")

if __name__ == "__main__":
    main()
```
**Sending Custom Cookies :**
```python
#!/usr/bin/python3

import requests

def main():
    url = "http://requestbin.net/r/y6kqbay6"
    cookies = {'Custom1': 'test101', 'Custom2': 'exploit', 'Custom3': 'hello_world'}
    re = requests.post(url, cookies=cookies)
    print(f"Status Code : {re.status_code}")

if __name__ == "__main__":
    main()
```

**Session Objects :**
```python
#!/usr/bin/python3

import requests

def main():
    session1 = requests.Session()
    re1 = session1.get('http://facebook.com/somepage')
    re2 = session1.get('http://facebook.com/someotherpage')
    '''
        The above two requests maintan the same session
    '''

if __name__ == "__main__":
    main()
```

**Setting Timeouts :**

```python
#!/usr/bin/python3

import requests

def main():
    re = requests.get('http://sec-art.net/', timeout=3)
    re =  requests.get('http://sec-art.net/', timeout=0.001)

if __name__ == "__main__":
    main()
```

#### SocketServer Module :

Python also has `socketserver` module, It is basically a framework for network server. socketserver module makes using the low-level socket functions easier.


**Steps to create a network server :**

1. Create a request handler by sub classing the BaseRequestHandler and overriding the handle(), setup(), finish() methods.

* setup() - Prepare the request handler for the request.
* handle() - Do the real work for the request. Parse the incoming request, process the data, and send a response.
* finish() - Clean up anything created during setup().

```python
import SocketServer

class MySrvHandler(SocketServer.baseRequestHandler):

	def handle(self):
		self.data = self.request.recv(1024).strip()
		print(f"Data Received: {self.data}")
		self.request.sendall(self.data)
```

2. Create an instance of a BaseServer subclass by passing the network address, port number and the request handler class.
```python
host = "localhost"
port = 8080
# instantiate the server, and bind to localhost on port 8080
server = SocketServer.TCPServer((host, port), MySerHandler)
```
3. Call the methods handle_request() or serve_forever() on the server instance to process one or more number of requests from the clients.
```python
# activate the server
server.serve_forever()
```
Example :

```python
#!/usr/bin/python3

import logging
import socketserver

logging.basicConfig(level=logging.DEBUG,format='[$] %(message)s')

class MySrvHandler(socketserver.BaseRequestHandler):
    # the RequestHandler class for our server

    def setup(self):
        logging.debug('setup')

    def handle(self):
        logging.debug('handle')
        ip,port = self.client_address
        logging.debug('client %s:%d' % (ip,port))
        self.data = self.request.recv(1024).strip()
        print(f"[$] Data Received: {self.data}")
        self.request.sendall(self.data)

    def finish(self):
        logging.debug('finish')


def main():
    host = "localhost"
    port = 8080
    # instantiate the server, and bind to localhost on port 8080
    logging.debug("Server started..")
    server = socketserver.TCPServer((host, port), MySrvHandler)
    # activate the server
    server.serve_forever()


if __name__ == "__main__":
    main()
```
Output :
```shell
[$] Server started..
[$] setup
[$] handle
[$] client 127.0.0.1:56060
[$] Data Received: b'hello world'
[$] finish
[$] setup
[$] handle
[$] client 127.0.0.1:56062
[$] Data Received: b'this is another message'
[$] finish
```

**Multi-Threaed Server Example :**
```python
#!/usr/bin/python3

import logging
import socketserver

logging.basicConfig(level=logging.DEBUG,format='[$] %(message)s')

class MySrvHandler(socketserver.BaseRequestHandler):
    # the RequestHandler class for our server

    def handle(self):
        logging.debug('handle')
        ip,port = self.client_address
        logging.debug('client %s:%d' % (ip,port))
        self.data = self.request.recv(1024).strip()
        print(f"[$] Data Received: {self.data}")
        self.request.sendall(self.data)

class ThreadedTCPServer(socketserver.ThreadingMixIn, socketserver.TCPServer):
    pass


def main():
    host = "localhost"
    port = 8080
    # instantiate the server, and bind to localhost on port 8080
    logging.debug("Server started..")
    server = ThreadedTCPServer((host, port), MySrvHandler)
    # activate the server
    server.serve_forever()

if __name__ == "__main__":
    main()
```
Output :
```shell
[$] Server started..
[$] handle
[$] client 127.0.0.1:56840
[$] Data Received: b'hello'
[$] handle
[$] client 127.0.0.1:56842
[$] Data Received: b'hello again'
```

For more detailed example, visit below links :

https://pymotw.com/2/SocketServer/
https://bip.weizmann.ac.il/course/python/PyMOTW/PyMOTW/docs/SocketServer/index.html


Logging module Creating Generating logs in python
-------------------------------------------------

The logging module is used to log events at runtime for various perposes like debugging, logging, warning, error or critical. IN logging module there are 5 standard levels indicating the severity of events. Each has a corresponding method that can be used to log events at that level of severity. The defined levels, in order of increasing severity, are the following:

* DEBUG
* INFO
* WARNING
* ERROR
* CRITICAL

The logging module by default a "logger" to log messages/events without any configuration. Example :
```python
#!/usr/bin/python3

import logging

def main():
    logging.debug("This is debug message")
    logging.info("This is info message")
    logging.warning("This is warning message")
    logging.error("This is error message")
    logging.critical("This is critical message")

if __name__ == "__main__":
    main()
```
Output :
```shell
WARNING:root:This is warning message
ERROR:root:This is error message
CRITICAL:root:This is critical message
```
The output shows the severity level before each message along with root, which is the name the logging module gives to its default logger. This format, which shows the level, name, and message separated by a colon (:), is the default output format that can be configured to include things like timestamp, line number and other details.

Also note that the debug() and info() messages didnΓÇÖt get logged. This is because, by default, the logging module logs the messages with a severity level of WARNING or above. You can change that by configuring the logging module to log events of all levels if you want. You can also define your own severity levels by changing configurations.


**Basic COnfiguration :**


This is done by using `basicConfig()` method. The commonly used parameters are as follows :

* level : The root logger will be set to the specified severity level. Means we can set what level of log messages you want to record.

```python
#!/usr/bin/python3

import logging

logging.basicConfig(level=logging.INFO)

def main():
    logging.debug("This is debug message")
    logging.info("This is info message")
    logging.warning("This is warning message")
    logging.error("This is error message")
    logging.critical("This is critical message")


if __name__ == "__main__":
    main()
```
Output :
```shell
INFO:root:This is info message
WARNING:root:This is warning message
ERROR:root:This is error message
CRITICAL:root:This is critical message
```
As we can see that the message from loggin.info() is also printed on the console. another example :
```python
#!/usr/bin/python3

import logging

logging.basicConfig(level=logging.DEBUG)

def main():
    logging.debug("This is debug message")
    logging.info("This is info message")
    logging.warning("This is warning message")
    logging.error("This is error message")
    logging.critical("This is critical message")


if __name__ == "__main__":
    main()
```
Output :
```shell
DEBUG:root:This is debug message
INFO:root:This is info message
WARNING:root:This is warning message
ERROR:root:This is error message
CRITICAL:root:This is critical message
```
By the way default is Warning (if we do not set basicConfig()).

* format : This is the format of the log message. Example :

```python
#!/usr/bin/python3

import logging

logging.basicConfig(level=logging.DEBUG, format='%(name)s - %(levelname)s - %(message)s')

def main():
    logging.debug("This is debug message")
    logging.info("This is info message")
    logging.warning("This is warning message")
    logging.error("This is error message")
    logging.critical("This is critical message")


if __name__ == "__main__":
    main()
```
Output :
```shell
root - DEBUG - This is debug message
root - INFO - This is info message
root - WARNING - This is warning message
root - ERROR - This is error message
root - CRITICAL - This is critical message
```

To log the event inside a file instead of console, we can use below methods.

* filename : This specifies the file.
* filemode : If filename is given, the file is opened in this mode. The default is `a`, means append mode.

```python
i#!/usr/bin/python3

import logging

logging.basicConfig(filename='app.log', filemode='w')  # w : write mode

def main():
    logging.warning("This is warning message")
    logging.error("This is error message")
    logging.critical("This is critical message")

if __name__ == "__main__":
    main()
```
Output :
```shell
$ python test.py
$ cat app.log

WARNING:root:This is warning message
ERROR:root:This is error message
CRITICAL:root:This is critical message
```

Also note that calling basicConfig() to configure the root logger works only if the root logger has not been configured before. Basically, this function can only be called once. debug(), info(), warning(), error(), and critical() also call basicConfig() without arguments automatically if it has not been called before. This means that after the first time one of the above functions is called, you can no longer configure the root logger because they would have called the basicConfig() function internally.

**Output Formatting :**

Some other variable along with the `name`, `levelname` and `message` is `process` which is gives the process id. and also note that with process we have to use `d` (for decimal value) `%(process)d`, example :
```python
#!/usr/bin/python3

import logging

logging.basicConfig(format='%(process)d-%(levelname)s : %(message)s')

def main():
    logging.warning("This is warning message")

if __name__ == "__main__":
    main()
```
Output :
```shell
23726-WARNING : This is warning message
```
The all other variables can be found here :  https://docs.python.org/3/library/logging.html#logrecord-attributes
Another example with current time-stamp :
```python
#!/usr/bin/python3

import logging

logging.basicConfig(format='%(levelname)s[%(asctime)s]: %(message)s')

def main():
    logging.warning("This is warning message")

if __name__ == "__main__":
    main()
```
Output :
```shell
WARNING[2020-04-11 02:26:31,141]: This is warning message
```

**Logging Variable Data :**

It can be done by simply using `%s` or `%d` or f-string options. Example :
```python
#!/usr/bin/python3

import logging

name = "Ajay"
id = 1234

def main():
    logging.warning("( %s : %d ) - This is warning message", name, id)
    # or
    logging.error(f"( {name} : {id} ) - This is warning message")
if __name__ == "__main__":
    main()
```
Output :
```shell
WARNING:root:( Ajay : 1234 ) - This is warning message
ERROR:root:( Ajay : 1234 ) - This is warning message
```

**Logging classs and functions :**

By default there is a default logger `root`. We can also create our own logger by creating an object of logger class, it is very very useful when you have multiple modules in your application. Some of the important classes defined in logging module is as follows :

* **LogRecord :**  Loggers automatically create LogRecord objects that have all the information related to the event being logged, like the name of the logger, the function, the line number, the message, and more. It basically contains all the information related to logs.
* **Logger** : The object of logger class is used to directly call all the functions.
* **Handler** : Handlers send the LogRecord to the required output destination, like the console or a file. Handler is a base for subclasses like StreamHandler (for writing onto console), FileHandler (for writing onto file), SMTPHandler(sending logs through mail), HTTPHandler (sending logs through http), and more. These subclasses send the logging outputs to corresponding destinations, like sys.stdout or a disk file.
* **Formatter :** By using this class we can specify the format of output.

**Creating a custom logger :**

Method :
```python
logging.getLogger(name)
```
Example :
```python
#!/usr/bin/python3

import logging

logger = logging.getLogger("myLogger");

def main():
    logger.warning("This is warning message")
    logger.critical("This is critical message")


if __name__ == "__main__":
    main()
```
Output :
```shell
This is warning message
This is critical message
```
With custom formatter :
```python
#!/usr/bin/python

import logging

# create new logger
logger = logging.getLogger("myLogger")

# create handler
c_handler = logging.StreamHandler()

# create formatter
c_format = logging.Formatter("%(name)s : %(levelname)s - %(message)s")

# adding the formatter to the handler
c_handler.setFormatter(c_format)

# add handler to the logger
logger.addHandler(c_handler)


def main():
    logger.warning("This is warning message")
    logger.critical("This is critical message")


if __name__ == "__main__":
    main()
```
Output :
```shell
myLogger : WARNING - This is warning message
myLogger : CRITICAL - This is critical message
```
At above program, first we create :

* a custom logger : `logger = logging.getLogger("myLogger")`
* then create a new handle : `c_handler = logging.StreamHandler()`
* create a formatter for that handler : `c_format = logging.Formatter("%(name)s : %(levelname)s - %(message)s")`
* add format to that handler : `c_handler.setFormatter(c_format)`
* add handler to the logger : `logger.addHandler(c_handler)`

Now lets see another example with multiple handlers
```python
#!/usr/bin/python

import logging

# create new logger
logger = logging.getLogger("myLogger")

# create handlers
c_handler = logging.StreamHandler()   # for writing to console
f_handler = logging.FileHandler('app.log')     # for writing to a file

# setting different levels for both handlers
c_handler.setLevel(logging.WARNING)
f_handler.setLevel(logging.ERROR)
# c_handler handles the logs starting from WARNING to CRITICAL level
# f_handler handles the logs starting from ERROR to CRITICAL level

# create formatter
c_format = logging.Formatter("%(name)s : %(levelname)s - %(message)s")
f_format = logging.Formatter("%(asctime)s : %(name)s : %(levelname)s - %(message)s")

# adding the formatter to the handler
c_handler.setFormatter(c_format)
f_handler.setFormatter(f_format)


# add handler to the logger
logger.addHandler(c_handler)
logger.addHandler(f_handler)


def main():
    logger.warning("This is warning message")
    logger.error("This is error message")
    logger.critical("This is critical message")


if __name__ == "__main__":
    main()
```
Output :
```shell
myLogger : WARNING - This is warning message
myLogger : ERROR - This is error message
myLogger : CRITICAL - This is critical message
```
As we can see that there are three logs printed on the console, but in app.log file there is only two logs :
```shell
2020-04-12 19:03:35,798 : myLogger : ERROR - This is error message
2020-04-12 19:03:35,798 : myLogger : CRITICAL - This is critical message
```

**Using loggers within module based applications :**

When creating a new logger, we can use `__name__` variable which basically a built-in variable which evaluates to the name of the current module/script. Now in the next example we are going to use `__name__` within different modules.

**File : logconfig.py**
```python
import logging

c_handler = logging.StreamHandler()   									# create handler
c_format = logging.Formatter("%(name)s : %(levelname)s - %(message)s")  # create formatter
c_handler.setFormatter(c_format)                                        # adding the formatter to the handler
```
After importing this module we just have to create new logger and then add handler to the logger.
```python
logger = logging.getLogger(__name__)
logger.addHandler(c_handler)
```
**File: testmod1.py**
```python
from logconfig import *

# creating logger and add handler
logger = logging.getLogger(__name__)
logger.addHandler(c_handler)

def func1():
    logger.warning("this is warning message")

def func2():
    logger.critical("this is critical message")
```
**File: testmod2.py**
```python
from logconfig import *

# creating logger and add handler
logger = logging.getLogger(__name__)
logger.addHandler(c_handler)


def func1():
    logger.warning("this is warning message")

def func2():
    logger.critical("this is critical message")

def func3():
    logger.error("this is error message")
```
**File : main.py**
```python
#!/usr/bin/python3

from logconfig import *
import testmod1
import testmod2

# creating logger and add handler
logger = logging.getLogger(__name__)
logger.addHandler(c_handler)

def main():
    logger.warning("this is warning message")
    testmod1.func1()
    testmod2.func1()
    testmod1.func2()
    testmod2.func3()

if __name__ == "__main__":
    main()
```
Output :
```shell
$ python main.py

__main__ : WARNING - this is warning message
testmod1 : WARNING - this is warning message
testmod2 : WARNING - this is warning message
testmod1 : CRITICAL - this is critical message
testmod2 : ERROR - this is error message
```
The first part shows the module/script from which log is generated.

Writing plugins in Python
-------------------------

A plugin/module is a software component that adds a specific feature to an existing computer program. When a program supports plug-ins, it enables customization. The main benifits of plugins/modules are :
* New features are easier to develop and add
* Easy inhances the capability of existing application
* Application becomes smaller and easier to understand

**Sample Application Flow :**

We have a program that starts, does a few things, and exits.

![](appflow1.png)   

**Plugin Architecture Workflow :**

Now the business logic is refectored into plugin framework that can run registered plugins. The plugins will need to meet the specifications defined by our framework in order to run.

![](appflow2.png)   


**Example :**

Lets say we have an application with plugin framework, and the structure of application is :
```
Application
 |
 |----> internal.py   // contains internal business logic
 |
 |----> external.py   // contains user-created plugins
 |
 |----> main.py       // initialize and run application
```

**File: internal.py**
```python
# internal.py

# this is the in-built plugin/modules
# which runs every time automatically
# when the program execute
class InBuiltPlugin:
    def process(self):
        print("This is inbuilt Plugin")

# MyApplication class which runs all the
# plugins/modules (internal or application)
class MyApplication:
    def __init__(self, *, plugins: list=list()):
        self.internal_modules = [InBuiltPlugin()] # this line automatically
                                                 # imports the Builtin modules
        self._plugins = plugins

    def run(self):
        print("Starting program")
        print("-" * 79)

        modules_to_execute = self.internal_modules + self._plugins
        for modules in modules_to_execute:
            modules.process()

        print("-" * 79)
        print("Program done")
```
There is a class `InBuiltPlugin`, which is only inbuild plugin within the application.

**File : external.py**
```python
# These are used-defined/developed plugins

class UDPlugin1:
    def process(self):
        print("This is user-defined plugin1")

class UDPlugin2:
    def process(self):
        print("This is user-defined plugin2")
```
There are two external module UDPlugin1 and UDPlugin2.

**File : main.py**
```python
#!/usr/bin/python3

from internal import MyApplication
from external import UDPlugin1, UDPlugin2

def main():
    # at Here we have to pass all the external plugins
    # as list(), which are get executed by the MyApplication class
    app = MyApplication(plugins=[UDPlugin1(), UDPlugin2()])
    app.run()


if __name__ == "__main__":
    main()
```
At here we are creating object of MyApplication() and also passing external modules objects ([UDPlugin1(), UDPlugin2()]) as a list of objects. Now these are get executed by the run() method by Application class. The Output is :
```shell
$ python main.py

Starting program
-------------------------------------------------------------------------------
This is inbuilt Plugin
This is user-defined plugin1
This is user-defined plugin2
-------------------------------------------------------------------------------
Program done
```
Now running only one external plugin :
 ```python
#!/usr/bin/python3
# main.py

from internal import MyApplication
from external import UDPlugin1, UDPlugin2

def main():
    # at Here we have to pass all the external plugins
    # as list(), which are get executed by the MyApplication class
    app = MyApplication(plugins=[UDPlugin1()])
    app.run()


if __name__ == "__main__":
    main()
```
Output :
```shell
Starting program
-------------------------------------------------------------------------------
This is inbuilt Plugin
This is user-defined plugin1
-------------------------------------------------------------------------------
Program done
```
Running only internal plugin :
```python
#!/usr/bin/python3
# main.py

from internal import MyApplication
from external import UDPlugin1, UDPlugin2

def main():
    # at Here we have to pass all the external plugins
    # as list(), which are get executed by the MyApplication class
    app = MyApplication(plugins=[])  # we pass an empty list
    app.run()


if __name__ == "__main__":
    main()
```
Output :
```shell
Starting program
-------------------------------------------------------------------------------
This is inbuilt Plugin
-------------------------------------------------------------------------------
Program done
```
Now lets try to run more then one internal plugins, for this we have to add more internal plugin on internal.py and main.py will be same as above
```python
# internal.py

# this is the in-built plugin/modules
# which runs every time automatically
# when the program execute
class InBuiltPlugin1:
    def process(self):
        print("This is inbuilt Plugin1")

class InBuiltPlugin2:
    def process(self):
        print("This is inbuilt Plugin2")

# MyApplication class which runs all the
# plugins/modules (internal or application)
class MyApplication:
    def __init__(self, *, plugins: list=list()):
        # this line automatically imports the Builtin modules
        self.internal_modules = [InBuiltPlugin1(), InBuiltPlugin2()]
        self._plugins = plugins

    def run(self):
        print("Starting program")
        print("-" * 79)

        modules_to_execute = self.internal_modules + self._plugins
        for modules in modules_to_execute:
            modules.process()

        print("-" * 79)
        print("Program done")
```
Output :
```shell
Starting program
-------------------------------------------------------------------------------
This is inbuilt Plugin1
This is inbuilt Plugin2
-------------------------------------------------------------------------------
Program done
```

**Note** : This post is based on below article :

https://alysivji.github.io/simple-plugin-system.html

Basic CGI Programming
---------------------

http://www.sec-art.net/2016/08/simple-python-web-server-with-cgi.html


# Advance Topics :

Task Automation with Python
---------------------------

Exploit Development techniques
------------------------------

Exploit analysis Automation Process
-----------------------------------

Debugging basics
----------------

Python Tips and Tricks
----------------------

### Passing n number or arguments to a method as list(), dict() in python  :

Example :
```python
#!/usr/bin/python3

def TestFunc(*, items: list=list()):
    for i in items:
        print(i)

def main():
    TestFunc(items=["Apple", "Banana", "Mango", "Papaya", "Graps"])


if __name__ == "__main__":
    main()
```
Output :
```shell
Apple
Banana
Mango
Papaya
Graps
```
At the above example the `*` asterisk is used, which represents the N number of arguments and after that `items: list=list()` function is used which stores provided list as arguments into the the called function's list. Also note that the callie's list name must be match with the called functions list name (at the receiving end), which is in this case `items`.

Example of Dictonary :
```python
#!/usr/bin/python3

def TestFunc(*, Dlist: dict=dict()):
    for x,y in Dlist.items():
        print(x,":",y)

def main():
    TestFunc(Dlist={"Name":"John Doe", "Age":26, "Sex":"Male", "Occupation":"IT-Security"})


if __name__ == "__main__":
    main()
```
Output :
```shell
Name : John Doe
Age : 26
Sex : Male
Occupation : IT-Security
```

Implementation with class :
```python
#!/usr/bin/python3

class ListClass:
    def __init__(self, *, items: list=list()):
        for i in items:
            print(i)

class DictClass:
    def __init__(self, *, Dlist: dict=dict()):
        for x,y in Dlist.items():
            print(x, ":", y)


def main():
    ob1 = ListClass(items=["Apple", "Banana", "Papaya", "Graps", "Mango"])
    print()
    ob2 = DictClass(Dlist={"Name":"John Doe", "Age":26, "Sex":"Male", "Occupation":"IT-Security"})


if __name__ == "__main__":
    main()
```
Output :
```shell
Apple
Banana
Papaya
Graps
Mango

Name : John Doe
Age : 26
Sex : Male
Occupation : IT-Security
```

###  One-Liner code for reading from a file and store the lines of text in a list

The code example will read strings from a file and store it on list

```python
var = [line.strip() for line in open("file_name")]
```

Example

```python
Strv = [line.strip() for line in open("file.txt")]

print(Strv)
['This is 1st line', 'This is 2nd line', 'This is 3rd line', 'This is 4th line']
```

### Make graceful exit from a running multithreaded program using signal module

We can do this by using python's signal module, it provides all the basic signal handling operations in python.

> Note : A signal is like a notification of an event. When an event occurs in the system, a signal is generated to notify other programs about the event. For example, if you press Ctrl + c, a signal called SIGINT is generated and any program can actually be aware of that incident by simply reading that signal. Signals are identified by integers.

In signal module we have to create a signal handler which basically triggerd when the signal is received, and in this handler function we can do anything like showing the message and exiting the program etc. And also we have to define two arguments of handler function, aignal and frame value (integer).

**Steps :**

* define a handler function

```python
def sig_handler(signum, frame):
    print("You pressed Ctrl + C")
    some_cleanup_function()
    sys.exit(0)
```

* Assign the handler function to the signal SIGINT

```python
signal.signal(signal.SIGINT, handler)
```

Here is the full example :

```python
#!/usr/bin/python3

import signal
import time
import sys

# define the handler
def sig_handler(signum, frame):
    print("You Pressed Ctrl + C")
    sys.exit(0)

def main():
    # assign the sig_handler to the signal SIGINT
    signal.signal(signal.SIGINT, sig_handler)

    print("Press Ctrl + C")  # wait for SIGINT
    time.sleep(20)

if __name__ == "__main__":
    main()
```

Another example of graceful example without handler method definition in multitheaded environment.

Syntax :

```python
sigint_handler = signal.signal(signal.SIGINT, signal.SIG_IGN)
signal.signal(signal.SIGINT, signit_handler)
try:
    // your main code
except KeyboardInterrupt:
    pass
```

Example :

```python
#!/usr/bin/python3

import signal
import time

def main():
    sigint_handler = signal.signal(signal.SIGINT, signal.SIG_IGN)
    signal.signal(signal.SIGINT, sigint_handler)
    try:
        print("Press Ctrl + C")  # wait for SIGINT
        time.sleep(20)
    except KeyboardInterrupt:
        pass

if __name__ == "__main__":
    main()
```

