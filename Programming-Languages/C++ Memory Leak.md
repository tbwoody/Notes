#Common Memory Management Problems in C++      
From:[http://www.cprogramming.com/tutorial/c++_memory_problems.html](http://www.cprogramming.com/tutorial/c++_memory_problems.html)   

Some strategies can be taken into consideration in order to achieve clean code, free of memory leaks. The best and most common practice is to take care to delete all allocated memory with destructors and use tools like Purify (used in the Google Chrome project) or Valgrind to detect memory leaks and fix them. (For more information on using the Valgrind tool, see Using Valgrind to find memory leaks and invalid memory use.) You should try to be careful to always delete [] memory allocated for arrays and to check for proper deallocation on locally used pointers.

When new is used to gain a block of memory the size reserved by the operating system may be bigger than your request, but never smaller. Because of this and the fact that delete doesn't immediately return the memory to the operating system, when you inspect the whole memory that your program is using you may be made to believe that your application has serious memory leaks. So inspecting the number of bytes the whole program is using should not be used as a way of detecting memory errors. Only if the memory manager indicates a large and continuous growth of memory used should you suspect memory leaks. 

A good approach to protect from memory leaks is to use a garbage collector and replace new with its allocator. Even if C++ doesn't provide one (and it will not be present in the new 0X standard either), there are a lot of libraries which can provide good garbage collectors, or you can implement your own. This method is used in some well known applications such as X11, the Mono project, etc.

A half way to garbage collection is to use private heaps, that is, you first allocate a big chunk of memory and manage it in a Heap class (whenever you need new memory to be allocated, a request is being made to the Heap class). Instead of deleting every chunk you allocate for every object, you can delete the whole heap when the objects using it are no longer needed. This can also be used just for backup. In case you forget to delete some object you can be sure that by deleting the Heap there will be no memory leaks remaining.

While the last two approaches are fitted for larger projects and experienced programmers, the C++ standard provides another efficient way of fighting memory leaks, which can be applied by both experienced and intermediate level programmers: smart pointers. They are objects which store pointers to dynamically allocated memory. Writing a smart pointer class is generally not a very hard task, because C++ allows overloading the operator? . One smart pointer provided by the standard library (STL) is called auto_ptr and it is declared in the <memory> header (its class design can be found in Bjarne Stroustrup's book The C++ Programming Language). The following piece of code will illustrate its usage:

```
void f()
{
        Dialog *dl = new Dialog();
        auto_ptr
```   
At the end of the function the auto_ptr object will go out of scope and will delete automatically the memory allocated in the first statement. This above technique is used when there are some exceptions thrown between allocating and deallocating memory and you want to make sure there aren't going to be any memory leaks. 

For more information on auto_ptr, see Using auto_ptr to Handle Memory.

#Debugging Dynamic Memory    
The most common mistake when dealing with memory in general is trying to access a memory location without having proper permission. The bad use of pointers can make possible to access a memory location in the virtual memory that is not actually owned by your application. This will result sometimes in a crash, called a segmentation fault in Unix/Linux based systems and a general protection fault under Windows. Because the virtual memory (presented in the previous part) is divided in pages of fixed size (usually 4096 bytes) the behavior is unpredictable, that is, your application may crash or not, depending on how the memory is mapped for the process. To understand this, this let's assume we have 4 pages of memory allocated by the operating system for our application (the OS can allocate only pages of that fixed size). The policies for every page are kept in the virtual address table along with the mappings between virtual addresses and real locations. So the following code will crash only if the address a[32] falls in a page which is not owned by your application or in one for which you don't have write permission, for example in the text section of the process (see the diagram in the previous part, dynamic memory allocation and virtual memory):    
```
int a = new int[30];
a[32] = 5;
```    
There are some useful applications that can be used to trigger a crash every time you try to access an invalid location in memory, in order to fix them. The most common in the Unix/Linux environment is Electric Fence. In Microsoft Windows application you can make the Visual Studio debugger catch an out of bounds access error by setting it to break when the exception .array out of bounds exceeded. takes place. The tools presented in the previous section, Valgrind and Purify, can also be used to detect memory access errors. 

When you don't have a memory error detection tool or don't want to use one, there is always the old-school way: code inspection. To restrict the search in order to speed up the debugging process, you should first review the pieces of code with a dense use of pointers and casts. 
