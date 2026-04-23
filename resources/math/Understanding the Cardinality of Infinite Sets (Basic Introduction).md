# Understanding the Cardinality of Infinite Sets (Basic Introduction)

**UNDER CONSTRUCTION**

---

The idea of infinity has been around for quite some time and has consistently proved to be helpful in mathematics. Mathematical fields of interest like calculus, geometry, and so on all have infinity related in some capacity. In the early 20th century, mathematicians tried to formalize many hypotheses, theories, and ideas. Infinity was no exception.

---

## Axiom of Infinity

The most widely accepted axiom of infinity is denoted as the following:

$$
\exists \textbf{I} (\emptyset \in \textbf{I} \land (\forall x \in \textbf{I} \rightarrow (x \cup \\{ x \\}) \in \textbf{I})),
$$

which essentially defines an infinite set of natural numbers. This defines the smallest infinity within Zermelo–Fraenkel Set Theory ($ZF$).

The definition builds ordinals via the Von Neumann Construction, which states that

$$
0 = \emptyset
$$

$$
1 = 0 \cup \\{ 0 \\}
$$

$$
2 = 1 \cup \\{ 1 \\}
$$

$$
3 = 2 \cup \\{ 2 \\}
$$

$$
...
$$

$$
x + 1 = x \cup \\{ x \\}
$$

The Von Neumann Construction defines all natural numbers, $\mathbb N$, and the Axiom of Infinity states that $\exists \textbf{I}$ which is the set of all natural numbers. It follows that $\|\textbf{I}\|$ (the cardinality of $\textbf{I}$) is infinite. We define this infinity to be $\aleph_{0}$.

---

## Cardinals vs. Ordinals

There are two notions of infinity within set theory, being cardinals and ordinals. Cardinals denote the amount of objects within a set. For example, $\|\\{ 1, 5, 7 \\}\| = 3$. Ordinals define the order/enumeration of a set. To apply this to finite sets, we can assign each element of a set to the least natural number that has not been used yet. This definition can be extended to infinite sets. There are many cases where the cardinality and the ordinal of a set are the same. There are also some cases where they are not.

Recall that $\|\mathbb N\| = \aleph_{0}$. The ordinal for $\mathbb N$ is $\omega$. It is important to note that $\aleph_{0}=\aleph_{0}+1$, but $\omega\neq\omega+1$.

---

## Continuum Hypothesis

We can show a bijection $\mathbb Q \rightarrow \mathbb N$, which entails that they have the same cardinality. On the other hand, there is no bijection between $\mathbb R$ and $\mathbb N$. It follows that $\|\mathbb R\|>\aleph_{0}$. Let $\|\mathbb R\|=\mathfrak{c}$. Does $\mathfrak{c}=\aleph_{1}$, the next biggest infinity after $\aleph_{0}$, or is $\mathfrak{c}>\aleph_{1}$?

The continuum hypothesis states that: $\mathfrak{c}=\aleph_{1}$.
