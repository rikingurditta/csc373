# Approximation Algorithms and Local Search

$$
\newcommand{\ds}{\displaystyle}
\newcommand{\curlies}[1]{\left\lbrace #1 \right\rbrace}
\newcommand{\abs}[1]{\left\lvert #1 \right\rvert}

\newcommand{\BigO}{\mathcal{O}}
\newcommand{\NP}{\mathcal{NP}}
\newcommand{\prd}{\leq_P}
$$

## Decision problems vs optimization problems

Many decision problems can be reframed as optimization problems. A question of "does there exist a solution with objective $\geq k$" can be rephrased as "find a solution maximizing the objective".

For example:

- decision variant: "Is there an assignment which satisfies at least $k$ clauses of a given CNF formula $\varphi$?"
- optimization variant: "Find an assignment which satisfies as many clauses as possible of a given CNF formula $\varphi$."

This does not reduce the computational complexity of a problem, but it allows us to create algorithms that find "good" solutions rather than exact ones, e.g. with approximation algorithms.

## Approximation algorithms

Approximation algorithms have objectives, e.g. a profit to maximize or a cost to minimize.

Given a problem instance $I$, let $OPT(I)$ be an optimal solution and $ALG(I)$ be our approximation algorithm's solution.

The **approximation ratio** of $ALG$ on instance $I$ is
$$
\frac{profit(OPT(I))}{profit(ALG(I))} \text{ or } \frac{cost(ALG(I))}{cost(OPT(I))}
$$
(though sometimes, the reciprocal of this is called the approximation ratio)

By this convention, the approximation ratio is always $\geq 1$.

Generally, we investigate the worst approximation ratio of an algorithm over all instances of a problem. We say that an algorithm has a **worst case approximation ratio of $c$** if it has approximation ratio $\leq c$ for every instance of the problem. In this case we call it a **$c$-approximation algorithm**.

### PTAS and FPTAS

A **polynomial time approximation scheme** (PTAS) is when for every $\epsilon > 0$, there exists a $(1 + \epsilon)$-approximation algorithm that runs in time that is polynomial in $n$ (but not necessarily polynomial in $\epsilon$).

A **fully polynomial time approximation scheme** (FPTAS) is when for every $\epsilon > 0$, there exists a $(1 + \epsilon)$-approximation algorithm that runs in time that is polynomial in both $n$ and $\epsilon$.

## Greedy approximation

