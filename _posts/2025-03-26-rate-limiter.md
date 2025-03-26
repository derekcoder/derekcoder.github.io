---
title: "Rate Limiting Algorithms: Fixed Window"
layout: post
category: DSA
tag: Rate Limiting
---

Rate limiting is a mechanism used to control the **number of requests** that a client can perform in a **given period of time**. Its primary goals are to protect systems from overload, prevent abuse (such as brute-force attacks or API misuse).

Here are some common rate limiting algorithms:

- Fixed Window
- Sliding Window
- Token Bucket
- Leaky Bucket

In this post, we will look at the **"Fixed Window"** algorithm. 

## Fixed Window Algorithm

The **Fixed Window** algorithm is rate limiting technique that divides time into fixed intervals (e.g., one minute or one second). A counter will track the number of requests during each interval, and further requests will be dropped after the counter reaches the maximum limitation.

![CardioBot](/assets/posts/fixed_window.webp)

The simple implementation is as follows:
```swift
import Foundation

class FixedWindowRateLimiter {
    let limit: Int  // Number of requests allowed within "windowSize" seconds
    let windowSize: Int  // Window (Interval) size in seconds
    private var currentWindow: Int? = nil
    private var currentCount: Int = 0 // Number of requests received in current window

    init(limit: Int, windowSize: Int) {
        self.limit = limit
        self.windowSize = windowSize
    }

    func allowRequest(_ timestamp: TimeInterval) -> Bool {
        let timestampInt = Int(timestamp)
        let window = timestampInt - timestampInt % windowSize

        if currentWindow != window {
            currentWindow = window
            currentCount = 0
        }

        if currentCount < limit {
            currentCount += 1
            return true
        } else {
            return false
        }
    }
}

let timestamps = [1.1, 1.5, 1.7, 1.8, 1.9, 2.0, 2.2]
let limiter = FixedWindowRateLimiter(limit: 3, windowSize: 2)
for timestamp in timestamps {
    let result = limiter.allowRequest(timestamp)
    print("allowRequest(\(timestamp)) == \(result)")
}

// allowRequest(1.1) == true
// allowRequest(1.5) == true
// allowRequest(1.7) == true
// allowRequest(1.8) == false
// allowRequest(1.9) == false
// allowRequest(2.0) == true
// allowRequest(2.2) == true
```

## Pros and Cons

- Pros
    - **Simplicity**: Easy to implement because it only requires counting within fixed intervals.

- Cons
    - **Burstiness at Boundaries**: A client could make many requests at the very end of one window and immediately at the beginning of the next window, potentially doubling the allowed rate for a short period.
    - **Doesn't distribute load evenly**: It does not distribute the load evenly across the entire peroid; it only checks per window.

## References

- [Rate Limiter — Fixed Window Counter](https://medium.com/@avocadi/rate-limiter-fixed-window-counter-2d80fb992d47)