# 问题描述
***
Given an array `nums` of size `n`, return _the majority element_.

The majority element is the element that appears more than `⌊n / 2⌋` times. You may assume that the majority element always exists in the array.

**Example 1:**

**Input:** nums = [3,2,3]
**Output:** 3

**Example 2:**

**Input:** nums = [2,2,1,1,1,2,2]
**Output:** 2

**Constraints:**

- `n == nums.length`
- `1 <= n <= 5 * 104`
- `-109 <= nums[i] <= 109`

**Follow-up:** Could you solve the problem in linear time and in `O(1)` space?

# 个人思路
---
## 第一遍自己写：
使用数据结构为数组，数组的基本操作是排序和遍历。问题要我在 linear time 和 O(1)的空间复杂度中完成。

联想到条件给出众数大于一半，所以排序后中间位置的数肯定是多数元素
从线性排序想到的是 counting sort 、桶排序与radix sort。但是他们的空间复杂度都不为 O(1) 。
就地排序出发，能想到冒泡，选择，插入，但是他们都不是线性时间。

暂时没思路了，想了超过 10min 了，看大佬的

## 看博客思路之后：

从 **多数元素大于一半** 这个条件出发，使用 **Boyer-Moore 多数投票算法**

原理：
- 设置一个 count = 1 和 candidate = a[0]。
- 开始遍历数组
	- 当遍历到与 candidate相同的元素时，count--。
	- 反之 count--
	- 当count == 0 之后，将下一位设置为 candidate。
- 在 多数元素 > $\frac{n}{2}$ 的情况下，多数元素的数量大于其他所有元素和，所以最后的 candidate 肯定是多数元素
复杂度：
- 时间复杂度：只遍历了一遍数组，线性时间
- 空间复杂度：只设置了变量 candidate 和 count，就地排序

如果是众数的话，也就是去掉 目标元素大于数组一半这个条件。那么暂时没有想到也没有看到可以同时满足线性时间和就地排序这两个条件的

# 网上大佬的思路
***
https://leetcode.jp/leetcode-169-majority-element-%E8%A7%A3%E9%A2%98%E6%80%9D%E8%B7%AF%E5%88%86%E6%9E%90/

# 我的代码
***
```
public int majorityElement(int[] nums) {  
    int candidate = 0;  
    int count = 0;  
    for (int a : nums) {  
        if (count == 0) {  
            candidate = a;  
            count = 1;  
        } else if (candidate == a) {  
                count ++;  
  
        } else {  
          count--;  
        }  
    }  
    return candidate;  
}
```