## Introduction
In this part of the project, we want to reorganise the code base into C++ classes that can be instantiated as objects. We will need to have a meeting before you continue with this part of the project. The idea is that our system will have a set of classes that we can build our system on top of, each of which has a specific purpose. As an example, I would suggest the following classes:

* `CSystem` responsible for initialising the main timer, interrupts and eventually starting the main loop
* `CTask` an [abstract class](https://en.wikipedia.org/wiki/Abstract_type) that presents the interface of a runnable task, essentially it contains a member function `void operator()() = 0;` that can be overridden by the classes that will actually implement a task.
* `CResource` is a class containing a `spinlock_t`, the idea is that the `Serial` class will inherit `CResource`, making `Serial` lockable when used inside a `CTask`

## Instructions
1. I would install picocom under linux, it is a simple serial program which can save you a fair bit of time by chaining commands together e.g. `make && avrdude <avrdude upload commands> && picocom <picocom parameters>`
2. In a folder under your home directory on Linux, create a workspace folder and try to compile, upload (to the red board), and run my MWE. My minimum MWE is a standalone binary that should bring up the board and print `Ready>` to the terminal. At this point, you can press the `u` key on the keyboard and the MWE will reply with the time in milliseconds that the board has been running.
3. Please note that I use the baud rate of 57600 in the code base as I found this to be more reliable on the smartblock. Since your bootloader is probably working at 115200, you will probably have to use that value for avrdude
4. Sources for the MWE and Toolchain are as follows:
 * [Tool chain](https://dl.dropboxusercontent.com/u/34090710/toolchain.tar.gz)
 * [MWE source code](https://dl.dropboxusercontent.com/u/34090710/codebase-source.tar.gz)
5. Once my MWE is working, you can start adding classes to it, such as `CSystem`, `CTask`, and `CResource` etc. Eventually getting to the point where you have a similar example to what you had running in [[Part II|Project-Part-II:-Uploading-and-Running-AVR-OS]]. 

## Questions, Issues and Problems

> Add your questions etc here :)