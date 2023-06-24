---
layout:     post
title:      刷题Developer-Tester Integration
subtitle:   
date:       2022-06-24
author:     KX
header-img: img/post-bg-ios9-web.jpg
catalog: 	 true
tags:
    - Algorithm
    - DP
    - Sliding window
---
# 刷题Developer-Tester Integration

这道题是tic tok OA官方给的练习题

## 题目

一家软件公司正在组建由特定数量的员工组成的团队。有两种类型的员工：软件开发人员和软件测试人员。他们想确保开发人员和测试人员能很好地融合在一起，所以他们决定限制测试人员和开发人员与同一类型的员工连续就座的数量。考虑到所需的团队规模和这些限制，该公司可以用多少种不同的方式组成一个团队？
注意：方式的数量可能非常多，所以要以10为模数返回。
例如，我们说d=3，t=2，n=4。
这意味着你需要一个由4名员工组成的团队，其中连续在座的开发人员少于3人，连续在座的测试人员少于2人。在这些限制条件下，有5种方法来组建团队。如果 "D "表示开发人员，"T "表示测试人员，这些方式如下：
DDTD
TDDT
DTDD
TDTD
DTDT

```bash
input  d: int, t: int, q: array of integer
output array of integer, 对应q中每一个query的结果
```
## 首先考虑 DP
state transition function
```python
DP[0][n]: 以D开头长度为n的组合数量
DP[1][n]: 以T开头长度为n的组合数量
DP[0][n] = DP[1][n-1] + DP[1][n-2] + ... DP[1][n-(d-1)]
DP[1][n] = DP[0][n-1] + DP[0][n-2] + ... DP[0][n-(t-1)]
```
```python
def formTeam(d, t, q):
    m = max(q)
    dp = [[0 for x in range(m + 1)], [0 for x in range(m + 1)]]
    dp[0][0] = 1
    dp[1][0] = 1
    dp[0][1] = 1
    dp[1][1] = 1
    
    modulo = pow(10, 9) + 7
    
    for i in range(2, m + 1):
        for D in range(1, d):
            # print('i, D',i, D)
            if i - D >= 0:
                dp[0][i] = dp[0][i] + dp[1][i-D]
        dp[0][i] = dp[0][i] % modulo
        for T in range(1, t):
            if i - T >= 0:
                dp[1][i] = dp[1][i] + dp[0][i-T]
        dp[1][i] = dp[1][i] % modulo
        # print(dp)
    
    ret = [(dp[0][i] + dp[1][i]) % modulo for i in q]
    return ret
```
## 滑动窗口
考虑优化 for D in range(1, d)循环
DP[0][n] = DP[1][n-1] + DP[1][n-2] + ... DP[1][n-(d-1)]
DP[0][n+1] = DP[1][n] + DP[1][n-1] + ... DP[1][n+2-d]

dp[0][n+1] = dp[0][n] + dp[1][n] - dp[1][n+1-d]

```python
def formTeam(d, t, q):
    m = max(q)
    dp = [[0 for x in range(m + 1)], [0 for x in range(m + 1)]]
    dp[0][0] = 1
    dp[1][0] = 1
    dp[0][1] = 1
    dp[1][1] = 1
    
    modulo = pow(10, 9) + 7
    
    for i in range(2, m+1):
        if i < d:
            dp[0][i] = (dp[0][i-1] + dp[1][i-1]) % modulo
        else:
            dp[0][i] = (dp[0][i-1] + dp[1][i-1] - dp[1][i-d]) % modulo
        if i < t:
            dp[1][i] = (dp[1][i-1] + dp[0][i-1]) % modulo
        else:
            dp[1][i] = (dp[1][i-1] + dp[0][i-1] - dp[0][i-t]) % modulo
    ret = [(dp[0][i] + dp[1][i]) % modulo for i in q]
    return ret
```





