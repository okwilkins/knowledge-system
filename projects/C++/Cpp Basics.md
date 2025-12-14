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

## 1.5 Introduction to iostream: cout, cin, and endl

- `std::cout << 4;` `<<` is the insert operator.
	- It's only called that with iostreams (reason below).
- `<<` can be chained together multiple times.
- You can do the same with `>>`
```cpp
#include <iostream> // for std::cout
int main() {
    std::cout << "Hello" << " world!";
    return 0;
}
```

- It is actually an overloaded bitwise left-shift operator
	- Like if you did `x = y << 1`

- Use `std::endl` for an OS-specific character or sequence of characters to move the cursor to the next line.
```cpp
#include <iostream>
int main() {
	std::cout << "Hi!" << std::endl;
	std::cout << "My name is Alex." << std::endl;
}
```

- `std::cout` is buffered.
	- Just a FIFO.
- Will wait for the queue to be full or if enough time has passed it is "flushed" to the terminal.

- `std::endl` will also flush the buffer.
	- In that case, you would manually insert operator on a `'\n'`.
	- `\n` is treated as a single LF (line feed) character.
- Prefer to use `\n` over `endl` when outputting text to the console.
- Use `endl` when the output when you want to flush.

- `std::cin` is also buffered.
- Each line of input data is automatically terminated by a '\n' character.


## 1.6 Uninitialized variables and undefined behavior

- C/C++ do not init vars with a non-zero value. By default it takes whatever data happens to be at that memory address.

## 1.7 Keywords and naming identifiers

- It's convention to use snake_case for variables.
- Use camelCase for functions.
- Use PascalCase for user defined types like structs, classes etc.


## 1.9 Introduction to literals and operators

```cpp
std::cout << "Hello world!";
int x {5};
```
- Both the string and `5` a **literals** (or literal constants).
- The value of a literal cannot be changed.
	- The literal `5` always has a value of `5`.
- A literal's value is placed directly in the executable.

Operators:
- The number of operands that an operator can take is called the operator's **arity**.
- **Unary** operators operator acts on one operands.
	- -5
- Binary operators act on two.
	- 3 + 4
- Ternary act on three.
	- Only one of these in C++.
- Nullary acts on zero.
	- Only one of these in C++.

Side effects:
- An operator or function that has some observable effect is called a side effect.
	- It does more than just calculate and output a value.
- Like `throw`, `delete` or outputting to `cout`.

## 1.10 Introduction to expressions

```cpp
int five() {
	return 5;
}

int main() {
	int a{2};
	int b{2 + 3};
	int c {(2 * 3) + 4};
	int d{b};
	int e{five()};
}
```

- An **expression** is a non-empty sequence of literals, variables, operators and function calls.
- The process of executing an expression is called an **evaluation**.
- The resulting value produced is called the **result** of the expression.
- An **expression statement** is a statement that consists of an expression followed by a semi-colon.
- A **subexpression** is an expression used as an operand.
	- `x = 4 + 5` has `x` and `4 + 5` as subexpressions.
	- The subexpressions of `4 + 5` are `4` and `5`.
- A compound expression is an expression that contains two or more uses of operators.