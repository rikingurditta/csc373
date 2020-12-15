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

### Approximation landscape

Here are some examples of known approximation algorithms, from best to worst:

- FPTAS
  - the knapsack problem
- PTAS
  - the makespan problem
- $c$-approximation for constant $c$
  - vertex cover
  - JISP
- $\Theta(\log n)$-approximation
  - set cover
- no $n^{1-\epsilon}$-approximation for any $\epsilon > 0$
  - graph colouring
  - maximum independent set

## Greedy approximation

### Makespan

Given $m$ identical machines, $n$ jobs, and processing time $t_j$ for each job $j$, we want to assign jobs to each machine to minimize the makespan, i.e. the maximum load of a machine.

For each machine $i$, the set $S[i]$ stores the jobs assigned to $i$. The load on the machine is $\ds L_i = \sum_{j \in S[i]} t_j$, and the makespan of the system is $\ds L = \max_i L_i$. We want to minimize $L$.

This problem is $\NP$-complete! However, a greedy approach yields a 2-approximation.

#### Greedy approximation

Let $L^*$ be the optimal makespan. Since some machine must process the job with the highest processing time, we know that

$$
L^* \geq \max_j t_j
$$

Furthermore, since there are $m$ machines, at least one of them must do at least $1/m$ of the total work. The total work is $\ds \sum_j t_j$, so we can conclude that

$$
L^* \geq \frac{1}{m} \sum_j t_j
$$

We consider a greedy algorithm which assigns the a job to the machine with the least load at each step.

Let $L$ be the makespan of our greedy solution, and suppose machine $i$ is the bottleneck, so $L = L_i$ is the maximum final load. Let $p$ be the last job scheduled to machine $i$ by the greedy algorithm. Because of how the algorithm picks machines, this means that $i$ had the least load before job $p$ was scheduled. After job $p$ was scheduled, the loads of the other machines could have only increased as new jobs were scheduled. We can conclude that

$$
L_i - t_p \leq L_k \text{ for every machine } k
$$

We know that there is a machine that does at least $1/m$ of the total work, so 

$$
\begin{align*}
L_i - t_p &\leq \frac{1}{m} \sum_j t_j \\
L_i &\leq \frac{1}{m} \sum_j t_j + t_p \\
L_i &\leq \frac{1}{m} \sum_j t_j + \max_j t_j \\
L &\leq 2L^*
\end{align*}
$$

Therefore, our greedy solution is a 2-approximation.

The tight bound on makespan approximation by arbitrary greedy algorithms is $2 - 1/m$, by a similar argument. We can get better bounds on specific strategies though. For example, a greedy algorithm with a strategy of longest processing time first is a $\frac{4}{3} - \frac{1}{3m}$-approximation.

### Weighted Set Packing

Given a universe $U$ with size $\abs U = m$, and subsets $S_1, ..., S_n \subseteq U$ each with associated value $v_1, ..., v_n \geq 0$, we want to pick a nonintersecting collection of the subsets with maximum value. That is, we want to choose $W \subseteq \curlies{1, ..., n}$ to maximize $\ds \sum_{i \in W} v_i$ subject to the constraint $S_i \cap S_j = \emptyset$ for all $i, j \in W$.

This is an $\NP$-hard problem!