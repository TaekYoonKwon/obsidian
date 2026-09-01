In C++ Inheritance, it could be public, private and protected inheritance.

By default, classes derivation is private and struct derivation is public. So it is always good to be explicit about which inheritance we want to impose.

```C++
class A
{
public:
    int x;
private:
	int y;
protected:
	int z;
};

// Below illustrates equivalent structs
class B : public A 
{
public:
	int x;
protected:
	int z;
private:
	// int y; - unavailable.
};

class C : protected A
{
public:

protected:
	int x;
	int z;
private:
	// int y; -unavailable.
};

class D : private A
{
public:

protected:

private:
	int x;
	int z;
	// int y; - unavailable	
};
```

## Rule of thumb
While different derivation strategies are available, below is the rule of thumb. 
1. In the base class, declare as public, everything that outside may want to know about.
2. In the base class, declare as protected, everything that the derived class may want to know, but not the outside. 
3. In the base class, declare as private, only the base class should know. Potentially housekeeping stuff that the derived classes should not know about.
4. In inheritance, it often makes sense for the derived class to use public. This keeps the intention clear and consistent with the base class. Unless there is a very good reason why the accessibility must change, keep it as public.