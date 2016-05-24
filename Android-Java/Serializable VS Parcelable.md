Android developers often face a predicament while passing object references to activities of whether to go with the Java Serialization method or opt for Android Parcelable. This blog is my attempt to compare the two techniques and cite an example to help decide which one of these is the best approach for passing an object from one activity to another.

Passing primitive data types like string, integer, float, etc. through intents is quite easy in Android. All you have to do is put the data with unique key in intents and send it to another activity. If a user wants to send Java objects through intent, Java class should be implemented using the Parcelable interface. Serialization, on the other hand, is a Java interface that allows users to implement the interface which gets marked as Serializable.

During the Android application development process, developers often have to send Java class objects from one activity to another activity using the intent. Developers can opt from the two types of object passing techniques, i.e. Serialization and Parcelable of object.  The fact that Parcelable is faster than Serialization makes it a preferred choice of approach while passing an object. Here’s why:

#Implementation

The beauty of serializable is that you only need to implement the Serializable interface on a class and its children. It is a marker interface, meaning that there is no method to implement, Java will simply do its best effort to serialize it efficiently.

The problem with this approach is that reflection is used and it is a slow process. This mechanism also tends to create a lot of temporary objects and cause quite a bit of garbage collection.

Android Parcelable implementation allows objects to read and write from Parcels which can contain flattened data inside message containers.

If a developer wants to convert a Java object into Parcelable, then the best way to do so is by implementing the Parcelable interface and overriding the writeToParcel() methods in its own class. The first step is to override the writeToParcel() method and  write all object members into parcel objects. The second is to create a static Parcelable.Creator object to de-serialize the Java object.  
