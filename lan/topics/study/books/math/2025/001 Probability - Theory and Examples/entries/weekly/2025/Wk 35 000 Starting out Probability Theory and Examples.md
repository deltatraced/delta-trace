# 1 Journal

2025-08-30 Wk 35 Sat - 08:42

Measure theory has a hidden section Appendix A at the end of the book in pg 455 (vpg 463/490)

2025-08-30 Wk 35 Sat - 08:47

How do you write "(Ω, F, P )"

2025-08-30 Wk 35 Sat - 14:11

So I found that $\mathcal{F}$ is notation for family of sets! You can learn more about it in this wiki [wiki Set function](https://en.wikipedia.org/wiki/Set_function) which talks about the same notation also with $\Omega$!

So to say that $\mathcal{F}$ is a family of sets over $\Omega$ is to say that $\mathcal{F} \subseteq \wp(\Omega)$ where $\wp(\Omega)$ is the powerset of $\Omega$.

2025-08-30 Wk 35 Sat - 08:45

Putting probability space definition in [000 Probability Space](../../../definitions/2025/000%20Probability%20Space.md)

## 1.1 Probability Space

A probability space is a 3-tuple $(\Omega, \mathcal{F}, P)$  where

* $\Omega$ indicates

# 2 HowTos

## 2.1 Translating some symbols to LaTeX

2025-08-30 Wk 35 Sat - 08:47

How do you write this in LaTeX?

![Pasted image 20250830084751.png](../../../../../../../../../../attachments/Pasted%20image%2020250830084751.png)

I know the omega is just `\Omega`: $\Omega$.

P just looks like a regular P, $P$.

But what about the F? that's not $F$.

2025-08-30 Wk 35 Sat - 09:03

(LLM)

From ChatGPT5,

We can use `\mathcal{F}`: $\mathcal{F}$

We usually use $\in$ to mean "element-of" in a set-theoretic convention. But we can also think in terms of types (which are conditions for interpreting or constructing a value) rather than sets. Instead we use this symbol: $-\!\!\!\in$ to say "of-type". Ideally I'd want to shorten it to `\oft`.

(/LLM)

# 3 Investigations

## 3.1 What does pairwise mean in pairwise disjoint sets?

* [x] 

2025-08-30 Wk 35 Sat - 15:18

I know disjoin sets share no elements, but pairwise? The context is the description of sigma additivity in the [wiki](https://en.wikipedia.org/wiki/Sigma-additive_set_function):

 > 
 > Suppose that A ![{\displaystyle \scriptstyle {\mathcal {A}}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/0d586ece9308bf4ef901494079b434c71aac7d41) is a [σ-algebra](https://en.wikipedia.org/wiki/Sigma_algebra "Sigma algebra"). If for every [sequence](https://en.wikipedia.org/wiki/Sequence "Sequence") A 1 , A 2 , … , A n , … ![{\displaystyle A\_{1},A\_{2},\ldots ,A\_{n},\ldots }](https://wikimedia.org/api/rest_v1/media/math/render/svg/7a121200e0c558612beb99e748a738814d788c3f) of pairwise disjoint sets in A ,

[statisticshowto pairwise-disjoint](https://www.statisticshowto.com/pairwise-disjoint/) talks about this, but they keep mentioning "pairwise disjoint" together, without decomposing them into individual concepts!

But in [What is Pairwise?](https://www.statisticshowto.com/pairwise-independent-mutually/#PW) they explain:

 > 
 > Pairwise means to **form all possible pairs** — two items at a time — from a set. For example, in the set {1,2,3} all possible pairs are (1,2), (2,3), (1,3).

Okay, that's intuitive.

So I think pairwise + disjoint then would mean, if you take all possible pairs from a family of sets, and then we expect that all pairs of sets are disjoint.

## 3.2 Why F contains all unions of its underlying elements

* [ ] 

So in pg1 (vpg 9/490) of the [book](https://sites.math.duke.edu/~rtd/PTE/PTE5_011119.pdf),

They say

 > 
 > if $A_i \in \mathcal{F}$ is a countable sequence of sets then $\bigcup_iA_i \in \mathcal{F}$

Why would this be true?

2025-08-30 Wk 35 Sat - 17:54

(1)

$\mathcal{F}$ is a [σ-algebra](../../../../../../../../concept/math/definitions/entries/2025/007%20%CF%83-algebra.md) over $\Omega$, so it can hold elements up to $\wp(\Omega)$ where $\wp$ denotes the powerset.

<a name="errata-e14827" />^errata-e14827

2025-08-30 Wk 35 Sat - 18:22

Spawn [Drawing 2025-08-30 18.25.14.excalidraw](../../../drawings/Drawing%202025-08-30%2018.25.14.excalidraw.md) (sketch)

$\mathcal{F}$'s elements are possible *subsets* of $\Omega$, yet here the book says that an element $A_i$ is a countable *sequence*?

An element $A$ of $\mathcal{F}$ should be a subset of $\Omega$. An element $A_i$ of $A$ should be considered an individual item.

2025-08-30 Wk 35 Sat - 18:58

In [σ-additive defn premise 3](../../../../../../../../concept/math/definitions/entries/2025/018%20%CF%83-additive.md#199261-premise-3),

We also mention sequences, and this might be where this comes from.

 > 
 > Let $S$ be the set of all [sequences](../../../../../../../../concept/math/definitions/entries/2025/020%20Seq.md) of pairwise disjoint \[^1\] sets in $\mathcal{F}$

What motivated this is the writing in the [σ-additive set function wiki](https://en.wikipedia.org/wiki/Sigma-additive_set_function),

 > 
 > Suppose that A ![{\displaystyle \scriptstyle {\mathcal {A}}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/0d586ece9308bf4ef901494079b434c71aac7d41) is a [σ-algebra](https://en.wikipedia.org/wiki/Sigma_algebra "Sigma algebra"). If for every [sequence](https://en.wikipedia.org/wiki/Sequence "Sequence") A 1 , A 2 , … , A n , … ![{\displaystyle A\_{1},A\_{2},\ldots ,A\_{n},\ldots }](https://wikimedia.org/api/rest_v1/media/math/render/svg/7a121200e0c558612beb99e748a738814d788c3f) of pairwise disjoint sets in A ,

I think the point here is because you could take the union of any number of subsets, and the result will hold, not just 2. If you pick two subsets at random and form a pair, you find that they are disjoint, so it follows any randomly sampled sequence too will be disjoint.

The mentioning of sequence here is likely to relax the constraints of repetition and order of [sets](../../../../../../../../concept/math/definitions/entries/2025/001%20Set.md), and these are relaxed because the property holds with or without them.

But notice here that the sequence is of the *sets* in $\mathcal{F}$, we are still not saying as we interpret the book to say that $\mathcal{F}$ has within it anywhere some sequence!

### 3.2.1 Pend

# 4 Errata

## 4.1 non-empty collection not missing empty subset

In [^errata-e14827](Wk%2035%20000%20Starting%20out%20Probability%20Theory%20and%20Examples.md#errata-e14827)

I wrote

 > 
 > $\mathcal{F}$ is a [σ-algebra](../../../../../../../../concept/math/definitions/entries/2025/007%20%CF%83-algebra.md) over $\Omega$, so it can holds elements up to $\wp(\Omega) - \lbrace \varnothing \rbrace$ where $\wp$ denotes the powerset.

but this is not true. This is because I misinterpreted the definition of σ-algebra. It reads:

 > 
 > A σ-algebra on a set $X$ is a non-empty collection $\Sigma$ of [subsets](../../../../../../../../concept/math/definitions/entries/2025/005%20subset.md) of $X$ closed under [compliment](../../../../../../../../concept/math/definitions/entries/2025/006%20compliment.md), [countable](../../../../../../../../concept/math/definitions/entries/2025/010%20Countable%20set.md) [union](../../../../../../../../concept/math/definitions/entries/2025/008%20union.md), and [countable](../../../../../../../../concept/math/definitions/entries/2025/010%20Countable%20set.md) [intersections](../../../../../../../../concept/math/definitions/entries/2025/014%20intersection.md).

The algebra itself must be a *non-empty collection*. I interpreted it as *not having the empty set*.

So as correction,

$\mathcal{F}$ is a [σ-algebra](../../../../../../../../concept/math/definitions/entries/2025/007%20%CF%83-algebra.md) over $\Omega$, so it can hold elements up to $\wp(\Omega)$ where $\wp$ denotes the powerset.
