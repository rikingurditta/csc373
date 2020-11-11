# Network Flow

$$
\newcommand{\ds}{\displaystyle}
\newcommand{\curlies}[1]{\left\lbrace #1 \right\rbrace}
\newcommand{\abs}[1]{\left\lvert #1 \right\rvert}

\newcommand{\BigO}{\mathcal{O}}
$$

## Network Flow

Suppose we have a directed graph $G = (V, E)$ where each edge has a **capacity**, i.e. we have a function $c: E \to \R_{\geq 0}$, and we are given source and target vertices $s, t \in V$. We want to find the maximum "flow" from $s$ to $t$.

An **$s$-$t$ flow** is a function $f: E \to \R_{\geq 0}$. Intutively, it is an assignment of "amount of material" carried on each edge $e \in E$. $f$ must respect two constraints:

1. Capacities - for every edge $e$, $f(e) \leq c(e)$

2. Conservation - for every vertex $v$ (aside from $s$ and $t$), the flow coming in is equal to the flow going out, aka

$$
f^{in}(v) = \sum_{e=(x,v)} f(e) = \sum_{e=(v,y)} f(e) = f^{out}(v)
$$

We define the **value** of the flow $v(f)$ as 

$$
v(f) = f^{out}(s) = f^{in}(t)
$$

We want to find a flow $f^*$ with the greatest value.

## Greedy approach

1. Start from zero flow - set $f(e) = 0$ for each $e$
2. While there exists an $s$-$t$ path $P$ in $G$ such that $f(e) < c(e)$ for each $e \in P$
   - increase $f(e)$ by $\ds \min_P(c(e) - f(e))$ (bottleneck) for each $e \in P$

This algorithm does not work because it makes irrevocable decisions, so it cannot reverse bad decisions.

## Ford-Fulkerson Algorithm

### Residual Graphs

To come up with an algorithm where we can reverse bad decisions, we will "reverse" flow in the following way:

![image-20201001142201843](/Users/rikin/Library/Application Support/typora-user-images/image-20201001142201843.png)

In concrete terms, this results in reducing the flow between $u$ and $v$ by $10$, but we can think of it as reversing the direction of the flow.

To make this more concrete, we can define a **residual graph** $G_f = (V, E_f)$ with the same vertices as $G$, but with $E_f$ containing:

- *forward edges*, which are edges $e \in E$ where $c(e) - f(e) > 0$
  - their capacity is $c_f = c(e) - f(e)$
  - $e \in E_f$ represents how much flow can add to $e \in E$
- *backward edges*, which are edges $e^{rev} = (v, u)$ where $e = (u, v) \in E$ and $f(e) > 0$
  - their capacity is $c_f(e) = f(e)$
  - $e^{rev} \in E_f$ represents how much flow we can remove from $e \in E$

### Augmenting paths

Let $P$ be an $s$-$t$ path in $G_f$ and $x = \text{bottleneck}(P, f)$ be the smallest capacity $c_f(e)$ across all edges $e \in P$. Define **augmenting** $P \subseteq E_f$ as:

- if $e \in P$ is a forward edge, then increase $f(e)$ by $x$
- if $e^{rev} \in P$ is a reverse edge, then decrease $f(e)$ by $x$

Then we can create a new flow $f'$ by augmenting $P$. This is a valid new flow because it satisfies the two constraints. It satisfies the capacity constraint:

- if $f(e)$ is increased, then that means the forward edge $e \in P$ has capacity $c_f(e) = c(e) - f(e)$. Since $x$ is the smallest capacity, $x \leq c(e) - f(e)$, so $f(e) + x \leq c(e)$.
- if $f(e)$ is decreased, then that means the backward edge $e^{rev} \in P$ has capacity $c_f(e^{rev}) = f(e)$. Since $x$ is the smallest capacity, $x \leq f(e)$, so $f(e) - x \geq 0$.

It also satisfies the conservation constraint. Suppose $v$ is an inner node (aka not $s$ or $t$) along the path. Then it has two edges incident to it in $P$, $e_1$ and $e_2$.

- suppose both $e_1$ and $e_2$ are forward edges, then $x$ has been added to both the flow coming in and the flow going out, so flow is conserved at $v$
- suppose both $e_1$ and $e_2$ are backward edges, then similarly $x$ is subtracted from both the flow coming in and going out, so flow is conserved
- suppose $e_1$ is a forward edge and $e_2 = e^{rev}$ is a backward edge, then $f(e_1)$ is increased by $x$ but $f(e^{rev})$ is decreased by $x$, so the flows coming in and going out of $v$ are unchanged
- the same holds when $e_1$ is a backward edge and $e_2$ is a forward edge

### The Ford-Fulkerson Algorithm

The Ford-Fulkerson algorithm is simply to augment paths in $G_f$ until there are no longer any paths left to augment.

```python
def MaxFlow(G):
    for e in G:
        f(e) = 0
    while P = FindPath(s, t, Residual(G, f)) != None:
        f = Augment(f, P)
    return f
```

### Running Time

Let $\ds C = \sum_{e = (s, v) \in E} c(e)$ be the sum of capacities of edges leaving the starting node $s$. Each augmentation increases flow by at least 1, which means that flow out of $s$ is increased by at least 1. Since the flow out of $s$ is at most $C$, this means that we can have at most $C$ augmentations.

Each augmentation is on a path in $G_f$, which has $n$ vertices and at most $2m$ edges. Thus, finding an $s$-$t$ path takes $\BigO(n + m)$ time.

Since a path is found for each augmentation, the total running time is $\BigO((n + m) \cdot C)$.

This is *pseudo-polynomial time*, since $C$ can be exponentially large in the input length (number of bits to specify capacity).

### Correctness

#### Cuts

An **$s$-$t$ cut** is a partition $(A, B)$ of $V$ (aka $A \sqcup B = V$) where $s \in A$ and $t \in B$.

Its **capacity** $cap(A, B)$ is the sum of capacities of edges leaving $A$, i.e.

$$
cap(A, B) = \sum_{e = \text{ leaving } A} c(u, v)
$$

For any flow $f$ and any $s$-$t$ cut $(A, B)$, $v(f) = f^{out}(A) - f^{in}(A)$. This is because

[...]

Furthermore, for any flow $f$ and $s$-$t$ cut $(A, B)$, $v(f) \leq cap(A, B)$. This is because

$$
\begin{align*}
v(f) &= f^{out}(A) - f^{in}(A) \\
&\leq f^{out}(A) \\
&= \sum_{e \text{ leaving } A} f(e) \\
&\leq \sum_{e \text{ leaving } A} c(e) \\
&= cap(A, B)
\end{align*}
$$

This means that the maximum value of any flow is less than or equal to the minimum capacity of any $s$-$t$ cut. Thus, if we find a flow whose value is the capacity of a cut, then we know that the flow is optimal.

#### Ford-Fulkerson finds a flow that is the capacity of a cut

Suppose $f$ is the flow that is found by the Ford-Fulkerson algorithm. Let $A$ be the nodes reachable from $s$ in $G_f$ and $B = V \setminus A$ be the remaining nodes. Since the algorithm terminates when there is no path in $G_f$ from $s$ to $t$ in $G_f$, we know that $t$ must not be reachable from $s$, so $B$ must contain $t$. This means that $(A, B)$ is an $s$-$t$ cut

Suppose $e = (u, v)$ is an edge from $u \in A$ to $v \in B$ in $G$. If $c(e) - f(e) > 0$, then $e$ is also $G_f$. Since $u$ is reachable from $s$ and $(u, v)$ is in the residual graph, $v$ would also be reachable from $s$. But this contradicts the assumption that $v \in B$, so we must have $f(e) = c(v)$.

Suppose $e = (v, u)$ is an edge from $v \in B$ to $u \in A$ in $G$. If $f(e) > 0$, then $e^{rev} = (u, v)$ would be in the residual graph, so once again $v$ would be reachable from $s$, again a contradiction.

Thus, for each edge $e_o$ from $A$ to $B$, $f(e_o) = c(e_o)$, and for each edge $e_i$ from $B$ to $A$, $f(e_i) = 0$. Thus,

$$
\begin{align*}
v(f) &= f^{out}(A) - f^{in}(A) \\
&= \sum_{e_o \text{ leaving } A} f(e_o) - \sum_{e_i \text{ entering } A} f(e_i) \\
&= \sum_{e_o \text{ leaving } A} c(e) - 0 \\
&= cap(A, B)
\end{align*}
$$

Since this flow's value is the capacity of an $s$-$t$ cut, we know that it is the maximum possible flow.

### Faster algorithms

The Edmonds-Karp algorithm is the Ford-Fulkerson algorithm, but with smarter choice of path to augment - it always finds and augments the shortest path. Its runtime is $\BigO(nm^2)$, which is what we consider *strongly polynomial time*.

We can also change the Ford-Fulkerson algorithm to always look for the maximum bottleneck path each iteration, which would change it to take $\BigO(m^2 \log(C))$ time, which is considered *weakly polynomial time*.

The current best solution to the problem, by Orlin, runs in $\BigO(nm)$ time.

## Network Flow Applications

Network flow began as a study of finding min cuts, so that the US could find out how to efficiently disable the Soviet rail network in the 1930s in case of war

### Integrality matrix

## Circulation

Given a directed graph $G = (V, E)$, edge capacities $c: E \to \N$, and node demands $d: V \to Z$, we want a circulation $f: E \to \N$ satisfying:

- for each $e \in E$, $0 \leq f(e) \leq c(e)$
- for each $v \in V$, $\ds \sum_{e \text{ entering } v} f(e) - \sum_{e \text{ leaving } v} f(e) = d(v)$

### Network flow formulation

We can equivalently formulate a circulation problem as a network flow problem. We do this by considering a new graph $G' = (V', E')$ which is $G$ but with a source node $s$ and target node $t$ added. $s$ is adjacent to every node in $G$ that has a negative demand, and $t$ is adjacent to every node in $G$ that has positive demand. Then we want the value of the flow to be

$$
\sum_{v \in V : d(v) > 0} d(v) = \sum_{v \in V : d(v) < 0} -d(v)
$$

in order to solve the original circulation problem.

## Circulation with Lower Bounds

Consider the circulation problem, however with added lower bound constraints - for every $e \in E$, $\ell(e) \leq f(e) \leq c(e)$.

We can convert this to a normal circulation problem by doing the following for each edge $e = (u, v)$:

- $c(e) \gets c(e) - \ell(e)$
- $d(u) \gets d(u) + \ell(e)$
- $d(v) \gets d(v) - \ell(e)$
- forget about $\ell(e)$

![lower bound circulation to normal circulation.png](lower bound circulation to normal circulation.png)

