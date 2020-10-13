# Divide and Conquer

$$
\newcommand{\ds}{\displaystyle}
\newcommand{\curlies}[1]{\left\lbrace #1 \right\rbrace}
\newcommand{\abs}[1]{\left\lvert #1 \right\rvert}

\newcommand{\BigO}{\mathcal{O}}
$$

## Divide and Conquer Algorithms

There is no formal definition of what it means for an algorithm to use a divide and conquer strategy, but generally, divide and conquer algorithms will:

- break a problem into multiple similar subproblems (i.e. the same problem needs to be solve but on a smaller input)
- solve each subproblem recursively
- (efficiently) combine solutions of subproblems and take care of any remaining details

### Master Theorem

A useful tool for analyzing divide and conquer algorithms is the Master Theorem, which helps classify functions written as recurrence relations into complexity classes.

If $a, b \geq 1$ are constants, $f: \N \to \R^+$ is a function, and $T$ is defined by the recurrence relation

$$
T(n) = aT(n/b) + f(n)
$$

Then:

- $T \in \Theta(n^{\log_b a})$ if $f \in \BigO(n^{\log_b a - \epsilon})$ for some $\epsilon$
- $T \in \Theta(n^{\log_b a} \log n)$ if $f \in \Theta(n^{\log_b a})$
- $T \in \Theta(f(n))$ if $f \in \Omega(n^{\log_b a + \epsilon})$ for some $\epsilon$ and if $a f(n/b) < cf(n)$ for $c < 1$ and sufficiently large $n$

## Counting Inversions

Given an array $a$ of length $n$, count the number of pairs $(i, j)$ such that $i < j$ but $a[i] > a[j]$.

### Applications

- voting theory
  - to measure the distance between different voters' preferences
- collaborative filtering
  - recommendation algorithms
- measuring "how sorted" an array is
- sensitivity analysis of page rank
- rank aggregation for meta-searching
- nonparametric statistics (Kendall's tau distance)

### Divide and conquer solution

The brute force solution would be to check all $\binom{n}{2}$ pairs, but we can do better!

**Divide**: break array into two equal halves $x$ and $y$

**Conquer**: sort and count inversions in each half recursively

**Combine**:

- count inversions with one entry in $x$ and one entry in $y$
  - runs in $\BigO(n)$ time (supposedly $2n$)

As a result, the runtime $T$ of the algorithm for an input of size $n$ is $T(n) = 2T(n/2) + 2n$, so using the master theorem $T \in \Theta(n \log n)$

## Closest Pair in $\mathbb R^2$

**Divide**: Divide set of points into equal halves by drawing a vertical line $L$

**Conquer**: Solve each half recursively, get $\delta_L$ and $\delta_R$ which are the shortest distances between points in each half

**Combine**:

- check points from solutions of both halves
- let $M$ be the list of points that are within $\delta = \min(\delta_L, \delta_R)$ of $L$
  - sort $M$ by $y$ coordinate
  - because of *geometry*, we only need to compare each point to a certain number of other points in $M$ (usually, we compare each point to the next 7) to check if there is a pair that is less than $\delta$ apart
  - since each point in $M$ only needs to be compared to 7 others, this step takes $\BigO(7\abs{M}) \approx \BigO(n)$ time

## Karatsuba's Algorithm (Integer Multiplication)

This is a fast way to multiply two $n$-digit integers $x$ and $y$ (the number of digits is relevant because computers work in bits)

The brute force algorithm needs $\BigO(n^2)$ operations, which is pretty slow

**Divide**: Divide each integer into two parts

Suppose we are working in base $b$. If $a_1, ..., a_n$ are the digits of $x$, then we define

$$
x_1 = a_1 ... a_{n/2} \text{ and } x_2 = a_{n/2+1} ... a_n
$$

so that $x = x_1 \cdot b^{n/2} + x_2$. We similarly define $y_1$ and $y_2$ so that $y = y_1 \cdot b^{n/2} + y_2$.

**Conquer/Combine:**

With this split, algebraically, we know that

$$
xy = x_1 y_1 \cdot b^n + (x_1 y_2 + x_2 y_1) \cdot b^{n/2} + x_2 y_2
$$

Notice that this requires 4 $n/2$-digit multiplication operations.

However, we can rewrite $x_1 y_2 + x_2 y_1$ as $(𝑥_1 + 𝑥_2)(𝑦_1 + 𝑦_2) − 𝑥_1 𝑦_1− 𝑥_2 𝑦_2$. If we compute $z_1 = x_1 y_1$ and $z_2 = x_2 y_2$ ahead of time, then

$$
xy = z_1 \cdot b^n + ((x_1 + x_2)(y_1 + y_2) - z_1 - z_2) \cdot b^{n/2} + z_2
$$

This expression requires a few more additions, but it only requires 3 $n/2$-digit multiplications!

We can do this recursively for each $x_i y_j$ as well, thus making it a divide and conquer algorithm.

Using the Master theorem, this algorithm runs in $\BigO(n^{\log_2 3})$ time.

### Example: Strassen's Algorithm (Matrix Multiplication)

Uses the concepts of Karatsuba's algorithm to find a fast way to multiply two $n \times n$ matrices

## Median and Selection

The $k$-selection problem is to find the $k^\text{th}$ smallest element in an array $A$ of $n$ comparable elements.

- if $k = 1$ then we are looking for the minimum
- if $k = (n+1)/2$ then we are looking for the median
- if $k = n$ then we are looking for the maximum

We can solve the problem in:

- $\BigO(nk)$ by modifying bubble sort
- $\BigO(n\log(n))$ by sorting
- $\BigO(n + k\log(n))$ by using a min-heap
- $\BigO(k + n\log(k))$ by using a max heap

But we can also do this in $\BigO(n)$ time, since selection is weaker than sorting!

### QuickSelect

Suppose the running time of QuickSelect on the array is $T(n)$

1. Find a pivot $p$
2. Divide $A$ into two subarrays:
   - $A_\text{less}$ has every element $\leq p$
   - $A_\text{more}$ has every element $> p$
   - If $\abs{A_\text{less}} \geq k$, then return the $k^\text{th}$ smallest item in $A_\text{less}$, otherwise return the $(k - \abs{A_{less}})^\text{th}$ smallest in $A_\text{more}$

This algorithm does not immediately help, as it could take $\BigO(n^2)$ time if the pivot does not reduce the size of the problem well. We want to choose a pivot that creates smaller subarrays.

To find a good pivot:

1. Arbitrarily divide $A$ into $n/5$ groups of $5$
   - takes $\BigO(n)$ time
2. Find the median for each of these groups
   - takes constant time for each group, so $\BigO(n)$ time in total
3. Find the $p^*$, the median of the medians
   - takes $T(n/5)$ time
   - $n/10$ medians are $\leq p^*$
   - each median is $\geq$ at least 3 elements (from its group of 5) as well as $\leq$ at least 3 elements
   - thus, $p^*$ is $\geq$ at least $3n/10$ elements, so $3n/10 \leq \abs{A_\text{less}}$ and similarly $3n/10 \leq \abs{A_\text{more}}$
   - since $A_\text{less}$ and $A_\text{more}$ are complements, they both have size at most $7n/10$
4. Use $p^*$ as the pivot and recurse
   - takes $T(7n/10)$ time, since both subarrays have size at most $7n/10$

Thus, this algorithm takes $\BigO(n) + T(n/5) + T(7n/10)$ time

By Master Theorem, this is $\BigO(n)$. This is the best running time we can hope for to solve the $k$-selection problem, since we need at least $\BigO(n)$ time to read the array!

## Finding the best algorithm for a problem

- Matrix multiplication has been improving very incrementally:
  - 1969 (Strassen): $\BigO(n^{2.807})$
  - 1990: $\BigO(n^{2.376})$
  - 2013: $\BigO(n^{2.3729})$
  - 2014: $\BigO(n^{2.3728639})$

