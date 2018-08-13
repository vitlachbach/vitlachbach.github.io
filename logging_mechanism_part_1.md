# Another approach about a legendary program
## Prelude
Most programmers know the **Hello World** program (**HWp**) used to introduce to a novice about a computer programming language. From *Programming in C: A Tutorial* of Brian Kernighan, the instance of the **HWp** is like following: 
```C
#include <stdio.h>

main( )
{
        printf("hello, world\n");
}
```
This program illustrates the basic of C-programming language syntax as simplest way as every *developer* can understand the function of this program: It outputs (or displays) *"hello, world"* (with line ending) to a user by calling **printf** utility in **stdio** library. This article have another approach about this legendary program: *How does it can display text in the console?*

## Some assumptions before we continue
Before start to find the answer of the topic's main question, We assume some concepts and configurations to limit our scope:
    * We have a basic background about Operating Systems, programming language compiler, Device driver, system call.
    * We use linux enviroment to explain the **HWp** step by step.
    * 
