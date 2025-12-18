Use a **stack** to ensure brackets close in correct type and order: push opening brackets, and for each closing bracket, check it matches the latest unclosed opening. This gives O(n) time and O(n) space.[1][2][3]

## Stack-based algorithm

### Idea

- Scan left to right.
- When you see an **opening bracket** `(`, `{`, `[`, push the **expected closing bracket** onto the stack.[4][3]
- When you see a **closing bracket** `)`, `}`, `]`:
  - If the stack is empty → invalid.
  - Pop from stack and check if it equals the current closing bracket. If not → invalid.
- After processing all characters, the stack must be **empty** (no unmatched openings left).[2][5]

### Java implementation

```java
class Solution {
    public boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();

        for (char c : s.toCharArray()) {
            // Push expected closing bracket for each opening
            if (c == '(') {
                stack.push(')');
            } else if (c == '{') {
                stack.push('}');
            } else if (c == '[') {
                stack.push(']');
            } else {
                // c is a closing bracket; stack must not be empty and top must match
                if (stack.isEmpty() || stack.pop() != c) {
                    return false;
                }
            }
        }

        // All openings must be matched and closed
        return stack.isEmpty();
    }
}
```

### Why pushing expected closers is nice

- Avoids needing a map from closing → opening; direct comparison with `c` is enough.[3][4]
- Keeps logic compact: any mismatch or early closer returns `false`.
- Final emptiness check guarantees no extra unmatched openings.[5][2]

### Examples

1. `s = "()"`  
   - `(` → push `)`  
   - `)` → pop `)` → match, stack empty → true.

2. `s = "()[]{}"`  
   - Push `)`, pop `)`; push `]`, pop `]`; push `}`, pop `}` → stack empty → true.

3. `s = "(]"`  
   - `(` → push `)`  
   - `]` → pop `)` ≠ `]` → false.

4. `s = "([])"`  
   - `(` → push `)`  
   - `[` → push `]`  
   - `]` → pop `]` → ok  
   - `)` → pop `)` → ok → stack empty → true.

5. `s = "([)]"`  
   - `(` → push `)`  
   - `[` → push `]`  
   - `)` → pop `]` ≠ `)` → false.

This stack pattern is the standard, robust approach for validating parentheses and bracket sequences.[6][1][2]
