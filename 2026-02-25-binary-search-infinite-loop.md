# 🐛 Binary Search: Infinite Loop & Strategy Mistakes

## Introduction
This note documents my critical mistakes when implementing Binary Search for finding duplicate numbers. Specifically, it highlights the confusion between **Index-Mapping** (for sorted arrays) and **Counting Method** (for unsorted arrays), and the common cause of infinite loops.

---

## ❌ Mistake 1: The Infinite Loop Trap
**The Error**: In my initial implementation, I updated the search boundaries incorrectly, causing the search range to never shrink.

### ❌ My Wrong Code
```python
while left <= right:
    mid = (left + right) // 2
    if condition_met:
        left = mid      # ⚠️ FATAL ERROR: Should be mid + 1
    else:
        right = mid     # ⚠️ FATAL ERROR: Should be mid - 1
```

**Why it failed:**
When `left` and `right` are adjacent (e.g., 2 and 3), `mid` becomes 2. If we set `left = mid`, `left` remains 2. The range [2, 3] never changes, leading to an infinite loop.

### ✅ The Fix
Always exclude `mid` from the next search range:
```python
left = mid + 1
right = mid - 1
```

---

## ❌ Mistake 2: Confusing Scenario B (Counting Method) with Sorting
**The Error**: I mistakenly thought the "Counting Method" for unsorted arrays required sorting the array first. I also confused it with a simple linear scan after sorting.

### ❌ My Wrong Understanding & Code
**Wrong Idea**: "To handle unsorted arrays, I should just sort them first and then look for neighbors."

**Wrong Code:**
```python
def find_duplicate_WRONG(nums):
    nums.sort()  # ⚠️ ERROR: Modifies the array (often forbidden)!
    for i in range(1, len(nums)):
        if nums[i] == nums[i-1]:
            return nums[i]
    # This is O(n log n) due to sorting, but violates "read-only" constraint.
```

**Why it failed:**
- **Constraint Violation**: Many problems (like LeetCode 287) explicitly state *do not modify the array*. Sorting changes the array.
- **Logic Confusion**: The true "Counting Method" uses Binary Search on the Answer Range, NOT sorting. It counts elements to decide the range, preserving the original array order.

### ✅ The Correct Counting Method (Binary Search on Answer)
**Logic**: Guess a number `mid`. Count how many numbers in the array are `<= mid`. If `count > mid`, the duplicate is in the lower half.

```python
def find_duplicate_correct(nums):
    left, right = 1, len(nums) - 1
    
    while left <= right:
        mid = (left + right) // 2
        count = 0
        
        # Count elements <= mid (O(n))
        for num in nums:
            if num <= mid:
                count += 1
        
        if count > mid:
            right = mid - 1 # Duplicate is in [left, mid]
        else:
            left = mid + 1  # Duplicate is in [mid+1, right]
            
    return left
```

---

## ⚔️ Comparison: When to Use Which?

| Feature | Index-Mapping (Scenario A) | Counting Method (Scenario B) |
|---------|---------------------------|------------------------------|
| Array State | Must be Sorted ✅ | Works on Unsorted ✅ |
| Modify Allowed? | No requirement | Cannot Modify (Read-only) ⚠️ |
| Time Complexity | $O(\log n)$ (Fastest 🚀) | $O(n \log n)$ (Slower 🐢) |
| My Previous Mistake | Used `left=mid` causing loops | Thought I needed to `sort()` first |
| Core Idea | Compare value vs index | Count elements <= guess |

---

## 💡 Key Takeaways
1. **Infinite Loop Prevention**: Always update pointers as `mid + 1` or `mid - 1`. Never use `mid` directly.
2. **Don't Sort Blindly**: For "Find Duplicate" in unsorted arrays, check constraints first. If "read-only", use Counting Method, NOT sorting.
3. **Understand the Trade-off**: The Counting Method is slower ( $O(n \log n)$ ) because it trades time for space ( $O(1)$ ) and immutability.
