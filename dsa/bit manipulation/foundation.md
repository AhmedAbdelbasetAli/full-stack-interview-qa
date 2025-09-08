You’re stepping into the **world of raw, metal-level computing** — where every bit matters, memory is tight, and speed is measured in CPU cycles.

> 💡 **Bit Manipulation** is like being a **ninja with a screwdriver inside the CPU** — flipping, shifting, masking bits to solve problems with elegance and brutal efficiency.

Used in:
- **Embedded Systems** → microcontrollers, sensors, memory-constrained devices.
- **Graphics & Game Engines** → color masks, collision detection.
- **Networking** → IP address manipulation, checksums.
- **Databases** → bitmap indexes, bloom filters.
- **Cryptography** → XOR ciphers, bit rotations.

And yes — **FAANG interviews** *love* bit manipulation for its “aha!” moments and low-level thinking.

Let’s master it — Java-first, production-aware, interview-sharp.

—

# 🧱 1. What It Is — Bit Manipulation

> **Analogy**: A **light switch panel**.  
> Each switch = 1 bit.  
> You can flip one (XOR), turn all off (AND 0), turn all on (OR 1), or check if a switch is on (AND 1).

- **Core Purpose**: Perform operations at the **bit level** — faster, less memory, closer to hardware.
- **Key Operators**:
  - `&` (AND) → mask bits
  - `|` (OR) → set bits
  - `^` (XOR) → toggle bits, find differences
  - `~` (NOT) → flip all bits
  - `<<`, `>>`, `>>>` → shift bits left/right

```
Example: 5 = 0b101
         3 = 0b011
5 & 3 = 0b001 → 1
5 | 3 = 0b111 → 7
5 ^ 3 = 0b110 → 6
~5 = ...11111010 (in two's complement)
5 << 1 = 0b1010 → 10
5 >> 1 = 0b10 → 2
```

> 💡 Uber: Geohash encoding → bit interleaving for location.  
> 💡 Android: Color manipulation → `0xAARRGGBB` → extract R with `(color >> 16) & 0xFF`.

—

# ⚙️ 2. How It Works (Java Implementation)

## ✅ Essential Bit Operations

```java
public class BitManipulation {
    public static void main(String[] args) {
        int x = 5;  // 0b101
        int y = 3;  // 0b011

        // AND — mask bits
        System.out.println(x & y); // 1 → 0b001

        // OR — set bits
        System.out.println(x | y); // 7 → 0b111

        // XOR — toggle / difference
        System.out.println(x ^ y); // 6 → 0b110

        // NOT — flip all bits (two's complement)
        System.out.println(~x);    // -6 → ...11111010

        // Left Shift — multiply by 2
        System.out.println(x << 1); // 10 → 0b1010

        // Right Shift (signed) — divide by 2, preserve sign
        System.out.println(x >> 1); // 2 → 0b10

        // Unsigned Right Shift — fill with 0s
        System.out.println(-1 >>> 1); // 2147483647 → 0b0111...111

        // Check if bit is set
        int bitPos = 2;
        boolean isSet = (x & (1 << bitPos)) != 0; // true → 0b101 has bit 2 set

        // Set bit
        x = x | (1 << bitPos); // already set → no change

        // Clear bit
        x = x & ~(1 << bitPos); // 0b101 → 0b001 → 1

        // Toggle bit
        x = x ^ (1 << bitPos); // 0b001 → 0b101 → 5
    }
}
```

## ✅ Common Bit Tricks

```java
// Is power of two? → only one bit set
public static boolean isPowerOfTwo(int n) {
    return n > 0 && (n & (n - 1)) == 0;
}

// Count set bits (Brian Kernighan’s Algorithm)
public static int countSetBits(int n) {
    int count = 0;
    while (n != 0) {
        n = n & (n - 1); // clear lowest set bit
        count++;
    }
    return count;
}

// Get lowest set bit
public static int lowestSetBit(int n) {
    return n & -n; // two's complement magic
}

// XOR swap (without temp variable) — rarely used, but cool
public static void swap(int[] arr, int i, int j) {
    if (i != j) {
        arr[i] ^= arr[j];
        arr[j] ^= arr[i];
        arr[i] ^= arr[j];
    }
}
```

### ⏱️ Time & Space Complexity

| Operation               | Time Complexity | Space Complexity | Notes                                     |
|------------------------|-----------------|------------------|-------------------------------------------|
| **Bitwise op (&,|,^,~,<<,>>)** | O(1)           | O(1)             | Single CPU instruction                    |
| **Count set bits**     | O(k)            | O(1)             | k = number of set bits (Brian Kernighan)  |
| **Check/set/clear bit**| O(1)            | O(1)             | With bit shifting                         |

—

# 🎯 3. When to Use It

## ✅ Real-World Java Use Cases

- **Color Manipulation** → Android `Color` class → `(color >> 16) & 0xFF` for red.
- **Permission Flags** → `READ = 1, WRITE = 2, EXECUTE = 4` → `userPerms = READ | WRITE`.
- **Network Protocols** → IP address subnet masking → `ip & subnetMask`.
- **Database Indexes** → Bitmap indexes for low-cardinality columns.
- **Game Dev** → Collision layers → `layerMask = PLAYER | ENEMY`.

