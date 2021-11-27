`r if (knitr::is_latex_output()) '
<div id="refs"></div>
\\pagebreak
'`

# (APPENDIX) Appendix {-} 

# Short-Time Fourier Transform {#istft}

## Parseval's formula

Parseval's formula for the Fourier transform can be transposed
to the STFTs of two signals.

:::{.theorem #parseval name="Parseval's formula"}
Consider the STFT of two signals $s1$ and $s2$ taken over
the windows $w_1$ and $w_2$ respectively.
\begin{equation}
\dotp{V_{w_1}s_1,V_{w_2}s_2}_{L^2(\R^2)} =
    \dotp{s_1,s_2}_{L^2(\R)} \bbar{\dotp{w_1,w_2}}_{L^2(\R)}
\end{equation}
:::

::: {.proof}
In order to prove Parseval's formula, we use Fubini's theorem
to interchange the order of integration, we assume that $\bbar{w_1}w_2\in L^1(\R)$.
We also make use of the fact that
\begin{equation}
\delta(t)=\int_\R e^{-2\pi iwt}\dw,\quad\forall t\in\R
\end{equation}
where $\delta$ is the Dirac distribution
\begin{align}
\dotp{V_{w_1}s_1,V_{w_2}s_2}_{L^2(\R^2)}
&= \int_{\R^2} \dotp{s_1,M_\w T_\tau w_1} \bbar{\dotp{s_2,M_\w T_\tau w_2}} \dtau\dw\\
&= \int_{\R^2} \int_\R s_1(t)\bbar{M_\w T_\tau w_1(t)}\dt
               \int_\R \bbar{s_2(t')} M_\w T_\tau w_2(t') \dt' \dtau\dw\\
&= \int_{\R^4} s_1(t)\bbar{T_\tau w_1(t)}
    \bbar{s_2(t')} T_\tau w_2(t') e^{-2\pi i\w (t-t')} \dt\dt'\dtau\dw\\
&= \int_{\R^3} s_1(t)\bbar{T_\tau w_1(t)} \bbar{s_2(t')} T_\tau w_2(t')
    \bbar{\int_\R e^{2\pi i\w (t-t')}\dw} \dt\dt'\dtau\\
&= \int_{\R^3} s_1(t)\bbar{T_\tau w_1(t)} \bbar{s_2(t')} T_\tau w_2(t')
    \bbar{\delta(t-t')} \dt\dt'\dtau\\
&= \int_{\R^2} s_1(t)\bbar{T_\tau w_1(t)}
    \int_\R \bbar{s_2(t')} T_\tau w_2(t') \bbar{\delta(t-t')}\dt'\dt\dtau\\
&= \int_{\R^2} s_1(t)\bbar{T_\tau w_1(t)} \bbar{s_2(t)} T_\tau w_2(t)\dt\dtau\\
&= \int_\R s_1(t)\bbar{s_2(t)} \int_\R\bbar{T_\tau w_1(t)} T_\tau w_2(t)\dtau\dt\\
&= \int_\R s_1(t)\bbar{s_2(t)} \int_\R\bbar{w_1(t-\tau)} w_2(t-\tau)\dtau\dt\\
&= \dotp{s_1,s_2}_{L^2(\R)} \bbar{\dotp{w_1,w_2}}_{L^2(\R)}
\end{align}
:::

which implies that $\norm{V_w s}_2 = \norm{s}_2 \norm{w}_2$, and for $\norm{w}_2=1$ we have
\begin{equation}\label{eq:isometry}
\norm{V_w s}_2 = \norm{s}_2,\quad s\in L^2(\R)
\end{equation}

This means that the STFT operator $V_w$ is an isometry from $L^2(\R)$
to $L^2(\R^2)$ if $\norm{w}_2=1$.

## Inverse Short-Time Fourier Transform

:::{.theorem name="STFT inversion formula"}
Let $w,h\in L^2(\R)$ with $\dotp{w,h}\neq0$. Then for all $s\in L^2(\R)$.
\begin{equation}
s(t) = \frac{1}{\dotp{w,h}} \iint_{\R^2}V_w s(\tau,\w)M_\w T_\tau h(t) \dw\dtau
\end{equation}
:::

::: {.proof}
Since $V_w s\in L^2(\R^2)$, let $\tilde s\in L^2(\R)$ be defined as
\begin{equation}
\tilde s(t) = \frac{1}{\dotp{w,h}} \iint_{\R^2}V_w s(\tau,\w)M_\w T_\tau h(t) \dw\dtau
\end{equation}
We therefore have, for all $\phi\in L^2(\R)$, using Parseval's formula
\begin{align}
\dotp{\tilde s, \phi}_{L^2(\R)}
&= \frac{1}{\dotp{w,h}} \iint_{\R^2} V_w s(\tau,\w) \dotp{M_\w T_\tau h, \phi}_{L^2(\R)} \dw\dtau\\
&= \frac{1}{\dotp{w,h}} \iint_{\R^2} V_w s(\tau,\w) \bbar{\dotp{\phi,M_\w T_\tau h}}_{L^2(\R)} \dw\dtau\\
&= \frac{1}{\dotp{w,h}} \iint_{\R^2} V_w s(\tau,\w) \bbar{V_h \phi(\tau,\w)} \dw\dtau\\
&= \frac{1}{\dotp{w,h}} \dotp{V_w s, V_h \phi}_{L^2(\R^2)}\\
&= \dotp{s, \phi}_{L^2(\R)}
\end{align}

Since $\dotp{\tilde s,\phi}_{L^2(\R)}=\dotp{s,\phi}_{L^2(\R)},\forall\phi\in L^2(\R)$
then in the weak sense $\tilde s = s$.
:::

It is worth noting that this proof is better expressed with concepts of operator algebra.
Let's consider $F$ a function on $E$ such that $F(x)\in B$ forall $x\in E$,
where $B$ is a Banach space, we therefore have
\begin{equation}
f = \int_E F(x)\dx \implies \dotp{f,\phi} = \int_E \dotp{F(x),\phi} \dx
\end{equation}
for all $\phi\in B^*$ where $B^*$ is the dual space to $B$.
Moreover, if the mapping $\ell(\phi)\mapsto\int_E \dotp{F(x),\phi}\dx$
is a bounded linear operator on $B^*$, then $\ell$ defines a unique element $f\in B^{**}$.
If $B$ is a reflexive Banach space $\pp{B^{**}=B}$, we have a $f\in B$.
In our case, $E=\R^2$ and $F\in L^2(\R^2)$. Let $f$ be defined as
\begin{equation}
f = \iint_{\R^2} F(\tau,\w)M_\w T_\tau \gamma \dw\dtau
\end{equation}
The operator $\ell$ is then defined as
\begin{equation}
\ell(\phi) = \iint_{\R^2} F(\tau,\w)\dotp{M_\w T_\tau \gamma,\phi}_{L^2(\R)} \dw\dtau
          = \iint_{\R^2} F(\tau,\w) \bbar{\dotp{V_\gamma \phi}}_{L^2(\R)} \dw\dtau
\end{equation}
We prove $\ell$ is a bounded operator on $B^*=L^2(\R)$ with the help
of the Cauchy-Schwartz inequality and Parseval's formula.

\begin{equation}
\forall\phi\in L^2(\R),\abs{\ell(\phi)} \leq \norm{F}_2 \norm{V_\gamma \phi}_2
= \norm{F}_2 \norm{\gamma}_2 \norm{\phi}_2
\end{equation}
Which implies that $\ell$ defines a unique function $f\in L^2(\R)$ such that
$\dotp{f,\phi}_{L^2(\R)} = \ell(\phi),\forall\phi\in L^2(\R)$.