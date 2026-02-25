# Difference Between Two Approaches to Binary Search

Binary search is a powerful algorithm for finding an element's position in a sorted array. However, there are usually multiple ways to implement it. In this document, we will illustrate two common approaches, explaining each step in beginner-friendly language.

## Approach 1: Iterative Binary Search

### Steps:
1. **Initialization**: Start with two pointers: `low` (the beginning of the array) and `high` (the end of the array).
2. **Calculate Middle**: Find the middle index by using the formula `(low + high) / 2`.
   - If the middle element equals the target, you found the target!
3. **Compare and Adjust**:
   - If the middle element is less than the target, move the `low` pointer to `middle + 1`.
   - If the middle element is greater than the target, move the `high` pointer to `middle - 1`.
4. **Repeat**: Continue the process until `low` exceeds `high`. If you reach this point without finding the target, it’s not in the array.

### Example:
- **Array**: [1, 2, 3, 4, 5]
- **Target**: 3
- Start: low = 0, high = 4, middle = 2 (element is 3, target found!)

## Approach 2: Recursive Binary Search

### Steps:
1. **Base Case**: Check if `low` is greater than `high`. If it is, return an indicator that the target is not found.
2. **Calculate Middle**: Similar to the iterative approach, find the middle index.
3. **Compare**:
   - If the middle element matches the target, return its index.
4. **Recursion**:
   - If the middle element is less than the target, call the function recursively with `low = middle + 1`.
   - If the middle element is greater, call the function recursively with `high = middle - 1`.

### Example:
- **Array**: [1, 2, 3, 4, 5]
- **Target**: 3
- Start: low = 0, high = 4, middle = 2 (element is 3, target found!)

## Summary of Differences
- **Control Flow**: 
  - **Iterative**: Uses a loop and modifies `low` and `high` directly.
  - **Recursive**: Calls itself to break the problem down into smaller problems.
- **Efficiency**:
  - Both approaches are efficient and run in O(log n) time, but iterative may be more memory-efficient because it does not involve the overhead of recursive calls.

By understanding both methods, you can choose the one that best suits your coding style or specific use case!