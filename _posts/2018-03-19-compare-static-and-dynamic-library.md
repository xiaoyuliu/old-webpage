---
title: C++ Static Libraries and Dynamic Libraries 
author: xiaoyuliu
date: Mon 19 Mar 2018 04:12:42 PM PDT
category: blog
layout: post
tag:
- C++
- Linux
- Windows
---

This articile is based on the Chinese version [blog][http://www.cnblogs.com/skynet/p/3372855.html] introducing static libraries and dynamic libraries.

### Definition of Library

A library is a collection of off-the-shelf scripts that can be used repeatedly. Basically it is the binary mode of executable codes and can be loaded into the operation system to run. There are two kinds of libraries on LINUX and WINDOWS:
    
    - *Static library*: `.a` for LINUX, `.lib` for WINDOWS
    - *Dynamic library*: `.so` for LINUX, `.dll` for WINDOWS

To generate an executable file, the source file will go through *pre-compile*, *compile*, *assemble* and *link*. `.a/.lib` and `.so/.dll` comes from *link* stage.

- **Static Library**
    
    A static library can be simply treated as a set of `.o/.obj` files. It can be linked together with `.o/.obj` files from *aseemble* to generate the executable file. 

    Static libraries increase the size of the code in your binary. They're always loaded and whatever version of the code you compiled with is the version of the code that will run. (from the [answer][https://stackoverflow.com/questions/140061/when-to-use-dynamic-vs-static-libraries])

    - **Generate Static Library**
    
        ```bash
        # ar under LINUX, lib.exe under WINDOWS
        g++ -c StaticFunction.cpp
        ar -crv libStaticFunction.a StaticFunction.o
        ```

    - **Use Static Library Under LINUX**
        
        ```
        g++ Teststaticfunction.cpp -L../StaticFunction -lstaticfunction
        ```

        `-L`: specify the search path of static libraries
        `-l`: specify the name of static libraries, no prefix or subfix.

- **Dynamic Library**

    A dynamic library will not be loaded into program until running. If different programs use the same dynamic library, there will be only one instance of the shared library in memory. This feature saves lots of memory and it is incremental update. *(When the library is updated, the programs that use this library don't need to be re-compiled, the user can simply update the library)*

    - **Generate Dynamic Library**
    
    ```bash
    g++ -fPIC -c DynamicFunction.cpp
    g++ -shared -o libdynamicfunction.so DynamicFunction.o

    # g++ -fPIC -shared -o libDynamicFunction.so DynamicFunction.cpp
    ```

    - **Use Dynamic Library Under LINUX**

    In order to make sure that our system can find the dynamic library, do either: 

    ```bash
    mv DynamicFunction.o /usr/lib/
    ```

    or add the path to the dynamic library to `/etc/ld.so.cache` file and run `ldconfig`.

    ```bash
    g++ TestDynamicFunction.cpp -L../DynamicFunction -ldynamicfunction
    ```


- **Comparison**

    - The features of static libraries:
        + The link is completed during *compile*, running fast
        + During running, the program is not related to static libraries any more
        + Space and resource consuming, because all `.o/.obj` files and related function libraries can be linked and combined into an executable file multiple times
        + When the library is updated, all programs that use this library need to be re-compiled

    - The features of dynamic libraries:
        + The link is completed during *running*, running slower
        + During running, the dynamic libraries must be kept while running
        + Save memory because there will be only one copy for the shared dynamic library for programs using it in the memory
        + When the library is updated, all customers need to update is simply the dynamic library.

- **Combine Multiple Static Libraries**
    
    ```bash
    # under LINUX
    ar x libA.a
    ar x libB.a
    ar cur libAB.a *.o
    ranlib libAB.a
    ```

- **Combine Multiple Dynamic Libraries**

    ```bash
    # under LINUX, do not test this yet
    # have to compile the dynamic libraries into static libraries and use this command to combine them into a .so file
    gcc -shared -o c.so -Wl,--whole-archive a.a b.a -Wl,--no-whole-archive
    ```
