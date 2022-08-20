---
title: Integrating the Bell Curve
layout: default
filename: bell-curve-integration.md
---

# Integrating the Bell Curve

The bell curve — or more formally: the normal (Gaussian) distribution — is one of the most important models in statistics. Given a mean and standard deviation, the bell curve can tell you all sorts of useful information about the probabilities and distribution of data. This article, however, is not statistically oriented but instead will demonstrate the techniques to find the integral of the bell curve, which assumes a undergraduate-level understanding of calculus.

***

When looking at the normal distribution, there are 2 mathematical functions we need to consider:

- Probability density function (PDF)
- Cumulative distribution function (CDF)

The probability density function (PDF) is our function of interest as it is the function which results in a bell-shaped curve:

![image](https://user-images.githubusercontent.com/73851560/185766190-a2a1aaba-1441-488c-8714-bbd956a52000.png)

Now, we need to find the antiderivative of the PDF. First, we need to know what even is the PDF. Well here it is:

$$
\large \displaystyle \large f(x) = \frac{e^{-\frac{1}{2}(\frac{x - \mu}{\sigma})^2}}{\sigma \sqrt{2\pi}}
$$

When plotted out, this function will take the shape of a bell (see figure above). At first, it may seem impossible to integrate this, but remember: $\sigma$ and $\mu$ are constants, as the standard deviation and mean of any dataset are just numbers. This makes it possible to integrate now:

$$
\large \displaystyle \large \int \frac{e^{-\frac{1}{2}(\frac{x - \mu}{\sigma})^2}}{\sigma \sqrt{2\pi}} \text{ }dx
$$

## Part I: Finding the Antiderivative
Before we start to integrate the PDF, we need to modify a few things about the integral. First, let us extract the constants in the denominator to the outside of the integral:

$$
\begin{aligned}
& \displaystyle \large \int \frac{e^{-\frac{1}{2}(\frac{x - \mu}{\sigma})^2}}{\sigma \sqrt{2\pi}} \text{ }dx \\
\large &= \large \frac{1}{\sigma \sqrt{2\pi}} \int e^{-\frac{1}{2}(\frac{x - \mu}{\sigma})^2} \text{ }dx \\
\large &= \displaystyle \large \frac{1}{\sigma \sqrt{2\pi}} \int e^{-\frac{(x - \mu)^2}{2 \sigma^2}} \text{ }dx
\end{aligned}
$$

Notice how I multiplied out the exponent of the integrand for the sake of convenience. Those who are experienced with different types of integrals may already see it, but for everyone else, choosing a distinct value for u-substitution is the most crucial step in solving this problem:

$$
\large \displaystyle \large u = \frac{x - \mu}{\sigma \sqrt 2}
$$

Why this value? Because if we square this u-value, we get our original exponent:

$$
\large \displaystyle \large u^2 = \big(\frac{x - \mu}{\sigma \sqrt 2}\big)^2 = \frac{(x - \mu)^2}{2 \sigma^2}
$$

So following through with finding a suitable $dx$ in terms of $du$ (remember that $𝜇$ is constant when taking the derivative):

$$
\displaystyle \large u = \frac{x - \mu}{\sigma \sqrt 2}
$$

$$
\implies \displaystyle \large \frac{du}{dx} = \frac{1}{\sigma \sqrt 2}
$$

$$
\implies \displaystyle \large dx = \sigma \sqrt 2 \text{ }du
$$

We get the following new integral with $u$ substituted in:

$$
\displaystyle \large \int e^{-u^2} \sigma \sqrt 2 \text{ } du
$$

Follow closely, as this is a seemingly arbitrary but important step many fail to see:

$$
\displaystyle \large \int e^{-u^2} \sigma \sqrt 2 \text{ } du = \int \frac{\sqrt \pi}{\sqrt \pi} e^{-u^2} \sigma \sqrt 2 \text{ } du
$$

First, remember that multiplying 1 to the integrand does nothing to it. But, why do we even multiply 1 in the first place? And why is 1 represented as the square root of $\pi$ divided by itself? To answer those questions, let me present you the Gaussian error function:

$$
\displaystyle \large \text{erf}(z) = \frac{2}{\sqrt\pi} \int_0^z e^{-t^2} \text{ } dt
$$

The error function is non-elementary, meaning it cannot be represented with a finite amount of logarithms, exponents, polynomials, or trigonomic functions. While that is a bummer, we can use a specialized $\text{erf}(x)$ notation to represent the error function. With that out of the way, is the pattern apparent? We are trying to get our integral in the form of the error function! Continuing with our integration, we are going to pull out the constants once again except for the denominator of the integrand and $\sqrt 2$:

$$
\begin{aligned}
& \displaystyle \large \int e^{-u^2} \sigma \sqrt 2 \text{ } du \\
\large &= \large \int \frac{\sqrt \pi}{\sqrt \pi} e^{-u^2} \sigma \sqrt 2 \text{ } du \\
\large &= \large \sigma\sqrt \pi \int \frac{e^{-u^2} \sqrt2}{\sqrt \pi} \text{ } du
\end{aligned}
$$

Now, we need to irrationalize the denominator in order to fit it into the form of the Gaussian error function:

$$
\begin{aligned}
\large & \displaystyle \large \int e^{-u^2} \sigma \sqrt 2 \text{ } du \\
\large &= \large \int \frac{\sqrt \pi}{\sqrt \pi} e^{-u^2} \sigma \sqrt 2 \text{ } du \\
\large &= \large \sigma\sqrt \pi \int \frac{e^{-u^2} \sqrt2}{\sqrt \pi} \text{ } du \\
\large &= \large \frac{\sigma \sqrt \pi}{\sqrt2} \int \frac{2e^{-u^2}}{\sqrt \pi} \text{ } du
\end{aligned}
$$

Now, we extract the constants from the integrand once again:

$$
\begin{aligned}
& \displaystyle \large \int e^{-u^2} \sigma \sqrt 2 \text{ } du \\
\large &= \large \int \frac{\sqrt \pi}{\sqrt \pi} e^{-u^2} \sigma \sqrt 2 \text{ } du \\
\large &= \large \sigma\sqrt \pi \int \frac{e^{-u^2} \sqrt2}{\sqrt \pi} \text{ } du \\
\large &= \large \frac{\sigma \sqrt \pi}{\sqrt2} \int \frac{2e^{-u^2}}{\sqrt \pi} \text{ } du \\
\large &= \large \frac{\sigma \sqrt \pi}{\sqrt2} \big( \frac{2}{\sqrt \pi} \int e^{-u^2} \text{ } du \big) \\
\large &= \large \frac{\sigma \sqrt \pi}{\sqrt2} \text{erf}(u)
\end{aligned}
$$

The term inside the parenthesis is the error function we talked about earlier. Finally, we can substitute the value for $u$ back into the expression. We cannot forget about the term that we dropped earlier for neatness, we have to add that back. Multiplying through, many constants cancel out, thus leaving with the final answer of:

$$
\begin{aligned}
& \displaystyle \large \frac{1}{\sigma \sqrt2 \sqrt\pi} \frac{\sigma\sqrt\pi}{\sqrt2} \text{erf}\big(\frac{x-\mu}{\sigma\sqrt2}\big) \\
\large &= \large \frac{1}{\sqrt2\sqrt2} \text{erf}\big(\frac{x-\mu}{\sigma\sqrt2}\big) \\
\large &= \large \frac{1}{2} \text{erf}\big(\frac{x-\mu}{\sigma\sqrt2}\big) + C
\end{aligned}
$$

# Part II: Definite Integrals
Finding the antiderivative of the PDF was pretty rough. Luckily computing the definite integral of the PDF will be relatively easier, as we have already done 90% of the work. Recall the Fundamental Theorem of Calculus, and apply that to our indefinite form, then integrate that expression from $a$ to $b$:

$$
 \large \displaystyle \left. \frac{1}{2} \text{erf}\big(\frac{x-\mu}{\sigma\sqrt2}\big) \right\vert_{a}^{b} = \large \frac{1}{2} \bigg[\text{erf}\big(\frac{b-\mu}{\sigma\sqrt2}\big) - \text{erf}\big(\frac{a-\mu}{\sigma\sqrt2}\big) \bigg]
$$

Unfortunately, the error function is a integral itself (see above), which is not very useful in computing a meaningful value. I will finish with the series expansion for the error function in one final gigantic monstrous equation for finding the definite integral of the bell curve (just plug in the constants and it should work):

$$
\displaystyle \large \int_a^b \frac{e^{-\frac{1}{2}(\frac{x - \mu}{\sigma})^2}}{\sigma \sqrt{2\pi}} \text{ }dx = \frac{1}{\sqrt\pi} \bigg[ \sum_{n=0}^\infty \bigg(\frac{\frac{b-\mu}{\sigma\sqrt2}}{2n+1} \prod_{k=1}^n \frac{-\big(\frac{b-\mu}{\sigma\sqrt2}\big)^2}{k}\bigg) - \sum_{n=0}^\infty \bigg(\frac{\frac{a-\mu}{\sigma\sqrt2}}{2n+1} \prod_{k=1}^n \frac{-\big(\frac{a-\mu}{\sigma\sqrt2}\big)^2}{k}\bigg) \bigg]
$$
