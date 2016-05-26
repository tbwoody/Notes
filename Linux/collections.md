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

**10. Deadlocks and Deadlock Prevention**
A deadlock is a situation where a thread is waitin for an object lock that another thread holds, and this second thread is waiting for an object lock that the first thread holds. Since each thread is waiting for the other thread to relinquish a lock, they both remain waiting forever. The threads are said to be deadlock.        

In order for a deadlock to occur, you must have all four of the following conditions met:
- 1. Mutual Exclusion: Only one process can access a resource at a given time. (Or, more accurately, there is a limited access to a resource. A deadlock could also occur if a resource has limited quantity).
- 2. Hold and wait: Processes already holding a resource can request additional resources, without relinquishing their curent resources.
- 3. No Preemption: One process cannot forcibly remove another process' resource.
- 4. Circular Wait: Two or more processes form a circular chain where each process is waiting on another resource in the chain.

Deadlock prevention entails removing any of hte above conditions, but it gets tricky because many of these conditions are difficult to satisfy.

**11. Soft Link & Hard Link**              
A softlink is a file that have the information to point to another file/inode. That inode points to the data on the hard drive.

A hardlink is direct pointer to the original inode of the original file. If you compare the original file with hardlink, there won't be any differences.

**12. C++ Virtual Function**                  
If there are member functions with same name in base class and derived class, virtual functions gives programmer capability to call member function of different class by a same function call depending upon different context. This feature in C++ programming is known as polymorphism which is one of the important feature of OOP.       

If a base class and derived class has same function and if you write code to access that function using pointer of base class then, the function in the base class is executed even if, the object of derived class is referenced with that pointer variable.

**13. URL & URI**          
URL is the abbreviation of Uniform Resource Locator. URL is the global address of documents and other resources on the World Wide Web. 

The first part of the URL is called a protocol identifier and it indicates what protocol to use, and the second part is called a resource name and it specifies the IP address or the domain name where the resource is located. The protocol identifier and the resource name are separated by a colon and two forward slashes.         

For example, the two URLs below point to two different files at the domain webopedia.com. The first specifies an executable file that should be fetched using the FTP protocol; the second specifies a Web page that should be fetched using the HTTP protocol:          

A URL is one type of Uniform Resource Identifier (URI); the generic term for all types of names and addresses that refer to objects on the World Wide Web.  

The term "Web address" is a synonym for a URL that uses the HTTP or HTTPS protocol.
The Uniform Resource Locator (URL) was developed by Tim Berners-Lee in 1994 and the Internet Engineering Task Force (IETF) URI working group. The URL format is specified in RFC 1738 Uniform Resource Locators (URL).


**14. Connect DB in C++**
```
/* Standard C++ includes */
#include <stdlib.h>
#include <iostream>

/*
  Include directly the different
  headers from cppconn/ and mysql_driver.h + mysql_util.h
  (and mysql_connection.h). This will reduce your build time!
*/
#include "mysql_connection.h"

#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/statement.h>

using namespace std;

int main(void)
{
cout << endl;
cout << "Running 'SELECT 'Hello World!' »
   AS _message'..." << endl;

try {
  sql::Driver *driver;
  sql::Connection *con;
  sql::Statement *stmt;
  sql::ResultSet *res;

  /* Create a connection */
  driver = get_driver_instance();
  con = driver->connect("tcp://127.0.0.1:3306", "root", "root");
  /* Connect to the MySQL test database */
  con->setSchema("test");

  stmt = con->createStatement();
  res = stmt->executeQuery("SELECT 'Hello World!' AS _message"); // replace with your statement
  while (res->next()) {
    cout << "\t... MySQL replies: ";
    /* Access column data by alias or column name */
    cout << res->getString("_message") << endl;
    cout << "\t... MySQL says it again: ";
    /* Access column fata by numeric offset, 1 is the first column */
    cout << res->getString(1) << endl;
  }
  delete res;
  delete stmt;
  delete con;

} catch (sql::SQLException &e) {
  cout << "# ERR: SQLException in " << __FILE__;
  cout << "(" << __FUNCTION__ << ") on line " »
     << __LINE__ << endl;
  cout << "# ERR: " << e.what();
  cout << " (MySQL error code: " << e.getErrorCode();
  cout << ", SQLState: " << e.getSQLState() << " )" << endl;
}

cout << endl;

return EXIT_SUCCESS;
}
```
