# Big O Notation — Beginner Cheat Sheet

## Plain-language version (the analogies)

Big O just describes **how much more work something takes as the pile of stuff gets bigger.** Not exact time — just the *pattern* of how work grows.

- **O(1) — Instant, no matter what.**
  Looking up a word in a dictionary app by typing it exactly. You don't scan anything — you jump straight to it.

- **O(log n) — Cuts the problem in half each time.**
  Finding a name in a phone book by opening to the middle, checking if it's before or after, then jumping to the middle of that half. Even if the phone book doubled in size, you'd only need one extra jump — not double the work.

- **O(n) — Look at everything once.**
  Reading every page of a book to find one specific word. Double the pages, double the time.

- **O(n²) — Compare everything to everything else.**
  Every person in a room asking every other person if they share a birthday. 10 people = ~100 conversations. 100 people = ~10,000 conversations. Blows up fast.

- **O(2ⁿ) — Explodes almost instantly.**
  Every choice branches into two more choices, which each branch into two more. Gets unmanageable very quickly — usually a sign of unoptimized recursive code.

**The one question that captures 90% of it:**
"If I gave this 10x more data, would it take the same time, 10x longer, or 100x longer?"
- Same time → O(1)
- 10x longer → O(n)
- 100x longer → O(n²)
- Barely any longer → O(log n)

**One example to anchor it:** checking a list for duplicates.
```python
# O(n²) — nested loop, compares everything to everything else (the birthday-party analogy)
def has_duplicate(my_list):
    for i in range(len(my_list)):
        for j in range(len(my_list)):
            if i != j and my_list[i] == my_list[j]:
                return True
    return False
```

---

## The core idea (in one sentence)
Big O describes **how much more work an algorithm does as the input gets bigger** — not exact time in seconds, just the *pattern* of growth.

The question to always ask yourself:
> "If I gave this function 10x more data, would it take roughly the same time, 10x longer, or 100x longer?"

---

## The main categories

| Big O | Name | Everyday analogy | What it looks like in code |
|---|---|---|---|
| O(1) | Constant | Looking up a word by typing it exactly | `return my_list[0]` |
| O(log n) | Logarithmic | Phone book — jump to the middle, narrow down each time | Binary search |
| O(n) | Linear | Reading every page once | Single loop over the list |
| O(n log n) | Log-linear | Divide the pile, then do linear work at each level | Merge sort, quick sort |
| O(n²) | Quadratic | Every person in a room talks to every other person | Nested loop over the same list |
| O(2ⁿ) | Exponential | Every choice branches into two more choices | Naive recursive Fibonacci |

**Growth as n goes from 10 → 100 (10x bigger):**
- O(1) → same work
- O(log n) → barely any more work
- O(n) → 10x more work
- O(n²) → 100x more work
- O(n³) → 1000x more work (three nested loops)

---

## How to spot the Big O of your own code

1. **Count nested loops over the same input.**
   Each nested loop multiplies → adds one more power to the exponent.
   - 1 loop → O(n)
   - 2 nested loops → O(n²)
   - 3 nested loops → O(n³)

2. **Sequential loops (one after another, not nested) just add — and constants get dropped.**
   ```python
   for a in list_a:   # O(n)
       ...
   for b in list_b:   # O(n)
       ...
   ```
   This is O(n) + O(n) = O(2n) → simplifies to **O(n)**. The "2" doesn't change the shape of growth, so we drop it.

3. **A loop with a lookup inside it — the lookup type matters a LOT.**
   - `x in some_list` → O(n) lookup (checks every item)
   - `x in some_set` or `x in some_dict` → O(1) lookup (basically instant)

   So: `for item in my_list: if item in seen_set` → O(n) total (loop × constant lookup)
   But: `for item in my_list: if item in seen_list` → O(n²) total (loop × linear lookup)

4. **Recursion → think branches × depth.**
   Naive recursive Fibonacci branches into 2 calls each time → roughly O(2ⁿ).

---

## The #1 pattern to know: trading space for speed

Brute-force nested loop (checking every pair):
```python
def has_duplicate(my_list):          # O(n²)
    for i in range(len(my_list)):
        for j in range(len(my_list)):
            if i != j and my_list[i] == my_list[j]:
                return True
    return False
```

Using a set to remember what you've seen:
```python
def has_duplicate(my_list):          # O(n)
    seen = set()
    for item in my_list:
        if item in seen:
            return True
        seen.add(item)
    return False
```

**Same correct answer, wildly different Big O.** Whenever you see nested loops checking for duplicates, matches, or pairs — ask yourself: *"can I use a set or dict instead, to avoid the second loop?"*

This exact trick solves the classic **Two Sum** problem too:
```python
def has_pair_with_sum(nums, target):     # O(n), not O(n²)
    seen = set()
    for num in nums:
        if target - num in seen:
            return True
        seen.add(num)
    return False
```

---

## Common Python traps (things that look O(1) but aren't)

| Looks like | Actually is | Why |
|---|---|---|
| `arr.pop(0)` | O(n) | Has to shift every remaining element over |
| `x in some_list` | O(n) | Checks every item one by one |
| `x in some_set` / `x in some_dict` | O(1) average | Hash-based lookup, no scanning needed |
| `s += char` in a loop | O(n²) total | Strings are immutable — each `+=` rebuilds the whole string |

Fix for the string trap: build a list and use `''.join(list_of_chars)` instead.

---

## Don't forget: space complexity exists too

Big O also applies to **memory**, not just time. State both when you can:
- "O(n) time, O(1) space" → nice, in-place solution
- "O(n) time, O(n) space" → fast, but uses extra memory (e.g. the `seen` set above)

There's often a trade-off: using more memory (like a set) to make code faster is extremely common and usually a *good* trade in interviews — just be ready to name it out loud.

---

## Quick self-check before every interview problem

Ask yourself, out loud if possible:
1. How many loops do I have, and are they nested or sequential?
2. What kind of lookup am I doing inside any loop — list, set, or dict?
3. Is there a nested loop I could remove by using a set/dict instead?
4. What's my time complexity? What's my space complexity?

If you can answer all four confidently, you're already ahead of a lot of candidates.
