# 问题描述

作业排序问题涉及一组作业，每个作业具有一定的利润、截止日期和执行时间。目标是**找到一种作业执行顺序**，使得在截止日期内完成的作业所获得的总利润最大化。如果一个作业在截止日期后完成，将产生惩罚或不会获得利润。

具体问题如下：

- 给定 n 个作业，每个作业 Ji​ 具有利润 pi​、截止日期 di​ 和执行时间 ti。
- 如果一个作业在其截止日期 di 之前完成，则可以获得利润 pi​；否则，将无法获得该作业的利润。

# 代码
---
```
[[include]] <iostream>
[[include]] <vector>
[[include]] <queue>
[[include]] <algorithm>

using namespace std;

struct Job {
    int id;
    int penalty;
    int deadline;
    int time;
};

struct Node {
    vector<bool> assigned; // which jobs have been assigned
    int level; // level of node in decision tree
    int cost; // cost of jobs assigned so far
    int bound; // upper bound of the cost in subtree rooted with this node
};

bool comparison(Job a, Job b) {
    return a.penalty > b.penalty;
}

int calculateBound(Node u, int n, vector<Job> &jobs) {
    int bound = u.cost;
    int remainingTime = 0;

    // Add remaining jobs' penalty if they can be completed on time
    for (int j = u.level + 1; j < n; j++) {
        if (!u.assigned[j]) {
            remainingTime += jobs[j].time;
            if (remainingTime <= jobs[j].deadline) {
                bound += jobs[j].penalty;
            } else {
                break;
            }
        }
    }

    return bound;
}

int branchAndBound(vector<Job> &jobs, int n) {
    sort(jobs.begin(), jobs.end(), comparison); // Sort jobs by penalty

    queue<Node> Q;
    Node u, v;

    // Initial root node
    u.level = -1;
    u.cost = 0;
    u.assigned = vector<bool>(n, false);
    Q.push(u);

    int maxPenalty = 0; // Initialize result

    while (!Q.empty()) {
        u = Q.front();
        Q.pop();

        if (u.level == n - 1) continue; // If reached the end of job list

        v.level = u.level + 1;
        v.assigned = u.assigned;

        // Consider including the next job
        v.assigned[v.level] = true;
        v.cost = u.cost + jobs[v.level].penalty;
        v.bound = calculateBound(v, n, jobs);

        if (v.bound > maxPenalty) {
            maxPenalty = max(maxPenalty, v.cost);
            Q.push(v);
        }

        // Consider excluding the next job
        v.assigned[v.level] = false;
        v.cost = u.cost;
        v.bound = calculateBound(v, n, jobs);

        if (v.bound > maxPenalty) {
            maxPenalty = max(maxPenalty, v.cost);
            Q.push(v);
        }
    }

    return maxPenalty;
}

int main() {
    vector<Job> jobs = {
        {1, 5, 1, 1},
        {2, 10, 3, 2},
        {3, 6, 2, 1},
        {4, 3, 1, 1}
    };

    int n = jobs.size();
    cout << "Maximum Penalty: " << branchAndBound(jobs, n) << endl;

    return 0;
}

```