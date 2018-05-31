title: Convex Optimization Cheat Sheet
date: 2018-04-21 15:00:59
---

This cheat sheet lists some common inequalities in convex optimization field. These inequalities are often used in theoretical proof of various algorithms for solving convex problems.

<!--more-->

First, we defind the notations we are going to use later:

* $\mathcal{X} \subset \mathbb{R}^d$, a compact convex set

* $f$: $\mathcal{X} \rightarrow \mathbb{R}$, a convex function

* $x, y$: arbitrary vectors in $\mathcal{X}$

* $x^*$: the minimizer of $f$

* $\partial f(x)$: the subgradient set of $f(x)$, note that we also use the same notation to denote some subgradient in the set when there is no ambiguity.

* $\|\cdot\|$: standard Euclidean norm.

### $f$ is convex
* $f(\lambda x + (1 - \lambda) y) \le \lambda f(x) + (1-\lambda) f(y)$, for any $\lambda \in [0, 1]$ (definition)

* $f(x) - f(y) \le \langle \partial f(x), x - y \rangle$, when $f$ is differentiable, $\nabla f(x) \in \partial f(x)$

* $\langle \partial f(x) - \partial f(y), x - y \rangle \ge 0$, i.e., the subgradient of convex $f$ is a monotone operator

### $f$ is $\mu$-strongly convex
* $f(x) - f(y) \le \langle \partial f(x), x - y \rangle - \frac{\mu}{2} \|x - y\|^2$

* $f(\gamma x + (1 - \gamma y)) \le \gamma f(x) + (1 - \gamma) f(y) - \frac{\mu}{2} \gamma (1 - \gamma) \|x - y\|^2, \forall \gamma \in [0, 1]$

* $\mu\|x - y\|^2 \le \langle \nabla f(x) - \nabla f(y), x - y \rangle$, i.e., the subgradient of convex $f$ is a strongly monotone operator



### $f$ is L-smooth
* $\| \nabla f(x) - \nabla f(y)\| \le L\|x - y\|$

* $|f(x) - f(y) - \langle \nabla f(y), x - y \rangle| \le \frac{L}{2} \|x - y\|^2$

* $\nabla^2 f(x) \preceq L$, i.e., if $f$ is twice differentiable, then the Hessian is $L$-Lipschitz

### $f$ is convex and $L$-smooth
* $\frac{1}{L}\| \nabla f(x) - \nabla f(y)\|^2 \le \langle \nabla f(x) - \nabla f(y), x - y \rangle$

* $0 \le f(x) - f(y) - \langle \nabla f(y), x - y \rangle \le \frac{L}{2} \|x - y\|^2$

* $f(x) - f(y) \le \langle \nabla f(x), x - y \rangle - \frac{1}{2L} \|\nabla f(x) - \nabla f(y)\|^2$

### $f$ is $L$-smooth and $\mu$-strongly convex
* $\frac{\mu L}{\mu + L}\|x - y\|^2 + \frac{1}{\mu + L}\|\nabla f(x) - \nabla f(y)\|^2 \le \langle \nabla f(x) - \nabla f(y), x - y\rangle$

* $\| x - y - \epsilon(\nabla f(x) - \nabla f(y))\| \le \mathrm{max} \big\{ |1 - \epsilon \mu|, |1 - \epsilon L| \big\} \| x - y \|$

* $(1-\epsilon L) I \preceq I - \epsilon \nabla^2 f \preceq (1-\epsilon \mu) I$, suppose that $f$ is twice differentiable and $\epsilon \ge 0$

* $\mu I \preceq \nabla^2 f \preceq L I$, suppose that $f$ is twice differentiable

### Other useful stuff

#### Average Inequalities
Assume that $a_i \ge 0$ for $i=1,\ldots,n$, then

$$
\sqrt{\frac{\sum_{i=1}^{n} a_i^2}{n}} \ge \frac{1}{n} \sum_{i=1}^{n} a_i \ge \sqrt{\prod_{i=1}^{n} a_i} \ge \frac{n}{\sum_{i=1}^{n} a_i}
$$

平方平均数 $\ge$ 算术平均数 $\ge$ 几何平均数 $\ge$ 调和平均数 （方算几调）

#### Cauchy-Schwarz Inequalities
The general form is read as $\langle x, y \rangle \le \|x\| \cdot \|y\|$.

We present two useful special cases (sum and expectation) as follows.

$$
\begin{aligned}
\sum_{i=1}^{n} a_i^2 \sum_{i=1}^{n} b_i^2 &\ge \big( \sum_{i=1}^{n} a_i b_i \big)^2  \;\;\; \mathrm{(sum)},  \\[10pt]
| \mathbb{E}(x, y) |^2 &\le \mathbb{E}(x^2) \mathbb{E}(y^2) \;\;\; \mathrm{(expectation)}
\end{aligned}
$$

#### Yong's Inequality

General form: assume that $a, b \ge 0$, $p, q \ge 0$ and $\frac{1}{p} + \frac{1}{q} = 1$, then

$$
ab \le \frac{a^p}{p} + \frac{b^q}{q}
$$

Two useful special cases:

* $ab \le \frac{a^2}{2} + \frac{b^2}{2}$

* $ab \le \frac{a^2}{2\epsilon} + \frac{\epsilon b^2}{2}$ (rob Peter to pay Paul，拆东墙补西墙)

#### Hölder's Inequality
Let $(S, \Sigma, \mu)$ be a measure space, $p, q \in [1, \infty]$, $1/p + 1/q = 1$. Then for all measurable real or complex functions $f$ and $g$ on $S$,

$$
\|f g\|_1 \le \|f\|_p \|g\|_q.
$$

#### Taylor's Expansion
Let $n \ge 1$ be an integer and let the function $f$: $\mathbb{R} \rightarrow \mathbb{R}$ be $n+1$ times differentiable on the open interval with $f^{(n)}$ continuous on the closed interval between $x$ and $a$. Then there exists a point $\theta$ between $a$ and $x$ such that

$$
f(x) = f(a) + \frac{f'(a)}{1!} (x - a) + \frac{f^{(2)}(a)}{2!} (x - a)^2 + \ldots + \frac{f^{(n)}(a)}{n!} (x - a)^n + \frac{f^{n+1}(\theta)}{(n+1)!} (x - a)^{(n+1)}
$$

#### Inequalities of some exponential functions
$$
\begin{aligned}
&(1 + ax)^{b/x} \le e^{ab}, \;\;\;\; \mathrm{if } ax \ge -1, b/x \ge 0.  \\[10pt]
%
&1 - x + \frac{x^2}{2} \ge e^{-x}, \;\;\;\; \mathrm{if } x \ge 0.  \\[10pt]
&(1 + x)^r \begin{cases}
\le 1 + rx, \;\;\;\; \mathrm{if } x \ge -1, 0 \le r \le 1, \\
%
\ge 1 + rx, \;\;\;\; otherwise.
\end{cases} \\[10pt]
%
&1 + x \le e^x, \forall x \in \mathbb{R}.
\end{aligned}
$$
