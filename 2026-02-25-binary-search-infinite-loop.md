# 🔍 2026-02-25: Find the Duplicate Number - Binary Search

## 📋 Problem Description
Given a sorted array `A = [a₀, a₁, ..., aₙ]` including all the integers in the range `{0, 1, 2, ..., n-1}` exactly once, except for one of them which appears **twice**. 

**Task**: Design a divide and conquer algorithm to find the only repeated element.

## 📥 Input
- A single line containing n integers, representing a sorted array `[a₀, a₁, ..., aₙ₋₁]`
- The array contains all integers from 0 to n-2 exactly once, except that one of them appears twice
- Array length is n, and all values are in the range [0, n-2]

## 📤 Output
Output a single integer — the only repeated element in the array.

## 🧪 Sample Test Cases

### Sample Input 1
```
[0,1,1,2,3,4]
```
**Sample Output 1**: `1`

### Sample Input 2
```
[0,1,2,2]
```
**Sample Output 2**: `2`

### Sample Input 3
```
[0,1,2,3,3,4]
```
**Sample Output 3**: `3`

## ❌ My Incorrect Code (Caused Infinite Loop)
```python
def findDuplicate(arr):
    left = 0
    right = len(arr) - 1
    
    while left <= right:
        mid = (left + right) // 2
        
        if arr[mid] == mid:
            left = mid  # ⚠️ FATAL ERROR: left doesn't move forward!
        else:
            right = mid  # ⚠️ FATAL ERROR: right doesn't move backward!
```

### 🔴 Why This Causes Infinite Loop?
When `left` and `right` are adjacent (e.g., 2 and 3):
- `mid` becomes 2
- If the condition is true, `left = mid` means `left` stays at 2
- If the condition is false, `right = mid` means `right` stays at 2
- The search range **never shrinks** → **Infinite Loop** 🔁

## ✅ Correct Solution

### 💡 Key Insight
In a sorted array containing 0 to n-2 (with one duplicate):
- At index `i`, the expected value should be `i`
- If `arr[i] > i`: duplicate is at or after index `i` → move `left` forward
- If `arr[i] == i`: keep searching or found it
- If `arr[i] < i`: duplicate is before index `i` → move `right` backward

### Solution: Binary Search with Proper Boundary Movement
```python
def findDuplicate(arr):
    left = 0
    right = len(arr) - 1
    
    while left <= right:
        mid = (left + right) // 2
        count = 0
        
        # Count how many elements are <= mid
        for num in arr:
            if num <= mid:
                count += 1
        
        # If count > mid + 1, duplicate is in [0, mid]
        if count > mid + 1:
            right = mid - 1  # ✅ Move right backward
        else:
            left = mid + 1   # ✅ Move left forward
    
    return left
```

### Alternative: Direct Comparison Approach
```python
def findDuplicate(arr):
    left = 0
    right = len(arr) - 1
    
    while left < right:
        mid = (left + right) // 2
        
        if arr[mid] > mid:
            # Duplicate is to the right (values shifted)
            left = mid + 1  # ✅ Guaranteed to move
        else:
            # Duplicate is to the left
            right = mid     # ✅ Still makes progress
    
    return arr[left]
```

## 💼 Complete Test Implementation
```python
def findDuplicate(arr):
    """Find the duplicate number in sorted array using binary search."""
    left = 0
    right = len(arr) - 1
    
    while left < right:
        mid = (left + right) // 2
        
        if arr[mid] > mid:
            left = mid + 1
        else:
            right = mid
    
    return arr[left]


# Test Cases
print(findDuplicate([0,1,1,2,3,4]))   # Output: 1 ✓
print(findDuplicate([0,1,2,2]))       # Output: 2 ✓
print(findDuplicate([0,1,2,3,3,4]))   # Output: 3 ✓
```

## ⚠️ Binary Search Common Pitfalls
| Pitfall | Issue | Fix |
|---------|-------|-----|
| `left = mid` instead of `left = mid + 1` | Infinite loop when adjacent | Always use `+1` or `-1` |
| `right = mid` instead of `right = mid - 1` | Infinite loop when adjacent | Use `-1` for upper bound |
| Using `<=` with improper boundaries | May skip elements | Be careful with loop condition |
| Integer overflow | `mid = (left + right) // 2` fails | Use `mid = left + (right - left) // 2` |

## 🎯 Time & Space Complexity
- **Time**: O(n log n) - binary search with linear counting
- **Space**: O(1) - only using pointers

---
**Date**: 2026-02-25 | **Topic**: Binary Search + Divide & Conquer | **Difficulty**: ⭐⭐ Medium