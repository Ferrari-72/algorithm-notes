# Binary Search Infinite Loop - Scenarios

## Introduction
In binary search algorithms, infinite loops can arise from various implementation details. This document explores two scenarios: Scenario A with a sorted array using the index-mapping method and Scenario B with an unsorted array using the counting method.

## Scenario A: Sorted Array with Index-Mapping Method (O(log n))
In a sorted array, binary search can be efficiently implemented using an index-mapping method. The algorithm divides the search space in half at each step.

### Implementation
```python
def binary_search_sorted(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = left + (right - left) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```

## Scenario B: Unsorted Array with Counting Method (O(n log n), LeetCode 287)
In this scenario, we sort the array first, then apply the binary search. This method may lead to higher time complexity due to initial sorting.

### Implementation
```python
def find_duplicate(nums):
    nums.sort()
    for i in range(1, len(nums)):
        if nums[i] == nums[i - 1]:
            return nums[i]
    return -1
```

## Comparison Table

| Method        | Time Complexity | Use Case                       |
|---------------|----------------|--------------------------------|
| Index Mapping | O(log n)       | Sorted arrays                  |
| Counting      | O(n log n)    | Unsorted arrays                |

## Why the Original Code Had an Infinite Loop
The infinite loop often occurs when the bounds of the search do not converge. This may happen if the mid-point calculation is incorrect or if the conditions to update the search boundaries (left and right) are not properly implemented.

## Conclusion
Choose the index-mapping method for sorted arrays to ensure efficiency. The counting method serves well for scenarios requiring flexibility with unsorted data but may require more resources.
