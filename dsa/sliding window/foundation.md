You’re stepping into the **world of O(n) elegance** — where you solve “longest substring”, “max subarray”, and “minimum window” in a single pass, no brute force, no nested loops.

> 💡 **Sliding Window** is like a **security camera scanning a hallway** — it moves forward, expands when needed, contracts when too big, and never looks back.

Used in:
- **Real-time Analytics** → “Last 5 minutes of user activity”.
- **Network Monitoring** → “Max requests per second in last 60s”.
- **Bioinformatics** → “Longest DNA subsequence without repeating bases”.
- **Ad Tech** → “Highest CTR ad in last 1000 impressions”.
- **Finance** → “Max profit in last N trades”.

And yes — **FAANG interviews** *live* for sliding window — it’s the #1 pattern for turning O(n²) into O(n).

Let’s master it — Java-first, production-aware, interview-sharp.

—

# 🧱 1. What It Is — Sliding Window

> **Analogy**: A **retractable dog leash**.  
> Dog runs ahead (expand window) → if leash too long (violates condition) → you pull back (shrink window).  
> Always moving forward — never rewind.

- **Core Purpose**: Find optimal subarray/substring in **O(n)** by maintaining a “window” of elements that satisfies a condition.
- **Two Flavors**:
  - **Fixed Size** → e.g., “max sum of 3 consecutive elements”.
  - **Variable Size** → e.g., “longest substring without repeating chars”.

```
Variable Window Example: "abcabcbb" → longest substring without repeat
Window: [a] → [a,b] → [a,b,c] → [b,c,a] → ... → max length = 3
```

> 💡 Uber: “Surge pricing if >100 rides in last 5 min” → sliding window over ride events.  
> 💡 Netflix: “Buffer 10s of video” → sliding window of video chunks.

—

# ⚙️ 2. How It Works (Java Implementation)

## ✅ Pattern 1: Fixed Size Window — Max Sum of K Consecutive Elements

```java
public class FixedWindow {
    public static int maxSum(int[] nums, int k) {
        if (k > nums.length) return -1;

        // Calculate sum of first window
        int windowSum = 0;
        for (int i = 0; i < k; i++) {
            windowSum += nums[i];
        }

        int maxSum = windowSum;

        // Slide window: remove leftmost, add rightmost
        for (int i = k; i < nums.length; i++) {
            windowSum = windowSum - nums[i - k] + nums[i]; // O(1) update
            maxSum = Math.max(maxSum, windowSum);
        }

        return maxSum;
    }

    public static void main(String[] args) {
        int[] nums = {1, 4, 2, 10, 23, 3, 1, 0, 20};
        System.out.println(maxSum(nums, 4)); // 39 → [10,23,3,1]? No → [2,10,23,3] = 38? Wait → [10,23,3,1] = 37? Let’s compute:
        // First window: 1+4+2+10 = 17
        // Slide: 17-1+23=39 → [4,2,10,23]
        // Then: 39-4+3=38, 38-2+1=37, 37-10+0=27, 27-23+20=24 → max=39
    }
}
```

## ✅ Pattern 2: Variable Size Window — Longest Substring Without Repeating Characters

```java
import java.util.HashMap;
import java.util.Map;

public class VariableWindow {
    public static int lengthOfLongestSubstring(String s) {
        Map<Character, Integer> lastSeen = new HashMap<>();
        int left = 0; // left pointer
        int maxLength = 0;

        for (int right = 0; right < s.length(); right++) {
            char c = s.charAt(right);

            // If char seen in current window → move left to after its last occurrence
            if (lastSeen.containsKey(c) && lastSeen.get(c) >= left) {
                left = lastSeen.get(c) + 1;
            }

            lastSeen.put(c, right); // update last seen
            maxLength = Math.max(maxLength, right - left + 1);
        }

        return maxLength;
    }

    public static void main(String[] args) {
        System.out.println(lengthOfLongestSubstring("abcabcbb")); // 3 → "abc"
        System.out.println(lengthOfLongestSubstring("bbbbb"));    // 1 → "b"
        System.out.println(lengthOfLongestSubstring("pwwkew"));   // 3 → "wke"
    }
}
```

