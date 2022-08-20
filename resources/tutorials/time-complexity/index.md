---
title: Time Complexity
layout: default
filename:
---

# Time Complexity

Time Complexity is often underrated when it comes to programming. Newbies often think that if their code “works”, they should be all set. However, slow code can often degrade performance, waste money, and consume resources inefficiently. Understanding how Time Complexity works is crucial to the algorithm design process.

---

We want a method to calculate how many operations it takes to run each algorithm, in terms of the input size $n$. Fortunately, this can be done relatively easily using Big O Notation, which expresses worst-case time complexity as a function of $n$ as $n$ gets arbitrarily large. Complexity is an upper bound for the number of steps an algorithm requires as a function of the input size. In Big O notation, we denote the complexity of a function as $f(n)$, where constant factors and lower-order terms are generally omitted from $f(n)$. We’ll see some examples of how this works, as follows. The following code is $O(1)$, because it executes a constant number of operations.

```cpp
int a = 5;
int b = 7;
int c = 4;
int d = a + b + c + 153;
```

Input and output operations are also assumed to be $O(1)$. In the following examples, we assume that the code inside the loops is $O(1)$. The time complexity of loops is the number of iterations that the loop runs. For example, the following code examples are both $O(n)$.

```cpp
for (int i = 1; i <=n; i++)
{
    // constant time code here
}
