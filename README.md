
Interesting puzzles I come across

***

You toss a fair coin 400 times. What’s the probability that you get at least 220 heads?
Round your answer to the nearest per cent.

We are dealing with a binomial distribution: n = 400, k = 1...220, p = 0.5

    f(k, n, p) = (n choose k) p^k (1-p)^(n-k) = ( n! / k! (n-k)! ) p^k (1-p)^(n-k)

    mean = n p

    variance = n p (1-p)

### Approximation

As n gets large, the binomial distribution approach the normal distribution.
We have:

    mean     = n p = 400 0.5 = 200
    variance = n p (1-p) = 400 0.5 0.5 = 100
    std dev  = sqrt(100) = 10

    k - mean = 220 - 200 = 20 = 2 * std dev

from standard normal cdf table, we see that 2 std dev from mean ~= 0.47725,
by symmetry P(X>=220) = 1 - 0.5 - 0.47725 = 0.02275

### Exact answer

Factorials get untractably large with not-so-large n (smaller than 400)
therefore we need an indirect (yet correct) strategy to compute the binomial
distribution. One such way is multiplicative:

    n choose k = product from i = 1 to k of (n+1-i) / i

we can then plug in computed binomial coefficient n choose k in exact binomial pdf

```python

import functools

def binomial_coefficient(n, r):
    r = min(r, n - r)
    if r == 0: return 1
    numer = functools.reduce(operator.mul, range(n, n - r, -1))
    denom = functools.reduce(operator.mul, range(1, r + 1))
    return numer / denom

def binomial_coefficient(n, k):
    result = 1.0
    for i in range(k):
        result *= (n-i) / (i+1)
    return int(result)

def binomial_pdf(n, k, p):
   return binomial_coefficient(n, k) * p**k * (1-p)**(n-k)

def binomial_cdf(n, k, p):
    k = n-k if 2*k>n else k # by symmetry
    result = 0
    for i in range(k+1):
        result += binomial_pdf(n, i, p)
    return result

print(binomial_cdf(400, 220, 0.5))
# 0.025520

```

***

