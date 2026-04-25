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

*Proof.* Let $arr = [a_0, a_1, ..., a_{n-1}] \in \mathbb{R}^n$. Assume that for the sake of contradiction, that there exists an algorithm $\mathcal{A}$ that solves the problem in $o(n)$ time. This implies that $\mathcal{A}$ does not examine all $n$ elements in every execution; that is, $\exists i \in [0, n)$ such that $arr[i]$ is not read. Consider an adversarial input where $arr[i]$ is some large positive value $\displaystyle arr[i] \gg \sum_{j=0}^{n-1} |A[j]|$, and all other values in the array are small or negative. The true maximum subarray must include $arr[i]$. However, since $\mathcal{A}$ does not access $arr[i]$, it cannot possibly return the correct maximum sum. Therefore, to guarantee correctness on any arbitrary input, any algorithm must read every entry of $arr$. Reading $n$ elements requires $\Omega(n)$ time. Any deterministic algorithm has a lower bound space complexity of $\Omega(1)$.
