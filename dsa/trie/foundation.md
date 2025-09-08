You’re stepping into the **world of prefix-powered performance** — where Google predicts your search, spellcheckers fix your typos, and routers find your IP in microseconds.

> 💡 **Trie (Prefix Tree)** is like a **dictionary organized by letter-by-letter paths**.  
> Start at ‘c’ → then ‘a’ → then ‘t’ → you’ve found “cat” — and all words starting with “ca” are nearby.

Used in:
- **Autocomplete** → Google Search, IDEs, ChatGPT prompts.
- **Spellcheck** → Suggest corrections by traversing nearby paths.
- **IP Routing** → Longest prefix match for network addresses.
- **Word Games** → Boggle, Scrabble solvers.
- **Bioinformatics** → DNA sequence matching.

And yes — **LeetCode** has Trie problems that FAANG companies *love* to ask.

Let’s master it — Java-first, production-aware, interview-sharp.

—

# 🧱 1. What It Is — Trie (Prefix Tree)

> **Analogy**: A **tree of letters**. Each path from root to node spells a word (or prefix).  
> Shared prefixes = shared paths → massive space savings for dictionaries.

```
Root
 ├─ c
 │  └─ a
 │     ├─ t → "cat" (end)
 │     └─ r → "car" (end)
 └─ d
    └─ o
       ├─ g → "dog" (end)
       └─ r → "dor" → "door" (if continued)
```

- **Core Purpose**: Efficiently store and search strings by prefix.
- **Key Feature**: **O(L)** search/insert/delete — where L = string length (not number of words!).
- **Space**: O(ALPHABET_SIZE * N * L) — but shared prefixes reduce this dramatically.

> 💡 Uber: Autocomplete for destination → Trie of popular locations.  
> 💡 Android: Keyboard suggestions → Trie of user’s typed words.

—

# ⚙️ 2. How It Works (Java Implementation)

## ✅ Trie Node Structure

```java
class TrieNode {
    private final int ALPHABET_SIZE = 26;
    TrieNode[] children = new TrieNode[ALPHABET_SIZE];
    boolean isEndOfWord = false; // marks complete word

    public TrieNode() {
        // Java initializes array to null — no need to fill
    }
}
```

## ✅ Trie Class — Insert, Search, StartsWith

```java
public class Trie {
    private TrieNode root;

    public Trie() {
        root = new TrieNode();
    }

    // INSERT — O(L) where L = word.length()
    public void insert(String word) {
        TrieNode current = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a'; // assume lowercase a-z
            if (current.children[index] == null) {
                current.children[index] = new TrieNode();
            }
            current = current.children[index];
        }
        current.isEndOfWord = true; // mark end of word
    }

    // SEARCH — O(L)
    public boolean search(String word) {
        TrieNode node = searchPrefix(word);
        return node != null && node.isEndOfWord;
    }

    // STARTS WITH — O(L)
    public boolean startsWith(String prefix) {
        return searchPrefix(prefix) != null;
    }

    // Helper: traverse to end of prefix
    private TrieNode searchPrefix(String prefix) {
        TrieNode current = root;
        for (char c : prefix.toCharArray()) {
            int index = c - 'a';
            if (current.children[index] == null) {
                return null;
            }
            current = current.children[index];
        }
        return current;
    }
}
```

## ✅ Usage Example

```java
public class TrieExample {
    public static void main(String[] args) {
        Trie trie = new Trie();
        trie.insert("cat");
        trie.insert("car");
        trie.insert("dog");

        System.out.println(trie.search("cat"));     // true
        System.out.println(trie.search("ca"));      // false (not end of word)
        System.out.println(trie.startsWith("ca"));  // true
        System.out.println(trie.search("dog"));     // true
        System.out.println(trie.search("door"));    // false
    }
}
```

### ⏱️ Time & Space Complexity

| Operation       | Time Complexity | Space Complexity (per op) | Notes                                     |
|----------------|-----------------|----------------------------|-------------------------------------------|
| **insert(word)** | O(L)           | O(L)                       | L = length of word                        |
| **search(word)** | O(L)           | O(1)                       | Traverse path                             |
| **startsWith(prefix)** | O(L)     | O(1)                       | Same as search                            |
| **Space Total** | —               | O(ALPHABET_SIZE * N * L)   | But shared prefixes → often much less     |

—

# 🎯 3. When to Use It

## ✅ Real-World Java Use Cases

- **Autocomplete Systems** → Search bars, IDEs, chatbots.
- **Spellcheck & Suggestions** → “Did you mean…?” → traverse nearby paths.
- **IP Routing Tables** → Longest prefix match for network addresses (e.g., 192.168.*).
- **Word Games** → Boggle solvers → DFS on Trie + grid.
- **Contact Search** → Android/iOS contact apps → “Type ‘Jo’ → show John, Jon, Joseph”.

> 💡 Spring Boot: REST API with `/search?prefix=abc` → Trie-backed autocomplete service.  
> 💡 Android: `AutoCompleteTextView` → can be backed by Trie for large datasets.

## ✅ Interview Scenarios (LeetCode Classics)

- **Implement Trie (Prefix Tree)** → #208 → foundational.
- **Design Add and Search Words Data Structure** → #211 → with ‘.’ wildcard.
- **Word Search II** → #212 → Boggle on board + Trie.
- **Longest Word in Dictionary** → #720 → build Trie, DFS for longest path with all prefixes.
- **Replace Words** → #648 → use Trie to find shortest root.

## ✅ System Design Contexts

