## Problem Understanding

Design a **MinStack** that supports all standard stack operations (**push, pop, top**) plus **getMin()** in O(1) time. The challenge is tracking the minimum without scanning the entire stack.[1][2][3]

## Optimal Solution: Dual Stack Approach

Use **two stacks**:
1. **Regular stack**: stores all values
2. **Min stack**: stores minimums at each level, maintaining monotonic decreasing order[2][3][4]

### Key Insight

Keep the **min stack in sync** with the regular stack. At each step:
- **Push**: push value to regular stack; if value ≤ current min, push to min stack
- **Pop**: pop from regular stack; if popped value equals current min, pop from min stack too
- **getMin**: peek at top of min stack[5][2]

## Java Implementation

```java
class MinStack {
    private final Stack<Integer> stack;      // regular stack for all values
    private final Stack<Integer> minStack;   // auxiliary stack for minimums

    public MinStack() {
        stack = new Stack<>();
        minStack = new Stack<>();
    }

    /**
     * Pushes element val onto the stack
     * Also updates minStack if val is <= current minimum
     */
    public void push(int val) {
        stack.push(val);
        
        // Push to minStack only if it's the new minimum (or equal to current min)
        if (minStack.isEmpty() || val <= minStack.peek()) {
            minStack.push(val);
        }
    }

    /**
     * Removes the element on top of the stack
     * Also pops from minStack if it was the tracked minimum
     */
    public void pop() {
        int val = stack.pop();
        
        // If the popped value was the minimum, remove it from minStack too
        if (val == minStack.peek()) {
            minStack.pop();
        }
    }

    /**
     * Gets the top element of the stack
     */
    public int top() {
        return stack.peek();
    }

    /**
     * Retrieves the minimum element in the stack in O(1)
     */
    public int getMin() {
        return minStack.peek();
    }
}
```

## Example Walkthrough

```
Operations: push(-2), push(0), push(-3), getMin(), pop(), top(), getMin()

Operation      | Stack      | MinStack   | Return | Explanation
push(-2)       | [-2]       | [-2]       | -      | First elem, becomes min
push(0)        | [-2,0]     | [-2]       | -      | 0 > -2, don't push to minStack
push(-3)       | [-2,0,-3]  | [-2,-3]    | -      | -3 < -2, push to minStack
getMin()       | [-2,0,-3]  | [-2,-3]    | -3     | Top of minStack ✓
pop()          | [-2,0]     | [-2]       | -      | -3 was min, pop from both
top()          | [-2,0]     | [-2]       | 0      | Top of stack
getMin()       | [-2,0]     | [-2]       | -2     | Top of minStack ✓
```

## Complexity Analysis

| Operation | Time | Space | Why |
|-----------|------|-------|-----|
| **push(val)** | **O(1)** | - | Single push to two stacks |
| **pop()** | **O(1)** | - | Single pop from stacks |
| **top()** | **O(1)** | - | Peek at stack top |
| **getMin()** | **O(1)** | - | Peek at minStack top |
| **Overall Space** | - | **O(n)** | Two stacks store n elements |

## Why This Works

**Invariant**: At every step, `minStack.peek()` equals the minimum element in `stack`.[2][5]

**Proof by induction**:
- Base: After first push, minStack has that element, which is the minimum. ✓
- Inductive: If invariant holds before operation, it holds after:
  - **push(val)**: If val ≤ current min, minStack gets it. Next minimum is val. ✓
  - **pop()**: If popped element was the min, remove it from minStack. Next min is correct. ✓

## Alternative: Single Stack with Pairs

Instead of two stacks, store `(value, currentMin)` pairs in a single stack:

```java
class MinStack {
    private Stack<int[]> stack;  // stores {value, minSoFar}

    public MinStack() {
        stack = new Stack<>();
    }

    public void push(int val) {
        if (stack.isEmpty()) {
            stack.push(new int[]{val, val});
        } else {
            int currentMin = stack.peek()[1];
            stack.push(new int[]{val, Math.min(val, currentMin)});
        }
    }

    public void pop() {
        stack.pop();
    }

    public int top() {
        return stack.peek()[0];
    }

    public int getMin() {
        return stack.peek()[1];
    }
}
```

**Pros**: Single data structure, conceptually simpler  
**Cons**: Uses more memory per element (stores extra int)

## Edge Cases

| Case | Operations | Result |
|------|-----------|--------|
| Single element | push(5), getMin() | 5 ✓ |
| Negative numbers | push(-2), push(-5), getMin() | -5 ✓ |
| Duplicates | push(3), push(3), pop(), getMin() | 3 ✓ |
| All same | push(1)×3, getMin() | 1 ✓ |

## Interview Tips

1. **Recognize the constraint**:
   - "getMin() must be O(1), so I can't scan the stack each time."

2. **Explain the approach**:
   - "I'll use an auxiliary min stack that tracks minimums."
   - "When pushing, if value ≤ current min, push to minStack."
   - "When popping, if it was the min, pop from minStack too."

3. **Trace through example**:
   ```
   push(-2): stack=[-2], minStack=[-2]
   push(0):  stack=[-2,0], minStack=[-2]  (0 > -2, skip)
   push(-3): stack=[-2,0,-3], minStack=[-2,-3]
   getMin(): return -3 ✓
   pop():    stack=[-2,0], minStack=[-2]  (-3 was min)
   getMin(): return -2 ✓
   ```

4. **Mention alternative**:
   - "Could store (value, min) pairs in a single stack."
   - "Two stacks is cleaner and equally efficient."

5. **Discuss space tradeoff**:
   - "We use O(n) space to achieve O(1) time for all operations."
   - "minStack stores each minimum when it changes, not every element."

This is a classic design problem teaching **auxiliary data structures** and **space-time tradeoffs**.[3][4][2]

