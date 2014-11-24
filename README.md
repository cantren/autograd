# FunkyYak (Functional Kayak)

Taking a few lessons from developing and working with Kayak, here is a
stateless reverse-mode autodiff implementation that also offers
higher-order derivatives.

Example use:

```python
import numpy as np
import matplotlib.pyplot as plt
from funkyyak import kyapply, grad # These are the only two functions exposed.
k = kyapply # kyapply(fun, *args) applies `fun` to `*args`. `k` is shorthand.

# Define a function capable of taking `Node` objects
def fun(x):
    return k(np.sin, x)

d_fun = grad(fun)    # First derivative
dd_fun = grad(d_fun) # Second derivative

x = np.linspace(-10, 10, 100)
plt.plot(x, map(fun, x), x, map(d_fun, x), x, map(dd_fun, x))
```
<img src="https://github.com/HIPS/FunkyYak/blob/master/examples/sinusoid.png" width="600">

The function can even have control flow, which raises the prospect
of differentiating through an iterative routine like an
optimization. Here's a simple example.

```python
# Taylor approximation to sin function
def fun(x):
    currterm = x
    ans = currterm
    for i in xrange(1000):
        currterm = - currterm * x ** 2 / ((2 * i + 3) * (2 * i + 2))
        ans = ans + currterm
        if k(np.abs, currterm) < 0.2: break # (Very generous tolerance!)

    return ans

d_fun = grad(fun)
dd_fun = grad(d_fun)

x = np.linspace(-10, 10, 100)
plt.plot(x, map(fun, x), x, map(d_fun, x), x, map(dd_fun, x))
```

<img src="https://github.com/HIPS/FunkyYak/blob/master/examples/sinusoid_taylor.png" width="600">
