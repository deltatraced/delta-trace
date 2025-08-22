---
status: pend
---

You can find the paper [here](https://arxiv.org/abs/1706.03762) [<a name="1" />^1](000%20Attention%20is%20all%20you%20need.md#1).

# 1 Journal

## 1.1 Reading 000

### 1.1.1 Evaluation Objective

Skim through the paper and note foundational next concepts to learn about to understand it, as well as terms to learn more about.

### 1.1.2 Concepts

### 1.1.3 Annotations

### 1.1.4 Keywords

 > 
 > dominant sequence transduction models
 > [<a name="quote-paper" />^quote-paper](000%20Attention%20is%20all%20you%20need.md#quote-paper)
 > <a name="keyword-000" />^keyword-000

 > 
 > Achieving {N} BLEU on the WMT 2014 English-to-German translation task
 > [<a name="quote-paper-paraphrase" />^quote-paper-paraphrase](000%20Attention%20is%20all%20you%20need.md#quote-paper-paraphrase)
 > <a name="keyword-001" />^keyword-001

 > 
 > sequence modeling and transduction problems such as language modeling and machine translation
 > [^quote-paper](000%20Attention%20is%20all%20you%20need.md#quote-paper)
 > <a name="keyword-002" />^keyword-002

# 2 Tasks

## 2.1 Find a working example implementation of Attention in section 3.2

* [ ] 

2025-08-10 Wk 32 Sun - 11:02

So there are two components here, Scaled Dot-Product Attention, and also it is vertically scaled as a component in Multi-Head Attention.

From what I understand, [self attention](000%20Attention%20is%20all%20you%20need.md#31-on-self-attention) is implemented using this Multi-Head Attention component passing to a forward pass neural net from what the paper describes.

### 2.1.1 Pend

# 3 Investigations

## 3.1 On Self Attention

We can find an explanation in  [Understanding Self-Attention - A Step-by-Step Guide](https://armanasq.github.io/nlp/self-attention/) [^2](000%20Attention%20is%20all%20you%20need.md#2).

The math there is similar to the one used in the [paper](https://arxiv.org/abs/1706.03762) [^1](000%20Attention%20is%20all%20you%20need.md#1). As explained in the [guide](https://armanasq.github.io/nlp/self-attention/) [^2](000%20Attention%20is%20all%20you%20need.md#2),

$$
\vec{Y} = \text{softmax}\lparen \frac{\vec{Q}\vec{K}^{T}}{\sqrt{d_k}} \rparen
$$

where

* $\text{softmax}$ is defined as in [3.2 On Softmax](000%20Attention%20is%20all%20you%20need.md#32-on-softmax)
* $d_k$ is the dimension of the vectors

### 3.1.1 Pend

## 3.2 On Softmax

[ai faq softmax](http://www.faqs.org/faqs/ai-faq/neural-nets/part2/section-12.html) [<a name="3" />^3](000%20Attention%20is%20all%20you%20need.md#3) shows that $\text{softmax}$ is defined as follows

### 3.2.1 Definition: Softmax

(1)

Let $\vec{X}$ be a vector of real-valued inputs of size `n` where `n` denotes the number of elements in $\vec{X}$.

So $\vec{X} \equiv \lbrace x_1, \cdots, x_n \rbrace$ .

(2)

Let $\vec{Y}$ be the output vector computed through $\text{softmax}$.

(`Defn`)

Then,

$$
y_i \equiv \frac{e^{x_i}}{\sum^n_{j=1}{e^{x_j}}}
$$

<a name="softmax-eq" />^softmax-eq

### 3.2.2 Constraints

As [ai faq softmax](http://www.faqs.org/faqs/ai-faq/neural-nets/part2/section-12.html) [^3](000%20Attention%20is%20all%20you%20need.md#3) explains,

$\text{softmax}$ allows the following properties to be satisfied for $\vec{Y}$ :

1. The range of $\vec{Y}$ is $[0, 1]$.  <a name="softmax-constr1" />^softmax-constr1
1. $\text{sum}(\vec{Y}) = 1$ <a name="softmax-constr2" />^softmax-constr2

#### 3.2.2.1 Showing property 1 holds

(`Prbl 1`)

Show that the range of $\vec{Y} = \text{softmax}(\vec{X})$  must be $[0, 1]$ .

(`Prms 1.1`)

The first property [^softmax-constr1](000%20Attention%20is%20all%20you%20need.md#softmax-constr1) will hold if and only if

$$
e^{x_i} \le \sum_{j=1}^n{e^{x_j}} \ \text{for any i}
$$

(`Prms 1.2`)

For any positive real number $a$, and any negative, zero, or positive real number $b$,

$a^b$ is always a positive number.

* When  $b \ge 1$, then $a^b \ge a$.
* When $b \in (0, 1)$, then $a^b \in (0, a)$.
* When $b = 0$, then $a^b = 1$.

(`Prs 1.3`)

Given (`Prms 1.2`) where $a=e$ and $b=x_i$ for any i,

we expect that $e^{x_j}$ in $\sum_{j=1}^n{e^{x_j}}$ to always be positive, and to also include $e^{x_i}$ in the sum, so it can only be equal to it or greater.

(`Cncl 1`)

Given (`Prms 1.3`), (`Prms 1.1`) must be satisfied.

#### 3.2.2.2 Showing property 2 holds

(`Prbl 2`)

For values $\vec{Y} \equiv \lbrace y_1, \cdots, y_n \rbrace$ computed via $\vec{Y} = \text{softmax}(\vec{X})$, we want to show that $\text{sum}(\vec{Y}) = 1$

(`Prms 2.1`)

The sum $\text{sum}(\vec{Y})$ is given by

$$
\begin{aligned}
&\text{sum}(\vec{Y}) \\
&= \sum_{i=1}^n {\frac{e^{x_i}}{\sum^n_{j=1}{e^{x_j}}}} \\
&= \frac{1}{\sum^n_{j=1}{e^{x_j}}}\sum_{i=1}^n {e^{x_i}} \\
&= \frac{\sum_{i=1}^n {e^{x_i}} }{\sum^n_{j=1}{e^{x_j}}} \\
&= 1
\end{aligned}
$$

(`Cncl 2`)

(`Prms 2.1`) shows through definition application of $\text{sum}$ and $\text{softmax}$  that the result must be 1.

#### 3.2.2.3 Corrections

2025-08-11 Wk 33 Mon - 04:34

Before I have written

 > 
 > (`Prms 2.1`)
 > $\sum^n_{j=1}{e^{x_j}}$ can be interpreted as a weighted average of elements with weighting $e^{x_j}$.

But really it's the total sum. The average (mean) of an n-sized set $A \equiv \lbrace a_1, \cdots, a_n \rbrace$ is $\frac{\sum_{i=1}^n{a_i}}{n}$.

I likely made this mistake because I was interpreting $\text{softmax}$  to have a proportion of part to whole (one factor to the total sum).

But either way, none of these premises are needed to prove the property. So (`Prms 2.1`) and (`Prms 2.2`) are removed, and (`Prms 2.3`) is renamed to (`Prms 2.1`).

 > 
 > (`Prms 2.1`)
 > $\sum^n_{j=1}{e^{x_j}}$ can be interpreted as a weighted average of elements with weighting $e^{x_j}$.
 > (`Prms 2.2`)
 > In

[^softmax-eq](000%20Attention%20is%20all%20you%20need.md#softmax-eq)

 > 
 > We can see that $e^{x_i}$ can  be interpreted as one weight from the elements that are taken a weighted average of in $\sum^n_{j=1}{e^{x_j}}$.

# 4 References

1. [paper: Attention Is All You Need](https://arxiv.org/abs/1706.03762) ^1

````bibtex
@misc{vaswani2023attentionneed,
      title={Attention Is All You Need}, 
      author={Ashish Vaswani and Noam Shazeer and Niki Parmar and Jakob Uszkoreit and Llion Jones and Aidan N. Gomez and Lukasz Kaiser and Illia Polosukhin},
      year={2023},
      eprint={1706.03762},
      archivePrefix={arXiv},
      primaryClass={cs.CL},
      url={https://arxiv.org/abs/1706.03762}, 
}
````

^quote-paper
^quote-paper-paraphrase

This paper will be annotated below for note taking purposes. All quotations will be marked to this paper.

2. [Understanding Self-Attention - A Step-by-Step Guide](https://armanasq.github.io/nlp/self-attention/) ^2

````mermaid
graph TD

%% Settings
classDef note fill:#f9f9a6,stroke:#333,stroke-width:1px,color:#000,font-style:italic;

%% Nodes
A1[^1 paper Attention is all you need]
A2[^2 article Understanding Self-Attention]
A4[^4 gh bertviz]
A5[^5 paper A Multiscale Visualization of Attention in the Transformer Model]

%% Connections
A2 --> |references| A4
A2 --> |explains| A1
A4 --> |has_paper| A5
````

3. [wiki: Softmax_function](https://en.wikipedia.org/wiki/Softmax_function) -> [ai faq softmax](http://www.faqs.org/faqs/ai-faq/neural-nets/part2/section-12.html) ^3

3. [gh jessevig/bertviz](https://github.com/jessevig/bertviz) <a name="4" />^4

3. [paper: A Multiscale Visualization of Attention in the Transformer Model](https://aclanthology.org/P19-3007.pdf) <a name="5" />^5

````bibtex
@inproceedings{vig-2019-multiscale,
    title = "A Multiscale Visualization of Attention in the Transformer Model",
    author = "Vig, Jesse",
    booktitle = "Proceedings of the 57th Annual Meeting of the Association for Computational Linguistics: System Demonstrations",
    month = jul,
    year = "2019",
    address = "Florence, Italy",
    publisher = "Association for Computational Linguistics",
    url = "https://www.aclweb.org/anthology/P19-3007",
    doi = "10.18653/v1/P19-3007",
    pages = "37--42",
}
````
