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