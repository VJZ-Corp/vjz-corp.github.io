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

It is very difficult for a human to find the area of an irregular polygon like the ones shown above. Therefore, we need a program to help us compute the area of irregular polygons. In fact, this problem often appears in topics related to computer graphics. After all, a visual game is composed of polygons and computer scientists need to find a fast and efficient way of getting the area of shapes so players do not lag when playing intensive games. The C program below demonstrates how we can compute the area of any polygon (regular or irregular) if we know its vertices.

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
