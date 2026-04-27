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
\bigg\{{n \choose k}\bigg\}_{k=0}^n = \bigg\{{n \choose 0}, {n \choose 1}, ..., {n \choose k-1}, {n \choose k}\bigg\}.
$$

For example, the third row of Pascal's Triangle has values of 

$$
\bigg\{{3 \choose k}\bigg\}_{k=0}^3 = \bigg\{{3 \choose 0}, {3 \choose 1}, {3 \choose 2}, {3 \choose 3}\bigg\} = \{1, 3, 3, 1\}.
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
    &=\frac{n!}{k(k-1)!} \frac{\cancel{n-k+1}}{\cancel{(n-k+1)}(n-k)!} \\
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
    &= \frac{n!}{k!(k+1)} \frac{\cancel{n-k}}{(n-k-1)!\cancel{(n-k)}} \\
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
