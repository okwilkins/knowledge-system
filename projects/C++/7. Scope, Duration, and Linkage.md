## 7.1 Compound statements (blocks)

- A **compound statement** (or sometimes a **block statement** is a group of zero or more statements that is treated by the compiler as if it were a single statement.
- Although functions cannot be placed into other functions, blocks can be nested inside other blocks:
```cpp
int add(int x, int y) {
	return x + y;
}

int main() {
	int value {};
	
	{
		add(3, 4);	
	}
	
	return 0;
}
```

- The C++ standard specifies that compilers should support up to 256 levels of nesting.


## 7.2 User-defined namespaces and the scope resolution operator

- Namespaces you create in your own programs are called **user-defined namespaces**.

```cpp
namespace NamespaceIdentifier {
	// Content in here
}
```
- Historically, namespaces were not capitalised and guides will still suggest this.
- Some users prefer this style and it will help to reduce namespace collisions with system or lib provided namespaces.
- The C++20 documentation and the core documentation uses the capitalisation style.

- The best way to way to tell the compiler to look in a particular namespace for an identifier is to use the **score resolution operator** (**::**).

- You can tell the compiler to look in the global namespace by doing something like: `::doSomething`.

- If an identifier in a namespace is used and no scope resolution is given, the compiler will first try to find a matching declaration in the same namespace.
- If No matching identifier is found, the compiler will then check each containing namespace in sequence if a match is found, with the global namespace being checked last.
```cpp
#include <iostream>

void print() {
	std::cout << "there\n";
}

namespace Foo {
	void print() {
		std::cout << "Hello";	
	}
	
	void printHelloThere () {
		print();   // calls print() in Foo namespace
		::print(); // calls print in the global namespace	
	}
}

int main() {
	Foo::printHelloThere();
	
	return 0;
}
```

Forward declaration of content int namespaces:
```cpp
// add.h
#ifndef ADD_H
#define ADD_H

namesapce BasicMaths {
	int add(int x, int y);
}

#endif
```

```cpp
// add.cpp
#include "add.h"

namespace BasicMaths {
	int add(int x, int y) {
		return x + y;	
	}
}
```

```cpp
// main.cpp
# include "add.h"

# include <iostream>

int main() {
	std::cout << BasicMaths::add(4, 3) << '\n';
	
	return 0;
}
```

- You need to place the `add()` forward declaration in the namespace, otherwise it would be placed in the global namespace instead.
- The compiler would then complain that it hadn't seen a declaration for `BasicMaths::add(4, 3)`.

- It is legal to declare namespace blocks in multiple locations either:
	- Across multiple files.
	- Multiple places in the same file.

- Namespaces can be nested inside other namespaces:
```cpp
#inclide <iostream>

namespace Foo {
	namespace Goo {
		int add(int x, int y) {
			return x + y;	
		}	
	}
}

int main() {
	std::cout << Foo::Goo::add(1, 2) << '\n';
	
	return 0;
}
```
- Since C++17, nested namespaces can also be declared as: `namespace Foo:Goo`.

- Because typing the qualifier name of a variable or function inside a nested namespace can be painful, C++ allows you to created **namespace aliases**.
```cpp
#include <iostream>

name Foo:Goo {
	int add(int x, int y) {
		return x + y;	
	}
}

int main() {
	namespace Active = Foo:Goo;
	
	std::cout << Active::add(1, 2) << '\n';
	
	return 0;
}
```
- One advantage of this is you can easily switch the location of a namespace, rather than replace every instance of the old namespace.