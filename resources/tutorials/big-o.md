---
title: Time Complexity
layout: default
filename: big-o
---

# Time Complexity

Time Complexity is often underrated when it comes to programming. Newbies often think that if their code “works," they should be all set. However, slow code can often degrade performance, waste money, and consume resources inefficiently. Understanding how time complexity works is crucial to the algorithm design process.

---

We want a method to calculate how many operations it takes to run each algorithm, in terms of the input size $n$. Fortunately, this can be done relatively easily using Big O Notation, which expresses worst-case time complexity as a function of $n$ as $n$ gets arbitrarily large. Complexity is an upper bound for the number of steps an algorithm requires as a function of the input size. In Big O notation, we denote the complexity of a function as $f(n)$, where constant factors and lower-order terms are generally omitted from $f(n)$. We’ll see some examples of how this works, as follows. The following code is $O(1)$, because it executes a constant number of operations:

```cpp
int a = 5;
int b = 7;
int c = 4;
int d = a + b + c + 153;
```

Input and output operations are also assumed to be $O(1)$. In the following examples, we assume that the code inside the loops is $O(1)$. The time complexity of loops is the number of iterations that the loop runs. For example, the following code examples are both $O(n)$:

```cpp
for (int i = 1; i <= n; i++)
{
    // constant time code here
}
```

```cpp
int i = 0;

while (i < n)
{
    // constant time code here
    i++;
}
```

Because we ignore constant factors and lower order terms, the following examples are also $O(n)$:

```cpp
for (int i = 1; i <= 5*n + 14; i++)
{
    // constant time code here
}
```

```cpp
for (int i = 1; i <= n + 45433; i++)
{
    // constant time code here
}
```

We can find the time complexity of multiple loops by multiplying together the time complexities of each loop. This example is $O(mn)$, because the outer loop runs $O(n)$ iterations and the inner loop runs $O(m)$ iterations:

```cpp
for (int i = 1; i <= n; i++)
{
    for (int j = 1; j <= m; j++)
    {
        // constant time code here
    }
}
```

In this example, the outer loop runs $O(n)$ iterations, and the inner loop runs anywhere between $1$ and $n$ iterations (which is a maximum of $n$). Since Big O notation calculates worst-case time complexity, we treat the inner loop as a factor of $n$. Thus, this code is $O(n^2)$:

```cpp
for (int i = 1; i <= n; i++)
{
    for (int j = 1; j <= n; j++)
    {
        // constant time code here
    }
}
```

If an algorithm contains multiple blocks, then its time complexity is the worst time complexity out of any block. For example, the following code is $O(n^2)$, even when the second for-loop is $O(n)$:

```cpp
for (int i = 1; i <= n; i++)
{
    for (int j = 1; j <= n; j++)
    {
        // constant time code here
    }
}

for (int i = 1;  i <= n + 453; i++)
{
    // constant time code here
}
```

The following code is $O(n^2 + m)$ because it consists of two blocks of complexity $O(n^2)$ and $O(m)$, and neither of them is a lower order function with respect to the other:

```cpp
for (int i = 1; i <= n; i++)
{
    for (int j = 1; j <= n; j++)
    {
        // constant time code here
    }
}

for (int i = 1;  i <= m; i++)
{
    // constant time code here
}
```

**Constant factor** refers to the idea that different operations with the same complexity take slightly different amounts of time to run. For example, three addition operations take a bit longer than a single addition operation. Another example is that although binary search on an array and insertion into an ordered set are both $O(\log n)$, binary search is noticeably faster. It is entirely ignored in big-O notation. This is fine most of the time, but if the time limit is particularly tight, you may receive time limit exceeded (TLE) with the intended complexity. When this happens, it is important to keep the constant factor in mind. For example, a piece of code that iterates through all ordered triplets runs in $O(n^3)$ time might be sped up by a factor of 6 if we only need to iterate through all *unordered* triplets.

Complexity factors that come from some common algorithms and data structures are as follows:

- Mathematical formulas that just calculate an answer: $O(1)$.
- Binary search: $O(\log n)$.
- Ordered set/map or priority queue: $O(\log n)$ per operation.
- Prime factorization of an integer, or checking primality or compositeness of an integer naively: $O(\sqrt n)$.
- Reading in $n$ items of input: $O(n)$.
- Iterating through all subsets of size $n$ of the input elements: $O(x^n)$.
- Iterating through all subsets: $O(2^n)$.
- Iterating through all permutations: $O(n!)$.
- 
Here are conservative upper bounds on the value of $n$ for each time complexity. You might get away with more than this, but this should allow you to quickly check whether an algorithm is viable:

![](1.png)
