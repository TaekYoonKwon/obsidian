Multi-threading in C++ involves using `std::thread` to execute multiple paths of code concurrently. In the context of complex systems like a guidance module, utilizing dedicated threads allows continuous, background operations - such as iterative optimization routines or warm-starting the solver, to run independently without blocking the main application logic. This document serves as a living workspace to track the fundamentals of C++ threading, the nuances of managing thread lifecycles within class architectures, and the strict synchronization principles required to ensure these concurrent operations remain real-time safe.

# Core Concept: How std::thread actually works
Under the hood, `std::thread` is not a representation of an actual thread, but just a thin wrapper, which manages the lifecycle of the underlying thread engine which are running at the OS level. For POSIX, it uses **pthreads**. 
Because `std::thread` object is just a wrapper, it is bind to strict rules. [[C++ Runtime]] will terminate the program, if the `std::thread` object goes out of scope while the actual pthread instance that it spawned is still running. We must either wait for the thread to finish or detach the thread before the `std::thread` object goes out of scope or destroyed.

## Basic Example
```C++
int main()
{
	std::cout<<"Hello World\n";
}
```
This is a standard C++ Hello World. If we were to make this multithreaded:
```C++
#include <iostream>
#include <thread>
void hello()
{
	std::cout<<"Hello Concurrent World\n";
}
int main()
{
	std::thread t(hello);
	t.join();
}
```

- Every thread needs to have an **initial function**. This also applies to the main thread in which this application is running. The initial function for the main application is *int main*. 
- When you create a new thread, you specify this initial function as part of the constructor. 
- Creating the thread object effectively launches the thread, request OS or the underlying low level API specific to the OS to create a new thread, and run the initial function.
- Joining the thread is required here because that's how we specify the application to wait for the thread execution to finish. Without this join function, the main application will launch the thread (which may take longer) then just exits, possibly before the thread even had a chance to run.
#### Object as a function
The only requirement for C++ thread library to create a thread and run it is that the constructor parameter is callable. So it is also possible that you pass in an object, as long as that object is callable like so:
```C++
class hello_bg
{
public:
void operator () () const
{
	std::cout << "Hello Concurrent world from hello_bg class\n" << std::endl;
}
};

int main()
{
	hello_bg bg_task;
	std::thread t{bg_task}; 
	// Note the {} for invoking the constructor. Using () also works but the compiler may get confused if the parameter is not a named variable but temporary. 
	t.join();
}
```
> [!WARNING]  When you create a C++ thread, the supplied function object (in this case, a hello_bg object) gets copied to the storage which belongs to the thread, and gets executed. Therefore the copy must behave equivalently to the original, or it will not work as intended.

### Decide the Lifecycle of the thread
After the thread has been created, it is essential that we pick the strategy with the thread before the thread object is destroyed - we must either **detach (let it run)** or **join (wait)**. 
> Note the thread may have finished executing already when we join or detach it, since we just need to do either before the thread object goes out of scope.
 
## The Thread Lifecycle - execution, joining and detaching
When we write something like `std::thread my_thread(func)`, the sequence that it runs is as follows:
1. System call
   The C++ STL requests that the OS creates a new thread. 
2. 
### Class Level Threading

# Synchronisation and Data Sharing

# Achieving Real Time Safety
