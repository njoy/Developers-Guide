---
layout: page
title: Directory Hierarchy and Filename Conventions
permalink: /Style/Hierarchy
---
## Filenames
Files specifying a namespace, a class definition, or a function should share the name of the namespace, class, or function, respectively.

While there is little advantage to using file extension convention over another, the convention should be used consistently throughout all NJOY21 projects. NJOY21 projects use the following conventions:

### C\+\+

- Header files should use the `.hpp` suffix
- Source files should use the `.cpp` suffix

### Python
Use the `.py` suffix.

## Distribution of Source Code between Source Files
NJOY21 has a fine-grained distribution of source code between source files.

- For each namespace defined as part of a project, a header file is provided containing a forward declaration of each class and function immediately enclosed within the namespace (as opposed to classes and functions enclosed within a nested namespace). In addition, namespace headers should also provide:
	- definitions for any constants immediately enclosed in the namespace
	- a faux forward declaration of any nested namespace
- For each class defined as part of a project, a header file should be provided specifying any class fields and a function signature for any class methods.
- Functions requiring greater than 1 logical line of code to define should not be implemented in the header file, if possible.
- For each function **name** within an *effective* namespace (a class acts as an effective namespace for its fields and methods), an implementation file (i.e., `.cpp`) is provided.
- For each **name** within an *effective* namespace, a corresponding implementation file (i.e., `.cpp`) is provided with unit tests for each function of that name. For naming conventions for these files, see the [Directory Structure] section below.

Distributing source code in this fashion offers a number of advantages, including:
- Small files require less effort to navigate through and reason about
- Filenames are more helpful when searching for relevant source code when problems or questions arise
- As a corollary, the project is more easily maintained
- A reduced likelihood that conflict resolution will be required when merging between version control branches
- Calls to `make`
	- Can exploit a higher degree of parallelism during the initial build 
	- Compile the minimum amount of source necessary to accommodate a revision, reducing build time overhead associated with test-driven development.

For instructions on avoiding filename collisions while conforming to this specification, see the [Directory Structure] section below.

## Directory Structure
The NJOY21 style dictates a directory structure that’s a bit atypical. The goal of this specification is to provide a structure convention that:

1. Reflects the structure of the source code (in combination with the specified file granularity, specified above);
2. Acts (as a corollary to 1) as a form of documentation that automatically remains in sync with the source; and
3. Allows for subsets of projects to be easily extracted and external projects to be easily composed, promoting code reuse.

Starting with the project-level namespace and head directory,

- For each *effective* namespace defined as part of a project, if implementation files or (in the case of *effective* namespaces corresponding to classes) child classes need be specified, a corresponding directory should be created, sharing the namespace name.
- This directory should contain, at a minimum, the following two subdirectories:
	1. `src`—Contains any implementation (i.e., `.cpp`) files corresponding to immediately enclosed functions.
	2. `test`—Contains:
		1. Implementation files providing tests for each immediately enclosed function. Files are named according to the convention `functionName.test.cpp`.
		2. An implementation file that:
			- Is named according to the convention `namespace.test.cpp`.
			- Provides a routine to drive the unit tests provided in the other implementation files in the directory.
-   If this *effective* namespace corresponds to a C\+\+ class, (which we’ll refer to as **CC**)
	- Header files for any immediate child classes (but not grandchildren) *residing within the same C\+\+ namespaces* are found in the **CC** directory
	- Directories for any immediate child classes *residing within the same C\+\+ namespaces* are found in the **CC** directory
- If this *effective* namespace corresponds to a C\+\+ namespace, (which we’ll refer to as **NS**)
	- Header files for any immediately enclosed C\+\+ namespaces are found in the **NS** directory
	- Directories for any immediately enclosed C\+\+ namespaces are found in the **NS** directory
	- Header files for each class immediately enclosed by the C\+\+ namespace not addressed by the **CC** conventions are found in the **NS** directory. (Classes without a parent classes or who’s immediate parent class residing in another namespace all into this category.)
	- Directories for each class immediately enclosed by the C\+\+ namespace not addressed by the **CC** conventions should be found in the **NS** directory.

In addition to these general principles, there are a few cases that require specific treatment

- The project namespace header should be placed within the highest level directory of the project and **does not** require its own directory. This makes it possible to include the project in a super project and contain everything in an appropriately named directory.
- Source code for any projects maintained independently of, but referenced by the project in question should reside in their own directory within the highest level project directory.

### Example
Often, use examples are more helpful when learning a convention. Consider the (abridged and annotated) file structure tree of the NJOY21 project, which was designed primarily in an object-oriented fashion:

```
njoy21/
├── catch                                     /* Independently maintained projects (catch in this case) reside in a directory
│                                              * immediately enclosed by the highest level project directory
│                                              */
├── CMakeLists.txt
├── implementation                            /* Members of each namespace share a directory */
│   ├── CMakeLists.txt
        ...
│   ├── LegacyTransform                       /* NJOY21::implementation::LegacyTransform has no parent classes within the
│   │   │                                      * implementation namespace. Hence, it's header resides within the implementation directory
│   │   │                                      */
│   │   ├── CMakeLists.txt
│   │   ├── LegacyACER                        /* NJOY21::implementation::LegacyACER is a child of NJOY21::implementation::LegacyTransform.
│   │   │                                      * The parent class resides in the same namespace, hence, the child class header 
│   │   │                                      * resides within the parent class directory
│   │   │                                      */
│   │   │   ├── CMakeLists.txt
│   │   │   ├── src
│   │   │   │   ├── parse.cpp                 /* Each class method has an implementation file residing in the src subdirectory
│   │   │   │   │                              * of the class directory
│   │   │   │   │                              */
│   │   │   │   ├── readCard1.cpp
│   │   │   │   ├── readCard2.cpp
                    ...
│   │   │   └── test
│   │   │       ├── CMakeLists.txt
│   │   │       ├── LegacyACER.test.cpp       /* An implementation file is provided to drive the unit tests */
│   │   │       │
│   │   │       ├── parse.test.cpp            /* For each class method, an implementation file is provided specifying unit
│   │   │       │                              * tests which residing in the test subdirectory of the class directory
│   │   │       │                              */
│   │   │       ├── readCard1.test.cpp
│   │   │       ├── readCard2.test.cpp
                    ...
│   │   ├── LegacyACER.hpp
            ...
│   ├── LegacyTransform.hpp
        ...
├── implementation.hpp
    ...
├── main.cpp
├── njoy2012
├── NJOY21.hpp.in                             /* The highest level directory acts as the project-level namespace directory,
│                                              * but also encloses the project namespace header
│                                              */
├── README.md
```
