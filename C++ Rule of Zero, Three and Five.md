https://en.cppreference.com/cpp/language/rule_of_three
In C++, when we use operators of a class with different parameters and the operators, not having a user-defined operators will mean the compiler will implicitly define the operators, which may not be the behaviour we want, and it may result in dangling pointers or segmentation faults.

The compiler defined, implicit functions should not be used if the class **manages a resource whose handle is an object of non-class type**. The implicit destructor does nothing, copy constructor and assignment operator performs a "shallow copy". Shallow copy is a problem for non-class type, which are just primitive types built into the language, such as **raw pointers** and **OS handles**, which the compiler doesn't know about the actual content of it beyond the object itself.
For example, if we declare a buffer:
```C++
int* buffer = new num[100];
```
To the compiler, num is just a pointer, memory address. So if one deletes this object, it does not delete the 100 ints allocated in the heap. Because this is a non-class type, it doesn't have a destructor, so if we shallow-copy this or delete this, the compiler will have no idea about the underlying buffer in the heap.  

## Rule of Zero
Overarching principle of software development - Single Responsibility Principle says the code should be separated into two distinct types of classes:
1. Resource managers - the ones that just contain data. This is the class that needs to implement rule of three and five, because they manage the raw data.
2. Business logic - classes that are **made up of STL containers or custom resource managers**, that have all the resource management stuff sorted out. For business logic classes, they can implement **Rule of Zero**, which write classes with **zero** custom memory-management functions.

## Rule of Three
Rule of Three applies if the class requires a user defined constructor, copy constructor and copy assignment operator. 
```C++
class example
{
	char* cstring; // raw pointers, object of non-class
	
public:
    explicit example(const char* s = "") : cstring(nullptr)
    {   
        if (s)
        {   
            cstring = new char[std::strlen(s) + 1]; // allocate
            std::strcpy(cstring, s); // populate
        }
    }
	~example()     // rule 1: destructor
	{
		delete[] cstring;  // Explicitly deallocate the heap
	}
	
	example(const example& other) // rule 2: Copy constructor
		: example(other.cstring) {}
	
	example& operator=(const example& other) // rule 3: Copy assignment
	{
		example temp(other);
		std::swap(cstring, temp.cstring);
		return *this
	}
}
```
### Rule 1: Destructor
The destructor explicitly deallocates the memory in heap. 
### Rule 2: Copy constructor
This is when the object is constructed with a reference to an existing object. The implicit behaviour for the compiler is to shallow copy this bit by bit. Of course this will not copy the content in the buffer. So when a copy constructor is invoked, we trigger the explicit constructor above, which defines the case when char pointer is passed as a parameter, in which case it will correctly allocate the buffer and copy the buffer. So the copy constructor becomes a wrapper for invoking the default constructor. 
### Rule 3: Copy assignment
Copy assignment is when you simply do a = b;. The implication is that a already exists and we are trying to copy the content of b. But in doing so, without this explicit definition, it will shallow copy the cstring, just copying the pointer address without copying the underlying buffer. Here we see that the copy assignment first invokes the copy constructor, which creates a deep copy to a temporary object, then swap the buffer and pointer. So *this* gets the deep copy of the *other*'s buffer (from copy constructor), the temp object ends up having a's obsolete buffer, then gets destroyed, deallocating the buffer at the end of the function.
## Rule of Five


# Polymorphism
When you create a class with at least one virtual function, the compiler constructs **vtable** for that class, then injects a `__vptr` into the physical memory of the instantiated object.  Then when you have a class which derives from that class, the compiler creates another **vtable**, copies the base vtable, and overwriting the overriden functions. 

During runtime, when you invoke a virtual function of a class, the following sequence of events happen. For example, invoking a Derived::init():
1. Look up the vtable of the derived class
2. Find the memory address of the init() function. 
3. Jump the execution thread to that address.

This sequence results in slight performance overhead. 
## Virtual Destructor
**Any base class meant to be inherited from MUST have a virtual destructor**
The reason is that when you invoke the destructor like `delete base_ptr;`, we want the compiler to look up the vtable. Because due to polymorphism, this `base_ptr` could be a `derived_ptr` in disguise. In such case, if the destructor was not declared as a virtual function, the compiler will just use the destructor for the base class, completely ignoring the `derived_ptr`'s custom destructor to free memory properly. . This could leave the members specific to the derived class will never be freed.

## Implications of the Virtual Destructor
As soon as you declare a virtual destructor, the compiler will behave differently, according to the table below:
![[Pasted image 20260505115855.png]]

Declaring a destructor means move operators (constructor and assignment) are not declared. Note this does not mean they cannot be called. It simply means it will be silently replaced by a copy operation. Now this is probably not the intended behaviour. This is part of the reason Rule of Five must be implemented for polymorphic classes, especially the derived class. 

## Practical example
```C++
class IBase 
{
public:
	// This is essential!! Otherwise never fully clean
	virtual ~IBase() = default;

	IBase(const IBase&) = delete;   // Copy Constructor banned
	IBase(IBase&&) = delete; // Move Constructor banned
	IBase& operator=(const IBase&) = delete; // Copy Assignment banned
	IBase& operator=(IBase &&) = delete; // Move Assignment banned
	
private:
protected:
	IBase() = default;
};
```
Here **all four move/copy operators are banned**, trying to perform move/copy on the base class will destroy the object. Because this is a base class, we never want to allow copy and move. This is because it may result in copying a derived object to a base object, which will cause slicing and vptr table corrupted. 