# Formal Construction of the Hyperreals

We are inclined to study the hyperreal structure ${}^{\ast}\mathbb{R}$ as it serves as a nonstandard model of $\mathbb{R}$ that has particular importance in analysis and combinatorics.

## Brief Intuition and Motivation

Just as $\mathbb{R}^{2}$ and $\mathbb{R}^{3}$ both form vector spaces over $\mathbb{R}$ yet are not the same, the hyperreals ${}^{\ast}\mathbb{R}$ can be seen as another model for $\mathbb{R}$ that is not the same as our classical $\mathbb{R}$. In particular, we hope it satisfies all first order axioms for $\mathbb{R}$. Whether it can hold claim to being "the" $\mathbb{R}$ is a question for the philosophers. Informally, ${}^{\ast}\mathbb{R}$ has infinitesimal and infinite elements. It is important to note that ${}^{\ast}\mathbb{R}$ has a fundamentally different structure from $\mathbb{R}$. It is not just a mere different construction (i.e., it is not the difference between Dedekind cuts and Cauchy sequences). 

Our specific formal construction will be concerned with sequences $(a_{0}, a_{1}, a_{2}, \dots)$ where addition, multiplication, etc. are essientally defined component-wise. We would want to associate $r \in \mathbb{R}$ with $(r, r, r, \dots)$ and infinitesimal elements with those that approach zero (inverse for infinite elements). 

## Filters

**Definition.** A *filter* $F$ on a set $S$ is a subset of the powerset $\mathcal{P}(S)$ that satisfies:
1. $\varnothing \notin F; S \in F$
2. If $A, B \in F$ then $A \cap B \in F$
3. If $A \in F$ and $A \subseteq B \subseteq S$, then $B \in F$

We now consider a special kind of filter.

**Definition.** An *ultrafilter* $F$ on $S$ is a filter where for every $A \subseteq S$, either $A \in F$ or $S \setminus A \in F$ but not both.

The idea behind filters is that they capture "large" sets.

## Hyperreal Construction

Consider a set of indicies $I$ (that is, a set enumerating indexes such as $\left\{ 0,1,2,\dots \right\}$) and fix an ultrafilter $\mathcal{U}$ on $I$. 

**Definition ([^1]).** The *ultrapower* of $\mathbb{R}$ modulo the ultrafilter $\mathcal{U}$ is the quotient set of family of real $I$-sequences $\mathbb{R}^{I}$ modulo the equivalence relationship $\equiv_{\mathcal{U}}$ as follows:

$$
\sigma \equiv_{\mathcal{U}} \tau \iff \{i \in I : \sigma(i) = \tau(i)\} \in \mathcal{U}.
$$

We will notate this quotient set as $\mathbb{R}^{I} / \mathcal{U}$. The $I$-sequences can be interpreted as functions $\sigma : I \rightarrow \mathbb{R}$ where $\sigma(i)$ picks out the $i$th element. 

For a sequence $\sigma$, we notate all equivalent sequences to $\sigma$ by $\equiv_{\mathcal{U}}$ as $[\sigma]$. The intuitive notion of this is that the equivalent sequences show "great similarity". Indeed, the ultrafilter $\mathcal{U}$ is an ultrafilter of sets of indicies (e.g., $\left\{ 0,1,2,\dots \right\}$), and equivalence is determined if the indicies that match form a set in the ultrafilter. Because filters are meant to capture "large" sets, we intuitively expect extremely similar sequences to be about equal. 

Now, we can further define addition and multiplication with 

$$
[\sigma] + [\tau] = [\sigma + \tau]
$$

and

$$
[\sigma] \cdot [\tau] = [\sigma \cdot \tau],
$$

where $\sigma + \tau$ and $\sigma \cdot \tau$ are defined by the pointwise sum and multiplication operators from the ring acting on the function space $I \rightarrow \mathbb{R}$ (this is the same as the intuitive notion of the component wise sums on the sequences). We can also write the total ordering $<$ on $\mathbb{R}^{I} / \mathcal{U}$ with 

$$
[\sigma] < [\tau] \iff \{i \in I : \sigma(i) < \tau(i)\} \in \mathcal{U}.
$$

For brevity, we will define

$$
{}^{\ast}\mathbb{R} = \mathbb{R}^{I} / \mathcal{U}.
$$

## Short Account of Properties

**Theorem.** The structure $({}^{\ast}\mathbb{R}, +, \cdot, <, \mathbf{0}, \mathbf{1})$ forms an ordered field.

*Proof.* We will aim to show just one property as the other properties all follow similar proofs. Let us attempt to prove that every $[\sigma] \neq \mathbf{0}$ has a multiplicative inverse. First, the sequence $\mathbf{0}$ will be defined as a sequence of all $0$'s. It is obvious that it satisfies the additive identity by the definitions of $+$ and $\cdot$. Now, we know that the set $A = \left\{ i \in I : \sigma(i) = 0 \right\} \notin \mathcal{U}$ by the fact that $[\sigma] \neq \mathbf{0}$. Using the fact that $\mathcal{U}$ is an *ultrafilter*, we can conclude that the complement $A^{c} = \left\{ i \in I : \sigma(i) \neq 0 \right\}$ is in $\mathcal{U}$. Now we choose an $I$-sequence $\tau$ such that $\tau(i) = \frac{1}{\sigma(i)}$ whenever $i \in A^{c}$ (that is, the index hits a nonzero element in the $\sigma$-sequence). Notice that $A^{c} \subseteq \left\{ i \in I : \sigma(i) \cdot \tau(i) = 1 \right\} \in \mathcal{U}$ by the properties of a filter. This implies that $[\sigma] \cdot [\tau] = 1$ as desired. $\Box$

The full proof of the above theorem will not be provided as it is simply just more trivial manipulations of dense notation. Additionally, the full proof of the facts that ${}^{\ast}\mathbb{R}$ satisfies the (first order) axioms of $\mathbb{R}$ and that $\mathbb{R}^{I} / \mathcal{U}$ satisfies our intuitive notions of ${}^{\ast}\mathbb{R}$ will not be provided for similar reasons.

Note that the *transfer principle* allows us to conclude that first order statements true about $\mathbb{R}$ are also true about ${}^{\ast}\mathbb{R}$, which is a testament to ${}^{\ast}\mathbb{R}$'s potential usefulness.

## Discussion

The ultrapower construction on $\mathbb{R}$ works more generally for different sets. It is a particularly useful tool in model theory for constructing nonstandard models. In particular, the ultrafilter $\mathcal{U}$ gives us "fine-motor" control over the nonstandard models. As for $\mathbb{R}$, it may be of question of whether two different ultrafilters would produce different ${}^{\ast}\mathbb{R}$. This turns out to be dependent on the Continuum Hypothesis.

[^1]: Definition and some additional information adopted from Nasso et al.