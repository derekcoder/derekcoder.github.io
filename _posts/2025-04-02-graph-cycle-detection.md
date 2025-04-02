---
title: Graph Cycle Detection
layout: post
category: DSA
tag: Graph
---

**Graph** is a data structure that contains a collection of nodes (vertices) connected by edges, used to represent relationships and connections between objects.

![Graph](/assets/posts/graph.png)

Today I will talk about one of the classic problem about the Graph: **how to detect the cycle in graph**.

Let's use a leetcode question [207. Course Schedule](https://leetcode.com/problems/course-schedule/) as an example to see how to solve such a problem.

> **207. Course Schedule**
> 
> There are a total of `numCourses` courses you have to take, labeled from `0` to `numCourses - 1`. You are given an array `prerequisites` where `prerequisites[i] = [ai, bi]` indicates that you must take course `bi` first if you want to take course `ai`.
> 
> For example, the pair `[0, 1]`, indicates that to take course `0` you have to first take course `1`. Return `true` if you can finish all courses. Otherwise, return `false`.

```swift
func canFinish(_ numCourses: Int, _ prerequisites: [[Int]]) -> Bool {
    var graph: [Int: [Int]] = [:]
    for prerequisite in prerequisites {
        let a = prerequisite[0]
        let b = prerequisite[1]
        graph[b, default: []].append(a)
    }

    // 0: unvisited, 1: visiting, 2: visited
    var states = [Int](repeating: 0, count: numCourses)

    // Return whether can finish for course `node`
    func dfs(_ node: Int) -> Bool {
        if states[node] == 1 {
            return false
        }

        if states[node] == 2 {
            return true
        }

        states[node] = 1

        let neighbors = graph[node] ?? []
        for neighbor in neighbors {
            if !dfs(neighbor) {
                return false
            }
        }

        states[node] = 2
        return true
    }

    for course in 0..<numCourses {
        if !dfs(course) {
            return false
        }
    }

    return true
}
```

- In line 2, we created a dictionary (hashmap) `graph` to represent the data.
- In line 3-7, we iterated over the `prerequisites` to build the graph.
- In line 10, we defined a `states` array to store the node's state.
     - 0 for unvisited
     - 1 for visiting
     - 2 for visited
- In line 13-33, we used a nested function called `dfs` (dfs stands for "depth-first search") to check whether we can finish the courses starting from a specific course `node`.
    - In line 14-16: If the course `node` is visiting, we can return `false` immediately due to cycle detected.
    - In line 18-20: If the course `node` is visited, it means no cycle for this course `node`.
    - In line 22: We marked the current course `node` as visiting.
    - In line 24-29: We recursively visited all the courses depend on current course `node`.
    - In line 31: We marked the current course `node` as visited since we can finish courses starting from it.
- In line 35-39: We checked each course. If any `dfs` call returns `false` (cycle detected), return `false`. Otherwise, return `true`.

Let's write some tests to verify the solution:

```swift
let tests = [
    (3, [[1, 0], [2, 1]]),
    (3, [[1, 0], [2, 1], [0, 2]]),
]

for test in tests {
    let numCourses = test.0
    let prerequisites = test.1
    let result = canFinish(numCourses, prerequisites)

    print("canFinish(\(numCourses), \(prerequisites)) == \(result)")
}

// 0 -> 1 -> 2 => true
// canFinish(3, [[1, 0], [2, 1]]) == true
// 0 -> 1 -> 2 -> 0 => false
// canFinish(3, [[1, 0], [2, 1], [0, 2]]) == false
```

## References

- [207. Course Schedule](https://leetcode.com/problems/course-schedule)
- [Graphs - DFS](https://leetcode.com/explore/featured/card/leetcodes-interview-crash-course-data-structures-and-algorithms/707/traversals-trees-graphs/4626/)