---
topic: "tools: valgrind"
desc: "Tool for checking run time behavior of C/C++ programs, including memory leaks"
indent: true
---



<div style='display:none'>
https://ucsb-cs32.github.io/topics/tools_valgrind/
</div>

Valgrind is actually multiple tools in one. The default tool that is invoked when you run valgrind is `memcheck`. 
You should compile your program with `-g` so that valgrind has access to all the debug information.

For now, we are only going to cover the "memcheck" part of Valgrind.  

# Memcheck 

`memcheck` will catch several, but not all, common memory related bugs. 
It does, so by watching when you allocate, deallocate and access memory and reports 
if something looks suspiciously like a bug.

```
valgrind ./debugProgram
```

Some of the errors that valgrind might report are not necessarily intuitive.  Here are some of them.

If you see ...
```
Conditional jump or move depends on uninitialised value(s)
```
Then you are probably using a variable before an assignment.

For example, this behavior is undefined:

```cpp
bool a;
if(a)
  ;//do something...
```

If you see...
```
Mismatched free() / delete / delete []
```
You need to be consistent with your dynamic memory operations.

C requires `malloc()`/`free()` for allocation, but C++ typically uses the `new` and `delete` keywords. Don't mix C/C++ memory management. 
If you need `realloc()` you should probably be using `std::vector`. If you are allocating an array in C++ always 
use `delete []` for deletion. If you are allocating a single element use `delete` for deletion.

If you see...
```
Warning: silly arg (-3) to malloc()
```
C++ should throw an exception if you try to alloc a negative number, but valgrind will also check that your 
program isn't doing some additional silly things. This is useful if you are working with programs at 
an operating systems level.   If you later on take CMPSC 170 (Operating Systems), this may come in very handy.

At the end of the execution `memcheck` will output some summary information.

```
==5387== HEAP SUMMARY:
==5387==     in use at exit: 5,848 bytes in 130 blocks
==5387==   total heap usage: 484 allocs, 354 frees, 26,888 bytes allocated
==5387== 
==5387== LEAK SUMMARY:
==5387==    definitely lost: 5,848 bytes in 130 blocks
==5387==    indirectly lost: 0 bytes in 0 blocks
==5387==      possibly lost: 0 bytes in 0 blocks
==5387==    still reachable: 0 bytes in 0 blocks
==5387==         suppressed: 0 bytes in 0 blocks
==5387== Rerun with --leak-check=full to see details of leaked memory
==5387== 
==5387== For counts of detected and suppressed errors, rerun with: -v
==5387== Use --track-origins=yes to see where uninitialised values come from
==5387== ERROR SUMMARY: 2 errors from 2 contexts (suppressed: 0 from 0)
```

One approach is to ensuring your program is leak free is to make sure that everything is 
deleted before your program exits. If you delete everything then valgrind will be able to detect 
that everything is accounted for and no leaks are possible. If you see any bytes in the `LEAK SUMMARY`
section you probably want to recheck to make sure everything is deleted.

It's very useful to rerun with
```
 valgrind --leak-check=full ./debugProgram
```

The tools: valgrind "did you read it" password is `Cucumber`.

Make sure that `--leak-check=full` is before the `./debugProgram` or it will be passed in as an argument to the 
`./debugProgram`.

Valgrind will keep records where allocations happen, so you can track down where a leak occurred.
```
==945== 928 bytes in 20 blocks are definitely lost in loss record 1 of 2
==945==    at 0x4C2B800: operator new[](unsigned long) (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==945==    by 0x4018BD: main (in /home/user/debugProgram)
```
