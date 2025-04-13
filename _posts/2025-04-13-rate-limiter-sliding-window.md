---
title: "Rate Limiting Algorithms: Sliding Window"
layout: post
category: DSA
tag: Rate Limiting
---

Rate limiting is a mechanism used to control the **number of requests** that a client can perform in a **given period of time**. Its primary goals are to protect systems from overload, prevent abuse (such as brute-force attacks or API misuse).

Here are some common rate limiting algorithms:

- [Fixed Window](/posts/rate-limiter-fixed-window)
- **Sliding Window**
- Token Bucket
- Leaky Bucket

In this post, we will look at the **"Sliding Window"** algorithm. 

## Sliding Window Algorithm

The **Sliding Window** algorithm is rate limiting technique that continuously tracks the number of requests over a moving time interval rather than relying on fixed, discrete time slots.

![Sliding Window](/assets/posts/sliding_window.png)

The simple implementation is as follows:
```swift
import Foundation

class SlidingWindowRateLimiter {
    let limit: Int
    let windowSize: Int

    private var requestTimestamps: [TimeInterval] = []  // Logs each allowed request timestamp

    init(limit: Int, windowSize: Int) {
        self.limit = limit
        self.windowSize = windowSize
    }

    func allowRequest(_ timestamp: TimeInterval) -> Bool {
        // Remove old timestamps that are out of the sliding window.
        // Keep only those timestamps that are within (timestamp - windowSize, timestamp]
        while let first = requestTimestamps.first, first <= timestamp - TimeInterval(windowSize) {
            requestTimestamps.removeFirst()
        }

        // Check if the number of requests in the sliding window is less than the limit.
        let currentCount = requestTimestamps.count

        if currentCount < limit {
            requestTimestamps.append(timestamp)
            return true
        } else {
            return false
        }
    }
}

let timestamps = [1.1, 1.5, 1.7, 1.8, 1.9, 3.0, 3.1]
let limiter = SlidingWindowRateLimiter(limit: 3, windowSize: 2)
for timestamp in timestamps {
    let result = limiter.allowRequest(timestamp)
    print("allowRequest(\(timestamp)) == \(result)")
}

// allowRequest(1.1) == true
// allowRequest(1.5) == true
// allowRequest(1.7) == true
// allowRequest(1.8) == false
// allowRequest(1.9) == false
// allowRequest(3.0) == false
// allowRequest(3.1) == true
```

## Pros and Cons

- Pros
    - Provides very precise rate limiting.
    - Avoids the burstiness that can occur at the boundaries of fixed windows.

- Cons
    - Can be memory intensive if there are many requests because it stores each individual timestamp.