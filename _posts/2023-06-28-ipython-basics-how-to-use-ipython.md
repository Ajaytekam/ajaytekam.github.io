---   
title: How to use IPYTHON for Python Development  
author: ajaytekam   
date: 2023-06-28 03:30:00 +0530   
img_path: /assets/posts/20230628/   
categories: [DevOps, Python, Development]    
tags: [python, ipython]  
image:
  path: ipython.png 
  alt: IPYTHON for Python Development 
---    

## Contents   

- [Introduction](#introduction)   
- [Installing IPython](#installing-ipython)   
- [Basic Usage](#basic-usage)    
- [Running simple python commands](#running-simple-python-commands)     
- [Running shell command](#running-shell-command)   
- [Tab completion](#tab-completion)    
- [Help](#help)   
- [Magic commands](#magic-commands)    

---------------

## Introduction   

IPython is an alternative Python interpreter. It is an interactive shell used for computing in Python. It provides many more useful features over the more popular default Python interpreter, such as :   

* Syntax highlighting  
* Proper indentation  
* Documentation  
* Run native shell commands  
* Tab completion    
* Pasting blocks on code  

## Installing ipython

with `pip`  

```shell  
pip install ipython  
```  

with `easy_install`   

```shell  
sudo easy_install ipython  
```  

## Basic Usage 

To start the interpreter use `ipython` command   

```shell   
Python 3.6.9 (default, Oct  8 2020, 12:12:24)
Type 'copyright', 'credits' or 'license' for more information
IPython 7.16.1 -- An enhanced Interactive Python. Type '?' for help.

In [1]:
```   

## Running simple python commands

```shell   
In [1]: print("Hello world")
Hello world

In [2]: def printM():
   ...:     name = input()
   ...:     print("Hello %s" % (name))
   ...:

In [3]: printM()
Ajay kumar
Hello Ajay kumar

In [4]:
```  

As we notice it shows a number at every command, statement we type, it represents the history of typed command. We can access it as lists 

```shell  
In [4]: print(In)

['', 'print("Hello world")', 'def printM():\n    name = input()\n    print("Hello %s" % (name))\n    ', 'printM()', 'print(In)']
```   

## Running shell command 

We can run file/directory related commands directly 

```shell   
In [41]: mkdir test

In [42]: cd test
/tmp/test

In [43]: pwd
Out[43]: '/tmp/test'

In [44]: ls -al
total 8
drwxrwxr-x  2 ajay ajay 4096 Dec  4 12:14 ./
drwxrwxrwt 17 root root 4096 Dec  4 12:14 ../
```   

But to run system level command we have to use `!` before command `!command`   

```shell  
In [46]: !whoami
ajay

In [47]: !users
ajay ajay ajay

In [48]: !ifconfig
enp4s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.5  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::a56a:c2f:fb1d:3658  prefixlen 64  scopeid 0x20<link>
        ether a0:1d:48:09:7e:2d  txqueuelen 1000  (Ethernet)
        RX packets 544743  bytes 732864159 (732.8 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 115585  bytes 13887018 (13.8 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0 
```   

## Tab completion 

Tab completion, especially for attributes, is a convenient way to explore the structure of any object you are dealing with. TO use this just press tab and it will show you all the options available with that object.  

![](sc1.png) 

and you can navigate through all the available options, then press return to select one.   

## Help 

To get help page for any command or objects we can use `object?` or `help(object)`  

```shell  

In [69]: print?
Docstring:
print(value, ..., sep=' ', end='\n', file=sys.stdout, flush=False)

Prints the values to a stream, or to sys.stdout by default.
Optional keyword arguments:
file:  a file-like object (stream); defaults to the current sys.stdout.
sep:   string inserted between values, default a space.
end:   string appended after the last value, default a newline.
flush: whether to forcibly flush the stream.
Type:      builtin_function_or_method

In [70]: len?
Signature: len(obj, /)
Docstring: Return the number of items in a container.
Type:      builtin_function_or_method

In [71]: str?
Init signature: str(self, /, *args, **kwargs)
Docstring:
str(object='') -> str
str(bytes_or_buffer[, encoding[, errors]]) -> str
```  

The `help` option shows all detailed information in a new buffer   

```shell  
help(str)  
```  

Some other examples  

```shell  
import os
os?
help(os)
```   

## Magic commands

Magic commands are ipython special commands which performs specified actions. These functions are called by using `%function_name`, but if the flag %automagic is set to on (which is default), then one can call magic commands without the preceding %. The `lsmagic` function lists all the magic functions     

```shell  
n [6]: lsmagic
Out[6]:
Available line magics:
%alias  %alias_magic  %autoawait  %autocall  %autoindent  %automagic  %bookmark  %cat  %cd  %clear  %colors  %conda  
%config  %cp  %cpaste %debug  %dhist  %dirs  %doctest_mode  %ed  %edit  %env  %gui  %hist  %history  %killbgscripts   
%ldir  %less  %lf  %lk  %ll  %load  %load_exi %loadpy  %logoff  %logon  %logstart  %logstate  %logstop  %ls  %lsmagic    
%lx  %macro  %magic  %man  %matplotlib  %mkdir  %more  %mv  %notebook  %page  %paste  %pastebin  %pdb  %pdef  %pdoc    
%pfile  %pinfo  %pinfo2  %pip  %popd  %pprint  %precision  %prun  %psearch  %psource  %pushd  %pwd  %pycat  %pylab    
%quickref  %recall  %rehashx  %reload_ext  %rep  %rerun  %reset  %reset_selective  %rm  %rmdir  %run  %save  %sc     
%set_env  %store  %sx  %system  %tb  %time  %timeit  %unalias  %unload_ext  %who  %who_ls  %whos  %xdel  %xmode

Available cell magics:
%%!  %%HTML  %%SVG  %%bash  %%capture  %%debug  %%file  %%html  %%javascript  %%js  %%latex  %%markdown  %%perl    
%%prun  %%pypy  %%python  %%python2  %%python3  %%ruby  %%script  %%sh  %%svg  %%sx  %%system  %%time  %%timeit  %%writefile

Automagic is ON, % prefix IS NOT needed for line magics.
```   

Now to get the mode details about magic functions use help options.   

```shell  
edit?
debug?
load?
run?
save?
```  

Some of the important magic functions are :  

**edit :** Open up editor where you can write code, `edit file.py`.    

**run :** Run the code written in a file, `run file.py`. Where `file.py` resides in current working directory.     

**load :** Load code from an external file, `load file.py`.    

**save :** Saves typed commands into a file.   

```shell   
n [1]: def printN(name):
  ...:     print(name)
  ...:

In [2]: name = input()
Ajay kumar

In [3]: printN(name)
Ajay kumar

In [4]: save file.py 1-3
The following commands were written to file `file.py`:
def printN(name):
    print(name)

    name = input()
    printN(name)
```   
