# 📚 Stacks & Queues Deep Dive  
**Implementations, Balanced Parentheses, and Monotonic Stack**

Here are **4 essential stack and queue problems** with **clean Java implementations**, **step-by-step explanations**, and **interview tips**.

All code is written in **Java**.

---

## 1. How do you implement a stack using queues?

> Implement a **stack (LIFO)** using one or two **queues (FIFO)**.

We'll use **two queues** for efficient `pop()`.

---

### ✅ Approach: Two Queues

- **`push(x)`**: Add to the non-empty queue
- **`pop()`**: Transfer all but last element to the other queue, then remove and return the last
- **`top()`**: Same as `pop()`, but add the last element back
- **`empty()`**: Both queues empty

---

### ✅ Java Code
```java
import java.util.*;

public class StackUsingQueues {
    private Queue<Integer> q1 = new LinkedList<>();
    private Queue<Integer> q2 = new LinkedList<>();

    // Push: O(1)
    public void push(int x) {
        if (!q1.isEmpty()) {
            q1.offer(x);
        } else {
            q2.offer(x);
        }
    }

    // Pop: O(n)
    public int pop() {
        Queue<Integer> dataQueue = !q1.isEmpty() ? q1 : q2;
        Queue<Integer> tempQueue = q1.isEmpty() ? q1 : q2;

        // Move all but last to temp queue
        while (dataQueue.size() > 1) {
            tempQueue.offer(dataQueue.poll());
        }

        return dataQueue.poll(); // Last element
    }

    // Top: O(n)
    public int top() {
        int val = pop();
        push(val); // Put it back
        return val;
    }

    // Empty: O(1)
    public boolean empty() {
        return q1.isEmpty() && q2.isEmpty();
    }
}
```

---

### 📌 Time & Space
- **`push`**: O(1)
- **`pop` / `top`**: O(n)
- **`empty`**: O(1)
- **Space**: O(n)

---

### 💡 Interview Tip
> _"I use two queues. For `push`, I add to the active queue. For `pop`, I move all but the last element to the other queue, then return the last. This makes `push` fast. Alternatively, I could make `pop` O(1) by shifting on `push`, but this approach is more intuitive."_  

---

## 2. How do you implement a queue using stacks?

> Implement a **queue (FIFO)** using two **stacks (LIFO)**.

---

### ✅ Approach: Two Stacks

- **`inputStack`**: For `push` operations
- **`outputStack`**: For `pop` and `peek`
- When `outputStack` is empty, **transfer all elements** from `inputStack`

---

### ✅ Java Code
```java
import java.util.*;

public class QueueUsingStacks {
    private Stack<Integer> input = new Stack<>();
    private Stack<Integer> output = new Stack<>();

    // Push: O(1)
    public void push(int x) {
        input.push(x);
    }

    // Pop: Amortized O(1)
    public int pop() {
        if (output.isEmpty()) {
            transfer();
        }
        return output.pop();
    }

    // Peek: Amortized O(1)
    public int peek() {
        if (output.isEmpty()) {
            transfer();
        }
        return output.peek();
    }

    // Empty: O(1)
    public boolean empty() {
        return input.isEmpty() && output.isEmpty();
    }

    // Transfer from input to output
    private void transfer() {
        while (!input.isEmpty()) {
            output.push(input.pop());
        }
    }
}
```

---

### 📌 Time & Space
- **`push`**: O(1)
- **`pop` / `peek`**: **Amortized O(1)** — each element moved at most once
- **`empty`**: O(1)
- **Space**: O(n)

---

### 💡 Interview Tip
> _"I use two stacks: one for input, one for output. I only transfer from input to output when output is empty. This makes `pop` and `peek` amortized O(1). It's efficient and mirrors how a real queue works — enqueue at one end, dequeue at the other."_  

---

## 3. Use a stack to check for balanced parentheses.

> Given a string containing `()`, `{}`, `[]`, determine if it's **balanced**.

Example: `"()[]{}"` → `true`  
Example: `"(]"` → `false`

---

### ✅ Approach
- Use a **stack** to track opening brackets
- For each character:
  - If opening bracket → push to stack
  - If closing bracket → pop and check match
