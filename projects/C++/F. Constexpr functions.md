## F.1 — Constexpr functions

### Constexpr functions can be used in constant expressions

- The `constexpr` keyword is used to signal to the compiler and other developers that a function can be used in a constant expression.

```cpp
#include <iostream>

constexpr double calcCircumference(double radius) {
	constexpr double pi {3.14159265259};
	return 2.0 * pi * radius;
}

int main() {
	constexpr double circumference {calcCircumference(3.0)};	
	
	std::cout << "Our circle has circumference " << circumference << '\n';
	
	return 0;
}
```

### Constexpr functions can be evaluated at compile time

To evaluate at compile-time, two things must also be true:
1. The call to the constexpr function must have arguments that are known at compile time. E.g. are constant expressions.
2. All statements and expressions within the constexpr function must be evaluate at compile-time.

### Constexpr functions can also be evaluated at runtime

```cpp
#include <iostream>

constexpr int greater(int x, int y) {
	return (x > y ? x : y);
}

int main() {
	int x{5}; // not constexpr
	int y{6}; // not constexpr
	
	std::cout << greater(x, y) << " is greater!\n";
	
	return 0;
}
```

- When a constexpr function evaluates at runtime, it evaluates just like a normal (non-constexpr) function would.
- In other words, the `constexpr` has no effect in this case.

- Allowing functions with a constexpr return type to be evaluated at either compile-time or runtime was allowed so that a single function can server both cases.
- Otherwise, you'd need to have separate functions.
	- One with constexpr return type and a function with a non-constexpr return type.
- This would not only require duplicate code, the two functions would also need to have different names.

