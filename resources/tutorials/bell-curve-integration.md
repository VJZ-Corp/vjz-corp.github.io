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
- 
The probability density function (PDF) is our function of interest as it is the function which results in a bell-shaped curve:

![image](https://user-images.githubusercontent.com/73851560/185766190-a2a1aaba-1441-488c-8714-bbd956a52000.png)

Now, we need to find the antiderivative of the PDF. First, we need to know what even is the PDF. Well here it is:

$$
\displaystyle f(x) = \frac{e^{-1/2(\frac{x - \mu}{\sigma})^2}}{\sigma \sqrt{2\pi}}
$$
