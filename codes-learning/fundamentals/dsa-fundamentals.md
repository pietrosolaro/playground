# CS Fundamentals: The 80/20 Plan

**Total time:** about 23–28 hours across 8 steps. Steps 1–7 are the 80/20 core (15–20 hours); Step 8 adds the classic algorithms (about 8 hours). **Goal:** reach working fluency in the roughly 20% of material that shows up in about 80% of problems, courses and interviews.

## How to use this guide

Work through one step at a time, in order. Each step follows the same loop:

1. **Read** the key ideas (about 10–15 minutes).
2. **Type out** the code template from memory, then check it against the one here.
3. **Solve** the practice problems. They are LeetCode problem names, so you can search for them directly.
4. **Answer** the self-check questions without looking, then compare with the answer key at the bottom.
5. **Tick** the step off in the progress tracker.

If you're stuck on a problem for more than 20–25 minutes, read a solution. Then re-solve it from scratch two days later. Doing the re-solve is what makes it stick.

## Progress tracker

- [x] Step 1: Big-O + Python tools (2 hrs)
- [x] Step 2: Hash maps & sets (2 hrs)
- [ ] Step 3: Recursion → trees → graphs (DFS/BFS) (4 hrs)
- [ ] Step 4: Two pointers, sliding window, binary search (3 hrs)
- [ ] Step 5: Heaps (1.5 hrs)
- [ ] Step 6: Dynamic programming basics (4 hrs)
- [ ] Step 7: Mixed practice (3 hrs)
- [ ] Step 8: Classic algorithms (8 hrs)

---

## Step 1: Big-O + Python tools (2 hrs)

### Key ideas

Big-O describes how runtime (or memory) grows as the input size *n* grows. It ignores constant factors.

| Class | Typical cause | Example |
| --- | --- | --- |
| O(1) | Direct access | `d[key]`, `lst[i]` |
| O(log n) | Halving the search space each step | Binary search |
| O(n) | One pass over the input | Sum a list |
| O(n log n) | Sorting, or divide and conquer | `sorted()`, merge sort |
| O(n²) | Nested loops over the input | Compare every pair |
| O(2ⁿ) | Branching recursion without caching | Naive Fibonacci, all subsets |

Use these rules of thumb to work out a complexity:

- **Sequential steps add**, and only the biggest term matters. O(n) + O(n²) is O(n²).
- **Nested loops multiply**: a loop inside a loop gives O(n · n).
- **Recursion cost** is the number of calls times the work per call.
- **Space** counts every extra structure you create, plus the depth of the recursion stack.
- **Amortized cost** is the average over many operations. `list.append` is O(1) amortized, even though an occasional resize costs O(n).

### Python costs you must know

| Fast: O(1) | Slow trap: O(n) |
| --- | --- |
| `lst.append(x)`, `lst.pop()` | `lst.pop(0)`, `lst.insert(0, x)` |
| `x in my_set`, `key in my_dict` | `x in my_list` |
| `deque.popleft()`, `deque.appendleft(x)` | `lst[a:b]` (copies b−a items) |

### The Python toolkit

```python
from collections import deque, Counter, defaultdict
import heapq
from functools import cache

counts = Counter("banana")             # {'a': 3, 'n': 2, 'b': 1}
groups = defaultdict(list)             # missing keys start as []
groups["fruit"].append("apple")

q = deque([1, 2, 3]); q.popleft()      # O(1) queue
people = [("ann", 30), ("bob", 25)]
people.sort(key=lambda p: p[1])        # sort by age
```

### Self-check

1. What is the complexity of a loop of size n that calls `x in my_list` inside it?
2. A function halves n until n is 1, doing O(1) work per step. What is its complexity?
3. Why is it a bad idea to use `lst.pop(0)` for a queue?

---

## Step 2: Hash maps & sets (2 hrs)

### Key ideas

