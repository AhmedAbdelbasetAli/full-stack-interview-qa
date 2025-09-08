You’re stepping into the **art of the locally optimal** — where you make the “best” choice at each step, hoping it leads to a globally optimal solution.

> 💡 **Greedy Algorithms** are like ordering at a buffet:  
> “Pick the tastiest dish right now” — and hope you end up with the best meal.  
> Sometimes it works. Sometimes you’re full of dessert and missed the steak.

Used in:
- **Scheduling** → Max meetings in a room.
- **Compression** → Huffman coding (used in ZIP, JPEG, MP3).
- **Networking** → Dijkstra’s (yes, it’s greedy!), Minimum Spanning Trees.
- **Finance** → Coin change (for canonical systems like USD).

And yes — **LeetCode** loves to test if you know *when* greedy works… and when it’s a trap.

Let’s master it — Java-first, production-aware, interview-sharp.

—

# 🧱 1. What It Is — Greedy Algorithms

> **Core Idea**: At each step, choose the option that looks best *right now* — without reconsidering later.  
> **No backtracking**. No memoization. No DP. Just **local optimum → hope for global optimum**.

```
Activity Selection:
Meetings: [ (1,3), (2,5), (4,7), (6,9) ]
Greedy: Pick earliest-ending meeting → then next compatible → [ (1,3), (4,7), (6,9) ] → 3 meetings.

✅ Works because “earliest end” leaves max room for others.
```

> 💡 Uber: Assign driver to nearest rider → greedy by distance.  
> 💡 Netflix: “Play next episode” → greedy by watch history + popularity.

—

# ⚙️ 2. How It Works (Java Implementation)

## ✅ Example 1: Activity Selection — Max Non-Overlapping Intervals

```java
import java.util.*;

public class ActivitySelection {
    public static int maxActivities(int[][] activities) {
        // Sort by end time
        Arrays.sort(activities, Comparator.comparingInt(a -> a[1]));

        int count = 1; // first activity
        int lastEnd = activities[0][1];

        for (int i = 1; i < activities.length; i++) {
            if (activities[i][0] >= lastEnd) { // compatible
                count++;
                lastEnd = activities[i][1];
            }
        }
        return count;
    }

    public static void main(String[] args) {
        int[][] activities = {{1,3}, {2,5}, {4,7}, {6,9}};
        System.out.println(maxActivities(activities)); // 3
    }
}
```

## ✅ Example 2: Fractional Knapsack — Max Value with Weight Limit

> You can take fractions of items (e.g., gold dust, not laptops).

```java
import java.util.*;

class Item {
    int value, weight;
    double ratio;
    Item(int value, int weight) {
        this.value = value;
        this.weight = weight;
        this.ratio = (double) value / weight;
    }
}

public class FractionalKnapsack {
    public static double getMaxValue(Item[] items, int capacity) {
        // Sort by value/weight ratio descending
        Arrays.sort(items, (a, b) -> Double.compare(b.ratio, a.ratio));

        double totalValue = 0.0;

        for (Item item : items) {
            if (capacity >= item.weight) {
                // Take whole item
                totalValue += item.value;
                capacity -= item.weight;
            } else {
                // Take fraction
                totalValue += item.ratio * capacity;
                break;
            }
        }
        return totalValue;
    }

    public static void main(String[] args) {
        Item[] items = {
            new Item(60, 10), // ratio 6.0
            new Item(100, 20), // ratio 5.0
            new Item(120, 30)  // ratio 4.0
        };
        System.out.println(getMaxValue(items, 50)); // 60 + 100 + (20/30)*120 = 240.0
    }
}
```

## ✅ Example 3: Jump Game — Can You Reach the End?

> Each element = max jump length. Can you reach last index?

```java
public class JumpGame {
    public static boolean canJump(int[] nums) {
        int maxReach = 0;
        for (int i = 0; i < nums.length; i++) {
            if (i > maxReach) return false; // can't reach this position
            maxReach = Math.max(maxReach, i + nums[i]);
        }
        return true;
    }

    public static void main(String[] args) {
        System.out.println(canJump(new int[]{2,3,1,1,4})); // true
        System.out.println(canJump(new int[]{3,2,1,0,4})); // false
    }
}
```

### ⏱️ Time & Space Complexity

| Problem               | Time       | Space      | Notes                                     |
|-----------------------|------------|------------|-------------------------------------------|
| **Activity Selection**| O(n log n) | O(1)       | Sort + one pass                           |
| **Fractional Knapsack**| O(n log n)| O(1)       | Sort by ratio                             |
| **Jump Game**         | O(n)       | O(1)       | Single pass, track max reach              |

—

# 🎯 3. When to Use It

## ✅ Real-World Java Use Cases

- **Scheduling Systems** → Max meetings, job shop scheduling.
- **Load Balancing** → Assign task to least-loaded server → greedy.
- **Data Compression** → Huffman coding → greedy by frequency.
- **Network Routing** → Dijkstra’s → greedy by shortest edge.
- **Ad Serving** → Show highest bid ad → greedy by revenue.

> 💡 Android: Battery optimization → defer non-critical tasks → greedy by urgency.  
> 💡 Spring: Task scheduling → `@Scheduled` with earliest deadline first.

