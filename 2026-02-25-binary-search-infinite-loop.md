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

## ❌ Mistake 3: Using `right = mid - 1` in `while left < right` Template
**The Error**: When using the `while left < right` template, I mistakenly used `right = mid - 1` instead of `right = mid`, causing the algorithm to miss the answer in some cases (especially when the duplicate appears near the end of the array).

### ❌ My Wrong Code
```python
while left < right:
    mid = (left + right) // 2
    
    if arr[mid] < mid:
        right = mid - 1  # ⚠️ FATAL ERROR: Should be right = mid
    else:
        left = mid + 1
```

**Why it failed:**
The `while left < right` template has a **core principle**: **the search interval must always contain the answer**.

- When `arr[mid] < mid`, it means a misalignment has occurred. The duplicate number might be `arr[mid]` itself, or to its left.
- If we write `right = mid - 1`, we **exclude the `mid` position** from the search range!
- **What if `mid` is exactly the second occurrence of the duplicate number?** We would miss it!

**Example Failure Case:**
Consider `[0, 1, 2, 2]`:
- Initial: `left=0, right=3`
- `mid=1, arr[1]=1, 1 < 1? No` → `left=2`
- `mid=2, arr[2]=2, 2 < 2? No` → `left=3`
- Final: `left=3, arr[3]=2` ✓ (This works by luck)

But consider `[0, 1, 1, 2, 3, 4]` with wrong code:
- Initial: `left=0, right=5`
- `mid=2, arr[2]=1, 1 < 2? Yes` → `right=1` (WRONG: should be `right=2`)
- `mid=0, arr[0]=0, 0 < 0? No` → `left=1`
- `mid=1, arr[1]=1, 1 < 1? No` → `left=2`
- Final: `left=2, arr[2]=1` ✓ (Works by luck, but the logic is wrong)

**The real problem**: If the duplicate is at position `mid` and we do `right = mid - 1`, we skip it!

### ✅ The Fix
In the `while left < right` template, **when you cannot be sure if `mid` is the answer, you must retain `mid`**:

```python
while left < right:
    mid = (left + right) // 2
    
    if arr[mid] < mid:
        right = mid  # ✅ CORRECT: Keep mid in search range
    else:
        left = mid + 1  # Safe to exclude mid since arr[mid] == mid
```

**Key Rule:**
- **`while left < right` template**: Search interval always contains answer → use `right = mid` when `mid` might be answer
- **`while left <= right` template**: Can exclude `mid` → use `right = mid - 1` safely

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
1. **Infinite Loop Prevention**: Always update pointers as `mid + 1` or `mid - 1`. Never use `mid` directly (in `while left <= right` template).
2. **Template-Specific Rules**: 
   - **`while left < right`**: Search interval must always contain answer → use `right = mid` (not `mid - 1`) when `mid` might be answer
   - **`while left <= right`**: Can safely exclude `mid` → use `right = mid - 1`
3. **Don't Sort Blindly**: For "Find Duplicate" in unsorted arrays, check constraints first. If "read-only", use Counting Method, NOT sorting.
4. **Understand the Trade-off**: The Counting Method is slower ( $O(n \log n)$ ) because it trades time for space ( $O(1)$ ) and immutability.

---

## 📝 Practice Problem: Find Duplicate in Sorted Array

### Problem Description
Given a sorted array `A = [a0, a1, ..., an-1]` containing all integers in the range `{0, 1, 2, ..., n-2}` exactly once, except for one of them which appears twice. Design a divide and conquer algorithm to find the only repeated element.

**Constraints:**
- Array is sorted
- Contains integers from `0` to `n-2`
- Array length is `n`
- Exactly one element appears twice

**Examples:**
- `[0, 1, 1, 2, 3, 4]` → `1`
- `[0, 1, 2, 2]` → `2`
- `[0, 1, 2, 3, 3, 4]` → `3`

### ✅ Correct Solution (Index-Mapping Method)

**Key Insight**: 
In a perfect sorted array `[0, 1, 2, ..., n-2]` of length `n`, we have `arr[i] == i` for all positions.
When there's a duplicate:
- Before the duplicate: `arr[i] == i` (normal)
- At/after the duplicate: `arr[i] < i` (because values are "pushed back")

**Example**: `[0, 1, 1, 2, 3, 4]`
- `arr[0] = 0 == 0` ✓
- `arr[1] = 1 == 1` ✓ (but 1 is duplicated)
- `arr[2] = 1 < 2` ✗ (first position where `arr[i] < i`)
- `arr[3] = 2 < 3` ✗
- `arr[4] = 3 < 4` ✗
- `arr[5] = 4 < 5` ✗

**Algorithm**: Binary search for the first position where `arr[i] < i`. The duplicate is at that position.

```python
import sys

def find_duplicate():
    # 1. 读取输入
    # sys.stdin.read() 读取所有输入内容
    # .split() 按空格分割成字符串列表
    input_data = sys.stdin.read().split()
    
    if not input_data:
        return

    # 将字符串列表转换为整数列表
    # 例如：输入 "0 1 2 2 3" -> arr = [0, 1, 2, 2, 3]
    arr = [int(x) for x in input_data]
    
    # 2. 核心算法：二分查找 (Divide and Conquer)
    left, right = 0, len(arr) - 1
    
    # 使用 left < right 模板，寻找第一个 arr[i] < i 的位置
    while left < right:
        mid = (left + right) // 2
        
        # 如果 arr[mid] < mid，说明错位发生在 mid 或 mid 左边
        # 重复的数字就在左半部分（包含 mid）
        if arr[mid] < mid:
            right = mid
        # 如果 arr[mid] == mid，说明左边都是正常的
        # 重复的数字在右半部分
        else:
            left = mid + 1
    
    # 3. 输出结果
    # 循环结束时 left == right，这个位置就是重复数字第一次导致错位的地方
    # 对应的值 arr[left] 就是我们要找的重复数字
    print(arr[left])

if __name__ == "__main__":
    find_duplicate()
```

**Why This Works:**
- In perfect array: `arr[i] == i` for all `i`
- When duplicate exists: some positions have `arr[i] < i`
- Binary search finds the **first** such position
- That position contains the duplicate value

**Trace Example** (`[0, 1, 1, 2, 3, 4]`):
```
Initial: left=0, right=5
  mid=2, arr[2]=1, 1 < 2? Yes → right=2
  mid=1, arr[1]=1, 1 < 1? No → left=2
Final: left=2, arr[2]=1 ✓
```

**Usage Example:**
```bash
# Input: 0 1 1 2 3 4
# Output: 1

# Input: 0 1 2 2
# Output: 2

# Input: 0 1 2 3 3 4
# Output: 3
```

**Test with Python:**
```python
# 可以通过重定向输入测试
# echo "0 1 1 2 3 4" | python find_duplicate.py
```

**Important Notes:**
1. **Boundary Updates**: Notice we use `right = mid` (not `mid - 1`) because:
   - We're using `while left < right` template, which requires the search interval to always contain the answer
   - When `arr[mid] < mid`, the duplicate might be at position `mid` itself (the second occurrence)
   - Using `right = mid - 1` would exclude `mid` and potentially miss the answer
2. **Loop Condition**: We use `left < right` (not `<=`) because when `left == right`, we've found the answer
3. **No Infinite Loop**: Since we always update `left = mid + 1` or `right = mid`, and `mid < right` when `left < right`, the range always shrinks
4. **Template Choice**: This is a critical distinction between `while left < right` and `while left <= right` templates - always match your boundary updates to your template!
