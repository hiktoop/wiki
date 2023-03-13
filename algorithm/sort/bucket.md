# 桶排序

> [数组相对排序：](https://leetcode.cn/problems/0H97ZC/description/?envType=study-plan&id=lcof-ii&plan=lcof&plan_progress=j8nuzct)
> 
> 给定两个数组，`arr1` 和 `arr2`，
> 
> - `arr2` 中的元素各不相同
> - `arr2` 中的每个元素都出现在 `arr1` 中
> 
> 对 `arr1` 中的元素进行排序，使 `arr1` 中项的相对顺序和 `arr2` 中的相对顺序相同。未在 `arr2` 中出现过的元素需要按照升序放在 `arr1` 的末尾。
> 
> **提示：**
> 
> - `1 <= arr1.length, arr2.length <= 1000`
> - `0 <= arr1[i], arr2[i] <= 1000`
> - `arr2` 中的元素 `arr2[i]` 各不相同
> - `arr2` 中的每个元素 `arr2[i]` 都出现在 `arr1` 中

```cpp
class Solution {
public:
    vector<int> relativeSortArray(vector<int>& arr1, vector<int>& arr2) {
        int upper = *max_element(arr1.begin(), arr1.end());
        vector<int> buc(upper + 1), ans;
        for(auto &a: arr1)
            buc[a]++;
        for(auto &a: arr2) {
            for(int i = 0; i < buc[a]; i++)
                ans.push_back(a);
            buc[a] = 0;
        }
        for(int i = 0; i <= upper; i++)
            for(int j = 0; j < buc[i]; j++)
                ans.push_back(i);
        return ans;
    }
};
```




