This guide focuses strictly on the standard Python `dict`. In many interviews, interviewers may ask you **not** to import libraries like `collections` (Counter/defaultdict) to test your logic.

Here is everything you need to know about the standard `dict` for DSA.

---

### 1. Time Complexity (The "Why")
The Dictionary is a **Hash Map**. It is the most powerful tool for optimizing algorithms (e.g., turning $O(N^2)$ logic into $O(N)$).

| Operation | Method | Average Case | Worst Case | Notes |
| :--- | :--- | :--- | :--- | :--- |
| **Get** | `d[key]` / `d.get(k)` | **O(1)** | O(N) | Instant lookup. |
| **Set** | `d[key] = val` | **O(1)** | O(N) | Instant insertion. |
| **Delete** | `del d[key]` | **O(1)** | O(N) | Instant removal. |
| **Contains**| `key in d` | **O(1)** | O(N) | Checks if key exists. |

*Note: Worst case O(N) happens only during extremely rare Hash Collisions. For interviews, treat these as O(1).*

---

### 2. Accessing Values (Safely)
The standard `d[key]` crashes your program with a `KeyError` if the key doesn't exist. Use these methods instead:

*   **The `.get()` Method (Interview MVP)**
    Retrieves a value, but returns `None` (or a default) instead of crashing if missing.
    ```python
    d = {"a": 1}
    
    # Crash risk:
    # val = d["b"] -> KeyError
    
    # Safe:
    val = d.get("b") # Returns None
    
    # Specific Default:
    val = d.get("b", 0) # Returns 0
    val = d.get("b", -1) # Returns -1
    ```

---

### 3. Adding & Updating (The Patterns)

*   **Bulk Update:**
    Merge two dictionaries. (If keys overlap, the new one overwrites).
    ```python
    d1 = {"a": 1, "b": 2}
    d2 = {"b": 3, "c": 4}
    
    d1.update(d2) 
    # d1 is now {'a': 1, 'b': 3, 'c': 4}
    ```

*   **The `setdefault()` Trick:**
    Useful for building Adjacency Lists (Graphs) or grouping items without checking `if key in d` first.
    ```python
    # Goal: Group indexes by number: {1: [0, 3], 2: [1]}
    nums = [1, 2, 1, 3]
    d = {}
    
    for i, num in enumerate(nums):
        # If 'num' exists, get the list. If not, create empty list [], insert into dict, return it.
        d.setdefault(num, []).append(i)
    ```

---

### 4. Removal Methods

*   **Safe Pop:**
    Removes key and returns value. Allows a default return to avoid errors.
    ```python
    d = {"a": 1, "b": 2}
    
    val = d.pop("a")        # Returns 1, removes "a"
    val = d.pop("z", None)  # Returns None, does nothing (Safe!)
    ```

*   **Pop Last Item (LIFO):**
    Since Python 3.7, dicts preserve insertion order.
    ```python
    d = {"a": 1, "b": 2}
    k, v = d.popitem() # Returns ("b", 2)
    ```

---

### 5. Iteration Tricks
Do not iterate just keys and lookup values manually (that’s redundant).

*   **Iterate Key and Value (Standard):**
    ```python
    for k, v in d.items():
        print(f"{k}: {v}")
    ```

*   **Iterate Keys (sorted):**
    Keys are not sorted by default.
    ```python
    for k in sorted(d): # Returns sorted list of keys
        print(k, d[k])
    ```

---

### 6. Dictionary Comprehensions
Just like list comprehensions, but for Key-Value pairs.

*   **Create Dict from List:**
    ```python
    keys = ['a', 'b', 'c']
    # Create map { 'a': 0, 'b': 0, 'c': 0 }
    d = {k: 0 for k in keys} 
    ```

*   **Invert a Dictionary (Swap Key/Value):**
    *Warning: Values must be unique and immutable.*
    ```python
    d = {'a': 1, 'b': 2}
    inv_d = {v: k for k, v in d.items()} 
    # Result: {1: 'a', 2: 'b'}
    ```

*   **Filtering a Dict:**
    ```python
    prices = {'apple': 50, 'banana': 10, 'mango': 100}
    # Keep only expensive items
    expensive = {k: v for k, v in prices.items() if v > 40}
    ```

---

### 7. Essential DSA Patterns (Using only `dict`)

#### Pattern A: The Frequency Map (Histogram)
Counting how many times elements appear.
*Without `collections.Counter`*:
```python
arr = [1, 2, 2, 3, 1, 1]
freq = {}

for x in arr:
    freq[x] = freq.get(x, 0) + 1
# Result: {1: 3, 2: 2, 3: 1}
```

#### Pattern B: Caching / Memoization
Used in Dynamic Programming (Top-Down).
```python
memo = {}
def fib(n):
    if n in memo: return memo[n] # O(1) Check
    if n <= 1: return n
    
    memo[n] = fib(n-1) + fib(n-2) # Store result
    return memo[n]
```

#### Pattern C: Finding Duplicates / "Two Sum"
Using the dict to check "Have I seen this before?"
```python
# Two Sum: Find indices of two numbers that add up to target
nums = [2, 7, 11, 15]; target = 9
seen = {} # Map value -> index

for i, num in enumerate(nums):
    diff = target - num
    if diff in seen:
        print(seen[diff], i) # Found pair!
    seen[num] = i
```

---

### 8. Sorting a Dictionary
You cannot "sort" a dictionary in place (it relies on insertion order), but you can create a **sorted view** or a new sorted dict.

*   **Sort by Key:**
    ```python
    d = {'b': 2, 'a': 10, 'c': 5}
    sorted_d = dict(sorted(d.items())) 
    # {'a': 10, 'b': 2, 'c': 5}
    ```

*   **Sort by Value (Common Interview Q):**
    Use a lambda function on `d.items()`.
    ```python
    d = {'apple': 5, 'banana': 2, 'orange': 10}
    
    # x[0] is key, x[1] is value
    sorted_by_val = dict(sorted(d.items(), key=lambda x: x[1]))
    # {'banana': 2, 'apple': 5, 'orange': 10}
    
    # Sort by value descending (High to Low)
    rev_sorted = dict(sorted(d.items(), key=lambda x: x[1], reverse=True))
    ```

---

### 9. Hashing Constraints (Crucial Concept)
In Python, dictionary **Keys** must be **Immutable** (Hashable).
*   **Valid Keys:** Integers, Strings, Tuples `(1, 2)`, Booleans.
*   **Invalid Keys:** Lists `[1, 2]`, Dictionaries, Sets.

**Trick for Arrays as Keys:**
If you need to store a list as a key (e.g., grouping anagrams), convert it to a **Tuple** first.
```python
d = {}
my_list = [1, 2, 3]
# d[my_list] = "val" # ERROR: unhashable type: 'list'
d[tuple(my_list)] = "val" # Works!
```