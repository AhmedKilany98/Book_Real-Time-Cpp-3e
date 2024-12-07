# Real Time C++ - Chapter 1:

1.1  The LED Program & 1.2 The Syntax of C++
------------------------------------------
```cpp
// The LED program.
#include <cstdint>
#include "mcal_reg.h"
class led
{
public:
    
    // Use convenient class-specific typedefs.
    typedef std::uint8_t port_type;
    typedef std::uint8_t bval_type;
    // The led class constructor.
    led(const port_type p,const bval_type b) : port(p),bval(b)
    {
        // Set the port pin value to low.
        *reinterpret_cast<volatile bval_type*>(port) &= static_cast<bval_type>(~bval);
        // Set the port pin direction to output.
        // Note that the address of the port direction
        // register is one less than the address
        // of the port value register.
        const port_type pdir = port - 1U;
        *reinterpret_cast<volatile bval_type*>(pdir) |= bval;
    }
    void toggle() const
    {
        // Toggle the LED via direct memory access.
        *reinterpret_cast<volatile bval_type*>(port) ^= bval;
    }
private:
    // Private member variables of the class.
    const port_type port;
    const bval_type bval;
};

namespace
{
    // Create led_b5 on portb.5.
    const led led_b5
    {
        mcal::reg::portb,
        mcal::reg::bval5
    };
}

int main()
{
    // Toggle led_b5 in a loop forever.
    for(;;)
    {
        led_b5.toggle();
    }
}
```
1.3 Class Types
------------------------------------------
- Constructor initialization list is used to initialize const member variable.
- inline keyword automatically is used through declaration and definition of function in the same header file.

1.4 Members
------------------------------------------
- `const` qualifier means that `void toggle() const {}` is a constant member function. A constant member function is not usually intended to alter the state of any class member variables. 
- A constant function can, however, modify class member variables that are qualified with the keyword `mutable`. Here, `mutable` means capable of being changed.
- The `public` members of a class constitute its user interface because they can be
accessed by any part of the program.
-  `Private` members can only be accessed by the class itself and its friends. Private members make it possible to hide selected data and implementation details when desired.
- `Protected` members are useful for code re-use via inheritance in class hierarchies.Class inheritance is also subject to access control.
- If left unspecified, the default levels of member access and inheritance are `private`
for `classes` and `public` for `structures`. This is the only non-stylistic difference between classes and structures in C++.

1.5 Objects and Instances
------------------------------------------
- In object-oriented programming, object is often used interchangeably with instance of a class. 

1.6 #include
------------------------------------------

- #include Files <cstdio> used for files that are in the compiler’s default include paths.
- #include Files "cstdio.h" used for user-defined files that are not in the compiler’s default include paths.

1.7 Namespaces
------------------------------------------
- `Namespace` is a collection of related symbol names.
- A **unnamed namespace** is called an anonymous namespace. An anonymous namespace limits the scope of anything within itself to file-level. A file-local anonymous namespace guarantees unique names for otherwise same-named symbols occurring in different files. The anonymous namespace may be considered superior to C-style static. In fact, some developers consider the anonymous namespace to be the preferred mechanism for file-level scope localization and reduction of naming ambiguity in C++ projects

1.8 C++ Standard Library
------------------------------------------
- The namespace std contains all the symbols in the C++ standard library.
-  The standard library also contains an extensive set of generic containers and algorithms called the standard template library (STL).

1.9  The main() Subroutine
------------------------------------------
- The `main()` subroutine is called from the startup code after the static initialization mechanisms for RAM and static constructors have been carried out.

- The second form is used when program arguments are passed to main(). For our embedded microcontroller programs, no arguments are passed to main() and the first form is used.
    ```cpp
    int main()
    {
        //... First Form
    }
    //----------------------------
    int main(int argc, char* argv[])
    {
        //... Second Form
    }
    ```


1.10 Low-Level Register Access
------------------------------------------
- The **reinterpret_cast** operator is the one that is designed for
casting integral types to pointers and back.
```cpp
    // C++ register access.
    *reinterpret_cast<volatile uint8_t*>(port) ^= bval;
    // Equivalent C-style.
    *((volatile uint8_t*) port) ^= bval;
```

1.11 Compile-Time Constant
------------------------------------------
-  the keyword **constexpr**, is guaranteed to be a compile-time constant. In general, using constexpr is considered superior to the preprocessor `#define` because generalized constant expressions have clearly defined type information.
- the compiler can directly initialize led_b5’s member variables without using the stack or intermediate CPU registers. This efficient kind of optimization is called constant
folding, and is often useful in real-time C++ programming.