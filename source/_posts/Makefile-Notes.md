title: Makefile Notes
date: 2016-04-17 22:29:58
tags: [Makefile]
categories: Coding
---
本文主要参考和整理自[这个网页](https://www.cs.umd.edu/class/fall2002/cmsc214/Tutorial/makefile.html)，总结了Makefile的语法和常见错误。  

### Compile into object file  

``` Makefile
g++ -Wall -c MovieTheater.cpp  
cxx -w0 -std strict_ansi -c MovieTheater.cpp  
```  
`-Wall` option generates many warning message. It is a good practice to always use this option.  

Note: When you compile using -c option, it only compiles the single .cpp file being referred to, and only refers to the .h for purposes of creating objects (making sure it's the correct size, etc) and checking that methods are being called correctly.  

### Makefile  
A makefile is based on a very simple concept. A makefile typically consists of many entries. Each entry has:  

* a target (usually a file, but not always)  
* the dependencies (files which the target depends on)  
* and commands to run, based on the target and dependencies.  

<!--more-->  
Basic syntax:  

``` bash  
<target>: [ <dependency > ]*  
   [ <TAB> <command> <endl> ]+  
 ```  

**The target is reconstructed if the time-stamp of its dependencies are more recent than the target. The updates are done recursively.**  

Example:  
``` Makefile  
Movie.o: Movie.cpp Movie.h Vector.h  
   g++ -Wall -c Movie.cpp  
```  

The target is Movie.o. The dependencies of a .o file are almost always:  

* The corresponding .cpp file.    
* The corresponding .h file.    
* Other .h files    

Dependency Transition  
`Vector.h: Foo.h`  
In this case, you don't need any targets. It just allows the compiler to determine that a dependency exists (dependencies are transitive, that is, if X is dependent on Y, and Y is dependent on Z, then X is dependent on Z).  

#### Commands  
All commands must immediately follow the dependency line, and the first character must be a **TAB**.  
(程序员背锅)  

#### Executable as targets  
Usually, the purpose of the makefile is to create an executable. That target is often the first target in the makefile. For example,    
``` Makefile  
p1 : MovieList.o Movie.o NameList.o Name.o Iterator.o  
    g++ -Wall MoveList.o Movie.o NameList.o Name.o Iterator.o -o p1  
```  

#### Macro definitions for flexibility  

You should use macros. You SHOULD use macros! YOU SHOULD USE MACROS!!!  
Use MACROS!!!! (We mean it). Just like what we do in C when we define a constant to avoid search and replace.  


##### Syntax  

`<macro_name> = <macro_string>`  
Convention:  
On the left hand side, ONLY USE UPPER CASE LETTERS AND UNDERSCORES  

Use backslash and an immediate return to indicates that the next line is a continuation of current line.  

* CC The name of the compiler  

* DEBUG The debugging flag. This is -g in both g++ and cxx. The purpose of the flag is to include debugging information into the executable, so that you can use utilities such as gdb to debug the code.  

* LFLAGS The flags used in linking. As it turns out, you don't need any special flags for linking. The option listed is "-Wall" which tells the compiler to print all warnings. But that's fine. We can use that.  

* CFLAGS The flags used in compiling and creating object files. This includes both "-Wall" and "-c". The "-c" option is needed to create object files, i.e. .o files.  

##### Example  
``` Makefile  
OBJS = MovieList.o Movie.o NameList.o Name.o Iterator.o  
CC = g++  
DEBUG = -g  
CFLAGS = -Wall -c $(DEBUG)  
LFLAGS = -Wall $(DEBUG)  

p1 : $(OBJS)  
    $(CC) $(LFLAGS) $(OBJS) -o p1  
```  

#### Dummy targets  

##### make clean  
``` Makefile  
clean:  
     \rm *.o *~ p1  
```  

##### make tar  
``` Makefile  
tar:  
     tar cfv p1.tar Movie.h Movie.cpp Name.h Name.cpp NameList.h \  
             NameList.cpp  Iterator.cpp Iterator.h  
```  

#### make all  
``` Makefile  
all: p1 p2 p3  

p1: Foo.o main1.o  
   g++ -Wall Foo.o main1.o -o p1  

p2: Bar.o main2.o  
   g++ -Wall Bar.o main2.o -o p2  

p3: Baz.o main3.o  
   g++ -Wall Baz.o main3.o -o p3  
```  
>Notice that the all target has no command lines. However, it lists the dependencies as p1, p2, and p3. It attempts to build the most up-to-date versions of those files (if they are up-to-date, it will let you know). Having an all target is convenient for making a bunch of executables, without having to type make on each target separately.  

### Common Errors  
1. Failing to put a TAB at the beginning of commands.    
2. Put a TAB at the beginning of a blank line.  

### Full Example  
``` Makefile  
OBJS = MovieList.o Movie.o NameList.o Name.o Iterator.o  
CC = g++  
DEBUG = -g  
CFLAGS = -Wall -c $(DEBUG)  
LFLAGS = -Wall $(DEBUG)  

p1 : $(OBJS)  
    $(CC) $(LFLAGS) $(OBJS) -o p1  

MovieList.o : MovieList.h MovieList.cpp Movie.h NameList.h Name.h Iterator.h  
    $(CC) $(CFLAGS) MovieList.cpp  

Movie.o : Movie.h Movie.cpp NameList.h Name.h  
    $(CC) $(CFLAGS) Movie.cpp  

NameList.o : NameList.h NameList.cpp Name.h  
    $(CC) $(CFLAGS) NameList.cpp  

Name.o : Name.h Name.cpp  
    $(CC) $(CFLAGS) Name.cpp  

Iterator.o : Iterator.h Iterator.cpp MovieList.h  
    $(CC) $(CFLAGS) Iterator.cpp  

clean:  
    \rm *.o *~ p1  

tar:  
    tar cfv p1.tar Movie.h Movie.cpp Name.h Name.cpp NameList.h \  
            NameList.cpp  Iterator.cpp Iterator.h  
```  
