---
layout: page
title: Directory Hierarchy and Filename Conventions
permalink: /Style/Hierarchy
---
## Filenames
Files specifying a namespace, a class definition, or a function should share the name of the namespace, class, or function, respectively.

While there is little advantage to using file extension convention over another, the convention should be used consistently throughout all NJOY21 projects. NJOY21 projects use the following conventions:

### C

- Header files must use the `.h` suffix
- Source files must use the `.c` suffix

### C\+\+

- Header files must use the `.hpp` suffix
- Source files must use the `.cpp` suffix

### Python
All Python source file must use the `.py` suffix.

### Fortran
All Fortran source files must use the `.f90` suffix.

## Distribution of Source Code between Source Files
NJOY21 has a fine-grained distribution of source code between source files.

- For each namespace defined as part of a project, a header file is provided containing a forward declaration of each class and function immediately enclosed within the namespace (as opposed to classes and functions enclosed within a nested namespace). In addition, namespace headers should also provide:
	- definitions for any constants immediately enclosed in the namespace
	- a faux forward declaration of any nested namespace
- For each class defined as part of a project, a header file should be provided specifying any class fields and a function signature for any class methods.
- Functions requiring greater than 1 logical line of code to define should not be implemented in the header file, if possible. Projects/libraries that are header-only are exempt from this as it doesn't really make sense. 
- For each function **name** within an *effective* namespace (a class acts as an effective namespace for its fields and methods), an implementation file (i.e., `.cpp`) is provided. Projects/libraries that are header-only are exempt from this as it doesn't really make sense. 
- For each **name** within an *effective* namespace, a corresponding implementation file (i.e., `.cpp`) is provided with unit tests for each function of that name. For naming conventions for these files, see the [Directory Structure] section below.

Distributing source code in this fashion offers a number of advantages, including:

- Small files require less effort to navigate through and reason about
- Filenames are more helpful when searching for relevant source code when problems or questions arise
- As a corollary, the project is more easily maintained
- A reduced likelihood that conflict resolution will be required when merging between version control branches
- Calls to `make`:
	- Can exploit a higher degree of parallelism during the initial build 
	- Compile the minimum amount of source necessary to accommodate a revision, reducing build time overhead associated with test-driven development.

For instructions on avoiding filename collisions while conforming to this specification, see the [Directory Structure](#directory-structure) section below.

## Directory Structure
The NJOY21 style dictates a directory structure that’s a bit atypical. The goal of this specification is to provide a structure convention that:

1. Reflects the structure of the source code (in combination with the specified file granularity, specified above);
2. Acts (as a corollary to 1) as a form of documentation that automatically remains in sync with the source; and
3. Allows for subsets of projects to be easily extracted and external projects to be easily composed, promoting code reuse.

Starting with the project-level namespace and head directory,


- For each *effective* namespace defined as part of a project, if implementation files or (in the case of *effective* namespaces corresponding to classes) child classes need be specified, a corresponding directory should be created, sharing the namespace name.

  This directory should contain, the following two subdirectories:

  1. `src`—Contains any implementation (i.e., `.cpp` and `.hpp` ) files corresponding to immediately enclosed functions.
  2. `test`—Contains:
     1. Implementation files providing tests for each immediately enclosed function. Files are named according to the convention `functionName.test.cpp`.
     2. An implementation file that:
        - Is named according to the convention `namespace.test.cpp`.
        - Provides a routine to drive the unit tests provided in the other implementation files in the directory.

- If this *effective* namespace corresponds to a C\+\+ class, (which we’ll refer to as **CC**)
  - Header files for any immediate child classes (but not grandchildren) *residing within the same C\+\+ namespaces* are found in the **CC** directory
  - Directories for any immediate child classes *residing within the same C\+\+ namespaces* are found in the **CC** directory

- If this *effective* namespace corresponds to a C\+\+ namespace, (which we’ll refer to as **NS**)
  - Header files for any immediately enclosed C\+\+ namespaces are found in the **NS** directory
  - Directories for any immediately enclosed C\+\+ namespaces are found in the **NS** directory
  - Header files for each class immediately enclosed by the C\+\+ namespace not addressed by the **CC** conventions are found in the **NS** directory. (Classes without a parent class or who’s immediate parent class residing in another namespace fall into this category.)
  - Directories for each class immediately enclosed by the C\+\+ namespace not addressed by the **CC** conventions should be found in the **NS** directory.

The project namespace header should be placed in a directory called `src/` at the top-level of the project. Inside this directory should be:

 1. Project header file---called `projectName.hpp`.
 2. Project directory---called `projectName/`.

All other code should reside in the `src/projectName/` directory.

All external dependencies are contained in the `dependencies/` directory as a sister directory to `src/`.

- All source code (including headers) are contained in a `src/` directory at the top-level of the project. This directory should contain a header file and directory of the same name.
- Source code for any projects maintained independently of, but referenced by the project in question should reside in their own directory within the highest level project directory.

### Example
Often, use examples are more helpful when learning a convention. Consider the (abridged and annotated) file structure tree of the NJOY21 project, which was designed primarily in an object-oriented fashion:

```
NJOY21/                               /* Top-level directory */
├─── CMakeLists.txt
├─── dependencies/                    /* Directory of external dependencies */
│ ├─── dimwits/ ...
│ ├─── Log/ ...
│ ├─── tclap─adapter/ ...
│ ├─── utility/ ...
├─── metaconfigure/ ...               /* git subtree for build configuration */
├─── src/                             /* all code for this project goes here */
│ ├─── njoy21/                        /* top-level namespace */
│ │ ├─── input/                       /* nested namespace */
│ │ │ ├─── ACER/ ...                  /* class */
│ │ │ ├─── BROADR/                    /* class */
│ │ │ │ ├─── Card1/                   /* nested class */
│ │ │ │ │ ├─── Nendf/                 /* nested class */
│ │ │ │ │ │ ├─── test/                /* testing directory */
│ │ │ │ │ │ │ ├─── CMakeLists.txt     /* Configuration by metaconfigure */
│ │ │ │ │ │ │ ├─── Nendf.test.cpp     /* Catch test file */
│ │ │ │ │ ├─── Nendf.hpp              /* nested class definition header */
          ...                         /* Additional nested classes */
│ │ │ │ │ ├─── test/                  /* Nested class test directory */
│ │ │ │ │ │ ├─── Card1.test.cpp
│ │ │ │ │ │ ├─── CMakeLists.txt
│ │ │ │ ├─── Card1.hpp
│ │ │ │ ├─── Card2/ 
        ...
│ │ │ │ ├─── test/
│ │ │ │ │ ├─── BROADR.test.cpp
│ │ │ │ │ ├─── CMakeLists.txt
│ │ │ ├─── BROADR.hpp                 /* class definition header */
│ │ │ ├─── MODER/ ...                 /* additional classes and definitions */
│ │ │ ├─── MODER.hpp
│ │ │ ├─── PURR/ ...
│ │ │ ├─── PURR.hpp
│ │ │ ├─── RECONR/ ...
│ │ │ ├─── RECONR.hpp
│ │ │ ├─── UNRESR/ ...
│ │ │ ├─── UNRESR.hpp
│ │ ├─── input.hpp                    /* nested namespace header */
│ ├─── njoy21.hpp                     /* top-level namespace header */
```