- At end, stack must be empty

---

### ✅ Java Code
```java
import java.util.*;

public static boolean isBalanced(String s) {
    if (s == null || s.isEmpty()) return true;

    Map<Character, Character> pairs = Map.of(
        ')', '(',
        '}', '{',
        ']', '['
    );
    Stack<Character> stack = new Stack<>();

    for (char c : s.toCharArray()) {
        if (pairs.containsValue(c)) {
            // Opening bracket
            stack.push(c);
        } else if (pairs.containsKey(c)) {
            // Closing bracket
            if (stack.isEmpty() || stack.pop() != pairs.get(c)) {
                return false;
            }
        }
        // Ignore other characters
    }

    return stack.isEmpty();
}

// Example usage
public static void main(String[] args) {
    System.out.println(isBalanced("()[]{}")); // true
    System.out.println(isBalanced("(]"));     // false
    System.out.println(isBalanced("([)]"));   // false
    System.out.println(isBalanced("{[]}"));   // true
}
```

---

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(n) — stack size

---

### 💡 Interview Tip
> _"I use a stack and a map of bracket pairs. For each closing bracket, I check if the top of the stack is the matching opening bracket. If not, it's unbalanced. At the end, the stack must be empty. This handles nested and mixed brackets correctly."_  

---

## 4. What is a monotonic stack? Give an example use case.

> A **monotonic stack** is a stack where elements are **always in sorted order** — either **increasing** or **decreasing**.

Used to solve **"next greater/smaller element"** problems efficiently.

---

### 🔹 4.1 Types
- **Monotonically Increasing**: `stack[i] <= stack[i+1]`
- **Monotonically Decreasing**: `stack[i] >= stack[i+1]`

---

### 🔹 4.2 Example: Next Greater Element

Given array: `[4, 1, 2, 3]`  
Output: `[ -1, 2, 3, -1 ]` — next greater element for each

---

### ✅ Java Code (Next Greater Element)
```java
import java.util.*;

public static int[] nextGreaterElement(int[] nums) {
    int n = nums.length;
    int[] result = new int[n];
    Arrays.fill(result, -1);
    Stack<Integer> stack = new Stack<>(); // Monotonically decreasing by index

    for (int i = 0; i < n; i++) {
        // While stack not empty and current > top
        while (!stack.isEmpty() && nums[i] > nums[stack.peek()]) {
            int index = stack.pop();
            result[index] = nums[i];
        }
        stack.push(i);
    }

    return result;
}

// Example usage
public static void main(String[] args) {
    int[] nums = {4, 1, 2, 3};
    int[] result = nextGreaterElement(nums);
    System.out.println(Arrays.toString(result)); // [-1, 2, 3, -1]
}
```

---

### 🔹 4.3 How It Works
- Stack stores **indices** of elements
- We maintain a **decreasing stack** of values
- When we find a larger element, it's the "next greater" for all smaller ones in the stack

---

### 🔹 4.4 Common Use Cases
| Problem | Monotonic Stack Type |
|--------|---------------------|
| ✅ **Next Greater Element** | Decreasing |
| ✅ **Next Smaller Element** | Increasing |
| ✅ **Largest Rectangle in Histogram** | Increasing |
| ✅ **Daily Temperatures** | Decreasing |
| ✅ **Trapping Rain Water** | Decreasing |

---

### 📌 Time & Space
- **Time**: O(n) — each element pushed and popped once
- **Space**: O(n)

---

### 💡 Interview Tip
> _"A monotonic stack maintains sorted order — decreasing for 'next greater' problems. I use it to efficiently find the next greater element by popping smaller elements when a larger one is found. It's O(n) and elegant. I also use it in histogram and rainwater problems."_  

---

## ✅ Final Tips for Coding Interviews

1. **Clarify**: What types of brackets? Should we ignore other characters?
2. **Draw the stack** when explaining
3. **State time & space complexity**
4. **Use maps** for bracket matching to avoid long if-else
5. **Practice monotonic stack patterns** — they come up often in medium/hard problems

---
