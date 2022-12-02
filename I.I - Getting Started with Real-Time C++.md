# I.I Getting Started with Real-Time C++

## Terminology
- **ctor** --> Constructor of a class

### Class Types
- **Classes, Structs, and Unions** are all class types in C++
- **Members** are the data, functions, and operators in a class type

#### Constructors
- Can have any number of input parameters
- We also have a **Constructor initialization list**, which initializes private member variables

```cpp
class led 
{
    public:
        // Constructor initialization list after the function
        led(const port_type p, const bval_type b): port(p), bval(b)
        {
            /* The above "port(p)" is essentially just "port = p;" */
        }

    private:
        const port_type port;
        const bval_type bval;
}
```

#### Program Layout Basics
- **must** be initialized in the ctor
- **can** be initialized in the constructor init list

```cpp
// led.h
class led
{
    public:
        led(const port_type p, const bval_type b);
        void toggle() const;
}

// led.c
#include "led.h"

led::led(const port_type p, const bval_type b) : port(p), bval(b)
{
    // Code here
}

void led::toggle() const
{
    // Code here
}
```

- In the above example, we define the method functionality by referencing the led **namespace** like in the form `led::toggle`
- **::** is a *scope resolution operator* --> Resolves class names

##### Inlining
Inlining can improve *optimization*, essentially, it means delcaring it all in the same file

### Members
- In the above example, a member function exists called `toggle()`

```cpp
void toggle() const
{
    // Toggle the led
}
```
The **const** after the function means it does not change any member variable
- It can only modify variables with the keyword **mutable**

#### Access Controls
```cpp
class led
{
    public: // PUT THESE FIRST
        // Essentially the 'UI', accessed by **any** part of the program

    protected: // PUT THIS SECOND
        // Can be used via inheritance.

    private: // PUT THIS LAST
        // Only accessed by class and 'friends'
}
```
Defaults:
- **public** --> structures
- **private** --> classes

### Includes
```cpp
// <...> usage for files in compiler's default includes path
#include <cstdint>
// "..." User-defined includes files
#include "mcal_reg.h"
```

### Namespaces
- Collection of related symbol names

```cpp
#include <stdint.h>

namespace std
{
    // Add ::uint8_t into namespace, so you can call std::uint8_t
    using ::uint8_t;
}
```

```cpp
// some_random_space.cpp
namespace some_random_space
{
    constexpr int version = 1;
}

// Some other file
int x = some_random_space::version; // x = 1
```

#### Anonymous Namespace
```cpp
namespace
{
    // This has no name, so it is anonymous
    // Limits scope of **everything** to within this file
}
```
- Anonymous namespaces are the **preferred way to localize members to a file**
- This is a better way to do it than in C (with *static*)

#### Adding Namespaces
- You can use `using` in order to add a namespace without having to scope (::) attributes

```cpp
// some_random_file.cpp
#include <cstdint>

using namespace std;

uint8_t some_int; // No need for (std::)
```

- You can also do **using std::uint8_t;** to include only one type.

#### C++ Standard Library
- Standard Library Template (SLT) --> Containers and algorithms in **std**

### Main() Subroutine
```cpp
int main()
{
    for(;;) // Infinite loop
    {
        led_light.toggle();
    }
}
```

- **int** just means **signed int** in C++
- Automatic generation of return code of **0** if no value is returned

#### Main with args

```cpp
// There are two supported definitions of main()

// Definition #1 (No command line args)
int main()
{
    // THIS ONE IS USED FOR MICROCONTROLLERS WITHOUT CLI
    // Returns 0 by default if no return code.
}

// Definition #2 (Command line args)
int main(int argc, char* argv[])
{
    // Returns 0 by default if no return code.
}
```

### Low-Level Register Access
- Interaction with hardware requires direct memory access through manipulation of **registers**

#### Cast operators in C++
##### reinterpret_cast
- used for casting types to pointers and back
- uncommon in C++, should be used rarely
- required for microcontroller register manipulation

```cpp
void toggle() const
{
    // Toggle the led
    *reinterpret_cast<volatile bval_type*>(port) ^= bval;
}
```

### Compile Time Constant
- **constexpr** --> Constant expression **guaranteed** to be compile-time constant
- Superior to *#define* because have clear type information
- Can initialize led_b5 without using stack --> Called **constant folding**

```cpp
namespace mcal
{
    namespace reg
    {
        // Define address of portb
        constexpr std::uint8_t portb = 0x25U;

    }
}
```