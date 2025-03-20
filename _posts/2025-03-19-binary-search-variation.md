---
title: Binary Search Variation
layout: post
category: DSA
tag: Binary Search
---

As we all know, **binary search** could half the search space each time util we find the target in the sorted list. This will result in **O(logn)** time complexity, which is far effecient than the linear search with time complexity **O(n)**.

Now we have one variation problem:
> How to search target from a **rotated sorted** list?
>
> A rotated sorted list is the sorted list by rotating `k` steps (`k >= 0`). <br>
> For example, `[4,5,1,2,3]` is a rotated sorted list. We are able to get `[4,5,1,2,3]` by rotating `2` steps from `[1,2,3,4,5]`.

How to think about this problem? Let's consider a regular binary search problem first. Why we are able to confidently discard half of the list after comparing middle value with target? The reason is that both halves of the list are sorted. If the middle value is greater than the target, it's assured that every value in the right half is greater than the target as well. If the middle value is less than target, it's guaranteed that every value in the left half is also less then the target. Therefore, we can safely discard one half in either case.

> To learn more about Binary Search, take a look at my [“Why we need Binary Search Algorithm”](/posts/binary-search/) post.

However, we may not able to determine the target is in the which half by simply comparing middle value with target for a rotated sorted list. When we split the list by middle value, we will get three components: left half, middle value, right half. It is important to note that there is at least one sorted list in two halves, and another one is either sorted list or rotated sorted list. Since we have at least one sorted half, we can check whether the target in the that half to determine which half should be discarded. 

> For the detailed explanation, please refer to [Approach 3: One Binary Search](https://leetcode.com/problems/search-in-rotated-sorted-array/editorial/).

Let's see how to implement it in Swift:

```swift
func binarySearch(_ nums: [Int], _ target: Int) -> Int {
    var left = 0
    var right = nums.count - 1

    while left <= right {
        let middle = left + (right - left) / 2

        if nums[middle] == target {
            return middle
        }

        if nums[middle] >= nums[left] { // Left half is sorted
            if target >= nums[left] && target < nums[middle] { // Target is in the left half
                right = mid - 1
            } else { // Target is in the right half
                left = mid + 1
            }
        } else { // Right half is sorted
            if target > nums[middle] && target <= nums[right] { // Target is in the right half
                left = mid + 1
            } else { // Target is in the left half
                right = mid - 1
            }
        }
    }

    return -1
}
```