## Don't Repeat Yourself (DRY)
The DRY principle (Don’t Repeat Yourself) is one of the most fundamental best practices in Object-Oriented Programming (OOP), especially in modern C++. This principle emphasizes reducing code duplication to improve code maintainability, clarity, and efficiency. By following the DRY principle, C++ developers can write more readable, maintainable, and reusable code while minimizing the risk of bugs and future errors.

## The one definition rule (ODR)
1. Within a _file_, each function, variable, type, or template in a given scope can only have one definition. Definitions occurring in different scopes (e.g. local variables defined inside different functions, or functions defined inside different namespaces) do not violate this rule.
2. Within a _program_, each function or variable in a given scope can only have one definition. This rule exists because programs can have more than one file (we’ll cover this in the next lesson). Functions and variables not visible to the linker are excluded from this rule.
3. Types, templates, inline functions, and inline variables are allowed to have duplicate definitions in different files, so long as each definition is identical.

## Angled brackets vs double quotes
Use double quotes to include header files that you’ve written or are expected to be found in the current directory. Use angled brackets to include headers that come with your compiler, OS, or third-party libraries you’ve installed elsewhere on your system.

## The order of inclusion for header files
To maximize the chance that missing includes will be flagged by compiler, order your `#includes` as follows (skipping any that are not relevant):

- The paired header file for this code file (e.g. `add.cpp` should `#include "add.h"`)
- Other headers from the same project (e.g. `#include "mymath.h"`)
- 3rd party library headers (e.g. `#include <boost/tuple/tuple.hpp>`)
- Standard library headers (e.g. `#include <iostream>`)

The headers for each grouping should be sorted alphabetically (unless the documentation for a 3rd party library instructs you to do otherwise).

## Use Header Guards
```cpp
#ifndef SOME_UNIQUE_NAME_HERE
#define SOME_UNIQUE_NAME_HERE

// your declarations (and certain types of definitions) here

#endif
```
When this header is `#included`, the preprocessor will check whether _SOME_UNIQUE_NAME_HERE_ has been previously defined in this translation unit. If this is the first time we’re including the header, _SOME_UNIQUE_NAME_HERE_ will not have been defined. Consequently, it `#defines` _SOME_UNIQUE_NAME_HERE_ and includes the contents of the file. If the header is included again into the same file, _SOME_UNIQUE_NAME_HERE_ will already have been defined from the first time the contents of the header were included, and the contents of the header will be ignored (thanks to the `#ifndef`).

All of your header files should have header guards on them. _SOME_UNIQUE_NAME_HERE_ can be any name you want, but by convention is set to the full filename of the header file, typed in all caps, using underscores for spaces or punctuation.

## L-value & R-value references
- **L-value reference (Locator value):**
	Prior to C++11, only one type of reference existed in C++, and so it was just called a “reference”. However, in C++11, it’s called an l-value reference. L-value references can only be initialized with modifiable l-values.
- **R-value reference (Right-hand value):**
	C++11 adds a new type of reference called an r-value reference. An r-value reference is a reference that is designed to be initialized with an r-value (only). R-value references have two properties that are useful. First, r-value references extend the lifespan of the object they are initialized with to the lifespan of the r-value reference (l-value references to const objects can do this too). Second, non-const r-value references allow you to modify the r-value!

 While an l-value reference is created using a single ampersand, an r-value reference is created using a double ampersand:
```cpp
int x{ 5 };
int& lref{ x }; // l-value reference initialized with l-value x
int&& rref{ 5 }; // r-value reference initialized with r-value 5
```
## RAII (Resource Acquisition Is Initialization)
RAII (Resource Acquisition Is Initialization) is a programming technique whereby resource use is tied to the lifetime of objects with automatic duration (e.g. non-dynamically allocated objects). In C++, RAII is implemented via classes with constructors and destructors. A resource (such as memory, a file or database handle, etc…) is typically acquired in the object’s constructor (though it can be acquired after the object is created if that makes sense). That resource can then be used while the object is alive. The resource is released in the destructor, when the object is destroyed. The primary advantage of RAII is that it helps prevent resource leaks (e.g. memory not being deallocated) as all resource-holding objects are cleaned up automatically.

## Move Semantics
Transfer ownership of the object rather than making a copy:

If we construct an object or do an assignment where the argument is an l-value, the only thing we can reasonably do is copy the l-value. We can’t assume it’s safe to alter the l-value, because it may be used again later in the program. If we have an expression “a = b” (where b is an lvalue), we wouldn’t reasonably expect b to be changed in any way.
However, if we construct an object or do an assignment where the argument is an r-value, then we know that r-value is just a temporary object of some kind. Instead of copying it (which can be expensive), we can simply transfer its resources (which is cheap) to the object we’re constructing or assigning. This is safe to do because the temporary will be destroyed at the end of the expression anyway, so we know it will never be used again!

C++11, through r-value references, gives us the ability to provide different behaviors when the argument is an r-value vs an l-value, enabling us to make smarter and more efficient decisions about how our objects should behave.

## Rule of Five
If the copy constructor, copy assignment, move constructor, move assignment, or destructor are defined or deleted, then each of those functions should be defined or deleted.