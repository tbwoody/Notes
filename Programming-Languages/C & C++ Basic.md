#1. Classes and Inheritance
The code below demonstrates the mimplementation of a basic class with inheritance.   

```
#include<iostream>
using namespace std;

#define NAME_SIZE 50 //Defines a macro

class Person{
  int id; //all members are private by default
  char name[NAME_SIZE];
  
  public:
  void aboutMe(){
    cout << "I am a person.";
  }
};

class Student: public Person{
  public:
  void aboutMe(){
    cout << "I am a student.";
  }
};

int main(){
  Student *p = new Student();
  p->aboutMe();
  delete p;
  return 0;
}

```
All data members and methods are private by default in C++. One can modify this by introducing the key word public.

#2. Constructors and Destructors
The constructor of a class is automatically called upon an object's creation. If no constructor is defined, the compiler automatically
generates on called Default Constructor. Alternatively, we can define our own constructor.

If you just need to initialize primitive types, a simple way to do it is this:
```
Person(int a){
  id = a;
}
```
This works for primitive types, but you might instead want to do this:
```
Person(ina a) : id(a){
  ...
}
```
The data member id is assigned before the actual object is created and before the remainder of the constructor code is called. This approach
is necessary when the fields are constant or class types.

The destructor cleans up upon coject deletion and is automatically called when an object is destroyed. It cannot take an argument as we don't 
explicitly call a destructor.
```
~Person(){
  delete obj; // free any memory allocated within class.
}
```
#3. Virtual Functions
In an earlier example, we defined p to be type Student:
```
Student * p = new Student;
p->aboutMe();
```
What would happen if we defined p to be a Persion*, like so?
```
Person * p = new Student();
p->aboutMe();
```
In this case, "I am a person." would be printed instead. This is because the function aboutMe is resolved at compile-time, in a mechanism known as static binding.

If we want to ensure that the Student's implementation of aboutMe is called, we can define aboutMe in the Person class to be Virtual.
```
class Person{
  ...
  virtual void aboutMe(){
    cout << "I am a person" << endl;
  }
};

class Student: public Person{
  public:
  void aboutMe(){
    cout<< "I am a student." << endl;
  }

};
```
Another usage for virtual functions is when we can't (or don't want to) implement a method for the parent class. Imagine, for example, 
that we want Student and Teacher to inherit from Person so that we can implement a common method such as addCouse(string s). Calling addCouse on Person, however, wouldn't make much sense since the implementation depends on whether the object is actually a Student or Teacher.

In this case, we might want addCourse to be a virtual function defined within Person, with the implementation being left to the subclass.

```
class Person{
  int id;
  char name[NAME_SIZE];
  
  public:
  virtual void aboutMe(){
    cout << "I am a person." << endl;
  }
  
  virtual bool addCourse(string s) = 0;
};

class Student: public Person{
  public:
  void aboutMe(){
    cout << "I am a student.";
  }
  
  void addCourse(string course){
    cout << "Added course "<< s <<" to student." << endl;
  }
};

int main(){
  Person *p = new Student();
  p->aboutMe(); //prints "I am a student";
  p->addCourse("History"); //prints "Added couse History to student."
  delete p;
}

```

Note that by defining addCouse to be a pure virtual function, Person is now an abstract class and we cannot instantiate it.

#4. Virtual Destructor
The virtual function naturally introduces the concept of a virtual destructor. Suppose we wanted to implement a destructor method for Person 
and Student. A naive solution might look like this:
 ```
 class Person{
  public:
  ~Person(){
    cout << "Deleting a person."<<endl;
  }
 };
 
 class Student:public Person{
  public:
  ~Student(){
    cout << "Deleting a student." << endl;
  }
 };
 
 int main(){
  Person *p = new Student();
  delete p; //Prints "Deleting a person."
 }
 ```
 
 As in the earlier example, since p is a Person, the destructor for the Person class is called. This is problematic because the 
 memory fot the Student may not be cleaned up. To fix this, we simply define the destructor for Person to be virtual.
 ```
  class Person{
  public:
  virtual ~Person(){
    cout << "Deleting a person."<<endl;
  }
 };
 
 class Student:public Person{
  public:
  ~Student(){
    cout << "Deleting a student." << endl;
  }
 };
 
 int main(){
  Person *p = new Student();
  delete p; //Prints "Deleting a person."
 }
 ```
This will output the following:                
*Deleting a student.*             
*Deleting a person.*                

