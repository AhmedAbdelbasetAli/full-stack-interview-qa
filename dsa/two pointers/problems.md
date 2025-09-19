

✅ **Collision (Start + End)** — for sorted arrays, palindromes, containers  
✅ **Fast/Slow (Tortoise & Hare)** — for linked lists, cycles, middle  
✅ **Read/Write (In-Place Manipulation)** — for removing, compacting, partitioning

—

# 🎯 TWO POINTERS — ALL PATTERNS + PROBLEMS

---

## 🔄 PATTERN 1: COLLISION (START + END) — Left and Right Move Toward Center

> Use when: Array is **sorted**, or you need to **compare ends**, or find **pairs**.

### 🟢 Easy

1. **Two Sum II — Input Array Is Sorted** (#167)  
   → *Given sorted array, find two indices that sum to target.*  
   ✅ Focus: Move left if sum too small, right if too large.

2. **Valid Palindrome** (#125)  
   → *Ignore non-alphanumeric, check if string is palindrome.*  
   ✅ Focus: Skip invalid chars, compare left and right.

3. **Reverse String** (#344)  
   → *Reverse char array in-place.*  
   ✅ Focus: Swap left and right until they meet.

### 🟡 Medium

4. **Container With Most Water** (#11)  
   → *Max area between two lines.*  
   ✅ Focus: Move shorter line — greedy choice.

5. **3Sum** (#15)  
   → *Find all triplets that sum to 0.*  
   ✅ Focus: Fix one, use two pointers for other two + deduplication.

6. **3Sum Closest** (#16)  
   → *Find triplet with sum closest to target.*  
   ✅ Focus: Track min difference, same as 3Sum.

7. **Two Sum Less Than K** (Premium, but common)  
   → *Find max sum < K.*  
   ✅ Focus: Sort, then two pointers.

### 🔴 Hard

8. **Trapping Rain Water** (#42)  
   → *Calculate water trapped between bars.*  
   ✅ Focus: LeftMax, RightMax → collision pointers.

9. **Sort Colors (Dutch National Flag)** (#75)  
   → *Sort array of 0s,1s,2s in-place.*  
   ✅ Focus: Three pointers — low, mid, high.

---

## 🐢 PATTERN 2: FAST/SLOW (TORTOISE & HARE) — Different Speeds

> Use when: **Linked list** — find cycle, middle, or intersect.

### 🟢 Easy

10. **Linked List Cycle** (#141)  
    → *Detect if linked list has cycle.*  
    ✅ Focus: Fast moves 2x, slow 1x — if meet, cycle exists.

11. **Middle of the Linked List** (#876)  
    → *Return middle node.*  
    ✅ Focus: Fast moves 2x, slow 1x — when fast ends, slow is at middle.

### 🟡 Medium

12. **Linked List Cycle II** (#142)  
    → *Return start of cycle.*  
    ✅ Focus: After detecting cycle, reset one pointer to head, move both 1x.

13. **Palindrome Linked List** (#234)  
    → *Check if linked list is palindrome.*  
    ✅ Focus: Find middle, reverse second half, compare with first half.

14. **Remove Nth Node From End of List** (#19)  
    → *Remove nth node from end.*  
    ✅ Focus: Fast moves n steps first, then both move until fast ends.

---

## ✍️ PATTERN 3: READ/WRITE (IN-PLACE MANIPULATION) — Modify Array in One Pass

> Use when: Need to **remove, compact, or partition** array **in-place**.

### 🟢 Easy

15. **Remove Element** (#27)  
    → *Remove all instances of val in-place.*  
    ✅ Focus: Read pointer scans, write pointer writes if not val.

16. **Remove Duplicates from Sorted Array** (#26)  
    → *Remove duplicates in sorted array in-place.*  
    ✅ Focus: Write only if different from previous.

17. **Move Zeroes** (#283)  
    → *Move all zeroes to end, keep order.*  
    ✅ Focus: Write non-zeroes to front, fill rest with zeroes.

### 🟡 Medium

18. **Remove Duplicates from Sorted Array II** (#80)  
    → *Allow at most 2 duplicates.*  
    ✅ Focus: Track count, write if count <= 2.

19. **Partition Array by Parity** (#905)  
    → *All evens before odds.*  
    ✅ Focus: Write evens to front, odds to back — or use two pointers from ends.

20. **Sort Array By Parity II** (#922)  
    → *Even index → even number, odd index → odd number.*  
    ✅ Focus: Two pointers for even and odd indices.

21. **Merge Sorted Array** (#88)  
    → *Merge two sorted arrays in-place (nums1 has space).*  
    ✅ Focus: Start from end — avoid overwriting.

---

## 🧩 BONUS: HYBRID / ADVANCED

22. **Minimum Size Subarray Sum** (#209) — Sliding Window + Two Pointers  
23. **Longest Mountain in Array** (#845) — Collision + Expand  
24. **Valid Triangle Number** (#611) — Sort + Two Pointers (like 3Sum)  
25. **Subarray Product Less Than K** (#713) — Sliding Window (but often solved with two pointers)

---

# 🧠 HOW TO PRACTICE — SENIOR ENGINEER METHOD

For each problem:

1. **Identify the pattern** — Collision? Fast/Slow? Read/Write?
2. **Draw it out** — use pencil and paper — trace pointers.
3. **Code it** — without looking at solution.
4. **Test edge cases** — empty, single, all same, no match.
5. **Optimize** — can you reduce space? Simplify condition?

—

# 📊 TRACK YOUR PROGRESS

| Pattern       | Easy Solved | Medium Solved | Hard Solved |
|---------------|-------------|---------------|-------------|
| Collision     |             |               |             |
| Fast/Slow     |             |               |             |
| Read/Write    |             |               |             |

→ Print this. Fill it in. Own it.

—

# 🚀 FINAL TIP — MASTER THE TEMPLATES

### 🔄 Collision Template

```java
int left = 0, right = n - 1;
while (left < right) {
    int sum = arr[left] + arr[right];
    if (sum == target) return ...;
    else if (sum < target) left++;
    else right--;
}
```

### 🐢 Fast/Slow Template

```java
ListNode slow = head, fast = head;
while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
    if (slow == fast) { ... } // cycle detected
}
```

### ✍️ Read/Write Template

```java
int writeIndex = 0;
for (int readIndex = 0; readIndex < n; readIndex++) {
    if (condition) {
        arr[writeIndex] = arr[readIndex];
        writeIndex++;
    }
}
```

—
