---
layout: post
title:  "Gamma Function, Beta Distribution and Conjugate Priori"
date:   2021-07-03 18:01:15 +0800
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

In this blog, I will briefly introduce Gamma function, then use it to construct Beta distribution and at last prove that Beta distribution is the conjugate priori of binomial distribution.

#### Gamma Function

We're all familiar with the recursive definition of factorial function:


$$
f(n)=nf(n-1),n\geq 1\\
f(0)=1\\
$$


so we have:


$$
f(n)=\prod_{i=1}^ni,n\geq 1
$$


However, in this form, the factorial function can only be applied to nonnegative integers. Gamma function, denoted by $\Gamma$, is a common extension of the factorial function to complex numbers. Since the topic I'm going to talk about has nothing to do with complex numbers, I restrict myself to the domain of nonnegative real numbers. The Gamma function is defined as :


$$
\Gamma(x)=\int_0^\infty t^{x-1}e^{-t}dt
$$


We now show that it did have similar properties with the factorial function:


$$
\begin{align}
\Gamma(1)&=\int_0^\infty e^{-t}dt=1\\
\Gamma(n+1)&=\int_0^\infty t^ne^{-t}dt\\
&= (-e^{-t}t^n)|^\infty_0-\int nt^{n-1}(-e^{-t})dt\\
&= n\Gamma(n)
\end{align}
$$


That means for positive integers$\Gamma(n)=(n-1)!$

#### Beta distribution

We're all familiar with the binomial distribution:


$$
p(k)=\tbinom{k}{N}p^k(1-p)^{N-k}=\frac{N!}{k!(N-k)!}p^k(1-p)^{N-k}
$$


By replacing factorial function with Gamma function, we have:


$$
p(k)=\frac{\Gamma(N+1)}{\Gamma(k+1)\Gamma(N-k+1)}p^k(1-p)^{N-k}
$$


By treating $\alpha=k+1,\beta=N-k+1$ as parameter and $x=p$ as random variable, we have the common representation of Beta distribution:


$$
f(x;\alpha,\beta)=\frac{\Gamma(\alpha+\beta)}{\Gamma(\alpha)\Gamma(\beta)}x^{\alpha-1}(1-x)^{\beta-1}
$$

#### Conjugate Priori

We now show that when the priori distribution of model parameters $\mu$ is a Beta distribution and the likelihood of data $x$ given $\mu$ is binomial, the posterior distribution of $\mu$ given $x$ is also a Beta distribution:


$$
\begin{align}
p(x|\mu)p(\mu) &= \frac{N!}{x!(N-x)}\mu^x(1-\mu)^{N-x}\frac{\Gamma(\alpha+\beta)}{\Gamma(\alpha)\Gamma(\beta)}\mu^{\alpha-1}(1-\mu)^{\beta-1}\\
&= \frac{\Gamma(x+y+1)}{\Gamma(x+1)\Gamma(y+1)}\frac{\Gamma(\alpha+\beta)}{\Gamma(\alpha)\Gamma(\beta)}\mu^{\alpha+x-1}(1-\mu)^{\beta+y-1}
\end{align}
$$


and we have 


$$
\begin{align}
p(x)&=\int p(x|\mu)p(\mu)d\mu\\
&= \frac{\Gamma(x+y+1)}{\Gamma(x+1)\Gamma(y+1)}\frac{\Gamma(\alpha+\beta)}{\Gamma(\alpha)\Gamma(\beta)}\int\mu^{\alpha+x-1}(1-\mu)^{\beta+y-1}d\mu\\
\end{align}
$$


Finally,


$$
\begin{align}
p(\mu|x) &=\frac{p(x|\mu)p(\mu)}{p(x)}\\
&= \frac{\mu^{\alpha+x-1}(1-\mu)^{\beta+y-1}}{\int\mu^{\alpha+x-1}(1-\mu)^{\beta+y-1}d\mu}\\
&= \frac{\Gamma(\alpha+x+\beta+y)}{\Gamma(\alpha+x)\Gamma(\beta+y)}\mu^{\alpha+x-1}(1-\mu)^{\beta+y-1}\\
&= Beta(\mu;\alpha+x,\beta+y)
\end{align}
$$


The tricky proof of $\int_0^1\mu^{\alpha-1}(1-\mu)^{\beta-1}d\mu=\frac{\Gamma(\alpha+\beta)}{\Gamma(\alpha)\Gamma(\beta)}$ can be found in [Wikipedia](https://zh.wikipedia.org/zh-hans/%CE%92%E5%87%BD%E6%95%B0#%E4%BC%BD%E7%8E%9B%E5%87%BD%E6%95%B0%E4%B8%8E%E8%B4%9D%E5%A1%94%E5%87%BD%E6%95%B0%E4%B9%8B%E9%97%B4%E7%9A%84%E5%85%B3%E7%B3%BB).
