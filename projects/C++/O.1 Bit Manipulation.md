# O.1 Bit flags and bit manipulation via std::bitset

- As the smallest addressable unit of memory is a byte.
- This means that objects must be at least 1 byte in size.
- For boolean values, this is very wasteful.
- In some storage-intensive vases, it can be useful to "pack" 8 individual Boolean values into a single byte.

- A **flag** is a value that signals when some condition exists in a program.
- To define a set of bit flags, we'll typically use an unsigned integer of appropriate size (8 bits, 16 bits, 32 bits, etc).
```cpp
#include <bitset>

std::bitset<8> mybitset{};
```

- `std::bitset` provides 4 key member functions that are useful for doing bit manipulation:
	- `test()` allows us to query whether a bit is a 0 or a 1.
	- `set()` allows us to turn a bit on.
	-  `reset()` allows us to turn a bit off.
	- `flip()` allows us to flip a value form 0 to 1 and vice versa.

```cpp
#include <bitset>
#include <iostream>

int main() {
	std::bitset<8> bits {0b0000'0101};
	bits.set(3);
	bits.flip(4);
	bits.reset(4);
	
	std::cout << "All the bits: " << bits << '\n';
	std::cout << "Bit 3 has value: " << bits.test(3) << '\n';
	std::cout << "Bit 4 has value: " << bits.test(4) << '\n';
	
	return 0;
}
```

```
All the bits: 00001101
Bit 3 has value: 1
Bit 4 has value: 0
```

- `std::bitset` is optimised for speed, not memory savings.
- The size of a `std::bitset` is typically the number of bytes needed to hold the bits, rounded up to the nearest `sizeof(size_t)`, which is 4-bytes on a 32-bit machine and 8-bytes on a 64-bit machine.
- Because of this, `std::bitset` is most useful when we desire convenience, not memory savings.

Here are some more useful member functions that are useful:
- `size()` returns the number of bits in the bitset.
- `count()` returns the number of bits in the bitset that are set to `true`.
- `all()` if all are `true`.
- `any()` if any are `true`.
- `none()` if all are not `true`.


## O.2 Bitwise operators

| Operator    | Symbol | Form   | The operation returns a value where:                                      |
| ----------- | ------ | ------ | ------------------------------------------------------------------------- |
| left shift  | <<     | x << n | the bits from `x` are shifted left by `n` positions, new bits are `0`.    |
| right shift | >>     | x >> n | the bits from `x` are shifted right by `n` positions, new bits are `0`.   |
| bitwise NOT | ~      | ~x     | each bit from `x` is flipped.                                             |
| bitwise AND | &      | x & y  | each bit is set when both corresponding bits in `x` and `y` are `1`.      |
| bitwise OR  | \|     | x \| y | each bit is set when either corresponding bit in `x` and `y` is `1`.      |
| bitwise XOR | ^      | x ^ y  | each bit is set when the corresponding bits in `x` and `y` are different. |

- Avoid using the bitewise operators with signed integral operands, as many operators will return implementation-defined results or have other potential gotchas.

```cpp
#include <bitset>
#include <iostream>

int main() {
	std::bitset<4> x {0b1100};
	
	std::cout << x << '\n';
	std::cout << (x >> 1) << '\n';
	std::cout << (x << 1) << '\n';
	
	return 0;
}
```

```
1100
0110
1000
```

- Bit-shifting in C++ is endian-agnostic.
- Left-shift is always towards the most significant bit and right-shift is towards the least significant bit.


Bitwise assignment operators;

| Operator    | Symbol | Form    | The operation modifies the left operand where:                            |
| ----------- | ------ | ------- | ------------------------------------------------------------------------- |
| left shift  | <<     | x <<= n | the bits in `x` are shifted left by `n` positions, new bits are `0`.      |
| right shift | >>     | x >>= n | the bits in `x` are shifted right by `n` positions, new bits are `0`.     |
| bitwise AND | &      | x &= y  | each bit is set when both corresponding bits in `x` and `y` are `1`.      |
| bitwise OR  | \|     | x \|= y | each bit is set when either corresponding bit in `x` and `y` is `1`.      |
| bitwise XOR | ^      | x ^= y  | each bit is set when the corresponding bits in `x` and `y` are different. |
- For example, instead of writing `x = x >> 1` you can write `x >>= 1`.
```cpp
#include <bitset>
#include <iostream>

int main() {
	std::bitset<4> bits {0b0100};
	bits >>=1;
	std::cout << bits << '\n';
	
	return 0;
}
```

