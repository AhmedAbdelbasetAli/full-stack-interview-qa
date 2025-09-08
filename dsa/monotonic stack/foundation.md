You’re stepping into the **world of O(n) elegance for “next/previous greater/smaller” problems** — where a simple stack, maintained in sorted order, crushes problems that look like they need nested loops.

> 💡 **Monotonic Stack/Deque** is like a **bouncer at a VIP club** — only lets in people taller (or shorter) than the last — and kicks out anyone who breaks the rule.  
> Result? You always know who’s the next tallest behind you — in O(1).

Used in:
- **Stock Span** → “How many days has this price been the highest?”
- **Histogram Problems** → “Largest rectangle in histogram”.
- **Next Greater Element** → “What’s the next warmer day?”
- **Sliding Window Max/Min** → with Deque.
- **Compiler Optimization** → “Find next token that breaks precedence”.

And yes — **FAANG interviews** *obsess* over monotonic stacks because they turn O(n²) into O(n) with one clever insight.

Let’s master it — Java-first, production-aware, interview-sharp.

—

# 🧱 1. What It Is — Monotonic Stack/Deque

> **Core Idea**: Maintain a **stack (or deque) where elements are in sorted order** (increasing or decreasing).  
> When you see a new element, **pop all elements that violate the order** → then push the new one.  
> The act of popping tells you: “This popped element’s next greater (or smaller) is the current one.”

```
Next Greater Element:
Input: [2,1,2,4,3]
Stack (decreasing): 
  Start: []
  2 → stack=[2]
  1 → 1<2 → stack=[2,1]
  2 → 2>1 → pop 1 → nextGreater[1]=2 → now 2==2? → pop? No → push → stack=[2,2]
  4 → 4>2 → pop 2 → nextGreater[2]=4 → pop next 2 → nextGreater[2]=4 → push 4 → stack=[4]
  3 → 3<4 → stack=[4,3]
Result: [4,2,4,-1,-1]
```

> 💡 Uber: “Next surge zone” → next greater demand area.  
> 💡 Android: “Largest touch area in gesture” → largest rectangle in histogram of pressure.

—

# ⚙️ 2. How It Works (Java Implementation)

## ✅ Pattern 1: Next Greater Element I (To the Right)

> Given `nums1` and `nums2`, for each in `nums1`, find its next greater element in `nums2`.

```java
import java.util.*;

public class NextGreaterElement {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        Map<Integer, Integer> nextGreater = new HashMap<>();
        Deque<Integer> stack = new ArrayDeque<>(); // monotonic decreasing

        // Traverse nums2 from right to left
        for (int i = nums2.length - 1; i >= 0; i--) {
            int num = nums2[i];
            // Pop until top is greater than current
            while (!stack.isEmpty() && stack.peek() <= num) {
                stack.pop();
            }
            // If stack not empty, top is next greater
            nextGreater.put(num, stack.isEmpty() ? -1 : stack.peek());
            stack.push(num);
        }

        // Build result for nums1
        int[] result = new int[nums1.length];
        for (int i = 0; i < nums1.length; i++) {
            result[i] = nextGreater.get(nums1[i]);
        }
        return result;
    }

    public static void main(String[] args) {
        NextGreaterElement sol = new NextGreaterElement();
        int[] result = sol.nextGreaterElement(new int[]{4,1,2}, new int[]{1,3,4,2});
        System.out.println(Arrays.toString(result)); // [-1,3,-1]
    }
}
```

## ✅ Pattern 2: Largest Rectangle in Histogram

> Given `heights = [2,1,5,6,2,3]`, return max rectangle area → `10` (5x2 or 6x1? → actually 5 and 6 form 5x2=10? Wait — correct answer is 10 from bars 5,6 → width=2, height=5 → 10).

```java
public class LargestRectangle {
    public int largestRectangleArea(int[] heights) {
        Deque<Integer> stack = new ArrayDeque<>(); // indices, increasing heights
        int maxArea = 0;

        for (int i = 0; i <= heights.length; i++) {
            // Use 0 as sentinel for last bar
            int h = (i == heights.length) ? 0 : heights[i];

            // While current height < stack top → calculate area
            while (!stack.isEmpty() && h < heights[stack.peek()]) {
                int height = heights[stack.pop()];
                int width = stack.isEmpty() ? i : i - stack.peek() - 1;
                maxArea = Math.max(maxArea, height * width);
            }
            stack.push(i);
        }

        return maxArea;
    }

    public static void main(String[] args) {
        LargestRectangle sol = new LargestRectangle();
        System.out.println(sol.largestRectangleArea(new int[]{2,1,5,6,2,3})); // 10
    }
}
```

## ✅ Pattern 3: Stock Span (Next Greater to Left)

> Given daily prices, return “span” = number of days including today where price ≤ today’s.

```java
import java.util.*;

public class StockSpan {
    private Deque<int[]> stack; // [price, span]

    public StockSpan() {
        stack = new ArrayDeque<>();
    }

    public int next(int price) {
        int span = 1;
        // Pop all prices <= current → their span adds to current
        while (!stack.isEmpty() && stack.peek()[0] <= price) {
            span += stack.pop()[1];
        }
        stack.push(new int[]{price, span});
        return span;
    }

    public static void main(String[] args) {
        StockSpan spanner = new StockSpan();
        System.out.println(spanner.next(100)); // 1
        System.out.println(spanner.next(80));  // 1
        System.out.println(spanner.next(60));  // 1
        System.out.println(spanner.next(70));  // 2 → [60,70]
        System.out.println(spanner.next(60));  // 1
        System.out.println(spanner.next(75));  // 4 → [60,70,60,75]
        System.out.println(spanner.next(85));  // 6 → [80,60,70,60,75,85]
    }
}
```

