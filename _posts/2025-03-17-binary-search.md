---
title: Why we need Binary Search Algorithm
layout: post
category: DSA
tag: Binary Search
---

Typically, when we try to find an index of item from a list, we can use a linear search like below.

```swift
func search(_ nums: [Int], _ target: Int) -> Int {
    for (index, num) in nums.enumerated() {
        if num == target {
            return index
        }
    }

    return -1
}
```

It's okay for a normal searching. However, if the collection is **sorted**, whether we could have a better solution than a linear search? Yes, we have. It is called **Binary Search**, which is far better than linear search, especially when the data set is large.

### What is Binary Search?

Binary Search is a highly efficient algorithm used to find a target element within a sorted list. Instead of checking every element one by one, it works by repeatedly dividing the search interval in half. 

Here's how it works:
- **Initial setup**: You start with the entire sorted list.
- **Middle comparison**: Check the middle element of the list.
- **Halving the search**:
    - If the middle element equals the target, you've found it.
    - If the middle element is greater than the target, repeat the search on the left half.
    - If the middle element is less than the target, repeat the search on the right half.
- **Iteration**: This process continues until the target is found or the search interval is empty.

```swift
func binarySearch(_ nums: [Int], _ target: Int) -> Int {
    var left = 0
    var right = nums.count - 1

    while left <= right {
        let middle = (left + right) / 2

        if nums[middle] == target {
            return middle
        } else if nums[middle] > target {
            right = middle - 1
        } else {
            left = middle + 1
        }
    }

    return -1
}
```

> To avoid data overflow, you can use `let middle = left + (right - left) / 2` to replace the code at line 6 above.

Binary search will result in a time complexity of **O(log n)**, which is much faster than the **O(n)** time complexity for a linear search.