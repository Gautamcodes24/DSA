# Bit Manipulation in Python — Complete Beginner to Advanced Notes

> **Goal of this note:** If you are learning bit manipulation for the first time, this guide should take you from zero to confident problem-solving.

---

## Table of Contents
1. [What Is Bit Manipulation?](#1-what-is-bit-manipulation)
2. [How Numbers Are Stored (Binary Basics)](#2-how-numbers-are-stored-binary-basics)
3. [Decimal ↔ Binary Conversion](#3-decimal--binary-conversion)
4. [Bit Positions and Powers of 2](#4-bit-positions-and-powers-of-2)
5. [Bitwise Operators in Python](#5-bitwise-operators-in-python)
6. [First Hands-On: Read / Set / Clear / Toggle Bits](#6-first-hands-on-read--set--clear--toggle-bits)
7. [Most Important Bit Tricks You Must Know](#7-most-important-bit-tricks-you-must-know)
8. [Counting Set Bits (Popcount)](#8-counting-set-bits-popcount)
9. [XOR Patterns (Very Important for Interviews)](#9-xor-patterns-very-important-for-interviews)
10. [Negative Numbers and Two's Complement](#10-negative-numbers-and-twos-complement)
11. [Shifts in Detail (Arithmetic vs Logical)](#11-shifts-in-detail-arithmetic-vs-logical)
12. [Bitmasking as a Data Structure](#12-bitmasking-as-a-data-structure)
13. [Subsets Using Bitmasks](#13-subsets-using-bitmasks)
14. [Submask Iteration (Advanced Trick)](#14-submask-iteration-advanced-trick)
15. [DP with Bitmask (Intro + Skeleton)](#15-dp-with-bitmask-intro--skeleton)
16. [Common Real-World Uses](#16-common-real-world-uses)
17. [Complexity and Performance Notes](#17-complexity-and-performance-notes)
18. [Common Mistakes and Debug Tips](#18-common-mistakes-and-debug-tips)
19. [Quick Cheat Sheet](#19-quick-cheat-sheet)
20. [Practice Plan: Beginner → Advanced](#20-practice-plan-beginner--advanced)

---

## 1) What Is Bit Manipulation?

A **bit** is the smallest unit of data in a computer. It can be:
- `0`
- `1`

All integers are represented internally with bits. **Bit manipulation** means using operations that directly work on these bits.

Why this matters:
- Helps write faster and memory-efficient code.
- Essential for many interview problems.
- Common in systems, networking, security, compression, graphics, game-dev, and embedded programming.

---

## 2) How Numbers Are Stored (Binary Basics)

Computers use **binary** (base 2), not decimal (base 10).

### Decimal (base 10)
Each position has power of 10.

Example:
`538 = 5*10^2 + 3*10^1 + 8*10^0`

### Binary (base 2)
Each position has power of 2.

Example:
`1101₂ = 1*2^3 + 1*2^2 + 0*2^1 + 1*2^0 = 13`

So decimal `13` is binary `1101`.

---

## 3) Decimal ↔ Binary Conversion

### Decimal to Binary (manual idea)
Repeatedly divide by 2, collect remainders bottom-up.

Example for `13`:
- 13 / 2 = 6 remainder 1
- 6 / 2 = 3 remainder 0
- 3 / 2 = 1 remainder 1
- 1 / 2 = 0 remainder 1

Read remainders in reverse: `1101`.

### In Python

```python
n = 13
print(bin(n))          # 0b1101
print(format(n, 'b'))  # 1101
print(format(n, '08b'))  # 00001101 (8-bit padded)
print(int('1101', 2))  # 13
```

---

## 4) Bit Positions and Powers of 2

For binary `101101`:

- Rightmost bit is position `0` (LSB: least significant bit)
- Then `1, 2, 3, ...`

```text
Position:   5 4 3 2 1 0
Bits:       1 0 1 1 0 1
Value:     32 0 8 4 0 1 = 45
```

Useful fact:
- `1 << i` means bit pattern where only i-th bit is set.
- Example: `1 << 3 = 8` (`1000` in binary)

---

## 5) Bitwise Operators in Python

Let:

```python
a = 13  # 1101
b = 10  # 1010
```

## `&` (AND)
Bit becomes 1 only if both bits are 1.

```python
print(a & b)  # 8 -> 1000
```

## `|` (OR)
Bit becomes 1 if at least one bit is 1.

```python
print(a | b)  # 15 -> 1111
```

## `^` (XOR)
Bit becomes 1 when bits are different.

```python
print(a ^ b)  # 7 -> 0111
```

XOR truth table:
- `0 ^ 0 = 0`
- `0 ^ 1 = 1`
- `1 ^ 0 = 1`
- `1 ^ 1 = 0`

## `~` (NOT)
Flips bits. In Python, integers are not fixed-width, so:

```python
print(~13)  # -14
```

Identity:

```python
~x == -(x + 1)
```

## `<<` (left shift)
Shift left by `k`: roughly multiply by `2^k`.

```python
print(5 << 1)  # 10
print(5 << 3)  # 40
```

## `>>` (right shift)
Shift right by `k`: for non-negative, roughly floor divide by `2^k`.

```python
print(20 >> 2)  # 5
```

---

## 6) First Hands-On: Read / Set / Clear / Toggle Bits

These four are fundamental.

```python
def is_set(n: int, i: int) -> bool:
    """Return True if i-th bit in n is 1."""
    return (n & (1 << i)) != 0


def set_bit(n: int, i: int) -> int:
    """Set i-th bit to 1."""
    return n | (1 << i)


def clear_bit(n: int, i: int) -> int:
    """Set i-th bit to 0."""
    return n & ~(1 << i)


def toggle_bit(n: int, i: int) -> int:
    """Flip i-th bit."""
    return n ^ (1 << i)


n = 0b1010  # 10
print(is_set(n, 1))      # True
print(set_bit(n, 0))     # 11 (1011)
print(clear_bit(n, 3))   # 2  (0010)
print(toggle_bit(n, 1))  # 8  (1000)
```

### Why these formulas work
- `n | mask`: OR with `1` forces that bit to 1.
- `n & ~mask`: AND with `0` at that position forces it to 0.
- `n ^ mask`: XOR with 1 flips the bit.

---

## 7) Most Important Bit Tricks You Must Know

## A) Remove lowest set bit

```python
n = 0b110100
print(bin(n & (n - 1)))  # 0b110000
```

Pattern: `n & (n - 1)` drops rightmost `1` bit.

## B) Extract lowest set bit

```python
def lowbit(n: int) -> int:
    return n & -n

print(lowbit(12))  # 4
```

## C) Check if power of two

```python
def is_power_of_two(n: int) -> bool:
    return n > 0 and (n & (n - 1)) == 0
```

## D) Check odd/even

```python
def is_odd(n: int) -> bool:
    return (n & 1) == 1
```

---

## 8) Counting Set Bits (Popcount)

Set bit = bit with value `1`.

## Method 1: Brian Kernighan Algorithm
Every iteration removes one set bit.

```python
def popcount_kernighan(n: int) -> int:
    count = 0
    while n:
        n &= (n - 1)
        count += 1
    return count

print(popcount_kernighan(13))  # 3
```

## Method 2: Python built-in

```python
print((13).bit_count())  # 3
```

Use `bit_count()` in real code.

---

## 9) XOR Patterns (Very Important for Interviews)

XOR has unique properties:
- `a ^ a = 0`
- `a ^ 0 = a`
- Commutative: `a ^ b = b ^ a`
- Associative: `(a ^ b) ^ c = a ^ (b ^ c)`

## Problem 1: Single number (others appear twice)

```python
def single_number(nums: list[int]) -> int:
    x = 0
    for v in nums:
        x ^= v
    return x

print(single_number([4, 1, 2, 1, 2]))  # 4
```

## Problem 2: Two numbers appear once, others appear twice

```python
def two_single_numbers(nums: list[int]) -> tuple[int, int]:
    xr = 0
    for v in nums:
        xr ^= v

    # rightmost set bit in xr separates the two unique numbers
    diff = xr & -xr

    a = b = 0
    for v in nums:
        if v & diff:
            a ^= v
        else:
            b ^= v
    return a, b

print(two_single_numbers([1, 2, 1, 3, 2, 5]))  # (3,5) or (5,3)
```

## Problem 3: XOR of 1..n in O(1)

```python
def xor_1_to_n(n: int) -> int:
    r = n % 4
    if r == 0:
        return n
    if r == 1:
        return 1
    if r == 2:
        return n + 1
    return 0

print(xor_1_to_n(10))  # 11
```

---

## 10) Negative Numbers and Two's Complement

Most systems use **two's complement**.

In fixed width:
- Positive numbers are standard binary.
- Negative number representation is obtained by:
  1) invert bits
  2) add 1

Example in 8-bit:
- `5`  = `00000101`
- `-5` = `11111011`

### Important in Python
Python integers are arbitrary precision (no fixed 32-bit/64-bit default). So when a problem says “32-bit integer”, use masking.

```python
MASK32 = 0xFFFFFFFF

x = -5
unsigned_view = x & MASK32
print(unsigned_view)  # 4294967291
```

### Convert unsigned 32-bit to signed 32-bit

```python
def to_signed32(x: int) -> int:
    x &= 0xFFFFFFFF
    if x & (1 << 31):
        return x - (1 << 32)
    return x
```

---

## 11) Shifts in Detail (Arithmetic vs Logical)

## Left shift `<<`
Adds zeros to right in binary (for non-overflow fixed-width thought model).

```python
print(3 << 2)  # 12
```

## Right shift `>>`
In Python, this is **arithmetic shift** (keeps sign for negatives).

```python
print(-8 >> 1)  # -4
```

Some languages have logical right shift `>>>` (fills with zeros). Python does not.

### Emulate logical right shift in width bits

```python
def logical_rshift(x: int, k: int, width: int = 32) -> int:
    return (x % (1 << width)) >> k

print(logical_rshift(-2, 1))  # 2147483647 for width=32
```

---

## 12) Bitmasking as a Data Structure

You can store a set of small integers inside one integer.

Example universe: `{0,1,2,3,4,5}`
- If bit `i` is 1, element `i` is in the set.

```python
mask = 0

# add 2, 5
mask |= (1 << 2)
mask |= (1 << 5)

# check membership
print(bool(mask & (1 << 2)))  # True
print(bool(mask & (1 << 3)))  # False

# remove 2
mask &= ~(1 << 2)

# toggle 3
mask ^= (1 << 3)
```

Why useful:
- Very memory efficient.
- Fast union/intersection-like operations using `|`, `&`, `^`.

---

## 13) Subsets Using Bitmasks

For `n` elements, there are `2^n` subsets.
A number from `0` to `(1<<n)-1` can represent one subset.

```python
def subsets(arr: list[int]) -> list[list[int]]:
    n = len(arr)
    ans = []

    for mask in range(1 << n):
        current = []
        for i in range(n):
            if mask & (1 << i):
                current.append(arr[i])
        ans.append(current)

    return ans

print(subsets([10, 20, 30]))
```

Interpretation:
- mask `0b000` -> `[]`
- mask `0b101` -> `[arr[0], arr[2]]`

---

## 14) Submask Iteration (Advanced Trick)

Given a mask, iterate all of its submasks efficiently.

```python
mask = 0b1101
sub = mask

while sub:
    print(bin(sub))
    sub = (sub - 1) & mask

print(bin(0))  # include empty submask manually if needed
```

Used in advanced subset-DP and combinational optimizations.

---

## 15) DP with Bitmask (Intro + Skeleton)

Bitmask DP is useful when `n` is small (often `n <= 20`) and each state is a subset.

Generic idea:
- `dp[mask]` = best result for subset represented by `mask`.

```python
def subset_dp_min_cost(cost: list[int]) -> int:
    n = len(cost)
    INF = 10**18
    dp = [INF] * (1 << n)
    dp[0] = 0

    for mask in range(1 << n):
        for i in range(n):
            if not (mask & (1 << i)):  # i not selected yet
                nxt = mask | (1 << i)
                dp[nxt] = min(dp[nxt], dp[mask] + cost[i])

    return dp[(1 << n) - 1]
```

You’ll see this pattern in:
- Traveling Salesman Problem (TSP)
- Assignment variants
- Hamiltonian path/counting

---

## 16) Common Real-World Uses

- **Permissions** (read/write/execute flags)
- **Feature flags** in apps
- **Networking** packet fields and masks
- **Graphics/game states**
- **Compression/crypto primitives**
- **Scheduling and DP state compression**

---

## 17) Complexity and Performance Notes

- Basic bit operations are very fast.
- `popcount` via Kernighan: O(number of set bits).
- Subset generation: O(n * 2^n).
- Many bitmask DP solutions: O(n * 2^n), sometimes O(n^2 * 2^n).

In Python:
- For huge integers, cost may grow with number of machine words.
- For normal interview constraints, performance is usually fine.

---

## 18) Common Mistakes and Debug Tips

## Mistakes
1. Forgetting bit indexing starts at 0 from right.
2. Using `~x` without understanding Python's unbounded ints.
3. Missing `n > 0` in power-of-two check.
4. Mixing arithmetic shift with desired logical shift.
5. Off-by-one errors in loops (`range(1 << n)`).

## Debug tips
- Print both decimal and binary:

```python
x = 13
print(x, bin(x))
```

- Use fixed width while debugging:

```python
print(format(x & 0xFF, '08b'))
```

- Write tiny tests for helper functions:

```python
def test_helpers():
    assert is_set(0b1010, 1) is True
    assert set_bit(0b1000, 1) == 0b1010
    assert clear_bit(0b1010, 3) == 0b0010
    assert toggle_bit(0b1010, 1) == 0b1000
```

---

## 19) Quick Cheat Sheet

```python
# i-th bit check
(n & (1 << i)) != 0

# set i-th bit
n | (1 << i)

# clear i-th bit
n & ~(1 << i)

# toggle i-th bit
n ^ (1 << i)

# remove lowest set bit
n & (n - 1)

# get lowest set bit
n & -n

# power of two
n > 0 and (n & (n - 1)) == 0

# odd/even
n & 1

# popcount
n.bit_count()
```

---

## 20) Practice Plan: Beginner → Advanced

## Stage 1: Beginner (1-2 days)
1. Implement `is_set`, `set_bit`, `clear_bit`, `toggle_bit`.
2. Practice odd/even, power-of-two checks.
3. Do 5 easy bit manipulation problems.

## Stage 2: Intermediate (3-5 days)
4. Solve single-number and two-single-number XOR questions.
5. Generate all subsets with masks.
6. Practice problems involving counting bits/parity.

## Stage 3: Advanced (1+ week)
7. Solve maximum XOR pair problems.
8. Learn binary trie for XOR queries.
9. Solve 5 subset-DP problems (TSP-style small n).

---

## Final Advice

- Do not memorize blindly; understand how each bit changes.
- Draw binary on paper for first 10-15 problems.
- Start with helper functions and test each one.
- Bit manipulation becomes easy after repeated pattern practice.

If you finished this note and practiced the roadmap, you should be able to read and solve most interview-level bit manipulation problems confidently.
