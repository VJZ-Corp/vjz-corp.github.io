---
title: Recursive Generation of Binomial Coefficients in Linear Time
layout: default
filename: binom-coeff-linear-gen.md
---

# Recursive Generation of Binomial Coefficients in Linear Time
Binomial coefficients are the positive integers that occur as coefficients in the binomial theorem. More formally, given the binomial expansion

$$
\displaystyle (x + y)^n = \sum_{k=0}^n {n \choose k} x^{n-k}y^k = \sum_{k=0}^n {n \choose k} x^ky^{n-k},
$$

the binomial coefficients for such expansion are

$$
{n \choose k} = \frac{n!}{k!(n-k)!}.
$$

These coefficients also correspond to the $n$th row of Pascal's Triangle, more formally defined as 

$$
{n \choose k}_{k=0}^n = {n \choose 0}, {n \choose 1}, ..., {n \choose k-1}, {n \choose k}.
$$

For example, the third row of Pascal's Triangle has values of 

$$
{3 \choose k}_{k=0}^3 = {3 \choose 0}, {3 \choose 1}, {3 \choose 2}, {3 \choose 3} = \{1, 3, 3, 1\}.
$$

Consequently, the third-degree binomial expansion is

$$
(x+y)^3 = x^3 + 3xy^2 + 3x^2y + y^3.
$$



## Recursive Formulation
We can find a recurrence relation for binomial coefficients by looking at the $k-1$ term:

$$
\begin{align*}
    {n \choose k - 1} &= \frac{n!}{(k-1)!(n-(k-1))!} \\
    &=\frac{n!}{(k-1)!(n-k+1)!}
\end{align*}
$$

Observe how we can multiply the following term to get the original $k$th term back:

$$
\begin{align*}
    {n \choose k - 1} \frac{n-k+1}{k} &= \frac{n!}{(k-1)!(n-k+1)!} \frac{n-k+1}{k} \\
    &=\frac{n!}{k(k-1)!} \frac{n-k+1}{(n-k+1)!} \\
    &=\frac{n!}{k(k-1)!} \frac{n-k+1}{(n-k+1)(n-k)!} \\
    &=\frac{n!}{k(k-1)!(n-k)!} \\
     &=\frac{n!}{k!(n-k)!} \\
      &={n \choose k}
\end{align*}
$$

Let $f(k-1)$ be the special term we multiplied above. Therefore, $f(k-1)$ can be modified into the following:

$$
f(k-1) = \frac{n-k+1}{k} = \frac{n-(k-1)}{(k-1)+1} 
$$

Now let us find $f(k)$:

$$
f(k) = \frac{n-k}{k+1}
$$

If the statement $\displaystyle {n \choose k-1}f(k-1) = {n \choose k}$ is true, then the statement $\displaystyle {n \choose k}f(k) = {n \choose k+1}$ also holds true:

$$
\begin{align*}
    {n \choose k} \frac{n-k}{k + 1} &= \frac{n!}{k!(n-k)!} \frac{n-k}{k + 1} \\
    &= \frac{n!}{k!(k+1)} \frac{n-k}{(n-k-1)!(n-k)} \\
    &= \frac{n!}{k!(k+1)(n-(k+1))!} \\
    &= \frac{n!}{(k+1)!(n-(k+1))!} \\
    &= {n \choose k+1}
\end{align*}
$$

In general, if we multiply the $k$th term by $f(k)$, we will always get the $k+1$ term. Now that we have the formula for finding the $k+1$ term, we can add a base case to complete the recursive algorithm:

$$
   \begin{cases} 
      \displaystyle {n \choose 0} = 1 \\
      \displaystyle {n \choose k+1} = {n \choose k}\frac{n-k}{k + 1}
   \end{cases}
$$

## Linear Time Optimization
Here is a standard $O(n)$ implementation of the factorial function:
```py
def factorial(n):
    if n == 0:
        return 1
    else:
        return n * factorial(n - 1)
```

It can be used to calculate $\displaystyle {n \choose k}$. However, this approach is very inefficient.  We can see why by rewriting the formula of $\displaystyle {n \choose k}$ to better understand its time complexity:

$$
{n \choose k} = \frac{n!}{k!(n-k)!} = \frac{n(n-1)(n-2)\dots(n-(k-1))}{k!}.
$$

We can say that $\displaystyle n(n-1)(n-2)\dots(n-(k-1)) = \prod_{i=1}^k (n-i+1)$ runs in $O(k)$ time. Coupled with the denominator $k!$, which takes $O(k)$ time to run, this brings the total time to $O(2k)$. The ultimate goal is to generate $n+1$ binomial coefficients for a binomial $(x+y)^n$ with each coefficient calculation taking $O(2k)$ time to run. As $k$ goes from $0$ to $n$ consecutively, we observe that the average value of $\displaystyle k = \frac{n}{2}.$ In other words, the sequence $k_{k=0}^n$ becomes $\displaystyle \frac{n}{2}_{k=0}^n = \frac{n}{2}, \dots, \frac{n}{2}$ for the sake of calculating time complexity. Therefore, the final time complexity is $\displaystyle O(2k(n+1)) = O(n^2 + n) \propto O(n^2)$ or quadratic time. We can reduce the total time complexity down to $O(n)$ through memoization. The premise of recursive memoization is that we do not have to calculate the factorial function each time as we can cache the previous term in a data structure thus reducing the time complexity down to $O(n)$. Applying memoization, the recursive sequence now becomes:

$$
   \begin{cases} 
      \Phi_0 = 1 \\
      \displaystyle \Phi_{k+1} = \Phi_k\frac{n-k}{k + 1}
   \end{cases}
$$

Now, we can go through the steps to find binomial coefficients in $O(n)$ time:

1. Assuming indices start at 0, define an array $\Phi$ of size $n+1$.
2. Set $\Phi_0 = 1$.
3. Define loop starting from $k=0$ and ending at $k=n - 1$.
4. Set $\displaystyle \Phi_{k+1} = \Phi_k\frac{n-k}{k+1}$.
5. When the loop is finished, the array $\Phi$ contains all the binomial coefficients for the $n$th degree expansion.

The above algorithm is listed below written using Python:

```py
def binom_coeff(n):
    phi = [1]    # 𝚽[0] = 1
    for k in range(n):
        phi.append(phi[k] * (n - k) // (k + 1))
    return phi
```

Only one loop is used throughout the entire algorithm; every other operation such as array index lookup and multiplication can be assumed to be $O(1)$. On most popular programming languages, the code for this algorithm is very concise as well, averaging around 10 lines at most. Through recursive memoization, we can now expand the binomial $(x + y)^n$ in $O(n)$ time.
