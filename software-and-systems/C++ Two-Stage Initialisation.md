In C++, it is common to use constructor which looks like:
```C++
class Example : IExample 
{
public:
	Example() = default();    // Constructor	

private:
	char str_array[10];
	std::function<void(const char *)> cb;
}
```

This signature just says, you can create an `example` object by passing in no parameter. It will just get the compiler to implicitly define a constructor. So in that case, for the char array member, it will allocate an array of 10 bytes, but uninitialised. The content will be random. Then for the function pointer will be default-initialised to an empty state. Calling this without assigning it to an actual function would results in `std::bad_function_call` 
This is usually fine, and probably standard for C, but in C++, we want to utilise the constructor beyond just the implicit constructor. 

## Constructor Initialisation
The limitation with this implicit constructor is that it will do the same thing regardless of what is strictly required. For example, what if we know what the `example` object needs to hold, so we want to do
```C++
Example e{'Hello World'};
```
To initialise the char array and skip a step, because it can be error prone. We are relying on the developers to make sure they remember to populate this `str_array`, even if the content is known at compile time. 

So what if we do something like this?
```C++
class Example : public IExample
{
public:
    explicit Example(const char* msg)
    {
        std::strncpy(str_array, msg, sizeof(str_array) - 1);
        str_array[sizeof(str_array) - 1] = '\0';
    }
    // ...
};

// Example e = "Hello World";
// Note the explicit keyword here bans the implicit uses of the constructor. For example, we may have the constructor above, which may in turn call the constructor we have defined.
// By calling it explicit, we are telling the compiler, this will only be invoked iff Example e{'Hello World'};
```

## The problem: what happens when construction fails?
The problem then is, what if during this constructor, the passed parameter is incompatible? What if it is longer than 10 bytes, what if we fail to allocate memory etc? Actually maybe this case is fine, it's a simple memory copy, but what if we were calling some functions in the constructor that could fail? 

Constructor by design, when it encounters an error, will throw an **exception**. There's no return code to check - by the time the caller gets the object, it either exists in a valid state or doesn't exist at all. Using exception violates the **Real-Time principle** because handling an exception involves **stack unwinding**, runtime table lookups which may have non-deterministic latency. 

That's why we do **Two-Stage Initialisation**. Essentially, we put all the safe stuff to constructor, such as simple memory copy, then do the problematic part in **init()**. 
```C++
class Example : public IExample
{
public:
    explicit Example(const char* msg) noexcept
    {
        // Only work that genuinely cannot fail.
        std::strncpy(str_array, msg, sizeof(str_array) - 1);
        str_array[sizeof(str_array) - 1] = '\0';
        initialised_ = false;
    }

    [[nodiscard]] bool init()
    {
        // Fallible work goes here: allocations, file/device opens, etc.
        // Return false (or a richer error type) on failure.
        initialised_ = true;
        return true;
    }

private:
    char str_array[10];
    std::function<void(const char*)> callback;
    bool initialised_{false};
};
```

Here we can also declare `noexcept` in the constructor. Doing so, we promise the compiler that this section of the code will never throw exception, and if it does, the program will **terminate** abruptly, not producing any error logs. To be fair, there is a small compiler optimisation gain to be had from this, but marking this as `noexcept` means any future developer will understand this two stage initialisation paradigm.  