## ✅ Interview Scenarios (LeetCode Classics)

- **Jump Game** → #55 → greedy by max reach.
- **Gas Station** → #134 → greedy, track deficit.
- **Task Scheduler** → #621 → greedy with cooldown.
- **Minimum Number of Arrows to Burst Balloons** → #452 → greedy interval scheduling.
- **Huffman Coding** → rarely asked directly, but concept appears in compression questions.

## ✅ System Design Contexts

- **Ad Auctions** → Highest bidder wins → greedy.
- **Content Delivery** → Serve from nearest CDN → greedy by latency.
- **Resource Allocation** → Assign to most available resource → greedy.

—

# 🚫 4. When NOT to Use It

## ❌ Anti-Patterns & Misuse

### 🚫 The #1 Red Flag: Greedy Fails for 0/1 Knapsack

```java
Items: (value, weight)
A: (60, 10) → ratio 6.0
B: (100, 20) → ratio 5.0
C: (120, 30) → ratio 4.0
Capacity: 50

Greedy: Take A (60), then B (100) → total 160, weight 30 → room for 20 more → can’t take C (needs 30) → stuck.

Optimal: Skip A → take B (100) + C (120) → total 220 → better!

✅ Use DP for 0/1 Knapsack.
```

### 🚫 Coin Change with Arbitrary Denominations

```java
Coins: [1, 3, 4], Amount: 6

Greedy: Take 4 → then 1 → then 1 → 3 coins.
Optimal: 3 + 3 → 2 coins.

✅ Use DP.
```

## 🐢 Performance Traps

- **Not sorting when required** → e.g., activity selection without sorting by end time → wrong answer.

- **Assuming greedy always works** → always ask: “Does local optimum lead to global optimum?”

## ✅ Better Alternatives

| Scenario                        | Better Choice             | Why                                      |
|--------------------------------|---------------------------|------------------------------------------|
| 0/1 Knapsack                   | Dynamic Programming       | Greedy fails for arbitrary weights       |
| Coin Change (arbitrary coins)  | DP                        | Greedy not optimal                       |
| Shortest Path (negative edges) | Bellman-Ford              | Dijkstra (greedy) fails with negatives   |
| Global optimum required        | DP / Backtracking         | Greedy is heuristic, not guaranteed      |

—

# 💡 5. Pro Tips & Gotchas (Java-Specific)

## ⚠️ Sorting is Often Key

- **Always check if sorting by a criterion (end time, ratio, etc.) makes greedy work**.

```java
// ✅ Critical step
Arrays.sort(intervals, Comparator.comparingInt(a -> a[1])); // sort by end time
```

## 📦 Memory & Performance

- **Greedy is usually O(1) space** → no DP tables, no recursion stacks.
- **Prefer primitive arrays** → avoid `List<Integer>` overhead in tight loops.

## 🏭 Enterprise Best Practices

- **In Spring Boot**: Use greedy for real-time scheduling → e.g., “Assign task to first available worker”.
- **In Android**: Use greedy for battery/cpu throttling → “Defer lowest priority task”.
- **Logging decisions** → for auditability, log why a greedy choice was made (e.g., “Selected server X because lowest load”).

## 🤯 Fun Fact

**Huffman Coding** (greedy by frequency) is used in:
- ZIP files
- JPEG images
- MP3 audio
- And yes — your browser uses it to decompress data.

—

# ✍️ 6. Mini Challenge (Java Coding Exercise)

## 🎯 Problem: Minimum Number of Arrows to Burst Balloons

> Given a list of balloons represented as `[start, end]`, find the minimum number of arrows (shot vertically) to burst all balloons.  
> An arrow at `x` bursts any balloon where `start ≤ x ≤ end`.  
> Example: `[[10,16],[2,8],[1,6],[7,12]]` → shoot at `x=6` and `x=11` → 2 arrows.

LeetCode #452 — Greedy Interval Scheduling.

### 🧩 Starter Code

```java
import java.util.*;

public class MinArrows {
    public static int findMinArrowShots(int[][] points) {
        if (points.length == 0) return 0;

        // Sort by end coordinate
        Arrays.sort(points, Comparator.comparingInt(a -> a[1]));

        int arrows = 1;
        int lastArrowPos = points[0][1]; // shoot at end of first balloon

        for (int i = 1; i < points.length; i++) {
            if (points[i][0] > lastArrowPos) { // current balloon starts after last arrow
                arrows++;
                lastArrowPos = points[i][1]; // shoot new arrow at end of this balloon
            }
            // else: current balloon is burst by last arrow → do nothing
        }

        return arrows;
    }

    public static void main(String[] args) {
        int[][] balloons = {{10,16}, {2,8}, {1,6}, {7,12}};
        System.out.println(findMinArrowShots(balloons)); // 2
    }
}
```

### 🧪 Optional: JUnit Test

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class MinArrowsTest {
    @Test
    void testFindMinArrowShots() {
        int[][] balloons = {{10,16}, {2,8}, {1,6}, {7,12}};
        assertEquals(2, MinArrows.findMinArrowShots(balloons));
    }
}
```

> 💡 Time: O(n log n), Space: O(1) — classic greedy interval problem.

—
—

