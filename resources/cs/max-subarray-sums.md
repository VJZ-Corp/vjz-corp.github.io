---
title: Optimizing the Computation of Maximum Subarray Sums
layout: default
filename: max-subarray-sums.md
---

# Optimizing the Computation of Maximum Subarray Sums
The maximum sum subarray problem aims to find the subarray with the largest sum. Formally, the objective is to find

$$
\max_{0 \leq i \leq j < n} \sum_{x=i}^j arr[x],
$$

where $arr[x] \in \mathbb{R}$ is the $x$th element of array $arr$ and $n \in \mathbb{N}$ is the total size of the array.

**Theorem.** Any algorithm that can solve the maximum sum subarray problem has a lower bound time complexity of $\Omega(n)$ and space complexity of $\Omega(1)$.

*Proof.* Let $arr = [a_0, a_1, ..., a_{n-1}] \in \mathbb{R}^n$. Assume that for the sake of contradiction, that there exists an algorithm $\mathcal{A}$ that solves the problem in $o(n)$ time. This implies that $\mathcal{A}$ does not examine all $n$ elements in every execution; that is, $\exists i \in [0, n)$ such that $arr[i]$ is not read. Consider an adversarial input where $arr[i]$ is some large positive value $\displaystyle arr[i] \gg \sum_{j=0}^{n-1} \mid A[j] \mid$, and all other values in the array are small or negative. The true maximum subarray must include $arr[i]$. However, since $\mathcal{A}$ does not access $arr[i]$, it cannot possibly return the correct maximum sum. Therefore, to guarantee correctness on any arbitrary input, any algorithm must read every entry of $arr$. Reading $n$ elements requires $\Omega(n)$ time. Any deterministic algorithm has a lower bound space complexity of $\Omega(1)$.

## Naive Brute Force
```py
def naive_brute_force(arr):
    n = len(arr)
    maxSum = float('-inf')
    for i in range(n):
        for j in range(i, n):
            currentSum = 0
            for k in range(i, j + 1):
                currentSum += arr[k]

            if currentSum > maxSum:
                maxSum = currentSum
    return maxSum
```

The most straightforward approach is to iterate through every subarray possible. For each subarray, collect its sum and find the maximum. Needless to say, this algorithm is very inefficient and has a time complexity of $\Theta(n^3)$.

## Divide and Conquer
The divide-and-conquer approach recursively partitions the array into two halves, solving the maximum subarray problem independently on the left and right segments. After solving the subproblems, the algorithm computes the maximum subarray sum that crosses the midpoint by examining the largest suffix sum of the left half and the largest prefix sum of the right half.

```py
def MaxSubarray(arr, low, high):
    if low == high:
        return arr[low]
    mid = (low + high) // 2
    leftSum = MaxSubarray(arr, low, mid)
    rightSum = MaxSubarray(arr, mid + 1, high)
    crossSum = MaxCrossingSum(arr, low, mid, high)
    return max(leftSum, rightSum, crossSum)


def MaxCrossingSum(arr, low, mid, high):
    leftSum = float('-inf')
    sum = 0
    for i in range(mid, low - 1, -1):
        sum += arr[i]
        leftSum = max(leftSum, sum)
    rightSum = float('-inf')
    sum = 0
    for j in range(mid + 1, high + 1):
        sum += arr[j]
        rightSum = max(rightSum, sum)
    return leftSum + rightSum
```

This approach has a recurrence relation of $T(n) = 2T(\frac{n}{2}) + O(n)$. This simplifies to an overall time complexity of $O(n\log n)$.
