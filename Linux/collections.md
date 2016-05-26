**1. Linux Kernel**         
Linux kernel contains many components:
- Device Drivers
- Memory Management   
- Process Management   
- Communication Management

**2. System Calls**   
A system call is usually a request to the operating system (kernel) to do a hardware/system specific or privileged operation.      
As an example, you can close a file using syscall() like this (not advised):   
```
#include <syscall.h>
extern int syscall(int, ...);
int my_close(int filedescriptor)
{
  return syscall(SYS_close, filedescriptor);
}
```

**3. Ioctl**  
ioctl stands for input/output control and is used to manipulate a character device via a filedescriptor. The format of ioctl is   
```
ioctl(unsigned int fd, unsigned int request, unsigned long argument).          
```
The return value is -1 if an error occured and a value greater or equal than 0 if the request succeeded just like other system calls. 

**4. Inter-Process Communication**    
- Signals
- Pipes
- Sockets
- Message Queues
- Semaphores
- Shared Memory       
We can find more informaiton about IPC from [HERE](http://www.tldp.org/LDP/tlk/ipc/ipc.html).   

**5. Process States**
- Created
- Waiting
- Running
- Blocked
- Swapped out and waiting
- Swapped out and blocked
- Terminated


**6. Segmentation Fault**            
The following are some typical causes of a segmentation fault:      
- Dereferencing null pointers – this is special-cased by memory management hardware
- Attempting to access a nonexistent memory address (outside process's address space)
- Attempting to access memory the program does not have rights to (such as kernel structures in process context)
- Attempting to write read-only memory (such as code segment)  

These in turn are often caused by programming errors that result in invalid memory access:           
- Dereferencing or assigning to an uninitialized pointer (wild pointer, which points to a random memory address)
- Dereferencing or assigning to a freed pointer (dangling pointer, which points to memory that has been freed/deallocated/deleted)
- A buffer overflow
- A stack overflow
- Attempting to execute a program that does not compile correctly. (Some compilers will output an executable file despite the presence of compile-time errors.)     

In C code, segmentation faults most often occur because of errors in pointer use, particularly in C dynamic memory allocation. Dereferencing a null pointer will always result in a segmentation fault, but wild pointers and dangling pointers point to memory that may or may not exist, and may or may not be readable or writable, and thus can result in transient bugs.   

**7. Processes VS Threads**    
Threads   
- will by default share memory
- will share file descriptors
- will share filesystem context
- will share signal handling

Processes  
- will by default not share memory
- most file descriptors not shared
- don't share filesystem context
- don't share signal handling

**8. GCC**      
The GNU Compiler Collection includes front ends for C, C++, Objective-C, Fortran, Java, Ada, and Go, as well as libraries for these languages (libstdc++, libgcj,...). GCC was originally written as the compiler for the GNU operating system. The GNU system was developed to be 100% free software, free in the sense that it respects the user's freedom.  

The latest version of GCC is GCC-6.1, and it was release on 2016-04-27.

**9. GLIB**   
The GLib package contains low-level libraries useful for providing data structure handling for C, portability wrappers and interfaces for such runtime functionality as an event loop, threads, dynamic loading and an object system.  

GLib provides the core application building blocks for libraries and applications written in C. It provides the core object system used in GNOME, the main loop implementation, and a large set of utility functions for strings and common data structures.

The latest version of GLIB is glib-2.48  


