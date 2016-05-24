First of all Garbage Collection on the Dalvik Virtual Machine can be a little more relaxed than other Java implementations because it does no compacting. This means that the address of objects on the heap never change after their creation, making the rest of the VM implementation quite a bit simpler.
So garbage collection can be triggered when an allocation fails, when an       
- OutOfMemoryError is about to be triggered,    
- when the size of the heap hits some soft limit, and    
- when a GC was explicitly requested.  

Each of these causes has its own specification indicating whether the GC is a partial GC (only free from active heap), a concurrent GC (do most of the object marking while other threads running), and whether it is a preserving GC (keep soft references).
Your typical GC is triggered by a soft allocation limit, only freeing on the active heap, concurrent, and preserving. On the other extreme the GC triggered before an OutOfMemoryException is full, synchronous, and non-preserving.     

The actual GC is done using a **Mark-Sweep algorithm**.     

**Mark-Sweep Algorithm: How it works ?**    

The mark-and-sweep algorithm was the first garbage collection algorithm to be developed that is able to reclaim cyclic data structures.     

When using mark-and-sweep, unreferenced objects are not reclaimed immediately. Instead, garbage is allowed to accumulate until all available memory has been exhausted. When that happens, the execution of the program is suspended temporarily while the mark-and-sweep algorithm collects all the garbage. Once all unreferenced objects have been reclaimed, the normal execution of the program can resume.

The mark-and-sweep algorithm is called a tracing garbage collector because is traces out the entire collection of objects that are directly or indirectly accessible by the program. The objects that a program can access directly are those objects which are referenced by local variables on the processor stack as well as by any static variables that refer to objects. In the context of garbage collection, these variables are called the roots . An object is indirectly accessible if it is referenced by a field in some other (directly or indirectly) accessible object. An accessible object is said to be live . Conversely, an object which is not live is garbage.

The mark-and-sweep algorithm consists of two phases: In the first phase, it finds and marks all accessible objects. The first phase is called the mark phase. In the second phase, the garbage collection algorithm scans through the heap and reclaims all the unmarked objects. The second phase is called the sweep phase.
