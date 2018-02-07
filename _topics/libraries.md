---
topic: "libraries"
desc: "C/C++ libraries for various purposes"
category_prefix: "libraries: "
---

There are a variety of C/C++ libraries that can be accessed on the CSIL systems, or installed on your own systems.

One of the tough things to get right when using libraries is to know what magic sauce you need in your `Makefile` to get the 
libraries to compile and link correctly.

Typically, the header files (`.h`) files live in `/usr/include` and the libraries that you link with live in `/usr/lib`.

But, sometimes you need special stuff to help the compiler and linker find the files you need.

That's where the `pkg-config` utility can help.  Here's an example of setting things up for the `cairo` library.  Notice how the variables `CAIRO_CFLAGS` and `CAIRO_LIBS` are initialized and then used.

```makefile
CC=g++                                                                                              
#Compiler Optionen 

MAIN= cairoDemo

#the cairo header directory
CAIRO_CFLAGS=$(shell pkg-config --cflags cairo)
CAIRO_LIBS=$(shell pkg-config --libs cairo)

CXXFLAGS= -Wall -DEBUG $(CAIRO_CFLAGS)

#actual compiling

all: $(MAIN)

cairoDemo: cairoDemo.o
	$(CC) $(CXXFLAGS) -o $@ $< $(CAIRO_CFLAGS) $(CAIRO_LIBS)

clean:
	/bin/rm -f *.o $(MAIN)
```

# Using libraries on other systems

* On MacOS systems, you can install many of these packages with [homebrew](https://brew.sh/).
* On Windows, one option to install many of them is [Cygwin](https://www.cygwin.com/)

