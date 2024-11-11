# 问题描述

给定两个字符串，找到它们的最长公共子序列的长度和具体的子序列。公共子序列是不需要连续但需要保持相对顺序的字符序列。

# 五步走
---
## 1. 确定 dp 数组及下标的含义

- 定义 `dp[i][j]` 表示字符串 `text1` 的前 `i` 个字符和字符串 `text2` 的前 `j` 个字符的最长公共子序列长度。

## 2. 确定递推公式

- 如果 `text1[i-1] == text2[j-1]`，则 `dp[i][j] = dp[i-1][j-1] + 1`。
- 否则，`dp[i][j] = max(dp[i-1][j], dp[i][j-1])`。

## 3. DP 数组如何初始化

- 初始化 `dp` 数组大小为 `(m+1) x (n+1)`，其中 `m` 是 `text1` 的长度，`n` 是 `text2` 的长度。
- 初始化 `dp` 数组的边界条件：`dp[i][0] = 0` 和 `dp[0][j] = 0`，因为一个字符串为空时，公共子序列长度为 0。

## 4. 确定遍历顺序

![[IMG-20241028151531791.png]]

- 从 `1` 到 `m` 遍历 `i`，从 `1` 到 `n` 遍历 `j`，依次填充 `dp` 数组。
- 为了在递推的过程中，这三个方向都是经过计算的数值，所以要从前向后，从上到下来遍历这个矩阵。
# 复杂度

- **时间复杂度**:
    
    - 初始化 `dp` 数组的时间复杂度是 `O(m * n)`。
    - 填充 `dp` 数组的时间复杂度是 `O(m * n)`，因为每个元素都需要计算一次。
    - 追踪路径的时间复杂度是 `O(m + n)`，因为每次比较都移动指针。
    - 总的时间复杂度是 `O(m * n)`。
- **空间复杂度**:
    
    - `dp` 数组的空间复杂度是 `O(m * n)`，因为数组大小是 `(m + 1) x (n + 1)`。
    - 其他辅助空间的复杂度为常数 `O(1)`。
    - 总的空间复杂度是 `O(m * n)`。
# 代码
---
```
[[include]] <iostream>
[[include]] <vector>
[[include]] <string>

using namespace std;

// Function to find the length of LCS and track the actual LCS
pair<int, string> longestCommonSubsequence(string text1, string text2) {
    int m = text1.size();
    int n = text2.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
    
    // Fill the dp array
    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (text1[i - 1] == text2[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }

    // Reconstruct the LCS from the dp array
    int length = dp[m][n];
    string lcs;
    int i = m, j = n;
    while (i > 0 && j > 0) {
        if (text1[i - 1] == text2[j - 1]) {
            lcs.push_back(text1[i - 1]);
            --i;
            --j;
        } else if (dp[i - 1][j] > dp[i][j - 1]) {
            --i;
        } else {
            --j;
        }
    }
    reverse(lcs.begin(), lcs.end());
    
    return {length, lcs};
}

int main() {
    // Example input
    string text1 = "abcde";
    string text2 = "ace";

    // Calculate the length of LCS and the actual LCS
    pair<int, string> result = longestCommonSubsequence(text1, text2);
    int lcs_length = result.first;
    string lcs = result.second;

    // Output the length of LCS and the actual LCS
    cout << "The length of the longest common subsequence is: " << lcs_length << endl;
    cout << "The longest common subsequence is: " << lcs << endl;

    return 0;
}

```