## ✅ Pattern 3: Minimum Window Substring (Advanced)

> Given strings `s` and `t`, find min window in `s` containing all chars of `t`.

```java
import java.util.HashMap;
import java.util.Map;

public class MinWindowSubstring {
    public static String minWindow(String s, String t) {
        if (t.length() > s.length()) return "";

        Map<Character, Integer> need = new HashMap<>();
        Map<Character, Integer> window = new HashMap<>();

        for (char c : t.toCharArray()) {
            need.put(c, need.getOrDefault(c, 0) + 1);
        }

        int left = 0, right = 0;
        int valid = 0; // count of chars in window satisfying need
        int start = 0, len = Integer.MAX_VALUE;

        while (right < s.length()) {
            char c = s.charAt(right);
            right++;

            if (need.containsKey(c)) {
                window.put(c, window.getOrDefault(c, 0) + 1);
                if (window.get(c).equals(need.get(c))) {
                    valid++;
                }
            }

            // Shrink window if valid
            while (valid == need.size()) {
                if (right - left < len) {
                    start = left;
                    len = right - left;
                }

                char d = s.charAt(left);
                left++;

                if (need.containsKey(d)) {
                    if (window.get(d).equals(need.get(d))) {
                        valid--;
                    }
                    window.put(d, window.get(d) - 1);
                }
            }
        }

        return len == Integer.MAX_VALUE ? "" : s.substring(start, start + len);
    }

    public static void main(String[] args) {
        System.out.println(minWindow("ADOBECODEBANC", "ABC")); // "BANC"
    }
}
```

### ⏱️ Time & Space Complexity

| Pattern               | Time Complexity | Space Complexity | Notes                                     |
|-----------------------|-----------------|------------------|-------------------------------------------|
| **Fixed Window**      | O(n)            | O(1)             | Single pass, no extra space               |
| **Variable Window**   | O(n)            | O(k)             | k = charset size (e.g., 26 for a-z)       |
| **Min Window Substring** | O(n)         | O(k)             | Two pointers, hashmap for counts          |

—

# 🎯 3. When to Use It

## ✅ Real-World Java Use Cases

- **Real-time Dashboards** → “Max users in last 5 min” → fixed window.
- **Rate Limiting** → “Allow max 100 requests per minute” → sliding window counter.
- **Log Analysis** → “Longest error-free period” → variable window.
- **Ad Bidding** → “Highest CTR in last N impressions” → fixed or variable window.
- **Video Streaming** → “Buffer 10s of video” → fixed window of chunks.

> 💡 Spring Boot: `@RateLimiter` with sliding window → resilience4j.  
> 💡 Android: Touch gesture recognition → “Longest swipe without lift” → variable window.

## ✅ Interview Scenarios (LeetCode Classics)

- **Maximum Sum of K Consecutive Elements** → #? (common variant).
- **Longest Substring Without Repeating Characters** → #3.
- **Minimum Window Substring** → #76.
- **Longest Repeating Character Replacement** → #424.
- **Permutation in String** → #567 → fixed window + hashmap.

## ✅ System Design Contexts

- **API Rate Limiting** → Sliding window logs requests → reject if > limit.
- **Real-time Fraud Detection** → “Max transactions per minute per user”.
- **Load Shedding** → “If CPU > 90% for 30s → shed load” → sliding window average.
- **Session Analytics** → “Longest session without bounce” → variable window.

—

# 🚫 4. When NOT to Use It

## ❌ Anti-Patterns & Misuse

- **Using for global max/min** → if no “window” constraint → just iterate.

```java
// 🚫 Overkill
int max = 0;
for (int i = 0; i < n; i++) {
    for (int j = i; j < n; j++) { ... } // O(n²) brute force
}

// ✅ Just use
int max = Arrays.stream(nums).max().orElse(0);
```

