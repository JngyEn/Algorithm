# 问题描述：
***
![[image-20240605192617980.png]]

# 个人思路
***
题目要求在一个数组中找到所有包含三个不同位置的元素且和为0的组，且要求这些组中不能出现元素相同的组。

因此数据结构上明显使用数组，而数组的基本操作有排序和遍历。(做题的时候题干给出的输出让我以为不能对原始数组进行排序，所以没用双指针等办法，直接遍历了。遍历时间复杂度$O^3$ 太高了，再结合遍历临时数组，直接给干到 $O^6$ 去了，纯纯反例)

根据要求取三个变量作为index来遍历整个数组，i、j、k、的大小和顺序保证了在考虑顺序的情况下不会多次记录同一个组。

但是题目要求是不出现包含相同元素的组，一开始我打算用 ASCLL 码值相加来判断两个组是否包含相同元素，但是意识到如果输入数组为两位数就不行了，比如：{-21，0，21}和{-12，0，12}。

同时题干给出的输出让我以为不能对原始数组进行排序，因此我使用了一个临时数组，将所有已产生的三元数组排序之后都放进去，新产生的三元数组在排序之后与已有的进行比较，若不重合，则加入结果数组中。

***
在可以对数组进行排序的基础上，使用双指针法。双指针法在数组从小到大的基础上，选定最左边的作为基准 a[i]，取 i+1 和 n 作为两个指针
- 当 -a[i] < a[i+1] + a[n] 时，说明大了，右边指针反为 n - 1。这时，有可能 a[n-1] == a[n], 但是在循环判断之下，会保持 a[i+1] 不变一直 n--，直到  -a[i] = a[i+1] + a[n] 或者 -a[i] > a[i+1] + a[n] ，避免了重复。
- 当 -a[i] > a[i+1] + a[n] 时，情况相反
- 当 -a[i] = a[i+1] + a[n] 时，取三个值作为一个组，然后让 i++ 到 左指针 !=a[i++]

遍历结束之后 a[i] 要右移到下一个不同的数。数组从小往大排，出现过的a[i] 就不会再出现了，保证了得出的三元数组的唯一性

这样 排序为 $O(nlog n)$ ，外层选 a[i] 为 O(n)，内层指针比较也为 O(n)，最后时间复杂度为$O(n^2)$ 
# 网上大佬的思路
***
https://zhuanlan.zhihu.com/p/104800339

# 我的代码
***
```
public class threeSumSolution {  
    public static List<List<Integer>> threeSum(int[] nums) {  
        List<List<Integer>>  outPut = new ArrayList<>();  
        List<List<Integer>>  tempArray = new ArrayList<>();  
        if (nums.length < 3) {  
            System.out.println ("enter can't less than 3");  
        } else if (nums.length == 3) {  
            int sum = 0;  
            List<Integer> result = new ArrayList<Integer>();  
            for (int a : nums){  
                sum += a;  
                result.add(a);  
            }  
            System.out.println ("The only possible triplet does not sum up to " + sum);  
            if (sum == 0) {  
                outPut.add(result);  
            }  
        } else {  
            for (int i = 0; i < nums.length - 2; i++) {  
                for (int j = i + 1; j < nums.length - 1; j++){  
                    for (int k = j + 1; k < nums.length ; k++){  
                        if ( (nums[i] + nums[j] + nums[k]) == 0) {  
                            List<Integer> result = new ArrayList<Integer>();  
                            List<Integer> tempResult = new ArrayList<Integer>();  
                            result.add(nums[i]);  
                            result.add(nums[j]);  
                            result.add(nums[k]);  
                            System.out.println("nums[" + i + "] + nums[" + j + "] + nums[" + k + "] = " + (nums[i] + nums[j] + nums[k]));  
                            tempResult = result;  
                            tempResult.sort(Integer::compareTo);  
                            boolean found = false;  
                            for (List<Integer> e : tempArray){  
                                if (e.equals(tempResult)){  
                                    found = true;  
                                    break;  
                                }  
                            }  
                            if (!found){  
                                tempArray.add(tempResult);  
                                outPut.add(result);  
                            }  
  
                        }  
                    }  
                }  
            }  
        }  
        System.out.println(outPut.toString());  
        return outPut;  
    }  
    public static void main(String[] args){  
        int[] input = {-1,0,1,2,-1,-4};  
        List<List<Integer>>  outPut = threeSum(input);  
    }  
}
```

