---
title: Computing the Area of Irregular Polygons
layout: default
filename: irregular-polygons-area.md
---

# Computing the Area of Irregular Polygons
A polygon is defined as a two-dimensional figure made up of line segments connected to form a closed chain. There are two types of polygons: regular and irregular polygons. In introductory geometry, you were probably taught about regular polygons. The image below shows some examples of regular polygons.

![image](https://github.com/VJZ-Corp/vjz-corp.github.io/assets/73851560/85f50e29-8d4a-47b4-9228-40a1c6c66a7b)

As you could imagine, calculating the area of a regular polygon is very easy. However, how can we find the area of these irregular polygons?

![image](https://github.com/VJZ-Corp/vjz-corp.github.io/assets/73851560/15fdc522-ddd4-4143-bbbd-ee84cf78fdbf)

It is very difficult for a human to find the area of irregular polygons like the ones shown above. Therefore, we need a program to help us compute the area of irregular polygons. In fact, this problem often appears in topics related to computer graphics. After all, a visual game is composed of polygons and computer scientists need to find a fast and efficient way of getting the area of shapes so players do not lag when playing intensive games. The C program below demonstrates how we can compute the area of any polygon (regular or irregular) if we know its vertices.

```c
#include <stdio.h>
#include <math.h>

struct Vertex
{
    float x, y;
};

float det(float a, float b, float c, float d)
{
    return a * d - b * c;
}

float area(struct Vertex* vertices, int n)
{
    float result = 0; 
    for (int i = 1; i < n; i++)
        result += det(vertices[i - 1].x, vertices[i - 1].y, vertices[i].x, vertices[i].y);                   
    result += det(vertices[n - 1].x, vertices[n - 1].y, vertices[0].x, vertices[0].y);
    return fabs(result) / 2;
}

int main(void) 
{
    // edit the array with your own vertices in cartesian coordinates
    struct Vertex vertices[] = { 
        {-7,4},
        {5,2},
        {9,-3},
        {1,8},
        {-6,0}
    };
    
    float result = area(vertices, sizeof(vertices) / sizeof(vertices[0]));
    printf("The area of your polygon is %f", result);
}
```

## How It Works
Essentially, the C code provided is computing the area using the following formula:

$$
A = \frac{1}{2} \bigg(\begin{vmatrix}
x_{n-1} & y_{n-1} \\ 
x_0 & y_0 
\end{vmatrix} + \sum_{i = 1}^{n-1} \begin{vmatrix}
x_{i-1} & y_{i-1} \\ 
x_i & y_i 
\end{vmatrix} \bigg).
$$

In the equation above, $A$ is the area of the polygon, $(x_i, y_i)$ is the cartesian coordinate of the $i$-th vertex, and $n$ is the number of vertices. *The formula starts indexing at 0 to stay consistent with computer science conventions.*  In the next section, we will show you why this formula works for any regular or irregular polygon.

#### **At this point, you should have some understanding of vector calculus before proceeding.** 

Let us start by looking at the most simple polygon: a triangle. Consider an arbitrarily defined triangle below:

![Untitled drawing (3)](https://github.com/VJZ-Corp/vjz-corp.github.io/assets/73851560/845c0d40-2bc4-4c11-ac2b-98a96c9b8c14)

We can use a double integral to find the area of triangle $T$:

$$
A = \iint\limits_{T} dA.
$$

Since polygons (including this triangle) are made of line segments, we can relate a double integral to a line integral by using Green's theorem. The double integral now becomes:

$$
A = \iint\limits_{T} 1\, dA = \iint\limits_{T} \bigg(\frac{\partial Q}{\partial x} - \frac{\partial P}{\partial y}\bigg) \, dA.
$$

Let us define the vector field $\vec{F} = \langle -y, x \rangle$. Then, $\displaystyle \frac{\partial Q}{\partial x} - \frac{\partial P}{\partial y} = 2$. However, if you look at the equation for Green's theorem above, you will see that the integrand should be 1 and not 2. This is not a problem, since we can just rearrange some terms to get our desired result (note the one-half being present):

$$
A = \iint\limits_{T} 1\, dA = \frac{1}{2} \oint_{\partial T} \vec{F} \cdot d\vec{r}.
$$

In the equation above, we define $\vec{r} = \langle x, y \rangle$. Therefore, the closed integral becomes:

$$
\begin{align*}
\oint_{\partial T} \vec{F} \cdot d\vec{r} &= \oint_{\partial T} \langle -y, x \rangle \cdot d\langle x,y \rangle \\
&= \oint_{\partial T} \langle -y, x \rangle \cdot \langle dx,dy \rangle \\ 
&= \oint_{\partial T} (x \,dy - y \, dx).
\end{align*}
$$

The notation $\partial T$ denotes the boundary of region $T$. In this case, $T$ is a triangle so the boundary of $T$ is made of three connected line segments:

$$
\oint_{\partial T} (x \,dy - y \, dx) = \sum_{i=0}^2 \int_{C_i} (x \,dy - y \, dx).
$$

Notice how I introduced the summation notation. This is important because it is setting us up for the general formula of a polygon with $n$ vertices, which will have $n$ connected line segments. Since computers start indexing at zero, the summation goes up to $n-1$. Keep that in mind as we continue. For now, let us go back to the triangle example:

$$
\sum_{i=0}^2 \int_{C_i} (x \,dy - y \, dx) = \int_{C_0} (x \,dy - y \, dx) + \int_{C_1} (x \,dy - y \, dx) + \int_{C_2} (x \,dy - y \, dx).
$$

We need to now parameterize the curves $C_0, C_1$, and $C_2$ for $0 \leq t \leq 1$:

$$
C_0 : x = t(x_1 - x_0) + x_0, \, y = t(y_1-y_0) + y_0 
$$

$$
C_1 : x = t(x_2 - x_1) + x_1, \, y = t(y_2-y_1) + y_1 
$$

$$
C_2 : x = t(x_0 - x_2) + x_2, \, y = t(y_0-y_2) + y_2
$$

Now we take these parameterizations and subsitute them back into the line integrals (we only have to do one line integral to get the point across):

$$
\begin{align*}
\int_{C_0} (x \,dy - y \, dx) &= \int_0^1 x \frac{dy}{dt} \, dt - \int_0^1 y \frac{dx}{dt} \, dt \\
&= \int_0^1 (t(x_1 - x_0) + x_0)(y_1 - y_0) \, dt - \int_0^1 (t(y_1-y_0) + y_0)(x_1-x_0) \, dt \\
&= \int_0^1 (t(x_1 - x_0) + x_0)(y_1 - y_0) - (t(y_1-y_0) + y_0)(x_1-x_0) \, dt \\
&= \int_0^1 (-y_0 t(x_1 - x_0) + y_1 t(x_1 - x_0) + x_0 t(y_1 - y_0) - x_1 t(y_1 - y_0) - x_1 y_0 + x_0 y_1) \, dt \\
&= \int_0^1 (x_0y_1 - x_1y_0) \,dt \\
&= x_0y_1 - x_1y_0 \\
&= \begin{vmatrix}
x_0 & y_0\\ 
x_1 & y_1 
\end{vmatrix}.
\end{align*}
$$
