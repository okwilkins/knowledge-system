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


## 17.2 — std::array length and indexing

### The length of a std::array has type std::size_t

- Because the length of an array is constexpr, there is no issue with sign conversion when converting to a signed integral value.

### std::get() does compile-time bounds checking for constexpr indices

- Since the length of a `std::array` is constexpr, if the index is also constexpr value, then the compiler should be able to validate at compile-time that the constexpr index is withing the bounds of the array.
- `operator[]` does not bounds checking by definition.
- The `at()` member function only does runtime bounds checking.
- Function parameters can't be constexpr (even for constexpr or consteval functions).

- To get compile-time bounds checking, when having a constexpr index, the `std::get()` function template can be used.

```cpp
#include <array>
#include <iostream>

int main() {
	constexpr std::array prime{2, 3, 5, 7, 11};
	
	std::cout << std::get<3>(prime);
	// Invalid index (compile error)
	std::cout << std::get<9>(prime);
	
	return 0;
}	
```

- Inside the implementation of `std::get`, there is a static_assert that checks to ensure that the non-type template argument is smaller than the array length.
	- If it isn't, then the static_assert will halt the compilation process with compilation error.
- Since template arguments must be constexpr, `std::get()` can only be called with constexpr indices.


## 17.3 — Passing and returning std::array

- `std::array` can be passed by value, which means an expensive copy will be made.
- Therefore, typically, `std::array` is passed by const reference, to avoid such copies.

- With a `std::array`, both the element type and array length are part of the type information of the object.
	- Therefore, both the element type and array length must be given.

```cpp
#include <array>
#include <iostream>

void passByRef(const std::array<int, 5>& arr) {
	std::cout << arr[0] << '\n';
}

int main() {
	std::array arr{9, 7, 5, 3, 1};
	passByRef(arr);
	
	return 0;
}
```

### Using function templates to pass std::array of different element types or lengths

- Since `std::array` is defined like this:

```cpp
template<typename T, std::size_t N>
struct array;
```

- A function template that uses the same template parameter declaration can be created:

```cpp
#include <array>
#include <iostream>

template <typename T, std::size_t N>
void passByRef(const std::array<T, N>& arr) {
	static_assert(N != 0);
	std::cout << arr[0] << '\n';
}
```

### Auto non-type template parameters

- Having to remember (or look up) the type of a non-type template parameter so that it can be used in a template parameter declaration is a pain.
- In C++20, the `auto` keyword can be used in a template parameter declaration to have a non-type template parameter deduce its type from the argument

```cpp
#include <array>
#includ <iostream>

template <typename T, auto N>
void passByRef(const std::array<T, N>& arr) {
	static_assert(N != 0);
	std::cout << arry[0] << '\n';
}
```

### Static asserting on array length

```cpp
#include <array>
#include <iostream>

template <typename T, std::size_t N>
void printElement3(const std::array<T, N>& arr) {
	static_assert (N > 3);
	std::cout << arr[3] << '\n';
}
```

### Returning a std::array

- Unlike `std::vector`, `std::array` is not move-capable, so returning a `std::array` by value will make a copy of the array.
- The elements inside the array will be moved, if they are move-capable and are copied otherwise.

- There are two conventional option here and which you should pick depends on circumstances.

### Returning a std::array by value

 - It is ok to return a `std::array` by value when all of the following are true:
	 - The array isn't huge.
	 - The element type is cheap to copy (or move).
	 - The code isn't being used in a performance-sensitive context.


### Returning a std::array via an out parameter

- In cases where return by value is too expensive, an out-parameter can be used instead.
- In this case, the caller is responsible for passing in the `std:array`, by non-const reference (or by address) and the function can then modify this array.

```cpp
#include <array>
#include <limits>
#include <iostream>

template <typename T, std::size_t N>
void inputArray(std::array<T, N>& arr) {
	std::size_t index{0};
	
	while (index < N) {
		std::cout << "Enter value # " << index << ": ";
		std::cin >> arr[index];
		++index;	
	}
}

int main() {
	std::array<int, 5> arr{};
	inputArray(array);
	
	std::cout << "The value of element 2 is " << arr[2] << '\n';
	
	return 0;
}
```

- The primary advantage of this method is that no copy of the array is ever made.
- There are a few downsides:
	- This method of returning data is non-conventional and it is not easy to tell that the function is modifying the argument.
	- This method cannot be used to assign values to the array, not initialise it.
	- Such a function cannot be used to produce temporary objects.

### Returning a std::vector instead

- `std::vector` is move-capable and can be returned by value without making expensive copies.
- If returning a `std::array` by value, the `std::array` probably isn't constexpr and using (and returning) a `std::vector` should instead be considered.
