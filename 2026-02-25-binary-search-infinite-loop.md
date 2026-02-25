# 🐛 2026-02-25: Binary Search Infinite Loop

## ❌ Problem Description
While solving the "Find Duplicate in Sorted Array" problem using Binary Search, my code entered an **infinite loop** and never terminated.

## ❌ My Incorrect Code
```python
left = 0
right = len(arr) - 1

while left <= right:
    mid = (left + right) // 2
    
    if arr[mid] == mid:
        left = mid  # <--- ⚠️ FATAL ERROR HERE!
    else:
        right = mid
        
# Result: When 'left' and 'right' are adjacent (e.g., 2 and 3), 'mid' becomes 2.
# If 'left = mid' executes, 'left' remains 2. The search range never shrinks.