```
0010
```

- If the operands of a bitwise operator are an integral type small than an `int`, those operands will be converted to `int` or `unsigned int`.
- It's best to avoid bit shifting on integral types smaller than `int` whenever possible.


## O.3 Bit manipulation with bitwise operators and bit masks

- **Bit masks** is a predefined set of bits that is used to select which specific bits will be modified by subsequent bits will be modified by subsequent operations.

```cpp
#include <cstdint>
#include <iostream>

int main() {
	[[maybe_unused]] constexpr std::uint8_t mask0{0b0000'0001};
	[[maybe_unused]] constexpr std::uint8_t mask1{0b0000'0010};
	[[maybe_unused]] constexpr std::uint8_t mask2{0b0000'0100};
	[[maybe_unused]] constexpr std::uint8_t mask3{0b0000'1000};
	[[maybe_unused]] constexpr std::uint8_t mask4{0b0001'0000};
	[[maybe_unused]] constexpr std::uint8_t mask5{0b0010'0000};
	[[maybe_unused]] constexpr std::uint8_t mask6{0b0100'0000};
	[[maybe_unused]] constexpr std::uint8_t mask7{0b1000'0000};
	
	std::uint8_t flags{0b0000'0101};
	
	std::cout "bit 0 is " << (static_cast<bool>(flags & mask0) ? "on\n" : "off\n");
	std::cout << "bit 1 is " << (static_cast<bool>(flags & mask1) ? "on\n" : "off\n");
	
	return 0;
}
```

```
bit 0 is on
bit 1 is off
```

Setting a bit:
```cpp
#include <cstdint>
#include <iostream>

int main()
{
    [[maybe_unused]] constexpr std::uint8_t mask0{ 0b0000'0001 }; // represents bit 0
    [[maybe_unused]] constexpr std::uint8_t mask1{ 0b0000'0010 }; // represents bit 1
    [[maybe_unused]] constexpr std::uint8_t mask2{ 0b0000'0100 }; // represents bit 2
    [[maybe_unused]] constexpr std::uint8_t mask3{ 0b0000'1000 }; // represents bit 3
    [[maybe_unused]] constexpr std::uint8_t mask4{ 0b0001'0000 }; // represents bit 4
    [[maybe_unused]] constexpr std::uint8_t mask5{ 0b0010'0000 }; // represents bit 5
    [[maybe_unused]] constexpr std::uint8_t mask6{ 0b0100'0000 }; // represents bit 6
    [[maybe_unused]] constexpr std::uint8_t mask7{ 0b1000'0000 }; // represents bit 7

    std::uint8_t flags{ 0b0000'0101 }; // 8 bits in size means room for 8 flags

    std::cout << "bit 1 is " << (static_cast<bool>(flags & mask1) ? "on\n" : "off\n");

    flags |= mask1; // turn on bit 1

    std::cout << "bit 1 is " << (static_cast<bool>(flags & mask1) ? "on\n" : "off\n");

    return 0;
}
```

- We use the bitwise OR equals (operator |=) in conjunction with the bit mask for the appropriate bit.
- We can turn on multiple bits at the same time using multiple masks:
```cpp
flag |= (mask4 | mask5);
```

Resetting a bit:
- To reset a bit, we use bitwise AND and bitwise NOT together:
```cpp
flags &= ~(mask4 | mask5);
```

Flipping a bit:
- To flip a bit, we use bitwise XOR:
```cpp
flags ^= (mask4 | mask5);
```


- So whilst bitset can be used to do all of the above, it doesn't allow multiple bits to be manipulated at the same time.


## O.4 Converting integers between binary and decimal representation

Too dense and not useful...
