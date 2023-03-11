## 异或性质

> 对于一个数对，对其中一个数**异或1**能得到另一个数。

```c
0 ^ 1 = 1; 1 ^ 1 = 0; // （0, 1）
2 ^ 1 = 3; 3 ^ 1 = 2; // （2, 3）
...
```

例题

> 给定一个只包含整数的有序数组 `nums` ，每个元素都会出现两次，唯有一个数只会出现一次，请找出这个唯一的数字。
> 
> 你设计的解决方案必须满足 `O(log n)` 时间复杂度和 `O(1)` 空间复杂度。

> 思路：`(0, 1)(2, 3)(4, 5)...`下标处的值在未遇到唯一数时都相等的

```python
class Solution:
    def singleNonDuplicate(self, nums: List[int]) -> int:
        low, high = 0, len(nums) - 1
        while low < high:
            mid = (low + high) // 2
            if nums[mid] == nums[mid ^ 1]:
                low = mid + 1
            else:
                high = mid
        return nums[low]
```