#5. Default Values
Functions can specify default values, as shown below. Note that all default parameters must be on the right side of the function declaration,
 as there would be no other way to specify how the parameters line up.
 
 ```
 int func(int a, int b = 3){
  x = a;
  y = b;
  return a + b;
 }
 
 w = func(4);
 z = func(4,5);
 ```
 
#6. Operator Overloading
Operator overloading enables us to apply operators like + to objects that would otherwise not support these operations. For example,
 if we wanted to merge two BookShelves into one, we could overload the + operator like this:
 
 ```
 BookShelf BookShelf::operator+(Bookhelf & other){...}
 ```
#7. Pointers and References
A pointer holds the address of a variable and can be used to perform any operation that could be directly done on the variable, such as
accessing and modifying it.

Two pointers can equal each other, such that changing one's value also changes the other's value (since they, in fact, point to the same address).   

```
int *p = new int;
*p  = 7;
int *q = p;
*p = 8;
cout << *q; //prints 8
```

Note that the size of a pointer varies depending on the architecture: 32 bits on a 32-bit machine and 64 bits on a 64-bit machine. Pay attention
to this difference, as it's common for interviewers to ask exactly how much space a data structure takes up.

##7.1 References
A reference is another name (an alias) for a pre-existing object and it does not have memory of its own. For example:
```
int a = 5;
int & b = a;
b = 7;
cout << a; //prints 7
```

In line 2 above, b is a reference to a; modifying b will also modify a.
You cannot create a reference without specifying where in memory it refers to. However, you can create a free-standing reference as shown below:
```
//allocates memory to store 12 and make b a reference to this piece of memory.
int & b = 12;
```
Unlike pointers, references cannot be null and cannot be reassigned to another piece of memory.

##7.2 Pointer Arithmetic
One will often see programmers perform addition on a pointer, such as what you see below:
```
int * p = new int[2];
p[0] = 0;
p[1] = 1;
p++;
cout << *p; //Outputs 1
```

Performing p++ will skip ahead by sizeof(int) bytes, such that the code outputs 1. Had p been of different type, it would skip ahead as man bytes
as the size of the data structure.
#8. Templates
Templates are a way of reusing code to apply the same class to different data types. For example, we might have a list-like data structure
which we would like to use for lists of various types. The code below implements this with the ShiftedList class.

```
template <class T> class ShiftedList{
  T* array;
  int offset,size;
  
  public:
  ShiftedList(int sz):offset(0),size(sz){
    array = new T[size];
  }
  
  ~ShiftedList(){
    delete [] array;
  }
  
  void shiftBy(int n){
    offset = (offset + n) % size;
  }
  
  T getAt(int i){
    return array[convertIndex(i)];
  }
  
  void setAt(I item, int i){
    array[convertIndex(i)] = item;
  }
  
  private:
    int convertIndex(int i){
      int index = (i - offset) % size;
      while(index  < 0) index += size;
      return index;
    }
};
```
#8. Difference between heap and stack?
##8.1 The Stack     
What is the stack? It's a special region of your computer's memory that stores temporary variables created by each function (including the main() function). The stack is a "LIFO" (last in, first out) data structure, that is managed and optimized by the CPU quite closely. Every time a function declares a new variable, it is "pushed" onto the stack. Then every time a function exits, all of the variables pushed onto the stack by that function, are freed (that is to say, they are deleted). Once a stack variable is freed, that region of memory becomes available for other stack variables.

The advantage of using the stack to store variables, is that memory is managed for you. You don't have to allocate memory by hand, or free it once you don't need it any more. What's more, because the CPU organizes stack memory so efficiently, reading from and writing to stack variables is very fast.

A key to understanding the stack is the notion that when a function exits, all of its variables are popped off of the stack (and hence lost forever). Thus stack variables are local in nature. This is related to a concept we saw earlier known as variable scope, or local vs global variables. A common bug in C programming is attempting to access a variable that was created on the stack inside some function, from a place in your program outside of that function (i.e. after that function has exited).