- **Not moving left pointer** → infinite loop or O(n²).

```java
// 🚫 Broken
while (right < n) {
    // expand right
    while (condition) {
        // but never move left → stuck!
    }
}
```

- **Assuming window always expands** → sometimes you need to shrink aggressively.

## 🐢 Performance Traps

- **HashMap in tight loop** → autoboxing `char` → `Character` → GC pressure.

```java
// ✅ For a-z, use int[26]
int[] count = new int[26];
count[c - 'a']++;
```

- **Not breaking early** → if fixed window and found answer → sometimes can break.

## ✅ Better Alternatives

| Scenario                        | Better Choice             | Why                                      |
|--------------------------------|---------------------------|------------------------------------------|
| Global max/min                 | Single pass               | No window needed                         |
| Need random access in window   | Deque (for min/max)       | e.g., “Sliding Window Maximum” → #239     |
| Very small n (< 100)           | Brute force               | Simpler, less error-prone                |
| Need to store all windows      | List of subarrays         | Sliding window is for optimization only  |

—

# 💡 5. Pro Tips & Gotchas (Java-Specific)

## ⚠️ Use `int[26]` for a-z — Not HashMap

```java
// ✅ Faster, no autoboxing
int[] count = new int[26];
count['c' - 'a']++; // for lowercase

// ✅ For ASCII
int[] count = new int[128];
count[c]++; // c is char → promoted to int
```

## 📦 Memory & Performance

- **Fixed window** → O(1) space → just a few variables.
- **Variable window** → O(1) if charset fixed (e.g., 26 letters) → O(k) otherwise.

## 🏭 Enterprise Best Practices

- **In Spring Boot**: Use **Resilience4j** for sliding window rate limiting:

```java
RateLimiterConfig config = RateLimiterConfig.custom()
    .limitForPeriod(100) // 100 requests
    .limitRefreshPeriod(Duration.ofMinutes(1)) // per minute
    .build();
```

- **In Android**: Use `GestureDetector` → but for custom gestures, sliding window over touch events.

## 🤯 Fun Fact

**Sliding Window is the backbone of TCP congestion control** → “Congestion Window” expands and contracts based on acks — same pattern!

—

# ✍️ 6. Mini Challenge (Java Coding Exercise)

## 🎯 Problem: Longest Substring Without Repeating Characters

> Given a string `s`, find the length of the longest substring without repeating characters.  
> Example: `"abcabcbb"` → `3` (“abc”), `"bbbbb"` → `1` (“b”).

LeetCode #3 — the quintessential sliding window problem.

### 🧩 Starter Code

```java
import java.util.HashMap;
import java.util.Map;

public class LongestSubstring {
    public static int lengthOfLongestSubstring(String s) {
        Map<Character, Integer> lastSeen = new HashMap<>();
        int left = 0;
        int maxLength = 0;

        for (int right = 0; right < s.length(); right++) {
            char c = s.charAt(right);

            if (lastSeen.containsKey(c) && lastSeen.get(c) >= left) {
                left = lastSeen.get(c) + 1;
            }

            lastSeen.put(c, right);
            maxLength = Math.max(maxLength, right - left + 1);
        }

        return maxLength;
    }

    public static void main(String[] args) {
        System.out.println(lengthOfLongestSubstring("abcabcbb")); // 3
        System.out.println(lengthOfLongestSubstring("bbbbb"));    // 1
        System.out.println(lengthOfLongestSubstring("pwwkew"));   // 3
    }
}
```

### 🧪 Optional: JUnit Test

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class LongestSubstringTest {
    @Test
    void testLengthOfLongestSubstring() {
        assertEquals(3, LongestSubstring.lengthOfLongestSubstring("abcabcbb"));
        assertEquals(1, LongestSubstring.lengthOfLongestSubstring("bbbbb"));
        assertEquals(3, LongestSubstring.lengthOfLongestSubstring("pwwkew"));
    }
}
```

> 💡 Time: O(n), Space: O(min(m,n)) — m = charset size.

—