### ⏱️ Time & Space Complexity

| Pattern               | Time Complexity | Space Complexity | Notes                                     |
|-----------------------|-----------------|------------------|-------------------------------------------|
| **Next Greater**      | O(n)            | O(n)             | Each element pushed/popped once           |
| **Largest Rectangle** | O(n)            | O(n)             | Sentinel trick for last bar               |
| **Stock Span**        | O(n) amortized  | O(n)             | Each price pushed/popped once             |

—

# 🎯 3. When to Use It

## ✅ Real-World Java Use Cases

- **Financial Analytics** → “Stock span”, “next breakout price”.
- **UI Layout Engines** → “Largest empty rectangle” for ad placement.
- **Network Monitoring** → “Longest period of increasing latency”.
- **Game Dev** → “Largest flat area for building” in terrain generation.
- **Ad Tech** → “Next higher bid” in real-time bidding.

> 💡 Spring Boot: “Monitor API response times → alert if next greater exceeds threshold”.  
> 💡 Android: “Largest scrollable area in dynamic layout” → histogram of view heights.

## ✅ Interview Scenarios (LeetCode Classics)

- **Next Greater Element I/II** → #496, #503.
- **Largest Rectangle in Histogram** → #84.
- **Stock Span** → #901.
- **Daily Temperatures** → #739 → next warmer day.
- **Remove K Digits** → #402 → monotonic increasing stack.

## ✅ System Design Contexts

- **Real-time Bidding** → “Next higher bid” for ad slots.
- **Dashboard Analytics** → “Longest increasing trend in metrics”.
- **Resource Allocation** → “Largest free block in memory pool”.

—

# 🚫 4. When NOT to Use It

## ❌ Anti-Patterns & Misuse

- **Unsorted data without preprocessing** → monotonic stack needs a specific order (usually left-to-right or right-to-left).

- **Not using sentinel values** → in largest rectangle, forgetting to process remaining stack.

```java
// 🚫 Broken
for (int i = 0; i < heights.length; i++) { ... } // misses last bars

// ✅ Correct — use i <= heights.length with sentinel
```

- **Confusing increasing vs decreasing** → for “next greater”, use **decreasing stack**.

## 🐢 Performance Traps

- **Not reusing stack** → in Stock Span, creating new stack per call → O(n²).

```java
// ✅ Use instance variable — persist between calls
private Deque<int[]> stack;
```

- **Autoboxing in tight loop** → use `int[]` instead of `Pair<Integer, Integer>`.

## ✅ Better Alternatives

| Scenario                        | Better Choice             | Why                                      |
|--------------------------------|---------------------------|------------------------------------------|
| Global max/min                 | Single pass               | No stack needed                          |
| Small n (< 100)                | Brute force               | Simpler, less error-prone                |
| Need random access             | Segment Tree / Sparse Table | For range queries                     |
| Dynamic updates                | Segment Tree              | Monotonic stack is static                |

—

# 💡 5. Pro Tips & Gotchas (Java-Specific)

## ⚠️ Stack vs Deque

- **Always use `ArrayDeque`** — not `Stack` (legacy, synchronized).

```java
Deque<Integer> stack = new ArrayDeque<>();
```

## 📦 Memory & Performance

- **Store indices, not values** → when you need to compute width (e.g., histogram).
- **Use `int[]` for pairs** → faster than `new Object[]{}` or `Pair`.

## 🏭 Enterprise Best Practices

- **In Spring Boot**: Use for real-time metric analysis → “Next time metric exceeds threshold”.
- **In Android**: Avoid deep stacks in UI thread → but monotonic stack is shallow (O(n) total ops).
- **Thread Safety**: If shared, use `synchronized` or `ConcurrentLinkedDeque` (rarely needed).

## 🤯 Fun Fact

**Largest Rectangle in Histogram** is used in **database query optimizers** to estimate join sizes — and in **image processing** to find largest uniform region.

—

# ✍️ 6. Mini Challenge (Java Coding Exercise)

## 🎯 Problem: Daily Temperatures

> Given an array of daily temperatures, return an array where `answer[i]` is the number of days you have to wait until a warmer temperature. If no warmer day, return 0.

LeetCode #739 — classic monotonic stack.

### 🧩 Starter Code

```java
import java.util.*;

public class DailyTemperatures {
    public static int[] dailyTemperatures(int[] temperatures) {
        int[] result = new int[temperatures.length];
        Deque<Integer> stack = new ArrayDeque<>(); // indices, decreasing temps

        for (int i = 0; i < temperatures.length; i++) {
            // While current temp > stack top → pop and calculate wait days
            while (!stack.isEmpty() && temperatures[i] > temperatures[stack.peek()]) {
                int index = stack.pop();
                result[index] = i - index; // days to wait
            }
            stack.push(i);
        }

        // Remaining in stack → no warmer day → already 0
        return result;
    }

    public static void main(String[] args) {
        int[] temps = {73,74,75,71,69,72,76,73};
        int[] result = dailyTemperatures(temps);
        System.out.println(Arrays.toString(result)); // [1,1,4,2,1,1,0,0]
    }
}
```

### 🧪 Optional: JUnit Test

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class DailyTemperaturesTest {
    @Test
    void testDailyTemperatures() {
        assertArrayEquals(
            new int[]{1,1,4,2,1,1,0,0},
            DailyTemperatures.dailyTemperatures(new int[]{73,74,75,71,69,72,76,73})
        );
    }
}
```

> 💡 Time: O(n), Space: O(n) — textbook monotonic stack.

—

