# Computational Complexity

$$
\newcommand{\ds}{\displaystyle}
\newcommand{\curlies}[1]{\left\lbrace #1 \right\rbrace}
\newcommand{\abs}[1]{\left\lvert #1 \right\rvert}

\newcommand{\BigO}{\mathcal{O}}
\newcommand{\NP}{\mathcal{NP}}
\newcommand{\prd}{\leq_P}
$$

## Complexity Classes

### $\mathcal{NP}$ - Nondeterministic Polynomial Time

A decision problem (a problem whose answer is either $yes$ or $no$) is in the complexity class $\NP$ if solutions to it can be verified in polynomial time.

Suppose $A$ is a decision problem, and $x$ is an instance of $A$. For example, we could consider the Hamiltonian cycle problem, in which case an instance $x$ would be a graph, and the corresponding answer would be $yes$ or $no$ whether or not the graph $x$ has a Hamiltonian cycle.

$A \in \NP$ if there exists an algorithm `Verify(instance, advice)` with the following properties:

- it takes arguments $x$, an instance of $A$, and $y$, a piece of "advice"
- it returns the answer to $x$
- for every $x$ whose answer is $yes$, there exists advice $y$ whose size is a polynomial of $x$, i.e. there exists a polynomial $p$ so that $\abs y = p(\abs x)$, so that `Verify(x, y)` outputs $yes$ in polynomial time
- for every $x$ whose answer is $no$, `Verify(x, y)` returns $no$ for every advice $y$

### co-$\mathcal{NP}$

co-$\NP$ refers to the class of decision problems where there is an algorithm that can (in polynomial time, with a polynomial time piece of advice) verify that the answer to an instance is $no$.

$A \in \text{co-}\NP$ if there exists an algorithm `Verify(instance, advice)` with the same first two properties as in the $\NP$ case, but with swapped last two properties:

- for every $x$ whose answer is $no$, there exists advice $y$ whose size is a polynomial of $x$, i.e. there exists a polynomial $p$ so that $\abs y = p(\abs x)$, so that `Verify(x, y)` outputs $no$ in polynomial time
- for every $x$ whose answer is $yes$, `Verify(x, y)` returns $yes$ for every advice $y$

## $\mathcal P$-Reducibility

Consider problems $A$ and $B$. Suppose we have a subroutine `SolveB` to solve instances of $B$. If we can solve any instance of $A$ using a polynomial number of calls to `SolveB` as well as polynomial time computations, then we say **$A$ is $\mathcal P$-reducible to $B$**, and we write $A \prd B$.

It follows that if $B$ can be solved in polynomial time, then so can $A$. The contrapositive of this statement is also interesting: if $A$ cannot be solved in polynomial time, then neither can $B$.

Also note that this relation is transitive: if $A \prd B$ and $B \prd C$, then $A \prd C$.

## $\mathcal{NP}$-Completeness

Suppose we have a problem $A$ where:

- $A \in \NP$
- for every $B \in NP$, $B \prd A$

Then we say **$A$ is $\NP$-complete**.

### Showing $\mathcal{NP}$-completeness practically

Note that we do not need to show the second condition in full generality every time we prove that a problem is $\NP$-complete. Suppose $A \in \NP$ and there is an $\NP$-complete problem $Y$ where $Y \prd A$. Then if $X \in \NP$, then $X \prd Y \prd A$, so $X \prd A$. This holds for all $X \in \NP$, so $A$ is $\NP$-complete.

Notice that we proved the second condition, but we only needed to explicitly make one $\mathcal P$-reduction, $Y \prd A$. The rest of our proof relied on our prior knowledge that $Y$ is $\NP$-complete.