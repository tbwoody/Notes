A Java abstract class is a class which cannot be instantiated, meaning you cannot create new instances of an abstract class. The purpose of an abstract class is to function as a base for subclasses. This Java abstract class tutorial explains how abstract classes are created in Java, what rules apply to them. This tutorial gets into the purpose of abstract classes in Java in more detail towards the end of this text.  

#1. Declaring an Abstract Class in Java  
In Java you declare that a class is abstract by adding the abstract keyword to the class declaration. Here is a Java abstract class example:  
```
public abstract class MyAbstractClass {

}
```
That is all there is to declaring an abstract class in Java. Now you cannot create instances of MyAbstractClass. Thus, the following Java code is no longer valid:  
```
MyAbstractClass myClassInstance = 
    new MyAbstractClass();  //not valid
```
If you try to compile the code above the Java compiler will generate an error, saying that you cannot instantiate MyAbstractClass because it is an abstract class.

#2. Abstract Methods
An abstract class can have abstract methods. You declare a method abstract by adding the abstract keyword in front of the method declaration. Here is a Java abstract method example:  
```
public abstract class MyAbstractClass {

    public abstract void abstractMethod();
}
```

An abstract method has no implementation. It just has a method signature. Just like methods in a Java interface.

If a class has an abstract method, the whole class must be declared abstract. Not all methods in an abstract class have to be abstract methods. An abstract class can have a mixture of abstract and non-abstract methods.

Subclasses of an abstract class must implement (override) all abstract methods of its abstract superclass. The non-abstract methods of the superclass are just inherited as they are. They can also be overridden, if needed.  

Here is an example subclass of the abstract class MyAbstractClass:   
```
public class MySubClass extends MyAbstractClass {

    public void abstractMethod() {
        System.out.println("My method implementation");
    }
}
```    
Notice how MySubClass has to implement the abstract method abstractMethod() from its abstract superclass MyAbstractClass.

The only time a subclass of an abstract class is not forced to implement all abstract methods of its superclass, is if the subclass is also an abstract class.

#3. The Purpose of Abstract Classes
The purpose of abstract classes is to function as base classes which can be extended by subclasses to create a full implementation. For instance, imagine that a certain process requires 3 steps:  

- 1.The step before the action.
- 2.The action.
- 3.The step after the action.

If the steps before and after the action are always the same, the 3-step process could be implemented in an abstract superclass with this Java code:  

```
public abstract class MyAbstractProcess {

    public void process() {
        stepBefore();
        action();
        stepAfter();
    }

    public void stepBefore() {
        //implementation directly in abstract superclass
    }

    public abstract void action(); // implemented by subclasses

    public void stepAfter() {
        //implementation directly in abstract superclass
    }
}
```

Notice how the action() method is abstract. Subclasses of MyAbstractProcess can now extend MyAbstractProcess and just override the action() method.

When the process() method of the subclass is called, the full process is executed, including the stepBefore() and stepAfter() of the abstract superclass, and the action() method of the subclass.

Of course, the MyAbstractProcess did not have to be an abstract class to function as a base class. Nor did the action() method have to be abstract either. You could have just used an ordinary class. However, by making the method to implement abstract, and thus the class too, you signal clearly to users of this class that this class should not be used as it is. Instead it should be used as a base class for a subclass, and that the abstract method should be implemented in the subclass.

The above example did not have a default implementation for the action() method. In some cases your superclass might actually have a default implementation for the method that subclasses are supposed to override. In that case, you cannot make the method abstract. You can still make the superclass abstract though, even if it contains no abstract methods.

Here is a more concrete example that opens a URL, processes it and closes the connection to the URL afterwards.

```
public abstract class URLProcessorBase {

    public void process(URL url) throws IOException {
        URLConnection urlConnection = url.openConnection();
        InputStream input = urlConnection.getInputStream();

        try{
            processURLData(input);
        } finally {
            input.close();
        }
    }

    protected abstract void processURLData(InputStream input)
        throws IOException;

}
```

Notice how the processURLData() is an abstract method, and that URLProcessorBase is an abstract class. Subclasses of URLProcessorBase have to implement the processURLData() method because it is an abstract method.

Subclasses of URLProcessorBase abstract class can process data downloaded from URLs without worrying about opening and closing the network connection to the URL. This is done by the URLProcessorBase. Subclasses only need to worry about processing the data from the InputStream passed to the processURLData() method. This makes it easier to implement classes that processes data from URLs.

Here is an example subclass: 
```
public class URLProcessorImpl extends URLProcessorBase {

    @Override
    protected void processURLData(InputStream input) throws IOException {
        int data = input.read();
        while(data != -1){
            System.out.println((char) data);
            data = input.read();
        }
    }
}
```
Notice how the subclass only implements the processURLData() method, and nothing more. The rest of the code is inherited from the URLProcessorBase superclass.

Here is an example of how to use the URLProcessorImpl class:
```
URLProcessorImpl urlProcessor = new URLProcessorImpl();

urlProcessor.process(new URL("http://jenkov.com"));
```

The process() method is called, which is implemented in the URLProcessorBase superclass. This method in turn calls the processURLData() in the URLProcessorImpl class. 

#4. Abstract Classes Compared to Interfaces
Abstract classes are similar to interfaces. You cannot instantiate them, and they may contain a mix of methods declared with or without an implementation. However, with abstract classes, you can declare fields that are not static and final, and define public, protected, and private concrete methods. With interfaces, all fields are automatically public, static, and final, and all methods that you declare or define (as default methods) are public. In addition, you can extend only one class, whether or not it is abstract, whereas you can implement any number of interfaces.  

Which should you use, abstract classes or interfaces?

- Consider using abstract classes if any of these statements apply to your situation:
  - You want to share code among several closely related classes.
  - You expect that classes that extend your abstract class have many common methods or fields, or require access modifiers other than public (such as protected and private).
  - You want to declare non-static or non-final fields. This enables you to define methods that can access and modify the state of the object to which they belong.
- Consider using interfaces if any of these statements apply to your situation:
  - You expect that unrelated classes would implement your interface. For example, the interfaces Comparable and Cloneable are implemented by many unrelated classes.
  - You want to specify the behavior of a particular data type, but not concerned about who implements its behavior.
  - You want to take advantage of multiple inheritance of type.
