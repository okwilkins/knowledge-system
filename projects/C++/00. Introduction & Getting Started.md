## 0.1 Introduction to these tutorials

I am using [Learn C++](https://www.learncpp.com) to study.

I need to remember to keep in mind:
1. Type examples in by hand.
2. Fix mistakes and bugs. Try to go as far as you can before asking for help.
3. Plan to spend some time with the quizes.

## 0.2 Introduction to programs and programming languages

- **Instruction set** is all the possible machine language instructions that a given CPU can understand.
- The translation of assembly language to machine code is done by an **assembler**.
## 0.5 Introduction to the compiler, linker, and libraries

When compiling:
1. Compiler checks code.
2. Translates C++ code to machine language instructions.
	1. These instructions are stored in an intermediate file called an object file (`.o`).
	2. Each file in a given project will be converted to its own file.


After compiling:
- The linker program kicks in.
	- Its job is to combine all the object files and produce the final output file (like executable).
	- It will also link one .cpp file to another one, if they are dependant on each other. It:  ensures all cross-file dependencies are resolved properly.
	- Third, it will link in one or more **library files**. These are collections of pre-compiled code that have been packaged up for reuse in other programs.
	- Lastly, it outputs to desired output file, typically an executable but it could also be a library file.

## 0.9 Configuring your compiler: Build configurations

Compile flags:
- `ggdb` for debug builds
- `-O2 -DNDEBUG` for release builds
- `-O0` optimisation level for debug builds
- `O2` for release builds
- `O3` adds additional optimisations but may not be faster than `O2`. Need to compare between.