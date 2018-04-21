title: Convex Optimization Cheat Sheet
date: 2018-04-21 15:00:59
---

This post is a cheat sheet about common inequalities in convex optimization field, which are often used in theoretical proof of various algorithms for solving convex problems.

<!--more-->

### Notations
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

* $\mu\|x - y\|^2 \le \langle \nabla f(x) - \nabla f(y), x - y \rangle $, i.e., the subgradient of convex $f$ is a strongly monotone operator



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
