# Uncertainty principle {#uncertainty}

The Heisenberg-Gabor limit, is a direct result
of the Heisenberg-Pauli-Weyl inequality [@grochenig2001].

:::{.theorem name="Heisenberg-Pauli-Weyl inequality"}
Let $f\in L^2(\R)$, then $\forall a,b\in\R$
\begin{equation}
\pp{\int_\R (t-a)^2\abs{f(t)}^2\dt}^{1/2}
\pp{\int_\R (\w-b)^2\abs{\hat f(\w)}^2\dw}^{1/2}
\geq \frac{\norm{f}_2^2}{4\pi}
\end{equation}
:::

:::{.proof}
We aim to prove the uncertainty principle for self-adjoint operators
on a Hilbert space $H$ in the general case.
We then introduce self-adjoint operators on a Hilbert subspace of $L^2(\R)$
giving rise to the uncertainty principle for realizable signals
(the Heisenberg-Pauly-Weyl inequality).

:::{.lemma}
Let $A$ and $B$ be two self-adjoint linear operators
on a Hilbert space $H$, whose commutator is defined as
\begin{equation}
[A,B]=AB-BA
\end{equation}
Then for all $a,b\in\R$ and $f\in H$.
\begin{equation}
\norm{(A-a)f}\norm{(B-b)f}\geq \frac{1}{2}\abs{\dotp{[A,B]f,f}}
\end{equation}
:::

::: {.proof}
We first notice that the commutator is invariant by translation, that is $\forall a,b\in\R$
\begin{align*}
[A-a,B-b] &= (A-a)(B-b) - (B-b)(A-a)\\
    &= (AB-bA-aB+ab) - (BA-aB-bA+ab)\\
    &= AB - BA\\
    &= [A,B]
\end{align*}
We remind that an operator $A$ is self-adjoint if
\begin{equation}
\forall f,g\in H, \dotp{Af,g} = \dotp{f,Ag}
\end{equation}

We then deduce that $\forall f\in H$
\begin{align*}
\dotp{[A,B]f,f} &= \dotp{[A-a,B-b]f,f} \tag{translation invariance}\\
    &= \dotp{((A-a)(B-b)-(B-b)(A-a))f,f} \tag{bilinearity}\\
    &= \dotp{(A-a)(B-b)f,f} - \dotp{(B-b)(A-a)f,f}\\
    &= \dotp{(B-b)f,(A-a)f} - \dotp{(A-a)f,(B-b)f} \tag{self-adjointness}\\
    &= \dotp{(B-b)f,(A-a)f} - \bbar{\dotp{(B-b)f,(A-a)f}}\\
    &= 2i~\Im\dotp{(B-b)f,(A-a)f} \tag{$z-\bar z = 2i~\Im(z),\forall z\in\C$}
\end{align*}
Consequently,
\begin{align*}
\abs{\dotp{[A,B]f,f}} &= 2\abs{\Im\dotp{(B-b)f,(A-a)f}}\\
    &\leq 2\abs{\dotp{(B-b)f,(A-a)f}}\\
    &\leq 2\norm{(B-b)f}\norm{(A-a)f} \tag{Cauchy-Schwartz inequality}
\end{align*}
:::

We now consider the following operators
\begin{equation} 
\begin{cases}
Xf(t) = t\cdot f(t)\\
Pf(t) = \frac{1}{2\pi i}f'(t)
\end{cases}
\end{equation}
defined over
$H=\sset{f\in L^2(\R)\vert f', t\mapsto tf(t),t\mapsto tf'(t)\in L^2(\R)}\subset L^2(\R)$.

We first prove that these operators are self-adjoint, let $f,g\in H$
\begin{equation} 
\dotp{Xf,g} =\int_\R tf(t)\bbar{g(t)}\dt=\int_\R f(t)\bbar{tg(t)}\dt = \dotp{f,Xg}
\end{equation}