A hash map (Python's `dict`) and a hash set (`set`) give O(1) average insert, lookup and delete. They are the most common answer to the question "how do I make this faster?"

They are useful in four recurring patterns:

- **"Have I seen this before?"** Use a set.
- **"How many of each?"** Use a `Counter`.
- **"Find the partner."** Store what you've seen, then look up the complement.
- **"Group by some key."** Use a `defaultdict(list)`.

Trading memory for speed like this usually turns an O(n²) solution into O(n).

### Template: the complement lookup

```python
def two_sum(nums, target):
    seen = {}                          # value -> index
    for i, x in enumerate(nums):
        if target - x in seen:
            return [seen[target - x], i]
        seen[x] = i
    return []
```

### Practice (LeetCode)

- [x] Contains Duplicate
- [x] Two Sum
- [x] Valid Anagram
- [x] Group Anagrams
- [x] Top K Frequent Elements (you'll revisit this in Step 5)

### Self-check

4. What is the time complexity of Two Sum with a hash map, compared with brute force?
5. What would you use as the key to group anagrams together?

---

## Step 3: Recursion → trees → graphs (4 hrs)

### Key ideas

Recursion, tree traversal and graph search are one skill. Each one means visiting a node and then handling its neighbours (or children).

**Recursion** has three parts:

1. A **base case**, where the function stops.
2. A **step that shrinks the problem**, so each call gets closer to the base case.
3. **Trust**, meaning you assume the recursive call returns the correct answer for the smaller problem.

**Tree traversals** differ only in when you process the current node:

- **Preorder:** node, then left, then right.
- **Inorder:** left, then node, then right. On a binary search tree, this visits the values in sorted order.
- **Postorder:** left, then right, then node.
- **Level order:** row by row, using BFS.

**Graphs** are usually stored as an adjacency list: a `dict` mapping each node to a list of its neighbours.

- **DFS (depth-first search)** goes deep first. Use it for connectivity, counting components, cycle detection and exploring every path.
- **BFS (breadth-first search)** goes level by level. Use it for **shortest paths when edges are unweighted**.
- Both run in **O(V + E)**, where V is vertices and E is edges.
- Always track visited nodes, or you will loop forever on cycles.

### Templates

```python
# Tree depth: recursion on a tree
def max_depth(root):
    if root is None:
        return 0
    return 1 + max(max_depth(root.left), max_depth(root.right))

# DFS on a graph
def dfs(graph, node, seen):
    seen.add(node)
    for nxt in graph[node]:
        if nxt not in seen:
            dfs(graph, nxt, seen)

# BFS shortest path, unweighted
from collections import deque

def bfs_distances(graph, start):
    dist = {start: 0}
    q = deque([start])
    while q:
        node = q.popleft()
        for nxt in graph[node]:
            if nxt not in dist:
                dist[nxt] = dist[node] + 1
                q.append(nxt)
    return dist
```

**Grids are graphs.** Each cell is a node, and its neighbours are the cells up, down, left and right. For each neighbour, check that it is within the grid bounds and hasn't been visited yet.

### Practice

- [x] Maximum Depth of Binary Tree
- [x] Invert Binary Tree
- [x] Binary Tree Level Order Traversal (BFS)
- [ ] Validate Binary Search Tree
- [x] Number of Islands (DFS on a grid)
- [ ] Rotting Oranges (BFS on a grid)
- [ ] Clone Graph

### Self-check

6. Which search gives the shortest path in an unweighted graph, and why?
7. What happens if you forget the visited set on a graph that has a cycle?
8. What is the time complexity of DFS on a graph with V vertices and E edges?

---

## Step 4: Two pointers, sliding window, binary search (3 hrs)

### Key ideas

**Two pointers** places one index at each end of a (usually sorted) array and moves them inward. Use it for pair sums on sorted input, palindrome checks and the "container" problems. It runs in O(n).

**A sliding window** keeps a `left` and a `right` index over a contiguous range of the array. You expand `right` and shrink `left` whenever the window breaks a rule. Use it for "longest/shortest substring or subarray such that…" questions. It runs in O(n).

**Binary search** halves the search space each step, which makes it O(log n). It has two forms:

1. **Searching a sorted array** for a value.
2. **Binary search on the answer.** If you ask "is x enough?" and the answer flips from no to yes exactly once as x increases, you can binary search over x itself.

### Templates

```python
# Two pointers
def is_palindrome(s):
    l, r = 0, len(s) - 1
    while l < r:
        if s[l] != s[r]:
            return False
        l += 1; r -= 1
    return True

# Sliding window: longest substring without repeating characters
def longest_unique(s):
    last = {}                          # char -> last index seen
    left = best = 0
    for right, ch in enumerate(s):
        if ch in last and last[ch] >= left:
            left = last[ch] + 1
        last[ch] = right
        best = max(best, right - left + 1)
    return best

# Binary search in a sorted array
def binary_search(nums, target):
    lo, hi = 0, len(nums) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if nums[mid] == target:
            return mid
        if nums[mid] < target:
            lo = mid + 1
        else:
            hi = mid - 1
    return -1

# Binary search on the answer: smallest x in [lo, hi] where ok(x) is True
def smallest_ok(lo, hi, ok):
    while lo < hi:
        mid = (lo + hi) // 2
        if ok(mid):
            hi = mid
        else:
            lo = mid + 1
    return lo
```

### Practice

- [ ] Valid Palindrome
- [ ] Container With Most Water
- [ ] Best Time to Buy and Sell Stock
- [ ] Longest Substring Without Repeating Characters
- [ ] Binary Search
- [ ] Search in Rotated Sorted Array
- [ ] Koko Eating Bananas (binary search on the answer)

### Self-check

9. What wording in a problem suggests a sliding window?
10. When can you binary search over an answer instead of over an array?

---

## Step 5: Heaps (1.5 hrs)

### Key ideas

A heap always gives you the smallest item in O(1), and adding or removing an item costs O(log n). Python's `heapq` module is a **min-heap**. To get a max-heap, push negated values.

| Operation | Cost |
| --- | --- |
| `heapq.heappush` / `heappop` | O(log n) |
| `heap[0]` (peek at the smallest) | O(1) |
| `heapq.heapify(lst)` | O(n) |

The main uses are:

- **Top k / kth largest.** Keep a min-heap of size k. This costs O(n log k), which beats sorting everything when k is small.
- **"Always take the cheapest next option."** This is how Dijkstra's algorithm and task schedulers work.
- **Merging k sorted lists.**

### Template: k largest values

```python
import heapq

def k_largest(nums, k):
    heap = []
    for x in nums:
        heapq.heappush(heap, x)
        if len(heap) > k:
            heapq.heappop(heap)        # drop the smallest
    return heap                        # the k largest values (unordered)
```

### Practice

- [ ] Kth Largest Element in an Array
- [ ] Top K Frequent Elements (use the heap version)
- [ ] K Closest Points to Origin
- [ ] Last Stone Weight (max-heap via negation)

### Self-check

11. How do you get max-heap behaviour from Python's `heapq`?
12. Why is a size-k heap better than sorting when k is much smaller than n?

---

## Step 6: Dynamic programming basics (4 hrs)

### Key ideas

Dynamic programming (DP) is **recursion plus caching**. Use it when a problem breaks into smaller subproblems that **overlap**, meaning the same subproblem comes up many times.

Follow the same four steps for every DP problem:

1. **State.** Decide what `f(i)` (or `f(i, j)`) means, and write it in one sentence.
2. **Recurrence.** Express `f(i)` in terms of smaller states.
3. **Base cases.** Define the smallest inputs directly.
4. **Cache.** Add `@cache` to the function. Optionally, convert it to a bottom-up table later.

The complexity is the **number of states × work per state**.

Common signals that a problem needs DP: "number of ways…", "minimum or maximum cost…", "can you reach / is it possible…", and choices that affect what's available later.

### Template: top-down with `@cache`

```python
from functools import cache

def coin_change(coins, amount):
    @cache
    def f(a):                          # fewest coins needed to make amount a
        if a == 0:
            return 0
        if a < 0:
            return float("inf")
        return 1 + min(f(a - c) for c in coins)

    ans = f(amount)
    return -1 if ans == float("inf") else ans
```

Very large inputs can hit Python's recursion limit (about 1000 calls deep). If that happens, convert to a bottom-up loop:

```python
def coin_change_bottom_up(coins, amount):
    dp = [0] + [float("inf")] * amount
    for a in range(1, amount + 1):
        for c in coins:
            if c <= a:
                dp[a] = min(dp[a], dp[a - c] + 1)
    return -1 if dp[amount] == float("inf") else dp[amount]
```

### Practice (in this order)

- [ ] Climbing Stairs (state: number of ways to reach step i)
- [ ] House Robber (state: best total using houses 0..i)
- [ ] Coin Change
- [ ] Longest Increasing Subsequence
- [ ] Longest Common Subsequence (2D state: i, j)
- [ ] Unique Paths (2D grid)

### Self-check

13. Why is naive recursive Fibonacci O(2ⁿ), but O(n) once you add `@cache`?
14. What are the state and the recurrence for Climbing Stairs?
15. How do you calculate the time complexity of a DP solution?

---

## Step 7: Mixed practice (3 hrs)

### Key ideas

The real skill is **recognizing which pattern a problem needs**. Before writing any code, say out loud which pattern you'll use and why.

### Pattern lookup

| If the problem says… | Try… |
| --- | --- |
| "Seen before", "count", "pair that sums to" | Hash map / set |
| Tree, or nodes and edges | DFS / BFS |
| "Shortest path", "minimum steps" (unweighted) | BFS |
| Grid of cells | DFS/BFS on the grid |
| Sorted array, "pair" or "palindrome" | Two pointers |
| "Longest/shortest substring/subarray such that" | Sliding window |
| Sorted input, or "minimum x such that…" | Binary search |
| "Top k", "kth largest", "closest k" | Heap |
| "Number of ways", "min/max cost", "is it possible" | DP |
| "All combinations/subsets/permutations" | Backtracking (recursion that tries every choice) |

### Practice (mixed: name the pattern first)

- [ ] Product of Array Except Self
- [ ] Longest Consecutive Sequence
- [ ] 3Sum
- [ ] Course Schedule
- [ ] Word Break
- [ ] Subsets
- [ ] Merge Intervals
- [ ] Minimum Window Substring (stretch)

### Final checkpoint

Write a one-page cheat sheet **from memory**. For each pattern, note its trigger phrases, a code template and its complexity. Then compare your sheet with this guide.

---

## Step 8: Classic algorithms (8 hrs)

These are the named algorithms every CS course covers. Each one reuses skills from earlier steps, which is why they come last.

### 8a. Merge sort and quicksort (1.5 hrs)

**Merge sort** splits the list in half until each piece has one item, then merges sorted pieces back together. Merging two sorted lists is easy: repeatedly take the smaller of the two front items.

- There are **log n** levels of splitting, and each level merges **n** items in total, so it runs in **O(n log n)** time, always.
- It uses **O(n)** extra space for the merged lists.

```python
def merge_sort(nums):
    if len(nums) <= 1:
        return nums
    mid = len(nums) // 2
    return merge(merge_sort(nums[:mid]), merge_sort(nums[mid:]))

def merge(left, right):
    result, i, j = [], 0, 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i]); i += 1
        else:
            result.append(right[j]); j += 1
    result.extend(left[i:])
    result.extend(right[j:])
    return result
```

**Quicksort** picks a **pivot**, splits the items into "less than the pivot," "equal" and "greater than the pivot," then sorts each side recursively.

- On average the pivot splits the list roughly in half, giving **O(n log n)**.
- In the worst case, the pivot is always the smallest or largest item, so one side is empty every time. That gives n levels × n work = **O(n²)**. Picking a random or middle pivot makes this very unlikely.

```python
def quick_sort(nums):
    if len(nums) <= 1:
        return nums
    pivot = nums[len(nums) // 2]
    less    = [x for x in nums if x < pivot]
    equal   = [x for x in nums if x == pivot]
    greater = [x for x in nums if x > pivot]
    return quick_sort(less) + equal + quick_sort(greater)
```

Practice:

- [ ] Sort an Array (implement merge sort, then quicksort)
- [ ] Merge Sorted Array

### 8b. Backtracking (2 hrs)

Backtracking explores every possible set of choices: **make a choice, explore everything that follows from it, then undo the choice and try the next one.** It's like walking a maze and stepping back at every dead end.

It runs in exponential time (O(2ⁿ) for subsets, O(n!) for permutations), so it's only usable for small inputs (n up to about 20).

```python
def subsets(nums):
    result, path = [], []
    def backtrack(i):
        if i == len(nums):
            result.append(path[:])   # save a COPY of the current choices
            return
        path.append(nums[i])         # choose: include nums[i]
        backtrack(i + 1)
        path.pop()                   # undo the choice
        backtrack(i + 1)             # explore: exclude nums[i]
    backtrack(0)
    return result

def permutations(nums):
    result, path, used = [], [], [False] * len(nums)
    def backtrack():
        if len(path) == len(nums):
            result.append(path[:])
            return
        for i in range(len(nums)):
            if not used[i]:
                used[i] = True; path.append(nums[i])     # choose
                backtrack()                              # explore
                path.pop(); used[i] = False              # undo
    backtrack()
    return result
```

Practice:

- [ ] Subsets
- [ ] Permutations
- [ ] Combination Sum
- [ ] N-Queens (stretch)

### 8c. Topological sort (1 hr)

Topological sort orders tasks so that every task comes after the tasks it depends on, like ordering university courses by their prerequisites. It only works on a **directed graph with no cycles**.

**Kahn's algorithm:** count each node's incoming edges (its *in-degree*). Start a queue with every node that has in-degree 0, meaning nothing depends on it. Repeatedly remove a node from the queue, add it to the order, and lower its neighbours' in-degrees, queueing any neighbour that drops to 0.

It runs in **O(V + E)** time, like BFS, because it is a BFS.

```python
from collections import deque

def topo_sort(n, edges):             # edge (a, b) means a must come before b
    graph = [[] for _ in range(n)]
    indegree = [0] * n
    for a, b in edges:
        graph[a].append(b)
        indegree[b] += 1
    q = deque(i for i in range(n) if indegree[i] == 0)
    order = []
    while q:
        node = q.popleft()
        order.append(node)
        for nxt in graph[node]:
            indegree[nxt] -= 1
            if indegree[nxt] == 0:
                q.append(nxt)
    return order if len(order) == n else []   # [] means there's a cycle
```

Practice:

- [ ] Course Schedule II
- [ ] Alien Dictionary (stretch)

### 8d. Dijkstra's algorithm (1.5 hrs)

Dijkstra finds the **shortest paths in a weighted graph**, where edges have different costs (like road distances). BFS only works when every edge costs the same.

The idea is to always expand the **closest unfinished node next**, using a heap to find it quickly. Once a node is popped from the heap with its smallest distance, that distance is final.

- It runs in **O((V + E) log V)** time with a heap.
- **Edge weights must be non-negative.** With negative weights, use Bellman-Ford instead.

```python
import heapq

def dijkstra(graph, start):          # graph: node -> list of (neighbour, weight)
    dist = {start: 0}
    heap = [(0, start)]              # (distance so far, node)
    while heap:
        d, node = heapq.heappop(heap)
        if d > dist[node]:
            continue                 # stale entry: a shorter path was already found
        for nxt, w in graph[node]:
            nd = d + w
            if nd < dist.get(nxt, float("inf")):
                dist[nxt] = nd
                heapq.heappush(heap, (nd, nxt))
    return dist
```

Practice:

- [ ] Network Delay Time
- [ ] Path With Minimum Effort
- [ ] Cheapest Flights Within K Stops (stretch)

### 8e. Union-Find (1 hr)

Union-Find (also called *disjoint set union*) keeps track of items split into groups. It answers "are a and b in the same group?" and merges groups, both in nearly O(1) time.

Each item points to a parent, and each group's root points to itself. Two items are in the same group if they have the same root. **Path compression** makes items point closer to the root as you search, keeping the trees flat.

```python
def make_union_find(n):
    parent = list(range(n))          # each item starts as its own group

    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]   # path compression
            x = parent[x]
        return x

    def union(a, b):
        ra, rb = find(a), find(b)
        if ra == rb:
            return False             # already in the same group
        parent[ra] = rb
        return True

    return find, union
```

Practice:

- [ ] Redundant Connection
- [ ] Number of Provinces

### 8f. Greedy algorithms (1 hr)

A greedy algorithm makes the **locally best choice at each step** and never reconsiders. It's fast and simple, but it only gives the right answer for certain problems. When greedy fails, the problem usually needs DP instead.

The classic example is interval scheduling: to fit the most non-overlapping meetings into a day, always pick the meeting that **ends earliest**, because that leaves the most room for the rest.

```python
def max_non_overlapping(intervals):
    intervals.sort(key=lambda iv: iv[1])    # earliest finish first
    count, end = 0, float("-inf")
    for start, finish in intervals:
        if start >= end:
            count += 1
            end = finish
    return count
```

It runs in **O(n log n)** time because of the sort.

Practice:

- [ ] Non-overlapping Intervals
- [ ] Jump Game
- [ ] Gas Station

### Self-check

16. Why is quicksort's worst case O(n²), and how do you avoid it?
17. In backtracking, why do you need `path.pop()` after the recursive call?
18. What does topological sort return when the graph has a cycle, and why?
19. Why doesn't Dijkstra work with negative edge weights?
20. When should you use BFS instead of Dijkstra for shortest paths?
21. What question does Union-Find answer quickly?
22. In interval scheduling, why pick the meeting that ends earliest rather than the one that starts earliest?

---

## What this plan deliberately skips

You can add these later if a course or interview needs them:

- Linked lists beyond reversal and fast/slow pointers
- Tries
- Bellman-Ford and Floyd-Warshall (shortest paths with negative weights, or between all pairs)
- Greedy proofs
- Balanced binary search trees (AVL, red-black)
- NP-completeness

---

## Answer key

1. O(n²): n iterations, each doing an O(n) list scan. Use a set to make it O(n).
2. O(log n).
3. `pop(0)` shifts every remaining element, which is O(n) per pop. A `deque.popleft()` is O(1).
4. O(n) with a hash map, compared with O(n²) for brute force (checking every pair).
5. The sorted letters, e.g. `"".join(sorted(word))`, or a tuple of the 26 letter counts.
6. BFS. It explores nodes in order of distance, so the first time it reaches a node is along a shortest path.
7. The search revisits nodes endlessly, causing an infinite loop or a recursion error.
8. O(V + E).
9. "Contiguous", "substring" or "subarray" combined with "longest", "shortest" or "at most k".
10. When a yes/no check `ok(x)` flips from False to True exactly once as x increases (it is monotonic).
11. Push `-x` and negate again when you pop.
12. It costs O(n log k) and O(k) memory, compared with O(n log n) to sort everything.
13. Without caching, the same subproblems are recomputed exponentially many times. With caching, each of the n states is computed once.
14. The state `f(i)` is the number of ways to reach step i. The recurrence is `f(i) = f(i-1) + f(i-2)`, with `f(0) = f(1) = 1`.
15. Multiply the number of states by the work done per state.
16. If the pivot is always the smallest or largest item, one side is empty every time, giving n levels × O(n) work. A random or middle pivot makes this very unlikely.
17. `path` is shared by every branch. Undoing the choice restores it, so the next branch starts from the same state.
18. It returns fewer than n nodes (the template returns `[]`). Nodes in a cycle wait on each other, so their in-degree never reaches 0 and they never enter the queue.
19. Dijkstra treats a node's distance as final once it's popped. A negative edge found later could make that distance shorter, breaking the guarantee.
20. When every edge has the same cost. BFS is simpler and runs in O(V + E), without the log factor.
21. "Are a and b in the same group?", while also letting you merge groups, both in nearly O(1) time.
22. Ending earliest leaves the most time free for the remaining meetings. A meeting that starts early might run all day and block everything else.