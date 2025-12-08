## Problem Understanding

This is the classic **FizzBuzz** problem. Generate a string array where each element depends on divisibility by 3 and 5.[1]

The logic is straightforward but can be implemented in different ways with varying elegance and extensibility.

## Solution 1: Direct If-Else (Simple and Readable)

```java
class Solution {
    public List<String> fizzBuzz(int n) {
        List<String> result = new ArrayList<>();
        
        for (int i = 1; i <= n; i++) {
            if (i % 3 == 0 && i % 5 == 0) {
                result.add("FizzBuzz");
            } else if (i % 3 == 0) {
                result.add("Fizz");
            } else if (i % 5 == 0) {
                result.add("Buzz");
            } else {
                result.add(String.valueOf(i));
            }
        }
        
        return result;
    }
}
```

**Complexity**:
- Time: O(n) - iterate through 1 to n
- Space: O(1) - excluding output array[1]

## Solution 2: String Concatenation (Elegant and Extensible)

A more elegant approach that's easier to extend with new rules:

```java
class Solution {
    public List<String> fizzBuzz(int n) {
        List<String> result = new ArrayList<>();
        
        for (int i = 1; i <= n; i++) {
            String current = "";
            
            // Build string based on divisibility
            if (i % 3 == 0) {
                current += "Fizz";
            }
            if (i % 5 == 0) {
                current += "Buzz";
            }
            
            // If no divisibility condition matched, add the number
            if (current.isEmpty()) {
                current = String.valueOf(i);
            }
            
            result.add(current);
        }
        
        return result;
    }
}
```

**Advantages**:
- Easy to add new rules (divisible by 7 → "Whizz", etc.)
- No nested if-else chains
- Cleaner logic

## Solution 3: HashMap (Most Extensible)

For interview scenarios or larger rulesets:

```java
class Solution {
    public List<String> fizzBuzz(int n) {
        List<String> result = new ArrayList<>();
        
        // Map of divisor -> word
        Map<Integer, String> divisorMap = new LinkedHashMap<>();
        divisorMap.put(3, "Fizz");
        divisorMap.put(5, "Buzz");
        // Easy to add more: divisorMap.put(7, "Whizz");
        
        for (int i = 1; i <= n; i++) {
            String current = "";
            
            // Check all divisibility rules
            for (Map.Entry<Integer, String> entry : divisorMap.entrySet()) {
                if (i % entry.getKey() == 0) {
                    current += entry.getValue();
                }
            }
            
            // If no rule matched, use the number
            if (current.isEmpty()) {
                current = String.valueOf(i);
            }
            
            result.add(current);
        }
        
        return result;
    }
}
```

**Advantages**:
- Highly extensible for additional divisibility rules
- Rules are centralized and easy to modify
- Demonstrates design patterns

## Example Walkthrough

### `n = 15`

| i | i%3 | i%5 | i%15 | Output | Reason |
|---|-----|-----|------|--------|--------|
| 1 | 1 | 1 | 1 | "1" | No divisibility |
| 2 | 2 | 2 | 2 | "2" | No divisibility |
| 3 | 0 | 3 | 3 | "Fizz" | Divisible by 3 |
| 4 | 1 | 4 | 4 | "4" | No divisibility |
| 5 | 2 | 0 | 5 | "Buzz" | Divisible by 5 |
| 6 | 0 | 1 | 6 | "Fizz" | Divisible by 3 |
| 7 | 1 | 2 | 7 | "7" | No divisibility |
| 8 | 2 | 3 | 8 | "8" | No divisibility |
| 9 | 0 | 4 | 9 | "Fizz" | Divisible by 3 |
| 10 | 1 | 0 | 10 | "Buzz" | Divisible by 5 |
| 11 | 2 | 1 | 11 | "11" | No divisibility |
| 12 | 0 | 2 | 12 | "Fizz" | Divisible by 3 |
| 13 | 1 | 3 | 13 | "13" | No divisibility |
| 14 | 2 | 4 | 14 | "14" | No divisibility |
| 15 | 0 | 0 | 0 | "FizzBuzz" | Divisible by both 3 and 5 ✓ |

**Result**: `["1","2","Fizz","4","Buzz","Fizz","7","8","Fizz","Buzz","11","Fizz","13","14","FizzBuzz"]` ✓

## Comparison of Approaches

| Approach | Readability | Extensibility | Use Case |
|----------|-------------|----------------|----------|
| **If-Else** | **High** | **Low** | Simple problems, fixed rules |
| **String Concat** | **High** | **Medium** | Interview-friendly, balanced |
| **HashMap** | **Medium** | **High** | Complex rules, production code |

## Edge Cases

| Case | Input | Output |
|------|-------|--------|
| Single element | n=1 | ["1"] ✓ |
| Only Fizz | n=3 | ["1","2","Fizz"] ✓ |
| Only Buzz | n=5 | ["1","2","Fizz","4","Buzz"] ✓ |
| Large n | n=10000 | Works efficiently ✓ |

## Interview Tips

1. **Start simple**:
   - Begin with if-else approach to show you understand the problem
   - It's clear and works correctly

2. **Show extensibility**:
   - "If we needed to add more rules (like divisible by 7), we could use string concatenation instead"
   - "Or use a HashMap for maximum flexibility"

3. **Mention complexity**:
   - "Time: O(n) - single pass through numbers"
   - "Space: O(1) excluding the output array"

4. **Trace through example**:
   - Walk through the divisibility logic for i=15
   - Show how "Fizz" + "Buzz" = "FizzBuzz"

5. **Discuss optimization opportunities**:
   - "For this fixed problem, if-else is sufficient"
   - "For variations, string concatenation or HashMap would be better"

This is a classic problem testing **conditional logic**, **string manipulation**, and **code design**.[1]

