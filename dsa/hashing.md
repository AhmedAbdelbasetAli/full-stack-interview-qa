# 🔐 Hashing Deep Dive  
**HashMap Internals, Collisions, Two Sum & Frequency Count**

This document covers **essential hashing concepts** with **Java implementations**, **step-by-step explanations**, and **interview tips**.

All code is written in **Java**.

---

## 1. How does a HashMap work internally in Java?

> `HashMap` is a **key-value store** that provides **average O(1) time** for `get`, `put`, and `containsKey`.

It uses **hashing** to map keys to array indices.

---

### 🔹 1.1 Core Components

| Component | Purpose |
|---------|--------|
| **Array of Buckets** | Stores entries (`Node<K,V>[] table`) |
| **Hash Function** | `hash(key)` → int (uses `hashCode()` and bitwise operations) |
| **Index Calculation** | `index = (n - 1) & hash` (where `n` = array length, power of 2) |
| **Buckets** | Each bucket holds a linked list or tree of entries |
| **Load Factor** | Default 0.75 — triggers resize when exceeded |
| **Resize** | Doubles array size when load factor exceeded |

---

### 🔹 1.2 `put(K key, V value)` Step-by-Step

1. Compute `hash(key)`
2. Find `index = (table.length - 1) & hash`
3. If bucket is empty → create new node
4. If bucket has entries:
   - Traverse list/tree
   - If key exists → **update value**
   - Else → **add new node**
5. If size > `threshold` (loadFactor * capacity) → **resize (rehash)**

---

### 🔹 1.3 Collision Handling: Chaining

- Each bucket holds a **linked list** of `Node<K,V>`
- In **Java 8+**, if a bucket has **> 8 nodes** and table size **> 64**, it **converts to a Red-Black Tree** for O(log n) worst-case

✅ This prevents O(n) degradation in worst-case (many collisions)

---

### 🔹 1.4 Key Methods

| Method | How It Works |
|-------|-------------|
| `hash(key)` | `(key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16)` |
| `indexFor(hash, length)` | `(length - 1) & hash` (only works when length is power of 2) |
| `resize()` | Double capacity, rehash all entries |

---

### 📌 Time Complexity
- **Average**: O(1) for `get`, `put`
- **Worst**: O(n) — if all keys hash to same bucket (rare with good hash)
- **With Tree Conversion**: O(log n) worst-case

---

### 💡 Interview Tip
> _"HashMap uses an array of buckets. It computes a hash from the key, maps it to an index, and stores the entry in a linked list. In Java 8+, long chains convert to trees for performance. It resizes when load factor (0.75) is exceeded. The hash function mixes bits to reduce collisions."_  

---

## 2. What is hash collision? How is it handled?

> A **hash collision** occurs when **two different keys produce the same hash code** or map to the same bucket.

Example: `"Aa"` and `"BB"` both have `hashCode() == 2112` in Java.

---

### 🔹 2.1 Why Collisions Happen
- **Hash space is finite** — `int` has 2³² values
- **More keys than buckets** — pigeonhole principle
- **Poor `hashCode()` implementation**

---

### 🔹 2.2 Collision Resolution Strategies

| Strategy | How It Works | Used In |
|--------|-------------|--------|
| ✅ **Chaining** | Store colliding entries in a linked list or tree | Java `HashMap` |
| ✅ **Open Addressing** | Probe next slot (linear, quadratic, double hashing) | Python `dict` (probing) |
| ✅ **Robin Hood Hashing** | Move entries to reduce variance | Advanced hash tables |

---