Similarly,
\begin{align*}
\dotp{Pf,g} &= \dotp{\frac{1}{2\pi i}f',g}\\
    &= \frac{1}{2\pi i}\int_\R f'(t)\bbar{g(t)}\dt\\
    &= -\frac{1}{2\pi i}\int_\R f(t)\bbar{g'(t)}\dt \tag{integration by parts}\\
    &= \int_\R f(t)\bbar{\frac{1}{2\pi i}g'(t)}\dt\\
    &= \dotp{f,\frac{1}{2\pi i}g'}\\
    &= \dotp{f,Pg}
\end{align*}

The operators are linear since multiplication and derivation are linear. 

The commutator of $X$ and $P$ applied to $f\in H$,
\begin{align*}
[X,P]f(t) &= XPf(t) - PXf(t)\\
    &= X\pp{\frac{1}{2\pi i}f'(t)} - P\pp{tf(t)}\\
    &= \frac{t}{2\pi i}f'(t) - \frac{1}{2\pi i}\pp{f(t) + tf'(t)}\\
    &= \frac{t}{2\pi i}f'(t) - \frac{1}{2\pi i}f(t) - \frac{t}{2\pi i}f'(t)\\
    &= -\frac{1}{2\pi i}f(t)
\end{align*}

Hence $\forall f\in H$
\begin{equation}
\dotp{[X,P]f,f} = \dotp{-\frac{1}{2\pi i}f,f} = \frac{i}{2\pi}\dotp{f,f}
= \frac{i}{2\pi}\norm{f}_2^2
\end{equation}

Consequently
\begin{equation}\label{eq:hilb_op}
\abs{\dotp{[X,P]f,f}} = \frac{1}{2\pi}\norm{f}_2^2
\end{equation}

As $X$ and $P$ are self-adjoint linear operators on the Hilbert space $H$,
then following the lemma we have $\forall a,b\in\R,\forall f\in H$.

\begin{equation}
\norm{(X-a)f}_2 \norm{(P-b)f}_2 \geq \frac{1}{2}\abs{\dotp{[X,P]f,f}}
\end{equation}

Moreover, from equation (\ref{eq:hilb_op}) we have
\begin{equation}
\norm{(X-a)f}_2 \norm{(P-b)f}_2 \geq \frac{1}{4\pi}\norm{f}_2^2
\end{equation}

We develop the left-hand side
\begin{equation}\label{eq:delta_t}
\norm{(X-a)f}_2 = \pp{\int_\R \abs{(X-a)f(t)}^2\dt}^{1/2}
= \pp{\int_\R (t-a)^2\abs{f(t)}^2\dt}^{1/2}
\end{equation}

For the second factor, we apply Parseval's formula
$\norm{(P-b)f}_2 = \norm{\F\sset{(P-b)f}}_2$.

\begin{align*}
\F\sset{(P-b)f}(\w) &= \F\sset{\frac{1}{2\pi i}f' - bf}(\w)\\
    &= \frac{1}{2\pi i}\hat{f'}(\w) - b\hat{f}(\w)\tag{linearity of $\F\{\cdot\}$}\\
    &= \w\hat{f}(\w) - b\hat{f}(\w)\tag{derivation of $\F\{\cdot\}$}\\
    &= (\w-b)\hat{f}(\w)
\end{align*}

Hence
\begin{equation}\label{eq:delta_w}
\norm{(P-b)f}_2 = \norm{(\w-b)\hat{f}}_2
= \pp{\int_\R (\w-b)^2\abs{\hat{f}(\w)}^2\dw}^{1/2}
\end{equation}

By injecting (\ref{eq:delta_t}) and (\ref{eq:delta_w}) in (\ref{eq:hilb_op})
we have $\forall a,b\in\R,\forall f\in H$
\begin{equation}
\pp{\int_\R (t-a)^2\abs{f(t)}^2\dt}^{1/2}
\pp{\int_\R (\w-b)^2\abs{\hat{f}(\w)}^2\dw}^{1/2}
\geq \frac{1}{4\pi}\norm{f}_2^2
\end{equation}
:::