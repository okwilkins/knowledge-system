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


## F.2 — Constexpr functions (part 2)

### Constexpr function calls in non-required constant expressions

- It might be expected that a constexpr function would evaluate at compile-time whenever possible.
- This is unfortunately not the case.

- In 5.5 it was noted that in contexts that do not require a constant expression, the compiler may choose whether to evaluate a constant expression at either compile-time or at runtime.
- With this, any constexpr function call that is part of a non-required constant expression may be evaluated at either compile-time or runtime.

```cpp
constexpr int getValue(int x) {
	return x;
}

int main() {
	int x {getValue(5)}; // may evaluate at runtime or compile-time
	
	return 0;
}
```

### Diagnosis of constexpr functions in required constant expressions

- The compiler is not required to determine whether a constexpr function is evaluatable at compile-time until it is actually evaluated at compile-time.
- It is fairly easy to write a constexpr function that compiles successfully for runtime use but then fails to compile when evaluated at compile-time.

```cpp
int getValue(int x) {
	return x;
}

// This funtion can be evaled at runtime
// When evaled at compile-time, the function will produce aa compilation error
// because the call to getValue(x) cannot be resolved at compile-time
constexpr int foo(int x) {
	if (x < 0) return 0; // needed, read note below
	return getValue(); // call to non-constexpr function
}

int main() {
	int x {foo(5)}; // ok: will eval at runtime
	constexpr int y {foo(5)}; // compile error: foo(5) can't eval at compile-time
	
	return 0;
}
```

Best practice:
- All constexpr functions should be evaluatable at compile-time, as they will be required to do so in contexts that require a constant expression.
- Always test constexpr functions in a context that requires a constant expression, as the constexpr function may work when evaluated at runtime but fail when evaluated at compile-time.

Note on the need for the above if statement:
- Prior to C++23, if no argument values exist that would allow a constexpr function to be evaluated at compile-time, the program is ill-formed.
- Without the line `if (x < 0) return 0`, the above example would contain no set of arguments that allow the function to be evaluatable at compile-time, making the program ill-formed.
- Given that no diagnostic is required, the compiler may not enforce this.

### Constexpr/consteval function parameters are not constexpr

- The parameters of a constexpr function are not implicitly constexpr, nor may they be declared as constexpr.
- A constexpr function parameter would imply the function could only be called with a constexpr argument.
- This is not the case, constexpr functions can be called with non-constexpr arguments when the function is evaluated at runtime.

- Because such parameters are non constexpr, they cannot be used in constant expressions within the function.

```cpp
#include <iostream>

// c is not constxpr and cannot be used in constant expressions
consteval int goo(int c) {
	return c;
}

// b is not constexpr and cannot be used in constant expressions
constexpr int foo(int b) {
	constexpr int b2 {b}; // compile error: constexpr variable required constant expression initialiser
	
	return goo(b); // compile error: consteval func call requires const expression argument
}

int main() {
	constexpr int a {5};
	
	std::cout << foo(a); // ok: constant expression a can be used as argument to constexpr function foo()
	
	return 0;
}
```

- Use parameters that are templates to fix this. 

### Constexpr functions are implicitly inline

- When a constexpr function is evaluated at compile-time, the compiler must be able to see the full definition of the constexpr function prior to such function calls.
- A forward declaration will not suffice in this case, even if the actual function definition appears later in the same compilation unit.

- This means that constexpr function called in multiple files needs to have its definition include into each translation unit.
	- This would normally be a violation of the ODR.
- To avoid such problem, constexpr functions are implicitly inline, which makes them exempt from the ODR.

- As a result, constexpr functions are often defined in header files, so they can be included into any .cpp file that requires the full definition.

Best practice:
- Constexpr/consteval functions used in a single source file (.cpp) should be defined in the source files above where they are used.
- Constexpr/consteval functions used in multiple source files should be defined in a header file so they can be included into each source file

### Summary of Compile-time and Run-time evaluation

Categorisations of the likelihood that a function will actually be evaluated at compile-time:

- Always (required by the standard):
	- Constexpr function is called where constant expression is required.
	- Constexpr function is called from another function being evaluated at compile-time.
