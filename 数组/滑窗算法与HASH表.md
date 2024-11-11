>参考资料：
>[随想录](https://programmercarl.com/0209.%E9%95%BF%E5%BA%A6%E6%9C%80%E5%B0%8F%E7%9A%84%E5%AD%90%E6%95%B0%E7%BB%84.html#%E6%80%9D%E8%B7%AF)
>>[560. 和为k的子数组](https://github.com/doocs/leetcode/blob/main/solution/0500-0599/0560.Subarray%20Sum%20Equals%20K/README.md)


# 算法讲解
---
滑窗算法适用于 窗口内的和随窗口增大而单调递增/递减 的情况

560 中允许负数，此时无法确定应该收缩还是扩大滑窗，应该用 **哈希表 + 前缀和**

前缀和的前提条件是找连续的区间

- 通过减去k之后，能找到一个前缀和，说明此时的累计区间在减去一个存在的子区间后，能获得和为k的区间

与滑窗相比，哈希表 + 前缀和 可以适用于负数的情况

- **当我们需要查询一个元素是否出现过，或者一个元素是否在集合里的时候，就要第一时间想到哈希法。**
- hash表中记录了历史情况

![[IMG-20241109101249449.png]]

```JAVA
class Solution {

    public int subarraySum(int[] nums, int k) {

    // 定义前缀和

    int sum = 0;

    // 定义出现次数

    int result = 0;

    // 定义hash表

    Map<Integer,Integer> map =new HashMap<>();

    map.put(0,1);

    for (int x: nums) {

        sum += x;

        result += map.getOrDefault(sum - k,0);

        map.merge(sum,1,Integer::sum);

    }

    return result;

    }

}
```

# 对应题目：
---
- [[560. 和为 K 的子数组]]
- [[1. 两数之和]]
- 
