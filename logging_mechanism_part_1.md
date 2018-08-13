# Linux logging system. Part I: Another approach to a legendary program.
Author: HieuTC3 with 3 mins to read.
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
    * We have a basic background about Operating Systems, programming language compiler, linker, Device driver, system call.
    * We use **Linux** enviroment to trace the **HWp** step by step. In this article, I use a Linux VMware instance with following parameter:

```bash
        $ uname -a
        Linux ubuntu 4.15.0-30-generic #32~16.04.1-Ubuntu SMP Thu Jul 26 20:25:39 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
        $ gcc --version
        gcc (Ubuntu 5.4.0-6ubuntu1~16.04.10) 5.4.0 20160609
        Copyright (C) 2015 Free Software Foundation, Inc.
        This is free software; see the source for copying conditions.  There is NO
        warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

## What is printf?
If we *google* with *"printf man"* phrase, maybe you can find that *printf* function is declare like this:
```C
int printf(const char *format, ...);
```
and this function will produce output to **stdout** according to the *format*. Please goto this url https://linux.die.net/man/3/printf for more detail description.
## How to use printf?
Let's see some actions: include stdio.h library, format the output, etc ... But I know, for sure, that it would be so boring if I described these action in detail :) .

## How printf work?
It will be *die-hard* method if we trace the complexity source code of Linux system to understand what exactly *printf* function do at this time. I choose a lazy-effective one: use application debugging tool.
Firstly, we create the main.c file and edit the **HWp**.
```C
//main.c
#include <stdio.h>

main( )
{
        printf("hello, world\n");
}
```

```bash
$ gcc -Wall main.c -o printf_app
$ nm printf_app
0000000000601038 B __bss_start
0000000000601038 b completed.7594
0000000000601028 D __data_start
0000000000601028 W data_start
0000000000400460 t deregister_tm_clones
00000000004004e0 t __do_global_dtors_aux
0000000000600e18 t __do_global_dtors_aux_fini_array_entry
0000000000601030 D __dso_handle
0000000000600e28 d _DYNAMIC
0000000000601038 D _edata
0000000000601040 B _end
00000000004005b4 T _fini
0000000000400500 t frame_dummy
0000000000600e10 t __frame_dummy_init_array_entry
00000000004006f8 r __FRAME_END__
0000000000601000 d _GLOBAL_OFFSET_TABLE_
                 w __gmon_start__
00000000004005d0 r __GNU_EH_FRAME_HDR
00000000004003c8 T _init
0000000000600e18 t __init_array_end
0000000000600e10 t __init_array_start
00000000004005c0 R _IO_stdin_used
                 w _ITM_deregisterTMCloneTable
                 w _ITM_registerTMCloneTable
0000000000600e20 d __JCR_END__
0000000000600e20 d __JCR_LIST__
                 w _Jv_RegisterClasses
00000000004005b0 T __libc_csu_fini
0000000000400540 T __libc_csu_init
                 U __libc_start_main@@GLIBC_2.2.5
0000000000400526 T main
                 U puts@@GLIBC_2.2.5
00000000004004a0 t register_tm_clones
0000000000400430 T _start
0000000000601038 D __TMC_END__


```

```bash
$ strace ./printf_app
execve("./printf_app", ["./printf_app"], [/* 23 vars */]) = 0
brk(NULL)                               = 0x1ef5000
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=107470, ...}) = 0
mmap(NULL, 107470, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f6730d05000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P\t\2\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0755, st_size=1868984, ...}) = 0
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f6730d04000
mmap(NULL, 3971488, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f6730731000
mprotect(0x7f67308f1000, 2097152, PROT_NONE) = 0
mmap(0x7f6730af1000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1c0000) = 0x7f6730af1000
mmap(0x7f6730af7000, 14752, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f6730af7000
close(3)                                = 0
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f6730d03000
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f6730d02000
arch_prctl(ARCH_SET_FS, 0x7f6730d03700) = 0
mprotect(0x7f6730af1000, 16384, PROT_READ) = 0
mprotect(0x600000, 4096, PROT_READ)     = 0
mprotect(0x7f6730d20000, 4096, PROT_READ) = 0
munmap(0x7f6730d05000, 107470)          = 0
fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(136, 8), ...}) = 0
brk(NULL)                               = 0x1ef5000
brk(0x1f16000)                          = 0x1f16000
write(1, "Hello world\n", 12Hello world
)           = 12
exit_group(0)                           = ?
+++ exited with 0 +++
```
The results of *nm* or *strace* command look so horrible. But please do not be discouraged. It is really straightforward and I will explain.

Firstly, the following diagram describes a architecture of **Linux** application:

![Image of Linux application architecture](https://github.com/vitlachbach/vitlachbach.github.io/blob/master/images/logging_mechanism_part_1/app_layer.png)

Fig 01. Linux application architecture
It shows that each application will be create by its logic process and by linking (dynamic/static) of C runtime libraries. Each C runtime library will call the Operating System API by a system-call. Each line of **strace** command is a runtime system call from the *printf_app* application. Ignore some other system call of *printf_app* application to focus about our topic, We've just see following system call:
```bash
write(1, "Hello world\n", 12Hello world
```
This system call means that finally, the *printf_app* will call to device driver by *write* with file descriptor is, the **stdout** identify, 1.

For detail, if we use *gdb* to see more (using *stepi* command) about the printf call, we will find this calling sequence of the *printf_app*:
```bash
$ gdb ./printf_app
(gdb) start
(gdb) stepi
... many stepi go ...
0x00000000004009b7 in main ()
0x000000000040fa90 in puts ()
0x0000000000423820 in strlen ()
...
0x000000000041dbe0 in malloc ()
...
0x0000000000414fd0 in _IO_doallocbuf ()
...
0x0000000000462c80 in _IO_file_doallocate ()
...
0x00000000004133a0 in _IO_new_do_write ()
...
0x000000000043f3b0 in write ()
```

OOps, there is an apperance of memory allocation in the program sequence! What is happened actually ..? 
However it seems that's all for today and we will continue the topic with next post.
## Pre-prologue
No doubt that **HWp** is one of the most famous one. Software engineers can easy understand the simple function of **HWp**. We also admit that **HWp**, such its basic syntax, be the beginning lesson of a C/C++ programmers. But if we want to be a professional one we should dig more the iceberg to see its sunken where there is a lot of consider points in advanced:
    * Why does it need memory allocation?
    * The program need a buffer, doesn't it?
    * What is the mechanism of the memory buffer in printf?
    * How about the print in kernel mode?
    * ...
T.B.C