- Probably (there's little reason not to):
	- Constexpr functions is called where constant expression isn't required, all arguments are constant expressions.
- Possibly (if optimised under the as-if rule):
	- Constexpr function is called where constant expression isn't required, some arguments are not constant expressions but their values are known at compile-time.
	- Non-constexpr function capable of being evaluated at compile-time, all arguments are constant expressions.
- Never (not possible):
	- Constexpr function is called where constant expression isn't required, some arguments have values that are not known at compile-time.

- The compile might also choose to inline a function call or even optimise a function call away entirely.
- Both of these can affect when (or if) the content of the function call are evaluated.
- The compiler flags can also cause a function to change if a function is evaluated at run-time or compile-time.


## F.3 — Constexpr functions (part 3) and consteval

### Forcing a constexpr function to be evaluated at compile-time

- There is no way to tell the compiler that a constexpr function should prefer to evaluate at compile-time whenever it can.
- However, the compiler can force a constexpr function that is eligible to be evaluated at compile-time to actually evaluate at compile-time by ensuring the value is used where a constant expression is required.
	- This needs to be done on a per-call basis.
- The most common way to do this is to use a return value to initialise a constexpr variable.
	- Unfortunately, this requires introducing a new variable into the program just to ensure compile-time evaluation, which is ugly and reduced code readability.

### Consteval C++20

- C++20 introduced the **consteval** keyword.
- This is used to indicate that function must evaluate at compile-time, otherwise a compile-error will result.
- Such functions are called **immediate functions**.

```cpp
#include <iostream>

consteval int greater(int x, int y) {
	return (x > y ? x : y);
}

int main() {
	contexpr int g {greater(5, 6)}; // ok: will eval at compile-time
	std::cout << g << '\n';
	std::cout << greater(5, 6) << " is greater!\n"; // ok
	
	int x{5}; // not constexpr
	std::cout << greater(x, 6) << " is greater!\n"; // error: consteval functons must evaluate at compile-time
	
	return 0;
}
```

### `std::is_constant_evaluated` and `if consteval`

- Neither of these capabilities can identify whether a function call is evaluating at compile-time or runtime.

- `std::is_constant_evaluated()` returns a `bool` indicating whether the current function is executing in constant-evaluated context.
- A **constant-evaluated context** (also called a **constant content**) is defined as one in which a constant expression is required, such as the initialisation of a constexpr variable.
- So in cases where the compiler is required to evaluate a constant expression at compile-time `std::is_constant_evaluated()` will `true` as expected.

```cpp
#include <type_traits>

constexpr int someFunction() {
	if (std::is_constant_evaluated()) {
		doSomething();
	} else {
		doSomethingElse();	
	}
}
```

- The compiler may also choose to evaluate a constexpr function at compile-time in a context that doe not require a constant expression.
- In such cases, `std::is_constant_evaluated()` will return `false` even though the function did evaluate at compile-time.
- So it really means "the compiler is being forced to evaluate this at compile-time", rather than "this is evaluating at compile-time".

- In C++23 `if consteval` can be used in the exact same way.

### Using consteval to make constexpr execute at compile-time

- The downside of consteval functions is that such functions can't evaluate at runtime, making them less flexible than constexpr functions, which can do either.
- Therefore, it would still be useful to have a convenient way to force constexpr functions to evaluate at compile-time.
	- Even when the return value is being used where a constant expression is not required.
	- This is so that code can be explicitly force compile-time evaluated when possible and runtime evaluated when it can't.

```cpp
#include <iostream>

#define CONSTEVAL(...) [] consteval {return __VA__}() // C++20
#define CONSEVAL11(...) [] {constexpr auto _ = __VA_ARGS__; return _;}() // C++11

constexpr int compare(int x, int y) {
	if (std::is_constant_evaluated()) {
		return (x > y ? x : y);	
	} else {
		return (x < y ? x : y);	
	}
}

int main() {
	int x {5};
	std::cout << compare(x, 6) << '\n'; // will execute at runtime
	
	std::cout << compare(5, 6) << '\n'; // may or may not execute at compile-time but will always return 5
	
	std::cout << CONSTEVAL(compare(5, 6)) << '\n'; // will always exevute ate compile-time and return 6
	
	return 0;
}
```

- This uses variadic preprocessor macro to define a consteval lambda that is immediately invoked by the trialling parentheses.

- The following should also work and is a bit cleaner since it doesn't use preprocessor macros:

```cpp
#include <iostream>
#include <type_traits>

consteval auto CONSTEVAL(auto value) {
	return value;
}

constexpr int compare(int x, int y) {
	if (std::is_constant_evaluated()) {
		return (x > y ? x : y);
	} else {
		return (x < y ? x : y);	
	}
}

int main() {
	std::cout << CONSTEVAL(compare(5, 6)) << '\n'; // will execute at compile-time
	
	return 0;
}
```

- Note that the consteval function returns by value. This might be inefficient to do at runtime, if the value has something expensive to copy. 


## F.4 — Constexpr functions (part 4)

### Constexpr/consteval functions can use non-const local variables

```cpp
#include <iostream>

consteval int doSomething(int x, int y) {
	x = x + 2; // modify the value of non-const function parameters
	
	int z {x + y}; // can instantiate non-const local variables
	if (x > y) {
		z = z - 1; // and modify their values
	}
	
	return z;
}

int main() {
	constexpr int g {doSomething(5, 6)};
	std::cout << g << '\n';
	
	return 0;
}
```

- When such functions are evaluated at compile-time, the compiler will essentially "execute" the function and return the calculated value.

### Constexpr/consteval functions can use function parameters and local variables as arguments in constexpr function calls

- Surprisingly, A constexpr or consteval function can use its function parameters (which aren't constexpr) or even local variables (which may not be const at all) as arguments in a constexpr function call.
- When a constexpr or consteval function is being evaluated at compile-time, the value of all function parameters and local variables must be known to the compiler.
- Therefore, in this specific context, C++ allows these values to be used as arguments in a call to a constexpr function and that constexpr function call can still be evaluated at compile-time.

```cpp
#include <iostream>

constexpr int goo(int c) {
	return c;
}

constexpr int foo(int b) {
	return goo(b); // if foo() is resolved at compile-time, then goo(b) can be also resolved at compile-time
}

int main() {
	std::cout << foo(5);
	
	return 0;
}
```

### Can a constexpr function call a non-constexpr function?

- The answer is yes but only when the constexpr function is being evaluated in a non-constant context.
- A non-constexpr function may not be called when a constexpr function is evaluating in a constant context and doing so will produce a compilation error.

- Calling a non-constexpr function is allowed so that a constexpr function can do something like this:

```cpp
#include <type_traits>

constexpr int someFuntion() {
	if (std::is_constant_evaluated()) {
		return someConstexprFcn();	
	} else {
		return someNonConstexprFcn();	
	}
}
```

- Consider this variant:

```cpp
constexpr int someFunction(bool b) {
	if (b) {
		return someConstexprFnc();	
	} else {
		return someNonConstexprFcn();	
	}
}
```

- This is legal as long as `someFunction(false)` is never called in a constant expression.

For best results, follow:
1. Avoid calling non-constexpr functions from within a constexpr function if possible.
2. If a constexpr function requires different behaviour for constant and non-constant contexts, conditionalise the behaviour with `if (std::is_constant_evaluated())` (in C++20) or `if consteval` (C++23 onward).
3. Always test constexpr functions in a constant context, as they may work when called in non-constant contexts but fail in a constant context.

### When should I constexpr a function?

- As a general rule, if a function can be evaluated as part of a required constant expression, it should be made `constexpr`.

- A **pure function** is a function that meets the following criteria:
	- The function always returns the same return result when given the same arguments.
	- The function has no side effects.
- Pure functions should generally be made constexpr.

Best practice:
- Unless there is a specific reason no to, a function that can be evaluated as part of a constant expression should be made `constexpr`, even if it isn't currently used in that way.
- A function that cannot be evaluated as part of a required constant expression should not be marked as `constexpr`.

### Why not constexpr every function?

1. `constexpr` signals that a function can be used in a constant expression. If the function cannot be evaluated as part of a constant expression, it should not be marked as `constexpr`.
2. `constexpr` is part of the interface of a function. Once a function is made constexpr, it can be called by other constexpr functions or used in contexts that require constant expressions. Removing the `constexpr` later will break such code.
3. `constxpr` functions can be harder to debug since they cannot be used with breakpoints or stepped through with a debugger.
