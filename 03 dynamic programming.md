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



## Single-Source Shortest Paths

[...]

## Traveling Salesman

Given a directed graph $G = (V, E)$ where 

[...]