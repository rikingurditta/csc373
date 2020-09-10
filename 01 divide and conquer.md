# Divide and Conquer

$$
\newcommand{BigO}{\mathcal{O}}
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

- $T \in \Theta(n^{\log_b a})$ if $f \in \mathcal O(n^{\log_b a - \epsilon})$ for some $\epsilon$
- $T \in \Theta(n^{\log_b a} \log n)$ if $f \in \Theta(n^{\log_b a})$
- $T \in \Theta(f(n))$ if $f \in \Omega(n^{\log_b a + \epsilon})$ for some $\epsilon$ and if $a f(n/b) < cf(n)$ for $c < 1$ and sufficiently large $n$

## Example: Counting Inversions

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

**Conquer**: count inversions in each half recursively

**Combine**:

- count inversions with one entry in $x$ and one entry in $y$
  - [...]

As a result, the runtime $T$ of the algorithm for an input of size $n$ is $T(n) = 2T(n/2) + 2n$, so using the master theorem $T \in \Theta(n \log n)$

## Example: Closest Pair in $\mathbb R^2$

**Divide**: Divide set of points into equal halves by drawing a vertical line $L$

**Conquer**: Solve each half recursively, get $\delta_L$ and $\delta_R$ which are the shortest distances between points in each half

**Combine**:

- check points from solutions of both halves
- take points that are within $\min(\delta_L, \delta_R)$ of $L$
  - sort these points by $y$ coordinate
  - compare them pairwise

## Example: Karatsuba's Algorithm (Integer Multiplication)

This is a fast way to multiply two $n$-digit integers $x$ and $y$ (the number of digits is relevant because computers work in bits)

The brute force algorithm needs $O(n^2)$ operations, which is pretty slow

**Divide**: Divide each integer into two parts

Suppose we are working in base $b$. If $a_1, ..., a_n$ are the digits of $x$, then we define

$$
x_1 = a_1 ... a_{n/2} \text{ and } x_2 = a_{n/2+1} ... a_n
$$

so that $x = x_1 \cdot b^{n/2} + x_2$. We similarly define $y_1$ and $y_2$ so that $y = y_1 \cdot b^{n/2} + y_2$.

**Conquer:**



## Example: Strassen's Algorithm (Matrix Multiplication)

Uses the concepts of Karatsuba's algorithm to find a fast way to multiply two $n \times n$ matrices