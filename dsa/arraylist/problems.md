 
✅ Two Pointers  
✅ Sliding Window  
✅ Prefix Sum  
✅ Binary Search  
✅ Sorting + Searching  
✅ In-Place Manipulation  
✅ DP on Arrays  
✅ Matrix Traversal  
✅ Greedy on Arrays  
✅ Stack/Deque Patterns

—

## 📝 20 ARRAY PROBLEMS 

> 💡 Use `ArrayList<Integer>` or `int[]` as input — your choice.  
> 💡 Assume all inputs are valid unless stated.  
> 💡 Aim for optimal time/space — but brute force first is okay.

—

### 🟢 Easy (Warm-up)

1. **Remove Element**  
   Given `nums = [3,2,2,3]`, `val = 3` → remove all 3s → return new length = 2, `nums = [2,2,...]`.

2. **Best Time to Buy and Sell Stock I**  
   Given prices `[7,1,5,3,6,4]` → max profit = 5 (buy at 1, sell at 6).

3. **Contains Duplicate**  
   Given `[1,2,3,1]` → return `true`. `[1,2,3,4]` → `false`.

4. **Majority Element**  
   Given `[3,2,3]` → return `3` (appears > n/2 times).

5. **Plus One**  
   Given `[1,2,3]` → return `[1,2,4]`. Given `[9,9,9]` → `[1,0,0,0]`.

—

### 🟡 Medium (Core Patterns)

6. **Two Sum II — Input Array Is Sorted**  
   Given sorted `[2,7,11,15]`, target=9 → return `[1,2]` (1-indexed).

7. **3Sum**  
   Given `[-1,0,1,2,-1,-4]` → return all unique triplets that sum to 0 → `[[-1,-1,2],[-1,0,1]]`.

8. **Container With Most Water**  
   Given `[1,8,6,2,5,4,8,3,7]` → max area between two lines = 49.

9. **Next Permutation**  
   Given `[1,2,3]` → rearrange to `[1,3,2]`. If last permutation (e.g. `[3,2,1]`) → wrap to `[1,2,3]`.

10. **Rotate Image (2D Matrix)**  
    Given `[[1,2,3],[4,5,6],[7,8,9]]` → rotate 90° clockwise → `[[7,4,1],[8,5,2],[9,6,3]]`.

11. **Product of Array Except Self**  
    Given `[1,2,3,4]` → return `[24,12,8,6]` — no division allowed.

12. **Find First and Last Position of Element in Sorted Array**  
    Given `[5,7,7,8,8,10]`, target=8 → return `[3,4]`.

13. **Merge Intervals**  
    Given `[[1,3],[2,6],[8,10],[15,18]]` → return `[[1,6],[8,10],[15,18]]`.

14. **Insert Interval**  
    Given intervals `[[1,3],[6,9]]` and newInterval `[2,5]` → return `[[1,5],[6,9]]`.

15. **Spiral Matrix**  
    Given `[[1,2,3],[4,5,6],[7,8,9]]` → return `[1,2,3,6,9,8,7,4,5]`.

—

### 🔴 Hard (Advanced Patterns)

16. **Trapping Rain Water**  
    Given `[0,1,0,2,1,0,1,3,2,1,2,1]` → return `6` units of water trapped.

17. **Largest Rectangle in Histogram**  
    Given `[2,1,5,6,2,3]` → return `10` (rectangle formed by bars 5 and 6).

18. **Maximum Product Subarray**  
    Given `[2,3,-2,4]` → return `6` (subarray `[2,3]`). Given `[-2,0,-1]` → `0`.

19. **Sliding Window Maximum**  
    Given `[1,3,-1,-3,5,3,6,7]`, k=3 → return `[3,3,5,5,6,7]`.

20. **Jump Game II**  
    Given `[2,3,1,1,4]` → min jumps to reach end = 2 (index 0→1→4).

—

## 🧭 How We’ll Solve Them Together

When you’re ready, say:

> **“Let’s solve problem #X together.”**

I’ll guide you step by step:

1. 🎯 Understand the problem + edge cases  
2. 🧠 Brainstorm brute force → then optimize  
3. ✍️ Write clean Java code — with comments  
4. ⏱️ Analyze time/space complexity  
5. 💡 Reveal the pattern / real-world use  
6. 🚫 Discuss common mistakes/anti-patterns

—

## 💡 Pro Tip: Before We Start Solving

Try to **categorize each problem** by pattern:

- Two Pointers? → #1, #6, #8  
- Sliding Window? → #19  
- Binary Search? → #12  
- DP? → #18, #20  
- Stack? → #17  
- In-Place? → #1, #5, #9, #10  
- Sorting? → #7, #13, #14  
- Matrix? → #10, #15  
- Greedy? → #2, #4, #20


Let’s turn you into an **array problem-solving machine**. 💪🧠

Pick your first challenge.
