
# 1 Definition

A probability space is a 3-tuple $(\Omega, \mathcal{F}, P)$ where
- $\Omega$ is the set of outcomes
- $\mathcal{F}$ is set of events. 
	- It is also a [[007 σ-algebra|σ-algebra]] over $\Omega$.
- $P:\mathcal{F} \arw [0,1]$ is a function assigning probabilities to events $\mathcal{F}$

Being a σ-algebra over $\Omega$, $\mathcal{F}$ includes all non-empty [[005 subset|subsets]] of $\Omega$ and satisfies the conditions:
1. if $A \in \mathcal{F}$, then $A^c \in \mathcal{F}$
2. if $\forall i \in I, A_i \in \mathcal{F}$, then $\bigcup_{i \in \mathbb{I}} A_i \in \mathcal{F}$ where $I$ is the size of the sequence $A_i$.

# 2 Consequences

### 2.1.1 Closure under intersection


As a consequence of closure under union, the condition $\bigcap_{i \in \mathbb{I}} A_i \in \mathcal{F}$ can be rewritten as as a union $(\bigcup_{i \in I} A_i^c )^c \in \mathcal{F}$

This is by application of [De Morgan's laws](https://en.wikipedia.org/wiki/De_Morgan%27s_laws) which is also mentioned in wiki for [[006 compliment|compliment]].


# 3 Theorems

## 3.1 Theorem 1

This matches book theorem 1.1.1 on pg 2, vpg 10/490.

(1)

Let $\mu$ be a [[015 measure|measure]] on $(\Omega, \mathcal{F})$

(2)

$\mu$ satisfies monotonicity, if $A \subset B$ then $\mu(A) \le \mu(B)$ 

(3)

$\mu$ satisfies subadditivity, if $A \subset \bigcup_{m=1}^\infty A_m$ then $\mu(A) \le \sum_{m=1}^\infty \mu(A_m)$

(4)

$A_i \uparrow A$ denotes continuity from above, and translates to $A_1 \subset A_2 \subset A_3 \subset \dots \subset A_i$ and $\bigcup_i A_i = A$  

(5)

$A_i \downarrow A$ denotes continuity from below, and translates to $A1 \supset A_2 \supset A_3 \supset \dots \supset A_i$  and $\bigcap_i A_i = A$ 

(6)


# 4 Related Entries

- [[Wk 35 000 Starting out Probability Theory and Examples]]

# 5 Notes

## 5.1 On Sequence Notation

The notation $A_i \in \mathcal{F}$  used in the book is shorthand for $\forall i \in I, A_i \in \mathcal{F}$ , where $I$ is the length of the sequence in consideration. 

# 6 Config


$$

\newcommand{\arw}{\rightarrow}

$$