- **Search-as-you-type** → Trie + caching → low latency.
- **Network Routers** → Trie for IP longest prefix match → O(32) for IPv4.
- **Ad Targeting** → Trie of user interest keywords → “sports.f1.races” → match “sports.*”.
- **Content Filtering** → Trie of banned words → scan text in O(n) total.

—

# 🚫 4. When NOT to Use It

## ❌ Anti-Patterns & Misuse

- **Small dataset** → If you have 10 words, just use `HashSet` → simpler, faster.

```java
// 🚫 Overkill for small set
Trie trie = new Trie();
trie.insert("cat"); trie.insert("dog");

// ✅ Better
Set<String> words = Set.of("cat", "dog");
```

- **No prefix queries needed** → If you only need exact match → `HashSet` is O(1), Trie is O(L).

- **High memory pressure** → Each node = 26 pointers (for a-z) → 208 bytes per node (on 64-bit JVM) → can be heavy.

## 🐢 Performance Traps

- **Not handling non a-z characters** → if input has uppercase, digits, symbols → index out of bounds.

```java
// ✅ Handle uppercase
int index = Character.toLowerCase(c) - 'a';

// ✅ Or use Map<Character, TrieNode> for full Unicode
Map<Character, TrieNode> children = new HashMap<>();
```

- **Memory leak in delete** → if not implemented, nodes stay forever.

## ✅ Better Alternatives

| Scenario                        | Better Choice             | Why                                      |
|--------------------------------|---------------------------|------------------------------------------|
| Exact match only               | `HashSet<String>`         | O(1) average, simpler                    |
| Small dataset                  | `ArrayList` + `contains`  | If n < 100, linear scan is fine          |
| Full Unicode support           | `Map<Character, TrieNode>`| Instead of array → more memory, more flexible |
| Compressed Trie needed         | **Radix Tree (Patricia Trie)** | Merges single-child nodes → saves space |

—

# 💡 5. Pro Tips & Gotchas (Java-Specific)

## ⚠️ Character Handling

- **Assume lowercase?** → Document it or convert: `word.toLowerCase()`.
- **Need digits or symbols?** → Use `children = new HashMap<Character, TrieNode>()`.

```java
class FlexibleTrieNode {
    Map<Character, TrieNode> children = new HashMap<>();
    boolean isEndOfWord = false;
}
```

## 📦 Memory Optimization

- **Use `boolean[26]` for children?** → No, array of `TrieNode` is needed for traversal.
- **For production**: Consider **Radix Tree** or **DAWG** (Directed Acyclic Word Graph) for space efficiency.

## 🏭 Enterprise Best Practices

- **In Spring Boot**: Cache Trie if static (e.g., dictionary) → `@PostConstruct` init.
- **In Android**: Use `ArrayMap` or `SparseArray` for children if memory constrained.
- **Thread Safety**: Trie is **not thread-safe** → use `ReadWriteLock` or initialize once and make immutable.

## 🤯 Fun Fact

**IP Routing in Cisco Routers** uses **Trie-based structures** (like Binary Trie or Patricia Trie) to perform **longest prefix match** in O(32) time for IPv4 — enabling internet-scale routing.

—

# ✍️ 6. Mini Challenge (Java Coding Exercise)

## 🎯 Problem: Implement Trie (Prefix Tree)

> Implement the Trie class:  
> - `Trie()` Initializes the trie object.  
> - `void insert(String word)` Inserts the string word into the trie.  
> - `boolean search(String word)` Returns true if word is in the trie.  
> - `boolean startsWith(String prefix)` Returns true if there is any word with the given prefix.

LeetCode #208 — the foundational Trie problem.

### 🧩 Starter Code

```java
class TrieNode {
    TrieNode[] children;
    boolean isEndOfWord;

    public TrieNode() {
        children = new TrieNode[26]; // a-z
        isEndOfWord = false;
    }
}

class Trie {
    private TrieNode root;

    public Trie() {
        root = new TrieNode();
    }

    public void insert(String word) {
        TrieNode current = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a';
            if (current.children[index] == null) {
                current.children[index] = new TrieNode();
            }
            current = current.children[index];
        }
        current.isEndOfWord = true;
    }

    public boolean search(String word) {
        TrieNode node = searchPrefix(word);
        return node != null && node.isEndOfWord;
    }

    public boolean startsWith(String prefix) {
        return searchPrefix(prefix) != null;
    }

    private TrieNode searchPrefix(String prefix) {
        TrieNode current = root;
        for (char c : prefix.toCharArray()) {
            int index = c - 'a';
            if (current.children[index] == null) {
                return null;
            }
            current = current.children[index];
        }
        return current;
    }
}

// Test
public class TrieTest {
    public static void main(String[] args) {
        Trie trie = new Trie();
        trie.insert("apple");
        System.out.println(trie.search("apple"));   // true
        System.out.println(trie.search("app"));     // false
        System.out.println(trie.startsWith("app")); // true
        trie.insert("app");
        System.out.println(trie.search("app"));     // true
    }
}
```

### 🧪 Optional: JUnit Test

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class TrieTest {
    @Test
    void testTrie() {
        Trie trie = new Trie();
        trie.insert("apple");
        assertTrue(trie.search("apple"));
        assertFalse(trie.search("app"));
        assertTrue(trie.startsWith("app"));
        trie.insert("app");
        assertTrue(trie.search("app"));
    }
}
```

> 💡 Time: O(L) per op, Space: O(L) per insert — textbook Trie.

—

