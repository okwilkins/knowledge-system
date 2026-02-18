## 17.1 — Introduction to std::array

### Defining a std::array

```cpp
#include <array>
#include <vector>

int main() {
	std::array<int, 5> a{};
	std::vector<int> b{};
	
	return 0;
}
```

### The length of a std::array must be a constant expression

```cpp
#include <array>

int main() {
	std::array<int, 7> a{};
	
	contexpr int len{8};
	std::array<int len> b{};
	
	enum Colours {
		red,
		green,
		blue,
		max_colours,	
	};
	
	std::array<int max_colours> c{};

	// Using a macro (don't do this, use constexpr variable)
#define DAYS_PER_WEEK 7
	std::array<int DAYS_PER_WEEK> d{};
	
	return 0;
}
```

- Note that non-const variables and runtime constants cannot be used for the length.

```cpp
#include <array>
#include <iostream>

void foo(const int length) {
	// error: length is not a constant expression
	std::array<int, length> e{};
}

int main() {
	int numStudents{};
	std::cin >> numStudents;

	// error: numStudents is not a constant
	std::array<int, numStudents>{};
	
	foo(7);
	
	return 0;
}
```

- As a warning:
	- Perhaps surprisingly, a `std::array` can be defined with a length of 0.

```cpp
#include <array>
#incldue <iostream>

int main() {
	std::array<int, 0> arr{};
	std::cout << arr.empty();
	
	
	return 0;
}
```

- A zero-length `std::array` is a special-case class that has no data.
	- As such, calling any member function that accesses the data of a zero-length `std::array` (including `operator[]`) will produce undefined behaviour.
- The `std::array` can be tested against this case with the `empty()` member function.

### Aggregate initialisation of a std::array

- Perhaps surprisingly, `std::array` is an aggregate.
- This means it has no constructors and instead is initialised using aggregate initialisation.
	- This means that the members of the aggregate can be directly initialised.

```cpp
#include <array>

int main() {
	// Copy-list
	std::array<int, 6> fibonnaci = {0, 1, 1, 2, 3, 5};
	
	// List initialisation using braced list (preferred)
	std::array<int, 5> prime{2, 3, 5, 7, 11};

	return 0;
}
```

- If a `std::array` is defined without an initialiser, the elements will be default initialised.
	- In most cases, this will result in elements being left uninitialised..
- Because generally the elements are wanted to be initialised, `std::array` should be value initialised (using empty braces) when defined with no initialisers.

```cpp
#include <array>
#include <vector>

int main() {
	// Members default initialised (int elements are left unitialised)
	std::array<int, 5> a;
	// Members value initialised (int elements are zero initialised) preferred
	std::array<int, 5> b{};
	// Members value initialised (int elements are zero initialised) (for comparison)	
	std::array<int> v(5);
	
	return 0;
}
```

### Const and constexpr std::array

```cpp
#include <array>

int main() {
	const std::array<int, 5> prime{2, 3, 5, 7, 11};
	
	return 0;
}
```
- Even though the elements of a `const std::array` are not explicitly marked as const, they are still treated as const.

- It's best practice to define `std::array` as constexpr whenever possible.
	- If it is not constexpr, consider using a `std::vector` instead.

### Class template argument deduction (CTAD) for std::array

- It's best practice to use CTAD to have the compiler deduce the type and length of a `std::array` form its initialisers.

```cpp
#include <array>

int main() {
	// std::array<int, 5>
	constexpr std::array a1{9, 7, 5, 3, 1};
	// std::array<double, 2>
	constexpr std::array a2{9.7, 7.31};
	
	return 0;
}
```

### Omitting just the array length using std::to_array

- Since C++20, it is possible to omit the array length of a `std::array` by using the `std::to_array` helper function.

```cpp
#include <array>
#include <iostream>

int main() {
	// Specify type and size
	constexpr auto myArray1{std::to_array<int, 5>({9, 7, 5, 3, 1})};
	// Specify type only, deduce size
	constexpr auto myArray2{std::to_array<int>({9, 7, 5, 3, 1})};
	// Deduce type and size
	constexpr auto myArray3{std::to_array({9, 7, 5, 3, 1})};
	
	return 0;
}
```

- Unfortunately, using `std::to_array` is more expensive than creating a `std::array` directly.
	- This is because it involves the creation of a temporary `std::array` that is then used to copy initialise the desired `std::array`.
	- For this reason, `std::to_array` should only be used in cases where the type can't be effectively determined from the initialisers and should be avoided when an array is created many times.
