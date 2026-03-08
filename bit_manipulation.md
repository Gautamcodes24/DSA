# Bit Manipulation in Python (Basic → Advanced)

A practical, interview-ready, and implementation-focused guide.

---

## Table of Contents
1. [Why Bit Manipulation Matters](#1-why-bit-manipulation-matters)
2. [Binary Essentials](#2-binary-essentials)
3. [Python Bitwise Operators](#3-python-bitwise-operators)
4. [Core Bit Operations (with helper functions)](#4-core-bit-operations-with-helper-functions)
5. [Most Important Bit Tricks](#5-most-important-bit-tricks)
6. [Popcount / Counting Set Bits](#6-popcount--counting-set-bits)
7. [Interview Patterns with XOR](#7-interview-patterns-with-xor)
8. [Bitmasking for Subsets and State Compression](#8-bitmasking-for-subsets-and-state-compression)
9. [Advanced: Fixed Width, Two's Complement, and Shifts](#9-advanced-fixed-width-twos-complement-and-shifts)
10. [Advanced Pattern: DP over Subsets](#10-advanced-pattern-dp-over-subsets)
11. [Performance Notes](#11-performance-notes)
12. [Common Mistakes](#12-common-mistakes)
13. [Quick Cheat Sheet](#13-quick-cheat-sheet)
14. [Practice Roadmap](#14-practice-roadmap)

---

## 1) Why Bit Manipulation Matters

Bit manipulation means operating directly on the binary representation of values.

You use it for:
- **Flags/permissions** (compact state in one integer)
- **Fast checks/updates** (set, clear, toggle bits)
- **XOR-based problems** (finding unique values)
- **Subset/state DP** (bitmask representation)
- **Low-level protocols and systems logic**

---

## 2) Binary Essentials

A binary number is base-2:

- Rightmost bit = `2^0`
- Next = `2^1`, then `2^2`, ...

Example:
- `13` in binary is `1101`
- Value = `1*8 + 1*4 + 0*2 + 1*1`

### Conversion in Python

```python
n = 13
print(bin(n))          # 0b1101
print(format(n, 'b'))  # 1101
print(int('1101', 2))  # 13
```

Tip: You can print with fixed width:

```python
print(format(13, '08b'))  # 00001101
```

---

## 3) Python Bitwise Operators

Let:

```python
a = 13  # 1101
b = 10  # 1010
```

### `&` (AND)
1 only when both bits are 1.

```python
print(a & b)  # 8  -> 1000
```

### `|` (OR)
1 when either bit is 1.

```python
print(a | b)  # 15 -> 1111
```

### `^` (XOR)
1 when bits differ.

```python
print(a ^ b)  # 7  -> 0111
```

### `~` (NOT)
Flips bits. In Python, integers are not fixed-width, so:

```python
print(~13)  # -14
```

Identity:

```python
~x == -(x + 1)
```

### `<<` (Left shift)
Shifts left by `k` (roughly multiply by `2^k`).

```python
print(5 << 1)  # 10
print(5 << 3)  # 40
```

### `>>` (Right shift)
Shifts right by `k`.

```python
print(20 >> 2)  # 5
```

For non-negative numbers, this is like floor division by `2^k`.

---

## 4) Core Bit Operations (with helper functions)

Use 0-based indexing from the right (LSB is index 0).

```python
def is_set(n: int, i: int) -> bool:
    return (n & (1 << i)) != 0


def set_bit(n: int, i: int) -> int:
    return n | (1 << i)


def clear_bit(n: int, i: int) -> int:
    return n & ~(1 << i)


def toggle_bit(n: int, i: int) -> int:
    return n ^ (1 << i)


n = 0b1010
print(is_set(n, 1))      # True
print(set_bit(n, 0))     # 11 -> 0b1011
print(clear_bit(n, 3))   # 2  -> 0b0010
print(toggle_bit(n, 1))  # 8  -> 0b1000
```

---

## 5) Most Important Bit Tricks

### A) Remove lowest set bit

```python
n = 0b1100  # 12
print(n & (n - 1))  # 8 (0b1000)
```

### B) Extract lowest set bit

```python
def lowbit(n: int) -> int:
    return n & -n

print(lowbit(12))  # 4
```

### C) Check power of 2

```python
def is_power_of_two(n: int) -> bool:
    return n > 0 and (n & (n - 1)) == 0

print(is_power_of_two(16))  # True
print(is_power_of_two(18))  # False
```

### D) Check odd/even quickly

```python
def is_odd(n: int) -> bool:
    return (n & 1) == 1
```

---

## 6) Popcount / Counting Set Bits

### Method 1: Brian Kernighan Algorithm

```python
def popcount(n: int) -> int:
    c = 0
    while n:
        n &= n - 1
        c += 1
    return c

print(popcount(13))  # 3
```

### Method 2: Python built-in

```python
print((13).bit_count())  # 3
```

For production Python code, prefer `int.bit_count()`.

---

## 7) Interview Patterns with XOR

### A) Single number (others appear twice)

```python
def single_number(nums: list[int]) -> int:
    x = 0
    for v in nums:
        x ^= v
    return x

print(single_number([2, 3, 2, 4, 4]))  # 3
```

Why: `a ^ a = 0`, `0 ^ x = x`.

### B) Two unique numbers (others appear twice)

```python
def two_single_numbers(nums: list[int]) -> tuple[int, int]:
    xr = 0
    for v in nums:
        xr ^= v

    # rightmost set bit where the two unique values differ
    diff = xr & -xr

    a = b = 0
    for v in nums:
        if v & diff:
            a ^= v
        else:
            b ^= v
    return a, b
```

### C) XOR from `1` to `n` in O(1)

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
```

---

## 8) Bitmasking for Subsets and State Compression

For an array of size `n`, mask `0..(1<<n)-1` can represent every subset.

### Generate all subsets

```python
def subsets(arr: list[int]) -> list[list[int]]:
    n = len(arr)
    out = []

    for mask in range(1 << n):
        cur = []
        for i in range(n):
            if mask & (1 << i):
                cur.append(arr[i])
        out.append(cur)

    return out

print(subsets([1, 2, 3]))
```

### Represent a set with bits

```python
mask = 0

# add 2 and 5
mask |= (1 << 2)
mask |= (1 << 5)

# check membership
print(bool(mask & (1 << 2)))  # True

# remove 2
mask &= ~(1 << 2)
```

### Iterate all submasks of a mask

```python
mask = 0b1101
sub = mask
while sub:
    print(bin(sub))
    sub = (sub - 1) & mask
print(bin(0))
```

---

## 9) Advanced: Fixed Width, Two's Complement, and Shifts

### Two's complement intuition

In fixed width (e.g., 8 bits), negative numbers are represented using two's complement.

Python integers are arbitrary precision, so emulate width when needed.

```python
MASK32 = (1 << 32) - 1

x = -5
u = x & MASK32
print(u)  # unsigned 32-bit view
```

### Convert 32-bit unsigned → signed

```python
def to_signed32(x: int) -> int:
    x &= 0xFFFFFFFF
    if x & (1 << 31):
        return x - (1 << 32)
    return x
```

### Arithmetic vs logical right shift

- Python `>>` is **arithmetic** (sign-preserving).
- Python has no `>>>`.

Emulate logical right shift:

```python
def logical_rshift(x: int, k: int, width: int = 32) -> int:
    return (x % (1 << width)) >> k

print(logical_rshift(-2, 1))  # 2147483647 for width=32
```

---

## 10) Advanced Pattern: DP over Subsets

Typical state: `dp[mask]` where `mask` encodes visited/chosen elements.

Example skeleton (minimum cost to build subsets):

```python
def subset_dp_example(cost: list[int]) -> int:
    n = len(cost)
    INF = 10**18
    dp = [INF] * (1 << n)
    dp[0] = 0

    for mask in range(1 << n):
        for i in range(n):
            if not (mask & (1 << i)):  # i not chosen
                nxt = mask | (1 << i)
                dp[nxt] = min(dp[nxt], dp[mask] + cost[i])

    return dp[(1 << n) - 1]
```

This pattern appears in:
- Traveling Salesman Problem (TSP)
- Assignment matching variants
- Hamiltonian path counting

---

## 11) Performance Notes

- On fixed-width hardware, bitwise ops are effectively O(1).
- In Python, very large ints can make operations scale with number of machine words.
- `n &= n - 1` loops only over set bits.
- Subset iteration is O(2^n); subset-DP often O(n·2^n).

---

## 12) Common Mistakes

1. Assuming `~x` behaves like fixed-width NOT.
2. Forgetting to guard `n > 0` in power-of-two check.
3. Confusing bit index direction.
4. Using arithmetic shift when logical shift is required.
5. Overusing bit hacks when readability is more important.

---

## 13) Quick Cheat Sheet

```python
# test i-th bit
(n & (1 << i)) != 0

# set / clear / toggle i-th bit
n | (1 << i)
n & ~(1 << i)
n ^ (1 << i)

# remove / get lowest set bit
n & (n - 1)
n & -n

# power of two
n > 0 and (n & (n - 1)) == 0

# odd/even
n & 1

# popcount
n.bit_count()
```

---

## 14) Practice Roadmap

### Beginner
1. Implement bit get/set/clear/toggle.
2. Check odd/even, power of two.
3. Count set bits.

### Intermediate
4. Single number / two single numbers.
5. Generate subsets with mask.
6. Reverse bits of 32-bit integer.

### Advanced
7. Maximum XOR pair.
8. Trie + XOR queries.
9. TSP/assignment with subset DP.

---

## Recommended Study Method

- Build a `bit_practice.py` file and implement every snippet yourself.
- For each function, test edge cases (`0`, `1`, powers of two, negatives, large values).
- Then solve 10-15 LeetCode/Codeforces bit problems in increasing difficulty.

