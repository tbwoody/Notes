The Portable Operating System Interface (POSIX) is a family of standards specified by the IEEE Computer Society for maintaining compatibility between operating systems. POSIX defines the application programming interface (API), along with command line shells and utility interfaces, for software compatibility with variants of Unix and other operating systems.

POSIX is a family of standards to clarify and make uniform the application programming interfaces (and ancillary issues, such as commandline shell utilities) provided by Unix-y operating systems. When you write your programs to rely on POSIX standards, you can be pretty sure to be able to port them easily among a large family of Unix derivatives (including Linux, but not limited to it!); if and when you use some Linux API that's not standardized as part of Posix, you will have a harder time if and when you want to port that program or library to other Unix-y systems (e.g., MacOSX) in the future.

**POSIX.1**      
POSIX.1, Core Services (incorporates Standard ANSI C) (IEEE Std 1003.1-1988)        
- Process Creation and Control
- Signals
- Floating Point Exceptions
- Segmentation / Memory Violations
- Illegal Instructions
- Bus Errors
- Timers
- File and Directory Operations
- Pipes
- C Library (Standard C)
- I/O Port Interface and Control
- Process Triggers

**POSIX.1b**      
POSIX.1b, Real-time extensions (IEEE Std 1003.1b-1993, later appearing as librt - the Realtime Extensions library))     
- Priority Scheduling
- Real-Time Signals
- Clocks and Timers
- Semaphores
- Message Passing
- Shared Memory
- Asynch and Synch I/O
- Memory Locking Interface


**POSIX.1c**    
POSIX.1c, Threads extensions (IEEE Std 1003.1c-1995)     
- Thread Creation, Control, and Cleanup
- Thread Scheduling
- Thread Synchronization
- Signal Handling

**POSIX.2**    
POSIX.2, Shell and Utilities (IEEE Std 1003.2-1992)   
- Command Interpreter
- Utility Programs