### 🔹 2.3 Chaining in Java HashMap

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next; // Next node in chain
}
```

When collision occurs:
1. Compute hash → index
2. Traverse the linked list at that bucket
3. If key exists → update value
4. Else → add to front (or end in tree mode)

---

### 🔹 2.4 Open Addressing (Python dict example)

- If bucket is full, probe next index: `(i + 1) % size`
- Continue until empty slot found
- Lookup: probe same sequence until key found or empty

---

### 📌 Best Practices to Reduce Collisions
| Rule | Why |
|------|-----|
| ✅ **Override `hashCode()` and `equals()` together** | Critical for custom objects |
| ✅ **Distribute bits well** | Java’s `hash()` method mixes high/low bits |
| ✅ **Use prime number table sizes** | Or power of 2 with masking (Java) |
| ✅ **Good `hashCode()` implementation** | Based on all significant fields |

---

### 💡 Interview Tip
> _"Collisions happen when different keys map to the same bucket. Java HashMap handles it with chaining — each bucket has a linked list. When the list gets long (8+), it converts to a tree for O(log n) lookup. This prevents performance degradation. I always override `hashCode` and `equals` correctly for custom keys."_  

---

## 3. How do you find all pairs in an array that sum to a target? (Two Sum)

> Given an array and a target, return **all pairs of indices** where `nums[i] + nums[j] == target`.

For **all pairs**, not just first.

---

### ✅ Approach: Hash Table for O(n) time

- Use a `Map<Integer, List<Integer>>` to store **value → list of indices**
- For each element, compute `complement = target - num`
- If complement exists in map, form pairs with all its indices
- Add current index to map

---

### ✅ Java Code
```java
import java.util.*;

public static List<int[]> twoSumAllPairs(int[] nums, int target) {
    List<int[]> pairs = new ArrayList<>();
    Map<Integer, List<Integer>> valueToIndices = new HashMap<>();

    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];

        if (valueToIndices.containsKey(complement)) {
            for (int j : valueToIndices.get(complement)) {
                pairs.add(new int[]{j, i}); // [index1, index2]
            }
        }

        // Add current index to map
        valueToIndices.computeIfAbsent(nums[i], k -> new ArrayList<>()).add(i);
    }

    return pairs;
}

// Example usage
public static void main(String[] args) {
    int[] nums = {1, 2, 3, 2, 4};
    int target = 5;
    List<int[]> pairs = twoSumAllPairs(nums, target);
    for (int[] pair : pairs) {
        System.out.println("[" + pair[0] + ", " + pair[1] + "]");
    }
    // Output: [0,4], [1,2], [1,3], [2,1], [3,1]
}
```

> ❗ Note: This returns **all pairs**, including `(1,2)` and `(2,1)` if both sum to target.

---

### 📌 Time & Space
- **Time**: O(n²) worst-case (if all elements are same and sum to target)
- **Average**: O(n)
- **Space**: O(n)

---

### 💡 Interview Tip
> _"I use a hash map from value to list of indices. For each number, I check if its complement exists. If yes, I form pairs with all previous indices of the complement. This is O(n) average time. For just one pair, I can return early. For all pairs, I must store all indices."_  

---

## 4. Count frequency of characters in a string using a hash table.

> Count how many times each character appears in a string.

---

### ✅ Approach: `HashMap<Character, Integer>`

- Traverse string
- For each char, increment count in map
- Return map

---

### ✅ Java Code
```java
import java.util.*;

public static Map<Character, Integer> countFrequency(String str) {
    Map<Character, Integer> freq = new HashMap<>();
    if (str == null) return freq;

    for (char c : str.toCharArray()) {
        freq.put(c, freq.getOrDefault(c, 0) + 1);
    }

    return freq;
}

// Example usage
public static void main(String[] args) {
    String s = "hello";
    Map<Character, Integer> result = countFrequency(s);
    System.out.println(result); // {h=1, e=1, l=2, o=1}
}
```

---

### ✅ Bonus: Sort by Frequency
```java
public static List<Character> sortByFrequency(String str) {
    Map<Character, Integer> freq = countFrequency(str);
    List<Character> chars = new ArrayList<>(freq.keySet());
    chars.sort((a, b) -> freq.get(b) - freq.get(a)); // Descending
    return chars;
}
```

---

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(k) where k = number of unique characters (≤ 26 for a-z)

---

### 💡 Interview Tip
> _"I use a HashMap to count character frequency. I traverse the string and use `getOrDefault` to increment counts. This is O(n) time. I can extend it to sort characters by frequency or find the most frequent character."_  

---

## ✅ Final Tips for Coding Interviews

1. **Clarify**: Case-sensitive? Include spaces? Return indices or values?
2. **Use `getOrDefault()`** to simplify frequency counting
3. **Override `hashCode` and `equals`** for custom objects as keys
4. **Know the difference** between Java (chaining + tree) and Python (probing)
5. **State time & space complexity** — especially average vs worst case

---
