## Solution: Different Ways to Add Parentheses

The solution uses a **divide-and-conquer approach with memoization** to compute all possible results from different groupings of the expression .

## Algorithm

For each operator in the expression:
1. **Divide**: Split the expression into left and right subexpressions at that operator
2. **Conquer**: Recursively compute all possible results for both subexpressions
3. **Combine**: Apply the operator to every combination of left and right results
4. **Base case**: If no operators exist, the expression is a single number

## Key Features

- **Memoization**: Stores computed results for subexpressions to avoid redundant calculations
- **Time Complexity**: \(O(n \times C_n)\) where \(C_n\) is the nth Catalan number, representing the number of ways to parenthesize
- **Space Complexity**: \(O(n \times C_n)\) for storing all results

## Implementation Details

The algorithm handles all three operators (+, -, *) and works by treating each operator as a potential "root" of the expression tree . For expression "2*3-4*5", it explores all 5 possible binary tree structures, each representing a different grouping with parentheses .

This approach ensures all valid parenthesizations are explored systematically, guaranteeing that no valid result is missed while avoiding duplicate computations through memoization.

import java.util.*;

class Solution {
    private Map<String, List<Integer>> memo = new HashMap<>();

    public List<Integer> diffWaysToCompute(String expression) {
        // Check if result is already computed
        if (memo.containsKey(expression)) {
            return memo.get(expression);
        }

        List<Integer> results = new ArrayList<>();

        // Try to split at each operator
        for (int i = 0; i < expression.length(); i++) {
            char c = expression.charAt(i);

            if (c == '+' || c == '-' || c == '*') {
                // Divide: split expression into left and right parts
                String leftPart = expression.substring(0, i);
                String rightPart = expression.substring(i + 1);

                List<Integer> leftResults = diffWaysToCompute(leftPart);
                List<Integer> rightResults = diffWaysToCompute(rightPart);

                // Conquer: combine all results from left and right
                for (int left : leftResults) {
                    for (int right : rightResults) {
                        if (c == '+') {
                            results.add(left + right);
                        } else if (c == '-') {
                            results.add(left - right);
                        } else if (c == '*') {
                            results.add(left * right);
                        }
                    }
                }
            }
        }

        // Base case: if no operator found, it's a number
        if (results.isEmpty()) {
            results.add(Integer.parseInt(expression));
        }

        memo.put(expression, results);
        return results;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test Example 1
        System.out.println("Example 1:");
        String expr1 = "2-1-1";
        List<Integer> result1 = solution.diffWaysToCompute(expr1);
        Collections.sort(result1);
        System.out.println("Input: " + expr1);
        System.out.println("Output: " + result1);
        System.out.println();

        // Reset memo for next test
        solution.memo.clear();

        // Test Example 2
        System.out.println("Example 2:");
        String expr2 = "2*3-4*5";
        List<Integer> result2 = solution.diffWaysToCompute(expr2);
        Collections.sort(result2);
        System.out.println("Input: " + expr2);
        System.out.println("Output: " + result2);
        System.out.println();

        // Reset memo for next test
        solution.memo.clear();

        // Test Example 3
        System.out.println("Example 3:");
        String expr3 = "2+3*4";
        List<Integer> result3 = solution.diffWaysToCompute(expr3);
        Collections.sort(result3);
        System.out.println("Input: " + expr3);
        System.out.println("Output: " + result3);
    }
}
