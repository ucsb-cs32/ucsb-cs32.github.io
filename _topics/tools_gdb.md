---
topic: "tools: gdb"
desc: "GNU debugger"
indent: true
---

This page may be required reading for any CS32 labs that pertain to gdb.

The following [gdb "cheat sheet" at this .pdf link](http://darkdust.net/files/GDB%20Cheat%20Sheet.pdf) may also be useful.

One way to debug a program is to read through your code and reason about your program, but sometimes it's easier to debug a program by running it and seeing how it behaves. That is where debuggers come in.

An object is an instance of a class. Similarly, a process is an instance of a program undergoing the execution.

Debuggers helps you debug a program by allowing you to stop a process at a particular line of code and inspect things such as objects and variables.

Debuggers depend on debug symbols to be compiled into the machine language versions of your program. Tell your C++ compiler to include debug symbols by adding the option -g to your CXXFLAGS

# A Simple Example 

Let's start with a trivial program.

{% highlight cpp linenos %}
#include <iostream>

int main(int argc, char *argv[])
{
  int a = 10;

  a = a + 1;
  std::cout << a << std::endl;

  return 0;
}
{% endhighlight %}

Now run your program in GDB.  Remember to compile it with the <code>-g</code> flag.

```
user@computer:~$ gdb ./program
GNU gdb (Ubuntu 7.7.1-0ubuntu5~14.04.2) 7.7.1
Copyright (C) 2014 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from ./program...done.
(gdb) 
```

The `(gdb)` is the gdb prompt. There are many commands that are supported by gdb and all of the command documentation can be accessed using the help command from the prompt. If you don't know how to complete a command try hitting tab twice for a list of valid completions of your command. Auto-complete is useful for function names.

To run the program use the `r` command. Alternatively, you can use `run`, but `r` is shorter.

```
(gdb) r
Starting program: /home/user/program 
11
[Inferior 1 (process 14747) exited normally]
(gdb)
```

If you haven't set any breakpoints gdb will run the program to completion and notify you that the process has ended.

Now lets set a breakpoint on the main function using the `b` command. When the main function is called the process will be halted and you will be able to issue a new command to gdb. Using the `n` command, next through the program line by line. Finally, use the `q` command to quit gdb.

```
(gdb) b main
Breakpoint 1 at 0x40083c: file main.cpp, line 5.
(gdb) r
Starting program: /home/user/program 

Breakpoint 1, main (argc=1, argv=0x7fffffffde38) at main.cpp:5
5	  int a = 10;
(gdb) n
7	  a = a + 1;
(gdb) n
8	  std::cout << a << std::endl;
(gdb) n
11
10	  return 0;
(gdb) n
11	}  
(gdb) n
__libc_start_main (main=0x40082d <main(int, char**)>, argc=1, 
    argv=0x7fffffffde38, init=<optimized out>, fini=<optimized out>, 
    rtld_fini=<optimized out>, stack_end=0x7fffffffde28) at libc-start.c:321
321	libc-start.c: No such file or directory.
(gdb) n
[Inferior 1 (process 16300) exited normally]
(gdb) q
```

You may notice that sometimes you will run into a part of your program where the source code in not available. Your main function is called by `__libc_start_main` function. This usually isn't important for debugging your program, but it's important to know that there is more going on in your program than you might think and gdb does not hide anything from you.

You can only step through lines of source code if you have the source code. The `-g` option you used when compiling told your c++ compiler to embed the line numbers and the source file names into the program. Don't change the source files when debugging because gdb will become confused when the program doesn't match the source. 

# Printing

gdb has some useful printing and evaluation features

```cpp
class Foo
{
 public:
  Foo(int a, int b, int c) :a(a),b(b),c(c) {}

 private:
  int a, b, c;
};

int main(int argc, char *argv[])
{
  Foo foo(1,2,3);

  return 0;
}
```

```
user@computer:~$ gdb ./program
GNU gdb (Ubuntu 7.7.1-0ubuntu5~14.04.2) 7.7.1
Copyright (C) 2014 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from ./program...done.
(gdb) b main
Breakpoint 1 at 0x4004fc: file main.cpp, line 12.
(gdb) r
Starting program: /home/user/program 

Breakpoint 1, main (argc=1, argv=0x7fffffffde38) at main.cpp:12
12	  Foo foo(1,2,3);
(gdb) n
14	  return 0;
(gdb) print foo
$1 = {a = 1, b = 2, c = 3}
(gdb) print foo.a
$2 = 1
(gdb) foo.a == 1
Undefined command: "foo".  Try "help".
(gdb) print foo.a == 1
$3 = true
(gdb) q
A debugging session is active.

	Inferior 1 [process 27951] will be killed.

Quit anyway? (y or n) y
```

# Navigating your program

This program uses recursion. We will use it in our next example.

```cpp
int recursive(int a)
{
  if(a>0)
    return recursive(a-1);
  return 0;
}

int main(int argc, char *argv[])
{
  int a = 10;

  recursive(a);

  return 0;
}
```

Some notes about working with recursive programs:

* The `next` command will not descend into a function. If you want to go into a function call you want to use `step`. 
* Each time you descend into a function a new entry will be added to your backtrace (this corresponds to an activation record being pushed on to the run-time stack)
* Every time you return from a function an entry will be removed from the backtrace (i.e. an activation record is popped from the stack)
* The `up` and `down` commands let you go up and down the backtrace without executing any lines of code.  These are useful for inspecting variables of functions that may have called another function.

By the way, on behalf of the entire CS32 teaching staff, thanks for actually reading this page.  In return, we want to provide you with this: the password is "banana".    That's the "password" in case we ask you: did you really actually read the [tools: gdb] page at the [ucsb-cs32.github.io](https://ucsb-cs32.github.io) webpage?  If we ask, you can say "yup!  Banana!".  And we'll know.  And, we'll be impressed.  Ok, back to a discussion of gdb.

The `s` command steps into a function and the `bt` command prints a backtrace. The `up` and `down` commands do not execute any additional lines of code. Notice we are setting a breakpoint by line number. Finally, the `c` command continues the program execution until the next breakpoint is reached or the program exits.

```
user@computer:~$ gdb ./program
GNU gdb (Ubuntu 7.7.1-0ubuntu5~14.04.2) 7.7.1
Copyright (C) 2014 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from ./program...done.
(gdb) b 12
Breakpoint 1 at 0x40052a: file main.cpp, line 12.
(gdb) r
Starting program: /home/user/program 

Breakpoint 1, main (argc=1, argv=0x7fffffffde38) at main.cpp:12
12	  recursive(a);
(gdb) s
recursive (a=10) at main.cpp:3
3	  if(a>0)
(gdb) s
4	    return recursive(a-1);
(gdb) s
recursive (a=9) at main.cpp:3
3	  if(a>0)
(gdb) s
4	    return recursive(a-1);
(gdb) s
recursive (a=8) at main.cpp:3
3	  if(a>0)
(gdb) s
4	    return recursive(a-1);
(gdb) s
recursive (a=7) at main.cpp:3
3	  if(a>0)
(gdb) s
4	    return recursive(a-1);
(gdb) bt
#0  recursive (a=7) at main.cpp:4
#1  0x000000000040050b in recursive (a=8) at main.cpp:4
#2  0x000000000040050b in recursive (a=9) at main.cpp:4
#3  0x000000000040050b in recursive (a=10) at main.cpp:4
#4  0x0000000000400534 in main (argc=1, argv=0x7fffffffde38) at main.cpp:12
(gdb) up 4
#4  0x0000000000400534 in main (argc=1, argv=0x7fffffffde38) at main.cpp:12
12	  recursive(a);
(gdb) p a
$1 = 10
(gdb) down 4
#0  recursive (a=7) at main.cpp:4
4	    return recursive(a-1);
(gdb) bt
#0  recursive (a=7) at main.cpp:4
#1  0x000000000040050b in recursive (a=8) at main.cpp:4
#2  0x000000000040050b in recursive (a=9) at main.cpp:4
#3  0x000000000040050b in recursive (a=10) at main.cpp:4
#4  0x0000000000400534 in main (argc=1, argv=0x7fffffffde38) at main.cpp:12
(gdb) n
6	}
(gdb) bt
#0  recursive (a=7) at main.cpp:6
#1  0x000000000040050b in recursive (a=8) at main.cpp:4
#2  0x000000000040050b in recursive (a=9) at main.cpp:4
#3  0x000000000040050b in recursive (a=10) at main.cpp:4
#4  0x0000000000400534 in main (argc=1, argv=0x7fffffffde38) at main.cpp:12
(gdb) n
6	}
(gdb) bt
#0  recursive (a=8) at main.cpp:6
#1  0x000000000040050b in recursive (a=9) at main.cpp:4
#2  0x000000000040050b in recursive (a=10) at main.cpp:4
#3  0x0000000000400534 in main (argc=1, argv=0x7fffffffde38) at main.cpp:12
(gdb) n
6	}
(gdb) bt
#0  recursive (a=9) at main.cpp:6
#1  0x000000000040050b in recursive (a=10) at main.cpp:4
#2  0x0000000000400534 in main (argc=1, argv=0x7fffffffde38) at main.cpp:12
(gdb) c
Continuing.
[Inferior 1 (process 31188) exited normally]
(gdb) q
```

Starting gdb with the `-tui` option gives you a split screen mode. Part of the screen will display your source code.

```   ┌──main.cpp────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
   │2       {                                                                                                                 │
   │3         if(a>0)                                                                                                         │
   │4           return recursive(a-1);                                                                                        │
   │5         return 0;                                                                                                       │
   │6       }                                                                                                                 │
   │7                                                                                                                         │
   │8       int main(int argc, char *argv[])                                                                                  │
   │9       {                                                                                                                 │
B+>│10        int a = 10;                                                                                                     │
   │11                                                                                                                        │
   │12        recursive(a);                                                                                                   │
   │13                                                                                                                        │
   │14        return 0;                                                                                                       │
   │15      }                                                                                                                 │
   │16                                                                                                                        │
   │17                                                                                                                        │
   │18                                                                                                                        │
   │19                                                                                                                        │
   │20                                                                                                                        │
   └──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
child process 21253 In: main                                                                           Line: 10   PC: 0x400523 
Reading symbols from ./main...done.
(gdb) break main
Breakpoint 1 at 0x400523: file main.cpp, line 10.
(gdb) r 
Starting program: /home/user/program 
dl-debug.c:74: No such file or directory.
dl-debug.c:74: No such file or directory.

Breakpoint 1, main (argc=1, argv=0x7fffffffde38) at main.cpp:10
(gdb) 
```

Using the display command gdb will print out the value of the variable for step.

```
Breakpoint 1, main (argc=1, argv=0x7fffffffde38) at main.cpp:10
(gdb) display a  
1: a = 0
(gdb) n
1: a = 10
(gdb) n
1: a = 10
```

# Watch 

The watch command may be useful if you are trying to see if a particular variable is being changed.
Consider the following example.

```cpp
int foo[40] ={0};

int main(int argc, char * argv[])
{
  for(int i =0; i < 40; i++)
    foo[i]= 1;
  return 0;
}
```

We want to stop when the `foo[25]` value is changed.
```
(gdb) watch foo[25]
Hardware watchpoint 1: foo[25]
(gdb) r
Starting program: /home/user/program 
Hardware watchpoint 1: foo[25]

Old value = 0
New value = 1
main (argc=1, argv=0x7fffffffde38) at watch.cpp:6
6	  for(int i =0; i < 40; i++)
```

# Conditional break 

Sometimes you might only want gdb to break if a certain condition is true.

```cpp
#include <iostream>
int main(int argc, char * argv[])
{
  for(int i = 0; i < 40; i++)
    std::cout << i << std::endl;
  return 0;
}
```

We want to break on line 5 only when `i` is equal to 5

```
(gdb) break 5 if i == 5
Breakpoint 1 at 0x400845: file main.cpp, line 5.
(gdb) r
Starting program: /home/user/program
0
1
2
3
4

Breakpoint 1, main (argc=1, argv=0x7fffffffde38) at main.cpp:5
5	    std::cout << i << std::endl;

```

# For More Information 

See:
* An old CS16 lab designed to introduce gdb for C programming: <http://www.cs.ucsb.edu/~pconrad/cs16/10S/labs/lab06a/>

