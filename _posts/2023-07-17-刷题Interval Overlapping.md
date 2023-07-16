---
layout:     post
title:      刷题Interval Overlapping
subtitle:   
date:       2023-07-17
author:     KX
header-img: img/post-bg-ios9-web.jpg
catalog: 	 true
tags:
    - Algorithm
    - Interval Overlapping
    - Sliding Window
    - Priority Queue
---
# 刷题Interval Overlapping
今天在周赛遇到[2779. Maximum Beauty of an Array After Applying Operation](https://leetcode.com/contest/weekly-contest-354/problems/maximum-beauty-of-an-array-after-applying-operation/), 对于这种在区间上加加减减的题目都没什么思路所以集中做了一些.
(之前遇到过至少三次OA都没做出来ORZ, 今天才弄明白)


## 2779基本思路
这道题考虑数组中的数变换后如果在最终的substring中, max - min < 2k
找到一个宽度为2k的区间, 看它最多能覆盖数组中的多少元素
首先sort数组, 用滑动窗口, 如果右边的数在左边窗口+2k的区间内, 就扩展窗口向右, 如果超出了2k, 左边窗口向右.
因为我们需要最大的subsequent, 所以窗口只扩大不缩小.
```python
class Solution {
public:
    int maximumBeauty(vector<int>& nums, int k) {
        sort(nums.begin(), nums.end());
        int i = 0;
        int j = 0;
        for (i = 0; i < nums.size(); ++i) {
            if (nums[i] - nums[j] > k * 2) {
                ++j;
            }
        }
        return i - j;
    }
};
```
## 差分数组
参考了这篇回答[那些小而美的算法技巧：前缀和/差分数组](https://zhuanlan.zhihu.com/p/301509170)
题目:
[1109. Corporate Flight Bookings](https://leetcode.com/problems/corporate-flight-bookings/)
[370. Range Addition](https://leetcode.com/problems/range-addition/)
这两道题都比较直接, 就不写答案了

[2251. Number of Flowers in Full Bloom](https://leetcode.com/problems/number-of-flowers-in-full-bloom/)
因为加减操作在一个很大的区间上, 所以用Hashmap or Tree而不是Array来存差分数组(应该用ordered map但因为是python直接用了Counter就又把key sort出来存了个数组取数字)


```python
class Solution:
    def fullBloomFlowers(self, flowers: List[List[int]], people: List[int]) -> List[int]:
        n = max(people) + 1
        diff = collections.Counter()
        for start, end in flowers:
            if start < n: diff[start] += 1
            if end + 1 < n: diff[end + 1] -= 1
        keyDate = sorted(diff)
        for i in range(1, len(keyDate)):
            diff[keyDate[i]] += diff[keyDate[i-1]]
        def binarySearch(num):
            # 找到<=当前日期的最大值
            start = 0
            end = len(keyDate) - 1
            largest_smaller = None
            while start <= end:
                mid = (start + end) // 2
                if keyDate[mid] <= num:
                    largest_smaller = keyDate[mid]
                    start = mid + 1
                else:
                    end = mid - 1
            return largest_smaller
        return [diff[binarySearch(i)] for i in people]
```

## Meeting Room
我以前以为meeting room和前面的是一样的解法, 在[start, end] 之间都全部 + 1, 得到房间数(应该也可以有空试试), 最优解法是priority queue
[253. Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)
```python
class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        if not intervals:
            return 0
        free_rooms = []
        intervals.sort(key = lambda x: x[0])
        heapq.heappush(free_rooms, intervals[0][1])
        for i in intervals[1:]:
            if free_rooms[0] <= i[0]:
                heapq.heappop(free_rooms)
            heapq.heappush(free_rooms, i[1])
        return len(free_rooms)
```

有了这个基础之后, 可以试试需要统计每一件Meeting Room用了几次的
[2402. Meeting Rooms III](https://leetcode.com/problems/meeting-rooms-iii/)
这里用heap来存会议室, pop出数字最小的表示用过, 方法可以积累下
我在自己写的时候用数组存了相应的会议室是不是在用, 会超时
```python
class Solution:
    def mostBooked(self, n: int, meetings: List[List[int]]) -> int:
        count = [0 for i in range(n)]
        ready = [r for r in range(n)]
        heapify(ready)
        endTimes = []
        meetings.sort()
        for meeting in meetings:
            hasEmpty = False
            # update the used and endTime heap
            while endTimes and meeting[0] >= endTimes[0][0]:
                end, i = heappop(endTimes)
                heappush(ready, i)
            if ready:
                r = heappop(ready)
                heappush(endTimes, (meeting[1], r))
                count[r] += 1
            # for i in range(n):
            #     if used[i] == False:
            #         nextMeetingRoom = i
            #         used[i] = True
            #         heappush(endTimes, (meeting[1], i))
            #         count[i] += 1
            #         break;
            else:
                endTime, i = heappop(endTimes)
                count[i] += 1
                heappush(endTimes, (endTime - meeting[0] + meeting[1], i))
        ret = 0
        maxCount = 0
        for i in range(len(count)):
            if count[i] > maxCount:
                ret = i
                maxCount = count[i]
        return ret
```

其实都放到一起发现这些也不能算一类题, 不过每个我之前遇到过确实都不会, 又模模糊糊觉得方法应该不难, 就集中做了一下





