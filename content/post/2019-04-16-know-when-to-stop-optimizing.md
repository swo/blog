---
title: Know when to stop optimizing
author: ~
date: '2019-04-16'
slug: know-when-to-stop-optimizing
categories: []
tags: []
---

I'm a millenial, so my friends and I think a lot about optimizing our lives. But I think a common mistake made when optimizing is assuming that it's possible to optimize to arbitrary precision. In fact, because the world is messy, we can never get things perfect. This concept can be expressed mathematically in *stochastic optimization*.

To a mathematician, optimization means finding the maximum value `$f_\mathrm{max}$` (or the position of that maximum `$x_\mathrm{max}$` of some function `$f(x)$`:
`$$
\begin{aligned}
f_\mathrm{max} &= \max_x f \\
x_\mathrm{max} &= \mathrm{argmax}_x f
\end{aligned}
$$`
So, for example, if `$f(x) = -x ^ 2$`, then you do the thing we learned in calculus: take a derivative `$f'(x) = -2x$`, solve for the root `$f'(x) = 0$`, and find that `$x_\mathrm{max} = 0$`. Although this is an algorithm in the sense that its a process that has steps, finding the maximum in this way requires that you have the true form of the function `$-x^2$` in front of you, and that you can manipulate it with calculus.

To an engineer, optimization means finding the maximum value by querying some black box. If I want to design a car engine that is more fuel efficient, I can maybe make some mathematical model of how engine cylinder size or whatever will relate to efficiency, but at some point I just have to build an engine and see how it works. As another example, if you want your egg to be boiled to your ideal level of softness or hardness, you have to boil eggs for different lengths of time and decide what you like.

This leads to optimization algorithms. One of the simplest is [gradient descent](https://en.wikipedia.org/wiki/Gradient_descent), which means that you get to the top of the "hill" by seeing which way is up and how steep the slope is, and taking a step in the "up" direction, with a step size proportion to the steepness (more steep means bigger step):
`$$
x_{n+1} = x_n + \gamma \, f'(x_n)
$$`
You start at `$x_0$`, look at the derivative `$f'(x_0)$`, and take a step (getting to `$x_1$`), with `$\gamma$` as a constant scaling factor relating the derivative to the step size.

You can re-write the algorithm to query only the function `$f$` and computing the steepness with an approximate derivative:
`$$
x_{n+1} = x_n + \gamma \left[ \frac{f(x_n + \Delta) - f(x_n - \Delta)}{2 \Delta} \right],
$$`
where `$\Delta$` is how wide a space you use when determining the approximate slope. This algorithm depends only on `$f$`, so you don't ever need to know what that function actually *is*, you just need to know its value when you ask for it. So, with the egg example, you don't need to know the relationship between egg deliciousness and number of minutes boiled, you just need to have a procedure that allows you to boil an egg for a specific number of minutes and then evaluate its deliciousness.

Say `$f(x) = -x^2$` and `$x_0 = -1$`. (I also set `$\gamma = 0.1$` and `$\Delta = 10^{-6}$`.) Then the optimization, run to 100 steps, looks like this, with a smooth arc from `$x_0 = -1$` toward `$x_\mathrm{max} = 0$`:
```{r}
# Given current position, where do you go next?
next_x <- function(x, f, gamma, delta) {
  x + gamma * (f(x + delta) - f(x - delta)) / (2 * delta)
}

# Objective function to be maximized
obj <- function(x) -x ** 2

# This two-argument function works well with purrr's accumulate function
f2 <- function(x, dummy) next_x(x, obj, gamma = 0.1, delta = 1e-6)

# Accumulate takes the previous value and plugs it in
# again, keeping the trace of values
accumulate(rep(-1, 100), f2) %>%
  tibble(x = ., step_number = 1:length(.)) %>%
  ggplot(aes(step_number, x)) +
  geom_line()
```

This makes optimization look like a breeze. But in the real world, there are two critical problems. First, you can't make a perfect measurement. If I cook an egg for 7.0 minutes, ask you to taste it, then cook another egg for 7.0 minutes, and ask you to taste that, you won't give the exact same deliciousness rating each time. In other words, even though there might be a real function `$f$` that you're trying to optimize, the measurements you get are `$f$` but with some noise on them. (Hopefully the measurements you do make are "centered around" the true value in some sense. Mathematically we say you are trying to optimize `$f(x)$` but you're sampling `$g(x)$`, with the hope that `$\mathbb{E}\left[ g(x) \right] = f(x)$`.)

Second, you can't control your steps perfectly. I can't actually cook an egg for 7.00000 minutes; there will be some error in how long I cook it for. In mathematical terms, this means that you might want to adjust `$x_n$` by some amount `$x_{n+1} - x_n$`, but you'll end up tacking some noise onto that adjustment.

Of course, these two sources of error could interact with one another, but I'll keep it simple and address them one at a time.

In the first case, I'll say that I measure `$f$` but with some Gaussian noise on it. Things can go really wrong if the scale of the noise (i.e., the standard deviation `$\sigma$` of the Gaussian distribution) is large compared to the width `$\Delta$` we use to compute the slope.
```{r}
messy_plot <- function(sd, delta) {
  # The objective function now has some noise in it
  messy_obj <- function(x) -x ** 2 + rnorm(n = 1, sd = sd)

  # This two-argument function works well with purrr's accumulate function
  messy_f2 <- function(x, dummy) next_x(x, messy_obj, gamma = 0.1, delta = delta)

  plot <- accumulate(rep(-0.1, 100), messy_f2) %>%
    tibble(x = ., step_number = 1:length(.)) %>%
    ggplot(aes(step_number, x)) +
    geom_line()
    
  plot
}

messy_plot(0.1, 0.01)
```
In this case, we just wander all over the place. The reason is pretty intuitive: if I'm looking at a narrow range `$\Delta$`, but I have a large noise `$\sigma > \Delta$`, then I extrapolate that I need really big changes, based on noise alone. In my life-metaphor, this is the person who *thinks* they can know their own preferences to arbitrary precision, then makes tiny adjustments to their life, notices large changes in how much they like their life, and decides that the tiny adjustment was the cause of that large change.

The solution here is to ensure that `$\sigma \ll \Delta$`:
```{r}
messy_plot(0.01, 0.1)
```
Note, though, that we get to the *region* of `$x_\mathrm{max} = 0$`, but we never get exactly there. It turns out you can overcome this problem with the [Kiefer-Wolfowitz algorithm](https://en.wikipedia.org/wiki/Stochastic_approximation#Kiefer-Wolfowitz_algorithm), which basically says that you need to decrease the size of `$\gamma$` and `$\Delta$` with each step. Then you do eventually approach the true value, zero, even if you have error in your measurements.

But then we run into the second big problem, which is one of control. It sounds nice to reduce `$\gamma$` and `$\Delta$` to arbitrarily small values, but at some point this is impossible, because we simply can't control our system that well. So now imagine that, when we want to measure `$f(x)$`, we actually measure `$f(x + \varepsilon)$`, where `$\varepsilon$` is some Gaussian noise:
```{r}
messy_next_x <- function(x, f, gamma, delta, sd) {
  x + gamma * (f(x + delta) - f(x - delta)) / (2 * delta) + rnorm(n = 1, sd = sd)
}

obj <- function(x) -x ** 2
messy_f2 <- function(x, dummy) messy_next_x(x, obj, gamma = 0.1, delta = 0.1, sd = 0.01)

plot <- accumulate(rep(-0.1, 100), messy_f2) %>%
  tibble(x = ., step_number = 1:length(.)) %>%
  ggplot(aes(step_number, x)) +
  geom_line()
  
plot
```
Again, we can get to the region of `$x_\mathrm{max} = 0$`, but we don't converge. In this case, the Kiefer-Wolfowitz algorithm can't save us, because we can't perfectly adjust our state.

The analogy in this second case is someone who expects that they can perfectly control their life, that they have the means to select the optimal job, or the optimal partner, or whatever, with no error.

As mentioned above, these two types of error can interact. Probably more important, though, is the fact that the "true" objective function `$f(x)$` is not static. People's preferences change through time, so optimization is fruitless unless you can make changes quickly with respect to the timescale at which the objective function changes.

<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/latest.js?config=TeX-MML-AM_CHTML' async></script>