# 问题描述
---
Given an unsorted integer array `nums`. Return the _smallest positive integer_ that is _not present_ in `nums`.

You must implement an algorithm that runs in `O(n)` time and uses `O(1)` auxiliary space.

**Example 1:**

**Input:** nums = [1,2,0]
**Output:** 3
**Explanation:** The numbers in the range [1,2] are all in the array.

**Example 2:**

**Input:** nums = [3,4,-1,1]
**Output:** 2
**Explanation:** 1 is in the array but 2 is missing.

**Example 3:**

**Input:** nums = [7,8,9,11,12]
**Output:** 1
**Explanation:** The smallest positive integer 1 is missing.

**Constraints:**

- `1 <= nums.length <= 105`
- `-231 <= nums[i] <= 231 - 1`

# 个人思路
---
## 第一遍自己思路

要求给一个未排序的整数数组，返回比这个数组中没出现的最小正整数X。基础数据结构是数组，数组的基础操作是排序和遍历。要求线性时间和就地排序，八大排序都不能用了，自然想到众数（169）那道题。但是 Boyer-Moore 是利用众数超出元素一半的办法来得到最多元素的。此题要求不涉及众数。我遍历一遍，找出大于0的最小值，如果不是1，那么X=1。如果是是1，那么之前遍历再加入求得最大值和最小值，判断他们的正负。最大值 = 1 ，自然就是2.最小值是1 ···G。双指针呢？一个指向正整数里最小的，一个指向次小的。但这俩连一起就不行了。

## 大佬思路

参考博客：https://leetcode.wang/leetCode-41-First-Missing-Positive.html

思路总结：对于这种要求空间复杂度的，我们可以先考虑如果有一个等大的空间，我们可以怎么做。然后再考虑如果直接用原数组怎么做，主要是要保证数组的信息不要丢失。目前遇到的，主要有两种方法就是交换和取相反数。

### 交换

类似于count sort的思路，但是是利用原数组作中间数组。从`A[0]` 开始，`A[0]`的元素大小 a 在数组长度内，就交换 `A[0]` 和 `A[x]`。 接着处理交换过来的新`A[0]`，<= 0 或者 超出数组长度 就不管，对`A[1]` 进行相同操作。这样操作结束之后。从 0 到 n 每个位置上都待着 有效范围外的数 或者 数组中包含的`A[m] = m+1` 这步排序最多进行 n 次，时间复杂度 O(n)。之后只需要再遍历数组找到第一个 `A[k]!=k+1` 的 k+1 就是答案。这一步也最坏是 O(n)，那么总的时间复杂度是O(n)，且满足就地排序

# 我的代码
***
```
 public static void firstMissingPositive(int[] nums) {
        for (int i = 0; i < nums.length ; i++) {
            //nums[nums[i]-1]!=nums[i] + for 可以保证重复的数字不会占用存在的数字 i 的 A[i]
            while (nums[i]!=i+1 && nums[i]>0 && nums[i]<=nums.length && nums[nums[i]-1]!=nums[i]){
                swap(nums,i,nums[i]-1);
            }
        }
        for (int i = 0; i < nums.length ; i++) {
            if (nums[i] != i+1) {
                System.out.println("Number is = " + (i+1));
                return;
            }
        }
        System.out.println("Number is = " + nums.length+1);
    }

    public static void swap(int[] nums,int a, int b) {
        int temp = nums[a];
        nums[a] = nums[b];
        nums[b] = temp;
    }
```