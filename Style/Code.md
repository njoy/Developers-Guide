---
layout: page
title: Coding Style
permalink: /Style/Code
---
# C++ Source Formatting and Style

## 1 Line Length
1. Source code lines should be less than 81 characters

**Exceptions:**

   - `#include` statements
   - header guards
   - comment lines containing a literal URL

## 2 Whitespace
1. Unix-style linebreaks ('\n'), not Windows-style ('\r\n')
1. Use only spaces for indentation, and indent 2 spaces at a time
1. Never use tab characters
1. Contents of each new variable scope should be subject to an additional level of indentation  

    **Exception:**

     - scopes resulting from namespace declaration

1. Prefer to include a space following opening parenthetical characters and preceeding closing parenthetical characters, respectively, e.g.

   ```cpp
   template< typename T > void print( T t ){ std::cout << t << std::endl; }
   ```

1. Prefer to break before binary operator for statements spanning multiple lines, e.g.

   ```cpp
   if ( reallyLongVariableName
        && otherLongVariableName ){
   }
   ```
     
## 3 Control structures
1. Prefer [1TBS]: left brace at end of first line, cuddle else on both sides
1. Always brace controlled statements, even a single-line consequent of an `if`, `else if`, or `else`

**Example**

```cpp
if (...) {
} else if (...) {
} else {
}

if (...){ /* single line */ }

while (...) {
}

do {
} while (...);

for (...; ...; ...) {
}

```

## 4 Naming Conventions
1. Prefer `UpperCamelCase` when naming classes, structs, and enumeration classes
1. Prefer `lowerCamelCase` when naming variables, functions, and namespaces
1. Prefer `SCREAMCASE` when naming preprocessor constants and macros  
1. Avoid names referencing equation variables, preferring more descriptive names, e.g.  

   ```cpp
   double mu /* bad */, angleCosine /* good */;
   double sigmaTotal /* bad */, totalCrossSection /* good */;
   ```


**Exceptions:**
   - prefer capital letters for abbreviations in names, falling back to standard conventions e.g. `myENDFParser`
   - for variables refer to quantities specified in external documentation, prefer the conventional capitalization scheme, e.g. `double QValue `

## 5 Documentation
1. Classes, structs, and functions are to be annotated for the Doxygen documentation generation tool
1. Avoid providing examples embedded in comments and/or annotations, preferring examples in the form of unit tests
1. Prefer the [Javadoc] format for source annotations, e.g.

   ```cpp
   /** @brief An ordered sequence of signed integers */
   class IntegerList;
   ```

1. When forward declaring a class, struct, or function, annotate the forward declaration, but with the brief only
   - do not repeat the brief in the class, struct, or function definition


1. Annotations aside, comments should not be used to describe what code does or how it works
   - prefer literate, self-documenting source code


1. When faced with an algorithm or design choice, comments preserving relevant infomation and reasoning are welcome, especially under circumstances when the choice might be counter-intuitive e.g.

   ```cpp
   /* A binary search turned out to be slower than the Boyer-Moore algorithm for
    * the data sets of interest, thus we have used the more complex, but faster
    * method even though this problem does not at first seem amenable to a string
    * search technique.
    */
   ```

## 6 Class Conventions
1. `public`, `private`, and `protected` are not subject to indentation beyond the `class` keyword
1. Use name overloading when defining getter and setter methods, e.g.  

   ```cpp
   class CrossSection {
   public:
     /* good */
     const std::vector<double>&
     energies const ();
   
     /* bad */
     /* const std::vector<double>&
      * getEnergies const ();
      */
     
     /* good */
     void
     energies( std::vector<double>&& E );
     
     /* bad */
     /* void
      * setEnergies( std::vector<double>&& E );
      */
   };
   ```

1. Prefer to access non-static class and struct member variables and methods through the `this` pointer, e.g.

   ```cpp
   double
   MyClass::method( int i ) const {
     const auto foo = this->otherMethod( i );
     return foo * this->fieldVariable;
   }
   ```

## 7 Type Sigils
1. Prefer to snuggle type sigil to the base type rather than the variable name in variable declarations, e.g.

   ```cpp
   T* p; /* good */
   T& p; /* good */
   T *p; /* bad */
   T* p, q; /* OOPS put these on separate lines */
   ```

## 8 Miscellaneous
1. Prefer `nullptr` to `Null` or `0` for pointers
1. When testing a pointer, prefer `(!myPtr)` or `(myPtr)`; don't use `myPtr != nullptr` or `myPtr == nullptr`
1. When testing a boolean value, avoid `(x == true)` or `(x == false)`. Use `(x)` or `(!x)` instead
1. When specifying header guards, begin with the fully-qualified include path within the respective project, move to `SCREAMCASE`, then substitute `_` for `/`, `.`, and `-`, e.g.

   ```cpp
   /* acetk/implementation/ACETable.hpp */
   #ifndef ACETK_IMPLEMENTATION_ACETABLE_HPP
   #define ACETK_IMPLEMENTATION_ACETABLE_HPP

   #endif
   ```
[Javadoc]: https://en.wikipedia.org/wiki/Javadoc
[1TBS]: https://en.wikipedia.org/wiki/Indent_style#Variant:_1TBS