> 💡 Spring Boot: Feature flags as bit fields → `flags = FEATURE_A | FEATURE_C`.  
> 💡 Android: `View` visibility → `VISIBLE = 0x00000000`, `INVISIBLE = 0x00000004`.

## ✅ Interview Scenarios (LeetCode Classics)

- **Single Number** → #136 → XOR all → duplicates cancel.
- **Number of 1 Bits** → #191 → Brian Kernighan or `Integer.bitCount()`.
- **Power of Two** → #231 → `n > 0 && (n & (n-1)) == 0`.
- **Reverse Bits** → #190 → shift and mask.
- **Missing Number** → #268 → XOR trick.

## ✅ System Design Contexts

- **Bloom Filters** → Bit arrays + hash functions → “Is this item possibly in set?”.
- **Bitmap Indexes** → Databases → fast filtering on gender, status, etc.
- **Memory Pools** → Allocate/free blocks → bit = free/used.
- **Hash Functions** → Bit mixing for better distribution.

—

# 🚫 4. When NOT to Use It

## ❌ Anti-Patterns & Misuse

- **Premature optimization** → If `n < 1000`, just use `Set<Integer>` — clearer, safer.

```java
// 🚫 Overkill
if ((flags & FEATURE_A) != 0) { ... }

// ✅ Often clearer
if (features.contains("A")) { ... }
```

- **Signed right shift on negative numbers** → if you want logical shift, use `>>>`.

```java
// 🚫 -1 >> 1 = -1 (sign extended)
// ✅ -1 >>> 1 = 2147483647 (logical shift)
```

- **Assuming int is 32-bit everywhere** → Java `int` is always 32-bit — safe.

## 🐢 Performance Traps

- **Not using `Integer.bitCount()`** → it’s optimized (often single CPU instruction).

```java
// ✅ Use built-in
int count = Integer.bitCount(n);

// ❌ Unless you need to show Brian Kernighan in interview
```

- **XOR swap in production** → hard to read, no performance gain, breaks if i == j.

## ✅ Better Alternatives

| Scenario                        | Better Choice             | Why                                      |
|--------------------------------|---------------------------|------------------------------------------|
| Readability > performance      | `Set`, `boolean[]`        | Clearer intent                           |
| Large bitmaps                  | `BitSet`                  | Built-in, safe, supports large indices   |
| Need atomic bit ops            | `AtomicInteger` + masks   | Thread-safe bit flags                    |
| Complex bit fields             | `EnumSet`                 | Type-safe, optimized for enums           |

—

# 💡 5. Pro Tips & Gotchas (Java-Specific)

## ⚠️ Use `BitSet` for Large or Dynamic Bit Vectors

```java
BitSet bs = new BitSet(1000);
bs.set(10);      // set bit 10
bs.clear(10);    // clear bit 10
bs.get(10);      // check bit 10
bs.cardinality(); // count set bits
```

→ More readable, safer, and handles large indices.

## 📦 Memory & Performance

- **`int` flags** → 32 flags in 4 bytes → ultra compact.
- **`BitSet`** → grows dynamically → good for sparse large sets.
- **`EnumSet`** → for enum flags → type-safe, optimized.

## 🏭 Enterprise Best Practices

- **In Spring Boot**: Use `@Flag` or custom annotations for feature flags → but store as `int` bitmask in DB for efficiency.
- **In Android**: Use `View.setSystemUiVisibility(int flags)` → classic bit flag API.
- **Logging bit states** → log as hex: `System.out.printf("Flags: 0x%08X%n", flags);`

## 🤯 Fun Fact

**XOR is its own inverse** → `a ^ b ^ b = a`.  
→ This is why **“Single Number”** works: `a ^ a = 0`, `0 ^ b = b`.

—

# ✍️ 6. Mini Challenge (Java Coding Exercise)

## 🎯 Problem: Single Number

> Given a non-empty array of integers `nums`, every element appears twice except for one. Find that single one.  
> Must implement with O(1) space.  
> Example: `[4,1,2,1,2]` → `4`.

LeetCode #136 — the XOR classic.

### 🧩 Starter Code

```java
public class SingleNumber {
    public static int singleNumber(int[] nums) {
        int result = 0;
        for (int num : nums) {
            result ^= num; // duplicates cancel out, single remains
        }
        return result;
    }

    public static void main(String[] args) {
        int[] nums = {4,1,2,1,2};
        System.out.println(singleNumber(nums)); // 4
    }
}
```

### 🧪 Optional: JUnit Test

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class SingleNumberTest {
    @Test
    void testSingleNumber() {
        assertEquals(4, SingleNumber.singleNumber(new int[]{4,1,2,1,2}));
        assertEquals(1, SingleNumber.singleNumber(new int[]{1}));
    }
}
```

> 💡 Time: O(n), Space: O(1) — beautiful XOR solution.

—

