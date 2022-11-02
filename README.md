# sum-of-polynomials

## Motivation

Let's say that, for any reason whatsoever, you're interested in finding the sum:

```math
\sum_{0}^{n-1} p(x)
```

where $p(x)$ is some polynomial of degree $m$. 

You could evaluate $p(x)$ for every integer value of $x$ in this range, but that would take $O(n)$ time and take very long for large values of $n$.

But what if you could find a new polynomial:

```math
    q(n) = a_{m + 1}' n^{m + 1} + a_m' n^{m} + ... + a_1' n + a_0'
```

such that: 

```math
    q(n) = \sum_{0}^{n-1} p(x)
```

for every integer $n$ and polynomial $p$?

## Solution


It turns out there is a $O(m^2)$ solution to this problem, and it's based on using [falling factorials][1]:

```math
{\displaystyle {\begin{aligned}(x)_{n}=x^{\underline {n}}&=\overbrace {x(x-1)(x-2)\cdots (x-n+1)} ^{n{\text{ factors}}}\\&=\prod _{k=1}^{n}(x-k+1)=\prod _{k=0}^{n-1}(x-k)\,.\end{aligned}}}
```

[which behave similar to integrals when summed][2]:

```math
\begin{equation}{
\sum_{x = 0}^{n-1} x^{\underline{k}} = \frac{1}{k + 1} n^{\underline{k + 1}}
}\end{equation}
\tag{1}
```


The next thing to do is to figure out is how to turn this polynomial:

```math
p(x) = a_m x^m + a_{m - 1} x^{m - 1} + ... + a_1 x + a_0
```


Into its' representation as a factorial polynomial:

```math
ff(x) = b_m x^{\underline{n}} + b_{m - 1} x^{\underline{m - 1}} + ... + b_1 x^{\underline{1}} + b_0 x^{\underline{0}}
```

and to be able to do the _reverse transformation_ as well, because in the end we want to get something like $q(n)$. 

This can be done by noting that every **falling factorial** has its' **polynomial representation** (_notice that we're going in reverse here!_).

```math
\begin{align}
{\displaystyle {\begin{array}{rll}(x)_{0}&&=1\\(x)_{1}&&=x\\(x)_{2}&=x(x-1)&=x^{2}-x\\(x)_{3}&=x(x-1)(x-2)&=x^{3}-3x^{2}+2x\\(x)_{4}&=x(x-1)(x-2)(x-3)&=x^{4}-6x^{3}+11x^{2}-6x\end{array}}}
\end{align}
\tag{2}
```

The right hand sides of these equations sort of form a matrix, if you squint a bit.

So by finding the matrix of coefficients $S$ which correspond to the polynomial expansion of each falling factorial up to $x^{\underline{m}}$ and then, most importantly, by finding its' inverse $S^{-1}$, we can do this:

```math
  \begin{align}
    \begin{bmatrix}
           b_{0} \\
           b_{1} \\
           \vdots \\
           b_{m} \\
           b_{m + 1}
    \end{bmatrix} &= S^{-1}(m + 1)
        \begin{bmatrix}
           a_{0} \\
           a_{1} \\
           \vdots \\
           a_{m} \\
           0
    \end{bmatrix}
  \end{align}
  \tag{3}
```

We added a zero to the bottom of the polynomial vector and took $S(m + 1)$ because all exponents will be shifted up later (try to see why from $(1)$ ).

The only thing left now is to figure out how these coefficients in the expansions $(2)$ of $x^{\underline{m}}$ are made. They are [Stirling numbers of the first kind][3], which are in fact defined as the coefficients in the polynomial expansion of falling factorials (no surprise there):

```math
(x)_m = \sum_{k=0}^m s(m,k) x^k
```

and $s(n, k)$ can be solved using a recurrence relation:

```math
{\displaystyle {
\begin{aligned}
    s(0, 0) &= 1 \\
    s(\cdot,0) &= 0 \\
    s(0, \cdot) &= 0 \\
    s(m,k) &= -m\cdot s(m,k)+s(m,k-1) \\
\end{aligned}
}} 
```

Finally, we have $S$:

```math 
S(m + 1) = \begin{bmatrix} 
    s(0, 0) & \dots  & s(0, m + 1) \\
    \vdots & \ddots & \vdots \\
    s(m + 1, 0) & \dots  & s(m + 1, m + 1) 
    \end{bmatrix} ^ \top
```

And we can get $q(n)$ by applying the transformation inverse to $(3)$:

```math
  \begin{align}
    \begin{bmatrix}
           a_{0}' \\
           a_{1}' \\
           \vdots \\
           a_{m + 1}'
    \end{bmatrix} &= S(m + 1)
        \begin{bmatrix}
           b_{0} \\
           b_{1} \\
           \vdots \\
           b_{m+1}
    \end{bmatrix}
  \end{align}
```

## Code

Everything can be found in `poly_to_poly.ipynb` (take a look at the code, you can pass in polynomials as strings in a certain format and get a polynomial back).

**It also outputs coefficients as fractions!** (proven to be useful). 


## Acknowledgements

All credit goes to [Peter Košinár](https://math.stackexchange.com/users/77812/peter-ko%c5%a1in%c3%a1r), who made [this beautiful post on Math Stack Exchange][1]. 

Most of the equations are blatantly copied over from Wikipedia.


## References
  - [https://en.wikipedia.org/wiki/Falling_and_rising_factorials][1]
  - [https://math.stackexchange.com/questions/654765/sum-of-polynomial][2]
  - [https://en.wikipedia.org/wiki/Stirling_numbers_of_the_first_kind][3]


[1]: https://en.wikipedia.org/wiki/Falling_and_rising_factorials
[2]: https://math.stackexchange.com/questions/654765/sum-of-polynomial
[3]: https://en.wikipedia.org/wiki/Stirling_numbers_of_the_first_kind