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
\displaystyle \Huge f(x) = \frac{e^{-\frac{1}{2}(\frac{x - \mu}{\sigma})^2}}{\sigma \sqrt{2\pi}}
$$

When plotted out, this function will take the shape of a bell (see figure above). At first, it may seem impossible to integrate this, but remember: $\sigma$ and $\mu$ are constants, as the standard deviation and mean of any dataset are just numbers. This makes it possible to integrate now:

$$
\displaystyle \Huge \int \frac{e^{-\frac{1}{2}(\frac{x - \mu}{\sigma})^2}}{\sigma \sqrt{2\pi}} \text{ }dx
$$

## Part I: Finding the Antiderivative
Before we start to integrate the PDF, we need to modify a few things about the integral. First, let us extract the constants in the denominator to the outside of the integral:

$$
\begin{aligned}
\displaystyle \Huge \int \frac{e^{-\frac{1}{2}(\frac{x - \mu}{\sigma})^2}}{\sigma \sqrt{2\pi}} \text{ }dx &= \Huge \frac{1}{\sigma \sqrt{2\pi}} \int e^{-\frac{1}{2}(\frac{x - \mu}{\sigma})^2} \text{ }dx \\
&= \displaystyle \Huge \frac{1}{\sigma \sqrt{2\pi}} \int e^{-\frac{(x - \mu)^2}{2 \sigma^2}} \text{ }dx
\end{aligned}
$$

Notice how I multiplied out the exponent of the integrand for the sake of convenience. Those who are experienced with different types of integrals may already see it, but for everyone else, choosing a distinct value for u-substitution is the most crucial step in solving this problem:

$$
\displaystyle \Huge u = \frac{x - \mu}{\sigma \sqrt 2}
$$

Why this value? Because if we square this u-value, we get our original exponent:

$$
\displaystyle \Huge u^2 = \big(\frac{x - \mu}{\sigma \sqrt 2}\big)^2 = \frac{(x - \mu)^2}{2 \sigma^2}
$$

So following through with finding a suitable $dx$ in terms of $du$ (remember that $𝜇$ is constant when taking the derivative):

$$
\displaystyle \Huge u = \frac{x - \mu}{\sigma \sqrt 2}
$$

$$
\implies \displaystyle \Huge \frac{du}{dx} = \frac{1}{\sigma \sqrt 2}
$$

$$
\implies \displaystyle \Huge dx = \sigma \sqrt 2 \text{ }du
$$

We get the following new integral with $u$ substituted in:

$$
\displaystyle \Huge \int e^{-u^2} \sigma \sqrt 2 \text{ } du
$$

Follow closely, as this is the most seemingly arbitrary but important step many rookies fail to see:
