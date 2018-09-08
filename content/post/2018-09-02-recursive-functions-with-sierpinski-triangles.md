---
title: Recursive functions, with the Sierpinski triangle
author: ~
date: '2018-09-02'
slug: recursive-functions-with-sierpinski-triangles
categories: []
tags: []
---

I'm sure tons of people have done this explanation before, but I wanted to play around with it myself. Just for fun.

The [Sierpinski triangle](https://en.wikipedia.org/wiki/Sierpinski_triangle) is a kind of fractal. Start with a triangle. Divide that triangle into four sub-triangles. Cut out the "middle" subtriangle, and then subdivide each of the other sub-triangles, *ad infinitum*.

Fractals are intimately related to recursion and recursive functions. Given a point, is this point "inside" the triangle, or "cut out"? If it's in the "middle" sub-triangle, we're done: it's been cut out. If it's in one of the other sub-triangles, then you have to ask, for that sub-triangle, is it inside *its* middle sub-sub-triangle? And so on.

```{r}
vertices_to_edges(vertices) {
  lapply(seq_along(vertices), function(i) {
    if (i == length(vertices)) {
      vertices[1] - vertices[i]
    } else {
      vertices[i + 1] - vertices[i]
    }
  })
}

side_of = function(point, edge) {
  as.integer(sign(crossprod(edge, point)))
}

point_inside_triangle(query, vertex_lst) {
  edge_lst = vertices_to_edges(vertext_lst)
  sides = map_int(vector_lst, ~ side_of(query, .))
  sides
}
```

```{r}
subtriangles = function(triangle) {
  midpoints = 
}
```