##8.2 The heap  
The heap is a region of your computer's memory that is not managed automatically for you, and is not as tightly managed by the CPU. It is a more free-floating region of memory (and is larger). To allocate memory on the heap, you must use malloc() or calloc(), which are built-in C functions. Once you have allocated memory on the heap, you are responsible for using free() to deallocate that memory once you don't need it any more. If you fail to do this, your program will have what is known as a memory leak. That is, memory on the heap will still be set aside (and won't be available to other processes). As we will see in the debugging section, there is a tool called valgrind that can help you detect memory leaks.

Unlike the stack, the heap does not have size restrictions on variable size (apart from the obvious physical limitations of your computer). Heap memory is slightly slower to be read from and written to, because one has to use pointers to access memory on the heap. We will talk about pointers shortly.

Unlike the stack, variables created on the heap are accessible by any function, anywhere in your program. Heap variables are essentially global in scope.

##8.3 Stack vs Heap Pros and Cons    
**Stack**     
- very fast access
- don't have to explicitly de-allocate variables
- space is managed efficiently by CPU, memory will not become fragmented
- local variables only
- limit on stack size (OS-dependent)
- variables cannot be resized

**Heap**
- variables can be accessed globally
- no limit on memory size
- (relatively) slower access
- no guaranteed efficient use of space, memory may become fragmented over time as blocks of memory are allocated, then freed
- you must manage memory (you're in charge of allocating and freeing variables)
- variables can be resized using realloc()

#9. The difference between structs and classes in c++?
- Members of a class are private by default and members of struct are public by default.
- When deriving a struct from a class/struct, default access-specifier for a base class/struct is public. And when deriving a class, default access specifier is private.
#10. How does virtual function implemented?
Whenever a program has a virtual function declared, a v - table is constructed for the class. The v-table consists of addresses to the virtual functions for classes that contain one or more virtual functions. The object of the class containing the virtual function contains a virtual pointer that points to the base address of the virtual table in memory. Whenever there is a virtual function call, the v-table is used to resolve to the function address. An object of the class that contains one or more virtual functions contains a virtual pointer called the vptr at the very beginning of the object in the memory. Hence the size of the object in this case increases by the size of the pointer. This vptr contains the base address of the virtual table in memory. Note that virtual tables are class specific, i.e., there is only one virtual table for a class irrespective of the number of virtual functions it contains. This virtual table in turn contains the base addresses of one or more virtual functions of the class. At the time when a virtual function is called on an object, the vptr of that object provides the base address of the virtual table for that class in memory. This table is used to resolve the function call as it contains the addresses of all the virtual functions of that class. This is how dynamic binding is resolved during a virtual function call.

#11. What is abstract class?
Abstract classes are classes that contain one or more abstract methods. An abstract method is a method that is declared, but contains no implementation. Abstract classes may not be instantiated, and require subclasses to provide implementations for the abstract methods.

#12. What is pure virtural function?
In object-oriented programming, in languages such as C++, a virtual function or virtual method is an inheritable and overridable function or method for which dynamic dispatch is facilitated. This concept is an important part of the (runtime) polymorphism portion of object-oriented programming (OOP).

A pure virtual function is a function that has the notation "= 0" in the declaration of that function. Why we would want a pure virtual function and what a pure virtual function looks like is explored in more detail below.

Here is a simple example of what a pure virtual function in C++ would look like:

```
class SomeClass {
public:
   virtual void pure_virtual() = 0;  // a pure virtual function
   // note that there is no function body        
};
```
#13. What is the difference between virtual function and pure virtual function?
A virtual function makes its class a polymorphic base class. Derived classes can override virtual functions. Virtual functions called through base class pointers/references will be resolved at run-time. That is, the dynamic type of the object is used instead of its static type:
```
 Derived d;
 Base& rb = d;
 // if Base::f() is virtual and Derived overrides it, Derived::f() will be called
 rb.f();
```

A pure virtual function is a virtual function whose declaration ends in =0:   

```
class Base {
  // ...
  virtual void f() = 0;
  // ...
```

A pure virtual function implicitly makes the class it is defined for abstract (unlike in Java where you have a keyword to explicitly declare the class abstract). Abstract classes cannot be instantiated. Derived classes need to override/implement all inherited pure virtual functions. If they do not, they too will become abstract.

#14. What is the difference between thread and process?
Both processes and threads are independent sequences of execution. The typical difference is that threads (of the same process) run in a shared memory space, while processes run in separate memory spaces.

A process is an executing instance of an application. What does that mean? Well, for example, when you double-click the Microsoft Word icon, you start a process that runs Word. A thread is a path of execution within a process. Also, a process can contain multiple threads.

#15. What is the difference between mutex and semaphore?
**Mutex:**   

Is a key to a toilet. One person can have the key - occupy the toilet - at the time. When finished, the person gives (frees) the key to the next person in the queue.

Officially: "Mutexes are typically used to serialise access to a section of  re-entrant code that cannot be executed concurrently by more than one thread. A mutex object only allows one thread into a controlled section, forcing other threads which attempt to gain access to that section to wait until the first thread has exited from that section."

(A mutex is really a semaphore with value 1.)

**Semaphore:** 

Is the number of free identical toilet keys. Example, say we have four toilets with identical locks and keys. The semaphore count - the count of keys - is set to 4 at beginning (all four toilets are free), then the count value is decremented as people are coming in. If all toilets are full, ie. there are no free keys left, the semaphore count is 0. Now, when eq. one person leaves the toilet, semaphore is increased to 1 (one free key), and given to the next person in the queue.

Officially: "A semaphore restricts the number of simultaneous users of a shared resource up to a maximum number. Threads can request access to the resource (decrementing the semaphore), and can signal that they have finished using the resource (incrementing the semaphore)."

#16. What is the difference between new and operator new?

I usually try to phrase things differently to differentiate between the two a bit better, but it's a good question in any case.

Operator new is a function that allocates raw memory -- at least conceptually, it's not much different from malloc(). Though it's fairly unusual unless you're writing something like your own container, you can call operator new directly, like:

```
char *x = static_cast<char *>(operator new(100));
```

It's also possible to overload operator new either globally, or for a specific class. IIRC, the signature is:

```
void *operator new(size_t);
```

Of course, if you overload an operator new (either global or for a class), you'll also want/need to overload the matching operator delete as well. For what it's worth, there's also a separate operator new[] that's used to allocate memory for arrays -- but you're almost certainly better off ignoring that whole mess completely.

The new operator is what you normally use to create an object from the free store:

```
my_class *x = new my_class(0);
```

The difference between the two is that operator new just allocates raw memory, nothing else. The new operator starts by using operator new to allocate memory, but then it invokes the constructor for the right type of object, so the result is a real live object created in that memory. If that object contains any other objects (either embedded or as base classes) those constructors as invoked as well.  

#17. What is the difference between assignment and initialization?
Initialization is creating an instance(of type) with certain value.

```
int i = 0;
```

Assignment is to give value to an already created instance(of type).

```
int i;
i = 0
```

#18. What is polymorphism? How does it implemented?
1. [http://www.tutorialspoint.com/cplusplus/cpp_polymorphism.htm](http://www.tutorialspoint.com/cplusplus/cpp_polymorphism.htm)
2. [http://www.cplusplus.com/doc/tutorial/polymorphism/](http://www.cplusplus.com/doc/tutorial/polymorphism/)

#19. What is encapsulation?
[http://www.tutorialspoint.com/cplusplus/cpp_data_encapsulation.htm](http://www.tutorialspoint.com/cplusplus/cpp_data_encapsulation.htm)

In programming languages, encapsulation is used to refer to one of two related but distinct notions, and sometimes to the combination thereof:   

- A language mechanism for restricting direct access to some of the object's components.
- A language construct that facilitates the bundling of data with the methods (or other functions) operating on that data.

Some programming language researchers and academics use the first meaning alone or in combination with the second as a distinguishing feature of object-oriented programming, while other programming languages which provide lexical closures view encapsulation as a feature of the language orthogonal to object orientation.

The second definition is motivated by the fact that in many OOP languages hiding of components is not automatic or can be overridden; thus, information hiding is defined as a separate notion by those who prefer the second definition.

The features of encapsulation are supported using classes in most object-oriented programming languages, although other alternatives also exist.

#20. Copy constructor? When it is called?

#21. Difference between copy constructor and assignment?

#22. What is friend function?

#23. What’s the stack?

#24. What’s LIFO (Last In First Out)?

#25. overloading and overriding difference?

#26. how is the source file compiled to executable(from source file -> binary file)?

#27. After the source file is compiled to binary file, how is the binary file orgnized and stored?

#28. What is overloading? How does the computer know which function is according to which implementation since they have the same name?

#29. C++ file in Linux: which compiler do you use?

#30. Given a file, tell me the steps of reading the file.

#31. What is “IS” and “Has” in C++? What’s the difference?

#32. the disadvantage of using stored procedures with parameters.

#33. when inserting a record into a table, how can you let the relatived tables change accordingly?

#34. design pattern?
[https://sourcemaking.com/design_patterns](https://sourcemaking.com/design_patterns)
#35. The memory a program uses is typically divided into a few different areas, called segments.
- The code segment (also called a text segment), where the compiled program sits in memory. The code segment is typically read-only.
- The bss segment (also called the uninitialized data segment), where zero-initialized global and static variables are stored.
- The data segment (also called the initialized data segment), where initialized global and static variables are stored.
- The heap, where dynamically allocated variables are allocated from.
- The call stack, where function parameters, local variables, and other function-related information are stored.
