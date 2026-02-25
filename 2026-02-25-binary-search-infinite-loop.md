# 🐛 2026-02-25: Binary Search Infinite Loop

## ❌ Problem Description
While solving the **"Find Duplicate in Sorted Array"** problem using Binary Search, my code entered an **infinite loop** and never terminated.

## ❌ My Incorrect Code
```python
left = 0
right = len(arr) - 1

while left <= right:
    mid = (left + right) // 2
    
    if arr[mid] == mid:
        left = mid  # ⚠️ FATAL ERROR HERE!
    else:
        right = mid
```

### 🔍 Why This Causes Infinite Loop?
When `left` and `right` are adjacent (e.g., 2 and 3):
- `mid` becomes 2
- If `left = mid` executes, `left` remains 2
- The search range never shrinks → **Infinite Loop**

## ✅ Correct Code

### Method 1: Using `left = mid + 1` and `right = mid - 1`
```python
left = 0
right = len(arr) - 1

while left <= right:
    mid = (left + right) // 2
    
    if arr[mid] == mid:
        return mid  # Found!
    elif arr[mid] < mid:
        left = mid + 1  # ✅ Move left boundary forward
    else:
        right = mid - 1  # ✅ Move right boundary backward
```

### Method 2: Handling Edge Cases
```python
def findDuplicate(arr):
    left = 0
    right = len(arr) - 1
    
    while left <= right:
        mid = (left + right) // 2
        
        if arr[mid] == mid:
            return mid
        elif arr[mid] > mid:
            # Value is too large, search left
            right = mid - 1
        else:
            # Value is too small, search right
            left = mid + 1
    
    return -1  # Not found
```

## 💡 Key Takeaways
| Mistake | Solution |
|---------|----------|
| Using `left = mid` | Use `left = mid + 1` to ensure progress |
| Using `right = mid` | Use `right = mid - 1` to ensure progress |
| Not handling boundary correctly | Always increment/decrement by 1 |

## 🧪 Test Cases
```python
# Test Case 1: Duplicate found
arr1 = [0, 1, 2, 2, 3]
print(findDuplicate(arr1))  # Output: 2

# Test Case 2: No duplicate
arr2 = [0, 1, 2, 3, 4]
print(findDuplicate(arr2))  # Output: -1

# Test Case 3: Duplicate at start
arr3 = [0, 0, 1, 2]
print(findDuplicate(arr3))  # Output: 0
```

## ⚠️ Common Binary Search Pitfalls
1. **Off-by-one errors** with boundary adjustments
2. **Using `=` instead of `+1` or `-1`** when updating pointers
3. **Forgetting to handle edge cases** (empty array, single element)
4. **Integer overflow** with `mid = (left + right) // 2` (use `mid = left + (right - left) // 2`)

---
**Date**: 2026-02-25 | **Topic**: Binary Search | **Difficulty**: ⭐⭐ Medium