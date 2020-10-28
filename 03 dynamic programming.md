# Dynamic Programming

$$
\newcommand{\ds}{\displaystyle}
\newcommand{\curlies}[1]{\left\lbrace #1 \right\rbrace}
\newcommand{\abs}[1]{\left\lvert #1 \right\rvert}

\newcommand{\BigO}{\mathcal{O}}
$$

## Dynamic Programming

**Dynamic programming** is breaking a problem down into simpler subproblems, solving the subproblems, and storing their solutions. Then if we need to solve the same subproblem again later, we can do it quickly because we have stored its solution.

### Top-down vs bottom-up

Top-down dynamic programming is solving subproblems recursively, and storing their solutions when we come across them. This is also called memoization, since we are storing a "memo" for every solution we have already computed.

A top down solution is better when:

- not every sub-solution needs to be computed for every input
  - the algorithm will recurse and only get to the subproblems it needs to solve
- it's difficult to find the right order to solve the subproblems

Bottom-up dynamic programming is solving subproblems in a particular "best order" so that when a later problem needs a subproblem to be solved, it has already been computed. Thus we want to order the subproblems by how they depend on each other.

A bottom-up solution is better when:

- all subproblems need to be solved no matter what
- we want to prevend unnecessary memory accesses/recursion overhead
- the order and dependencies of the subproblems allow us to free up memory early by getting rid of previous subproblem solutions from memory

This can allow for faster real-life implementations that require less memory.

### Optimal substructure property

Dynamic programming applies well to problems that have the **optimal substructure property** - optimal solutions to a problem can be calculated easily if we have optimal solutions to subproblems.

Divide and conquer algorithms also use this property. However, in dynamic programming, subproblems can overlap, so we cannot divide them and then quickly conquer them individually. I.e., in dynamic programming, multiple subproblems may need to use the solution to the same subproblem. In divide and conquer, one subproblem's subproblems are disjoint from another's.

## Weighted Interval Scheduling

Suppose as in previous problems we have a set $J$ of $n$ jobs, each job $j \in J$ starting at $s_j$ and finishing at $f_j$. In this problem, each job also has a weight $w_j$. Our goal is to find a maximum-weight schedule of mutually-compatible jobs.

Any greedy approach, i.e. a greedy scheduling algorithm based on any ordering, does not work.

### Abstract solution

Suppose the jobs are sorted by finish time, $f_1 \leq ... \leq f_n$.

For each job $j \in J$, let $p[j]$ be the largest index $i < j$ so that jobs $i$ and $j$ are compatible, i.e. $f_i \leq s_j$. Note that because of the way $J$ is sorted, this means that jobs $1, ..., i$ are compatible with $j$ and jobs $i+1, ..., j-1$ are not.

Suppose $OPT$ is an optimal solution.

Suppose job $k$ is in $OPT$, then incompatible jobs are not, so $p[k] + 1, ..., k-1 \notin OPT$, so some optimal subset of $\curlies{1, ..., p[k]}$ is in $OPT$. If job $k$ isn't in $OPT$, then we need to look for an optimal subset of $\curlies{1, ..., k-1}$. Notice that in both of these situations, we need to solve the problem for a prefix of our ordering.

Suppose $OPT(j)$ is the maximum total weight of compatible jobs from $\curlies{1, ..., j} \subseteq J$, so it's the weight of the solution for the prefix up to $j$. Then, rewriting our logic above,

$$
OPT(j) = \begin{cases}
0 & \text{if } j = 0 \\
\max\curlies{OPT(j-1), w_j + OPT(p[j])} & \text{if } j > 0
\end{cases}
$$

This is the **Bellman equation** for the problem.

### Brute force algorithm

We can implement an algorithm that solves the Bellman equation directly:

```python
def brute_force_opt(J):
    sort jobs by finish time
    for j in J:
        compute p[j] using binary search
    return compute_opt(n)

def compute_opt(j):
    if j == 0:
        return 0
    else:
        return max(compute_opt(j-1),
                   w(j) + compute_opt(p[j]))
```

In the worst case, this algorithm takes exponential time! This is because it does a lot of redundant computations.

### Top-down dynamic programming algorithm

We will speed up the brute force algorithm using memoization.

```python
M = []
def top_down_opt(J):
    sort jobs by finish time
    for j in J:
        compute p[j] using binary search
    M[0] = 0
    return M_compute_opt(n)

def M_compute_opt(j):
    if M[j] not initialized:
        M[j] = max(M_compute_opt(j-1),
                   w(j) + M_compute_opt(p[j]))
    return M[j]
```

Notice that we set `M[j]` as we go, so `M_compute_opt` is only called once for each $j$. Thus, `M_compute_opt(n)` takes $\BigO(n)$ time, so the entire algorithm takes $\BigO(n\log(n))$ time (because of the sorting and searching).

### Bottom-up dynamic programming algorithm

A bottom-up algorithm finds the order in which to solve the subproblems so that they are ready when needed, rather than solving them whenever they are discovered and memoizing them.

```python
def bottom_up_opt(J):
    sort jobs by finish time
    for j in J:
        compute p[j] using binary search
    M[0] = 0
    for j in J:
        M[j] = max(M[j-1], w(j) + M[p[j]])
        # j-1, p[j] < j so they have already been computed
```

### Augmenting to find the optimal solution

The algorithms above present the optimal value, i.e. the maximum total weight of the schedule. However, to find the schedule itself, we just need to augment our algorithm to return the subsets it chooses as well, according to the following recursive equation for the solution $S(j)$ for $1, ..., j$

$$
S(j) = \begin{cases}
\emptyset & \text{if } j = 0 \\
S(j - 1) & \text{if } j > 0 \text{ and } OPT(j - 1) \geq w_j + OPT(p[j]) \\
\curlies{j} \cup S(p[j]) & \text{if } j > 0 \text{ and } OPT(j - 1) < w_j + OPT(p[j])
\end{cases}
$$

We can precompute which decision to make ($S(j) = S(j - 1)$ vs $S(j) = \curlies j \cup S(p[k])$) while we are calculating $OPT(j)$, and then make those decisions later when we are finding the actual solution, in order to save memory during the computation of $S(j)$.

## Knapsack Problem

Suppose we have items $I = \curlies{1, ..., n}$ with values $v_i > 0$ and weights $w_i > 0$. We have a knapsack with weight capacity $W$. Assume that each $v_i, w_i$ and $W$ are all integers. We want to find a subset of $I$ that fits in the knapsack (has total weight at most $W$) that has the greatest total value.

### Weight-based dynamic programming approach

Suppose $OPT(i, W)$ is the maximum value we can pack with a knapsack of capacity $W$ and using only the items $1, ..., i$. Then the maximum value for the entire problem is $OPT(n, W)$, so we want to compute this.

Consider an item $i$. if $w_i > w$, then we cannot pack it, so our solution must be $OPT(i-1, w)$. If $w_i \leq w$, then $i$ may or may not be chosen. If it is chosen, then the best value is $v_i + OPT(i-1, w - w_i)$, since we include it then make the best packing with the rest of our space. If we do not choose it, then once again we consider the preceeding items, so the best value is $OPT(i-1, w_i)$. This creates the Bellman equation:

$$
OPT(i, W) = \begin{cases}
0 & \text{if } i = 0 \\
OPT(i-1, W) & \text{if } w_i > W \\
\max\curlies{OPT(i-1, W), v_i + OPT(i-1, W-w_i)} & \text{if } w_i \leq W
\end{cases}
$$

Since we assumed $W$ is an integer, we know that $OPT$ might be called with $1, ..., n$ in its first argument and $1, ..., W$ in its second argument. This means that a dynamic programming algorithm based on our Bellman equation is bounded at $\BigO(nW)$ time. This is pseudo-polynomial time, but if $W$ is bounded to be a polynomial function of $n$, then it is polynomial time.

### Value-based dynamic programming approach

Suppose $OPT(i, v)$ is the minimum capacity needed to pack a total value of at least $v$ using only the items $1, ..., i$. Then, we can rephrase our old goal (find $OPT(n, W)$) as wanting to compute

$$
\max\curlies{v \in \Z: OPT(i, v) \leq W}
$$

i.e. the greatest value we can get while staying within the weight constraint.

For a given $V$, we want to minimize $OPT(i, v)$. If $v \leq 0$, then we do not need any capacity to pack it. If $v > 0$ but $i = 0$, then we cannot pack $v$, so we will say our answer is $\infty$. Otherwise, either $i$ is in the minimum-weight $v$ packing, in which case, the weight is $w_i + OPT(i-1, v-v_i)$ since we want to minimize packing the rest of the value. If $i$ is not in the minimum weight packing, then we want to pack the same value with the preceeding items, so the minimum weight is $OPT(i-1, v)$. Then we can write the Bellman equation:

$$
OPT(i, v) = \begin{cases}
0 & \text{if } v \leq 0 \\
\infty & \text{if } i = 0, v \geq 0 \\
\min\curlies{w_i + OPT(i-1, v-v_i), OPT(i-1, v)} & \text{if } i > 0, v > 0
\end{cases}
$$

By similar logic to above, a dynamic programming algorithm based on this Bellman equation has running time $\BigO(nV)$, where $\ds V = \sum_{i \in I} v_i$ is an upper bound on the maximum possible value.

## Single-Source Shortest Paths

Given a directed graph $G = (V, E)$ with edge lengths $\ell_e = \ell_{vw}$ for each edge $e = (v, w) \in E$ as well as a source vertex $s \in V$, we want to calculate the shortest path from $s$ to each vertex $v \in V$.

When $\ell_e$ is always nonnegative, then this can be done using Djikstra's algorithm. However, when edge lengths can be negative, the problem gets trickier. Consider a cycle with negative total length. This can be traversed arbitrarily many times, decreasing the path length an arbitary amount, so "shortest paths" are no longer even well-defined.

If there are no negative-length cycles, then there is always a shortest path that has no cycles, since a path with a cycle can be removed without increasing the path length.

### Optimal substructure

This problem has optimal substructure, because sub-paths must be shortest in order for the entire path to be the shortest. Consider a path $P$ from $s$ to $u$. Suppose $t$ is the last vertex in $P$ before $u$, then $P$ involves a path from $s$ to $t$. This must be the shortest path, since if it there were a shorter path $T$ from $s$ to $t$, then $T + (t, u)$ would be shorter than $P$, but $P$ is a shortest path.

Let $OPT(u, i)$ be the shortest path from $s$ to $u$ using at most $i$ edges. If this path is shorter than $i$, i.e. it uses at most $i-1$ edges, then $OPT(u, i) = OPT(u, i-1)$. If it uses $i$ edges, then it is a shortest path from $s$ to a preceding node $t$, then it follows the edge to $u$, so it has length $\ds OPT(u, i) = \min_{t \in V, (t, u) \in E} (OPT(t, i-1) + \ell_{tu})$. Thus, the Bellman equation for the path is

$$
OPT(u, i) = \begin{cases}
0  & \text{if } i = 0, u = s \\
\infty  & \text{if } i = 0, u \neq s \\
\min\curlies{OPT(u, i-1), \ds \min_{t \in V, (t, u) \in E} (OPT(t, i-1) + \ell_{tu})} & \text{otherwise}
\end{cases}
$$

A dynamic programming algorithm based on this approach would recurse $\BigO(n^2)$ times, and each call would take $\BigO(n)$ time, so the algorithm would have a running time in $\BigO(n^3)$.

### Maximum length paths

Analogously to the work above, if there are no positive-length cycles, we can similarly compute maximum length paths. If there are positive cycles and we are only interested in simple paths, then this problem becomes NP-hard. A special case is the Hamiltonian path problem.

## All Pairs Shortest Paths

Suppose we have a similar setup as above - a directed graph $G = (V, E)$ with edge lengths and no negative cycles. This time, we want to compute the lengths of the shortest pahts from all vertices $s$ to all other vertices $v$.

A simple solution would be to run our single-source shortest paths algorithm starting from each node. Without any modification, this takes $\BigO(n^4)$ time. However, we can modify it to take $\BigO(n^3)$ time:

[...]

## Chain Matrix Product

Given matrices $M_1, ..., M_n$ where the dimension of $M_i$ is $d_{i-1} \times d_i$, we want to compute $M_1 \cdot ... \cdot M_n$ as fast as possible. Since products of matrices are associative, this means that we want to choose the best order to multiply adjacent pairs of matrices, so we can discard $M_1, ..., M_n$ and only pay attention to $d_0, ..., d_n$.

Dynamic programming is not a bad approach for this problem because it has the optimal substructure property.

If $A$ has dimension $p \times q$ and $B$ has dimension $q \times r$, then computing $A \times B$ requires $p \times q \times r$ operations.

If $OPT(i, j)$ is the minimum number of operations needded to compute $M_i \cdot ... \cdot M_j$, we can construct the Bellman equation:

$$
OPT(i, j) = \begin{cases}
0 & \text{if } i = j \\
\min\curlies{OPT(i, k) + OPT(k+1, j) + d_{i-1}d_kd_j : i \leq k < j} & \text{if } i < j
\end{cases}
$$

This means that there are $\BigO(n^2)$ function calls, and each function call takes $\BigO(n)$ time, so the total running time is $\BigO(n^3)$.

The best algorithm to solve this problem right now is not a dynamic programming algorithm, it is Hu & Shing's algorithm.

## Edit distance

Suppose we have two strings $X = x_1...x_m$ and $Y = y_1...y_n$, and we can delete or replace symbols of either string, but with costs - for symbols $a$ and $b$ we have a $d(a)$ of deleting $a$ and $r(a, b) = r(b, a)$ of replacing $a$ with $b$ or $b$ with $a$. We want to compute the minimum total cost of making the strings match.

[...]

## Traveling Salesperson

Given a directed graph $G = (V, E)$ with distances $d_{i,j}$ for each edge $(i, j) \in E$, find the minimum total distance of a Hamiltonian cycle, i.e. the minimum distance that needs to be traveled to start at a node $v$, visit every node exactly once, and return to $v$.

For convenience, we will consider $V = \curlies{1, ..., n}$.

Note that it does not matter which node we start at! We will be travelling in a cycle through every node in the graph, so we can start at any node in this cycle.

Suppose we start at node $v_1 = 1$, then we want to find the ordering $(v_2, ..., v_n)$ of the rest of the nodes $\curlies{2, ..., n}$ that minimizes the total distance

$$
d_{v_1v_2} + d_{v_2v_3} + ... + d_{v_{n-1}v_n} + d_{v_nv_1}
$$

Checking every possible ordering means checking $(n-1)!$ different cycles. $n! \in \BigO((n/e)^n)$, so this is really bad.

### Dynamic programming approach

We define $OPT(S, c)$ to be the minimum total travel distance of starting at node $1$, visiting every node in $S$ exactly once, and ending at $c \in S$.

The solution to our problem will be

$$
\min_{2 \leq c \leq n} OPT(\curlies{2, ..., n}, c) + d_{c1}
$$

We can write a bellman equation for it:

$$
OPT(S, c) = \min_{m \in S \setminus \curlies c} OPT(S \setminus \curlies c, m) + d_{mc}
$$

We calculate $OPT(S, c)$ for every subset $S \subseteq \curlies{2, ..., n}$ and every $c \in \curlies{2, ..., n}$. There are $2^{n-1}$ subsets of $\curlies{2, ..., n}$ and $n-1$ choices of $c$, and each call takes time linear in $\abs S \in \BigO(n)$, so in total this approach takes $\BigO(n^2 2^n)$ time. This is still not polynomial time, but it is much better than $\BigO((n/e)^n)$ time! This problem is NP-complete, so it is not known whether a polynomial time algorithm is even possible.