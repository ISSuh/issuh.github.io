---
title: HackerRandk - Fraudulent Activity Notifications
date: 2023-12-22 18:19:00 +0900
categories: [Problem Solving, HackerRandk]
tags:
  - PS
  - HackerRandk
  - Interview Preparation Kit
  - Sort
---

### [ Fraudulent Activity Notifications ](https://www.hackerrank.com/challenges/fraudulent-activity-notifications/problem?h_l=interview&isFullScreen=true&playlist_slugs%5B%5D%5B%5D=interview-preparation-kit&playlist_slugs%5B%5D%5B%5D=sorting) - Medium

#### 문제

HackerLand National Bank has a simple policy for warning clients about possible fraudulent account activity. If the amount spent by a client on a particular day is greater than or equal to the client's median spending for a trailing number of days, they send the client a notification about potential fraud. The bank doesn't send the client any notifications until they have at least that trailing number of prior days' transaction data.

Given the number of trailing days and a client's total daily expenditures for a period of days, determine the number of times the client will receive a notification over all days.

#### 입출력 예

expenditure = [10, 20, 30, 40, 50]
d = 3

On the first three days, they just collect spending data. At day , trailing expenditures are . The median is and the day's expenditure is . Because , there will be a notice. The next day, trailing expenditures are and the expenditures are . This is less than so no notice will be sent. Over the period, there was one notice sent.

Note: The median of a list of numbers can be found by first sorting the numbers ascending. If there is an odd number of values, the middle one is picked. If there is an even number of values, the median is then defined to be the average of the two middle values. (Wikipedia)

**Function Description**
Complete the function activityNotifications in the editor below.

activityNotifications has the following parameter(s):

- int expenditure[n]: daily expenditures
- int d: the lookback days for median spending

**Returns**

- int: the number of notices sent

#### 풀이

- Sort

```cpp
int activityNotifications(vector<int> expenditure, int d) {
    int count = 0;
    vector<int> sub(expenditure.begin(), expenditure.begin() + d);
    std::sort(sub.begin(), sub.end());

    for (auto iter = expenditure.begin() + d ; iter != expenditure.end() ; ++iter) {
        double median = 0.0;
        if (d % 2 == 0) {
            int index1 = d / 2;
            int index2 = (d / 2) - 1;
            median = (*(sub.begin() + index1) + *(sub.begin() + index2)) / 2.0;
        } else {
            int index = d / 2;
            median = *(sub.begin() + index);
        }

        if (*iter >= (median * 2.0)) {
            ++count;
        }

        sub.insert(
            upper_bound(sub.begin(), sub.end(), *iter),
            *iter);

        sub.erase(
            lower_bound(sub.begin(), sub.end(), *(iter - d)));
    }
    return count;
}
```
