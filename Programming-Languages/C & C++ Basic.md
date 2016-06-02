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
In this case, "I am a person." would be printed instead. This is because the function aboutMe is resolved at compile-time, in a mechanism 
known as static binding.

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
that we want Student and Teacher to inherit from Person so that we can implement a common method such as assCouse(string s). Calling addCouse
on Person, however, wouldn't make much sense since the implementation depends on whether the object is actually a Student or Teacher.

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
