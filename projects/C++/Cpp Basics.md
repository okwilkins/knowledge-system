## 1.1 Statements and the structure of a program

- **Statement** is a type of instruction that causes the program to perform some kind of action.
- Every C++ program must have a main function.
- `#include` preprocessor directive indicates we would like to use the contents of a library.

## 1.2 Comments

- Start with `//`
- Multiline is `/*` and ends with `*/`

## 1.3 Introduction to objects and variables

Common values:
- Numbers `5` or `-6.7`
- Characters must be in single quotes and only a single symbol maybe used.
- Text must be in double quotes and can container 0 or more characters.

## 1.4 Variable assignment and initialization

- To init a variable:
```cpp
int width { 5 };
```

There are several ways of initing a variable:

```cpp
// Default initalization
int a;

// Traditional initilzation forms:
int b = 5; // Copy init 
int c ( 6 ); // Direct init


// Modern
int d { 7 }; // Direct init
int e {}; // Value init
```

- The modern way is preferred because:
	- Doing trad copy init can silently truncate if `int x = 4.5`
		- Modern way will cause compiler error
	- Empty braces guarantees you have a safe zero value
		- The default init means it might take the value of whatever garbage memory happened to be there previously.

- The modern ways of initing are called list-initialisation
	- It disallows "narrowing conversions": this is like `int d { 7.5 }`

- You should not init a variable if you intent the value to be replaced
	- `int x { 0 }`
	- `int x {}`


- In C++17 it introduced `[[maybe_unused]]`
	- Prepend this before variable creation to stop the comipler complaining the variable is not used.