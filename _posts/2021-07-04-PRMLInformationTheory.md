---
layout: post
title:  "PRML's introduction to information theory"
date:   2021-07-04 14:57:15 +0800
categories: jekyll update
---

<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>

This is my note from reading section 1.6 of PRML. All credits went to PRML and Wikipedia.

#### Good  interpretation of information

The mount of information can be viewed as the ‘degree of surprise’ on learning the value of x. But how to quantitatively measure the information of a random event?

We‘d like to define a quantity $h(x)$ to represent the information of a random event $x$. It's natural that we want it to have the following desired properties:

1. It's a monotonic decreasing  function of $p(x)$. The less is the probability of $x$ , the more information its happening can bring us;
2. $h(1)=0$  An event that is certain to happen can bring us no surprise thus no information;
3.  Given $p(x,y)=p(x)p(y)$, $h(x,y)=h(x)+h(y)$. The information of two independent random event is the sum of their respective information.

These axiom assumptions leave us no choice but to define $h$ as:


$$
h(x)=-\log_2p(x)
$$


#### The entropy is the expected information of a random variable


$$
H(x)= \mathbb{E}(h(x))=-\sum_xp(x)\log_2p(x)
$$


Note that $\lim_{p\rightarrow 0^+} p\ln p = 0$ .

The noiseless coding theorem (Shannon, 1948) states that the entropy is a lower bound on the number of bits needed to transmit the state of a random variable.

#### The connection with entropy in physics

Let say there are $N$ identical particles in a closed container. They need to be divided  into a set of bins and the $i$th bin will contain $n_i$ particles. So using basic combination technique, we have


$$
W=\frac{N!}{\prod_i n_i!}
$$


One physics definition of entropy is the logarithm of $W$ normalized by the number of particles:


$$
H=\frac{1}{N}\ln \frac{N!}{\prod_in_i !}=\frac{1}{N}(\ln N!-\sum_i\ln n_i!)
$$


When $N$ approaches infinity and $n_i/N$ stay constant (which means $n_i$ approaches infinity , too),  we have the following approximation:


$$
\ln N!\approx N\ln N +N
$$


So:


$$
\begin{align}
H&=\ln N-1-\sum_i(\frac{n_i}{N}\ln n_i-\frac{n_i}{N})\\
&= -\sum_i(\frac{n_i}{N}\ln n_i-\frac{n_i}{N}+\frac{n_i}{N}-\frac{n_i}{N}\ln N)\\
&= -\sum_i\frac{n_i}{N}\ln \frac{n_i}{N}\\
&= -\sum_ip_i\ln p_i
\end{align}
$$


#### Discrete distribution that maximizes the entropy

Since all discrete probabilities must sum to one, we can write the Lagrange as:


$$
L=-\sum_ip(x_i)\ln p(x_i) + \lambda(\sum_ip(x_i)-1)
$$


We define $M$ as the number of possible values of $X$. By taking the partial derivative:


$$
\begin{align}
\frac{\partial L}{\partial p(x_i)} &= \lambda-\ln p(x_i) -1=0\\
\frac{\partial L}{\partial \lambda}&=\sum_i p(x_i)-1=0
\end{align}
$$


which has the following solution:


$$
\begin{align}
p(x_i)&=\frac{1}{M}\\
\lambda&=1-\ln M
\end{align}
$$


which means the entropy is maximized when all events happen with the same possibility.

#### Continuous distribution that maximizes the entropy

Because the calculus of variation is far beyond my math ability, I directly give the result. Gaussian maximize the entropy given the mean and variance. Its entropy can be written as:


$$
H(x)=\frac{1}{2}(1+\ln (2\pi \sigma^2))
$$


The derivation is:


$$
\begin{align}
H(x)&=-\int\mathcal{N}(x)\ln \mathcal{N}(x)dx\\
&=-\int\mathcal{N}(x)\frac{(x-\mu)^2}{-2\sigma^2}dx-\int\mathcal{N}(x)(-\frac{1}{2}\ln 2\pi\sigma^2)dx\\
&=\frac{1}{2}(1+\ln (2\pi \sigma^2))
\end{align}
$$


####  KL divergence and mutual information

Conditional entropy


$$
\begin{align}
H(Y|X) &=-\int\int p(x,y) \ln p(y|x)dxdy\\
H(Y,X)&=H(X)+H(Y|X)
\end{align}
$$


Relative entropy or KL-divergence:


$$
KL(p||q)=-\int p(x)\ln \frac{q(x)}{p(x)}dx
$$


Jensen's inequality, for convex functions:


$$
\begin{align}
\lambda f(a)+(1-\lambda)f(b)&\geq f(\lambda a+(1-\lambda )b)\\
\sum_i\lambda_if(x_i)&\geq f(\sum_i\lambda_ix_i)\\
\mathbb{E}(f(x))&\geq f(\mathbb{E}(x))
\end{align}
$$


Since $-\ln x$ is a convex function, $KL(p\lvert\rvert q)\geq 0$.

Practically, KL-divergence can be approximated as:


$$
KL(p||q)\approx\sum_{i}(-\ln q(x_i|\theta)+\ln p(x_i))
$$


where $\theta$  is the parameters to learn. So we can see that minimizing KL-divergence between model $q$ and true distribution $p$  is equivalent to maximizing the likelihood of the dataset. 

To capture the reduce of uncertainty of $X$ given the value of another random variable $Y$ (and vice versa), we define mutual information as:


$$
I(X;Y) =KL(p(x,y)||p(x)p(y))=\int\int p(x,y)\ln \frac{p(x,y)}{p(x)p(y)}dxdy
$$


It has the following property:


$$
I(X;Y)=H(X)-H(X|Y)=H(Y)-H(Y|X)=I(Y;X)
$$
