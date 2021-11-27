# Sound reconstruction model

## From V1 to A1

The motivation behind the application of the model in sound reconstruction
is the idea that a sound can be seen as an image in the time-frequency domain.
The primary auditory cortex A1 receives the sensory input directly from the cochlea [@dallos1996],
which is a spiral-shaped fluid-filled cavity that composes the inner ear.

The mechanical vibrations along the basilar membrane are transduced into electrical activity
along a dense, topographically ordered, array of auditory-nerve fibers
which convey these electrical potentials to the central auditory system.

Since these auditory-nerve fibers (sensors or inner hair cells) are topographically ordered
along the cochlea spiral, different regions of the cochlea are sensitive to different frequencies.
Hair cells close to the base are more sensitive to low-frequency sounds and those near the apex
are more sensitive to high-frequency sounds [@yang1992].

![Perceived pitch of a sound depends on the location in the cochlea that the sound wave stimulated [@boscain2021].](img/cochlea.png){width=50%}

This spatial segregation of frequency sensitivity in the cochlea
means that the primary auditory cortex receives a time-frequency representation of the sound.
In this model, we consider the Short-Time Fourier Transform (STFT)
as the time-frequency representation $S(\tau,\w)$ of a sound signal $s\in L^2(\R)$

While the spectrogram of a sound signal $\abs{S}(\tau,\w)$ is an image,
the image reconstruction algorithm cannot be applied to a corrupted sound
since the rotated spectrogram would correspond to completely different input sound
therefore the invariance by rototranslation is lost.
Moreover, the image reconstruction would evolve the WC equation on the entire
image simultaneously. However, the sound image (spectrogram) does not reach
the auditory cortex simultaneously but sequentially.
Hence, the reconstruction can be performed only in a sliding window [@boscain2021].

## Sound reconstruction pipeline

As discussed in the previous section, the sound reconstruction model
is inspired by the V1 model.
First, a 2-dimensional image of the sound signal is obtained via a short-time Fourier transform,
which is analogous to the spectrogram the cochlea transmits to the auditory cortex.
The time derivative of the frequency $\nu=\dw/\dtau$, corresponding to the chirpiness of the sound,
allows adding a new dimension to the sound image.
Afterwards, the sound image is lifted into an *augmented space* that is $\R^3$
with the Heisenberg group structure.
Henceforth, the sound is processed in its 3D representation,
that is the obtained lift $L(\tau,\w,\nu)$.

Similarly to the V1 model, the sound is reconstructed by solving
the Wilson-Cowan integro-differential equation.

Finally, the solution to the Wilson-Cowan equations is projected into
the time-frequency representation which gives a sound signal through
an inverse short-time Fourier transform [@boscain2021].

![Sound reconstruction pipeline](img/pipeline.png){width=70%}

## Time-Frequency representation

The Fourier transform transforms a time signal $s\in L^2(\R)$
into a complex function of frequency $\hat s\in L^2(\R)$.
Since the time signal $s$ can be obtained from $\hat s$
using the Inverse Fourier Transform, they both contain
the exact same information.
Conceptually, $s$ and $\hat s$ can be considered two equivalent
representations of the same object $s$, but each one
makes visible different features of $s$.

A time-frequency representation would combine the features
of both $s$ and $\hat s$ into a single function.
Such representation provides an *instantaneous frequency spectrum*
of the signal at any given time [@grochenig2001].

### The Short-Time Fourier Transform

The Short-Time Fourier Transform (STFT) is a very common
Time-Frequency representation of a signal.
The principle of the STFT is quite straightforward.
In order to obtain a Time-Frequency representation of a signal $s$,
a Fourier transform is taken over a restricted interval
of the original signal *sequentially*.
Since a sharp cut-off introduces discontinuities and aliasing issues,
a smooth cut-off is prefered [@grochenig2001].
This is established by multiplying a segment of the signal by a weight function,
that is smooth, compactly supported, and centered around $0$,
referred to as *window*.
Essentially, the STFT $S(\tau,\w)$ is the Fourier transform of $s(t)w(t-\tau)$
(the signal taken over a sliding window along the time axis.)

\begin{equation}
S(\tau,\w) = \int_\R s(t)w(t-\tau)e^{-2\pi i\w t} \dt
\end{equation}

![Signal windowing for the STFT [@grochenig2001]](img/stft_grochenig.png){width=37%}

### Time and frequency shifts operators

In our study, we consider realizable signals $s\in L^2(\R)$.
Fundamental operators in time-frequency analysis are
time and phase shifts acting on realizable signals $s\in L^2(\R)$.

- **Time shift operator:** $T_\tau s(t)=s(t-\tau)$
- **Phase shift operator:** $M_\w s(t)=e^{2\pi i \w t} s(t)$

We notice that the STFT can be formulated using these unitary operators

\begin{align}
S(\tau,\w) &= \int_\R s(t)w(t-\tau)e^{-2\pi i\w t} \dt\\
    &= \int_\R s(t) \overline{M_\w T_\tau w(t)} \dt\\
    &= \dotp{s, M_\w T_\tau w}_{L^2(\R)}
\end{align}

We can redefine the STFT as an operator $V_w$ on $s\in L^2(\R)$
defined in function of $T_\tau$ and $M_\w$ [@grochenig2001;@boscain2021].

\begin{equation}\label{eq:stft_operator}
V_w s(\tau,\w) = \dotp{s, M_\w T_\tau w}_{L^2(\R)}
\end{equation}

### Discrete STFT

Similarly to the continuous STFT, the discrete STFT is the
Discrete Fourier Transform (DFT) of the signal over a sliding window.
Nevertheless, the window cannot slide continuously along the time axis,
instead the signal is windowed at different frames with an overlap.
The window therefore hops along the time axis.

Let $N$ be the window size (DFT size), we define the overlap $R$
as the number of overlapping frames between two consecutive windows.
The hop size is therefore defined as $H=N-R$.
We also define the overlap ratio as the ratio of the overlap
with respect to the window size $r=R/N$ where $r\in[0,1[$.

The discrete STFT of a signal $s\in L^2([0,T])$ is therefore
\begin{equation}
S[m,\w] = \sum_{t=0}^{T} s[t]w[t-mH]e^{-2\pi i\w t}
\end{equation}

The choice of parameters has direct influence over the discrete STFT
resolution, as well as its invertibility.

### STFT windowing

The choice of the window affects quality of the Fourier transform.
One should choose a window with anti-aliasing and that distributes
spectral leakage.

\begin{figure}[H]
	\centering
	\begin{subfigure}[t]{\textwidth}
		\centering
		\includegraphics[width=0.6\textwidth]{img/w_rectangle.png}
		\caption{Rectangular window}
	\end{subfigure}
	\begin{subfigure}[t]{\textwidth}
		\centering
		\includegraphics[width=0.6\textwidth]{img/w_triangle.png}
		\caption{Triangular window}
	\end{subfigure}
	\begin{subfigure}[t]{\textwidth}
		\centering
		\includegraphics[width=0.6\textwidth]{img/w_hann.png}
		\caption{Hann window}
	\end{subfigure}
    \caption{Different windows (left) and their respective Fourier transform (right)}
\end{figure}

`r if (is_html_output()) '
![(a) Rectangular window](img/w_rectangle.png)

![(b) Triangular window](img/w_triangle.png)

![(c) Hann window](img/w_hann.png)

<p class="caption">Different windows (left) and their respective Fourier transform (right)</p>
'`

Moreover, as we will see in the next sections, the STFT is invertible.
However, the STFT parameters need to satisfy the two following constraints [@muller2015;@griffin1983]:

- **Nonzero OverLap Add (NOLA):** $\sum\limits_{m\in\Z} w^2[t-mH] \neq 0$
- **Constant OverLap Add (COLA):** $\sum\limits_{m\in\Z} w[t-mH] = 1$

The NOLA condition is met for any window given an overlap ratio $r\in[0,1[$.
It is worth noting that this condition can be found without the square
depending on the inverse STFT algorithm.

The COLA constraint defines the partition of unity over the discrete time axis,
imposing a stronger condition.

\begin{figure}[H]
	\centering
	\begin{subfigure}[t]{\textwidth}
		\centering
		\includegraphics[width=0.45\textwidth]{img/woa_triangular_1_2.png}
		\caption{Triangular window, overlap ratio $r=\frac{1}{2}$}
	\end{subfigure}
	\begin{subfigure}[t]{\textwidth}
		\centering
		\includegraphics[width=0.45\textwidth]{img/woa_hann_1_2.png}
		\caption{Hann window, overlap ratio $r=\frac{1}{2}$}
	\end{subfigure}
	\begin{subfigure}[t]{\textwidth}
		\centering
		\includegraphics[width=0.45\textwidth]{img/woa_hann_3_8.png}
		\caption{Hann window, overlap ratio $r=\frac{3}{8}$}
	\end{subfigure}
    \caption{The COLA condition with different windows and overlap ratios}
\end{figure}

`r if (is_html_output()) '
![(a) Triangular window, overlap ratio $r=\\frac{1}{2}$](img/woa_triangular_1_2.png)

![(b) Hann window, overlap ratio $r=\\frac{1}{2}$](img/woa_hann_1_2.png)

![(c) Hann window, overlap ratio $r=\\frac{3}{8}$](img/woa_hann_3_8.png)

<p class="caption">The COLA condition with different windows and overlap ratios</p>
'`

In typical applications, the window functions used are non-negative,
smooth, bell-shaped curves [@roads2002].
A comprehensive list of windows and their properties may be found in [@heinzel2002].
In our model we use the Hann window, which satisfies the COLA condition
for any overlap ratio of $r=\frac{n}{n+1},n\in\N^*$.

The Hann window of length $L$ is defined as
\begin{equation}
w(x)=\begin{cases}
\frac{1+\cos\pp{\frac{2\pi x}{L}}}{2} & \text{if}~\abs{x}\leq\frac{L}{2}\\
0 & \text{if}~\abs{x}>\frac{L}{2}
\end{cases}
\end{equation}


### Uncertainty principle and resolution issues

As previously stated, both $s$ and $\hat s$ contain the exact same information.
However, there is a fundamental limit to the accuracy with which the values
for certain pairs of physical pairs can be observed.
A known example to issue is Heisenberg's uncertainty principle regarding
the position of a particle and its momentum [@sen2014].
Similarly, time and frequency are a pair of complementary variables.

In the context of time-frequency analysis, the Heisenberg-Gabor limit
(or simply the Gabor limit) defines this constraint by the following inequality
(proof in Appendix \@ref(uncertainty))

\begin{equation}
\sigma_t\cdot\sigma_\w \geq \frac{1}{4\pi}
\end{equation}
where $\sigma_t$ and $\sigma_\w$ are the standard deviations of the time and frequency respectively.

The Gabor limit means essentially that
"a realizable signal occupies a region of area at least one in the time-frequency plane."
Which means that we cannot sharply localize a signal in both the time domain and frequency domain.
This makes the concept of an instantaneous frequency impossible [@grochenig2001].

A direct result of the uncertainty principle is the fact that high temporal resolution
and frequency resolution cannot be acheived at the same time.

![STFT resolution with respect to different window sizes $\Delta T$ and overlap ratios $r$](img/stft_resolution.png)

In the figure above, we see the influence of the window size and the overlap ratio on the
STFT resolution.

- **Window size:**
  For larger window sizes, we have higher frequency resolution. However, the time resolution is low as we have fewer time samples for the STFT.
  For smaller windows, we get higher time resolution as we have more time samples for the STFT, while losing frequency resolution due to smaller FT size.
- **Overlap:**
  In the case of smaller overlaps, the resulting spectrum has time discontinuities. Indeed, the straight line appears to be a piece-wise constant function of time.
  For overlap ratios close to 1, the time resolution is significantly better obtaining the best results (given an adequate window size). However, one should keep in mind that high overlaps can be computationally costly.

### Inverse Short-Time Fourier Transform

The operator $V_w$ is an isometry from $L^2(\R)$ to $L^2(\R^2)$
if $\norm{w}_2=1$,
allowing for $s$ to be completely determined by $V_w s$.
With the help of the orthogonality relations (Parseval's formula) on the STFT we obtain
the inversion formula for the STFT (see Appendix \@ref(istft) for a detailed proof).

For $w,h\in L^2(\R)$ smooth windows such that $\dotp{w,h}\neq 0$
we have for all $s\in L^2(\R)$,

\begin{align}
s(t) &=\frac{1}{\dotp{w,h}} \iint_{\R^2}V_w s(\tau,\w)M_\w T_\tau h(t) \dw\dtau\\
	 &= \frac{1}{\dotp{w,h}}
    	\iint_{\R^2} S(\tau,\w) h(t-\tau) e^{2\pi i\w t} \dw\dtau
\end{align}

### Griffin-Lim Algorithm

The practical use of the inverse STFT is to obtain the signal
from a spectrum that has undergone some changes.
Daniel W. Griffin and Jae S. Lim [@griffin1983] proposed
an efficient algorithm for signal estimation from the modified STFT.
The GLA algorithm minimizes the mean squared error between the STFT magnitude
of the estimated signal and the modified STFT magnitude.

This method is efficient and easy to implement, and is widely
used in signal processing libraries.

Let $x\in L^2(\R)$ be a realizable signal and $X=V_w x\in L^2(\R^2)$
be its STFT. Let $Y\in L^2(\R^2)$ denote the modified STFT.
It's worth noting that $Y$, in general, is not necessarily an STFT
in the sense that there might not be a signal $y\in L^2(\R)$
whose STFT is $Y=V_w y$ [@griffin1983].

Let $y_\tau \in L^2(\R^2)$ the inverse Fourier transform of $Y$
with respect to the frequency $\w$ (its second variable)
at a fixed time $\tau\in\R$.

\begin{equation}
y_\tau(t) = \int_{\R} Y(\tau,\w) e^{2\pi i\w t} \dw
\end{equation}

The algorithm finds iteratively the signal $x$ that minimizes
the distance between $X$ and $Y$. The distance measure between
the two spectrums is defined as the norm of the difference
over the $L^2(\R^2)$ space

\begin{equation}
d(X,Y) = \norm{X-Y}_2^2
    = \iint_{\R^2} \abs{X(\tau,\w) - Y(\tau,\w)}^2 \dw\dtau
\end{equation}

Which is expressed for discrete STFT as
\begin{equation}
d(X,Y) = \sum_\tau \sum_\w \abs{X[\tau,\w] - Y[\tau,\w]}^2
\end{equation}

The signal $x[t]$ is therefore reconstructed iteratively along the formula
\begin{equation}
x[t] = \frac{\sum\limits_\tau y_\tau[t]w[t-\tau]}{\sum\limits_\tau w^2[t-\tau]}
\end{equation}

## The lift to the augmented space

In order to study the Wilson-Cowan model for neural activations,
we need to have a 3D representation of the sound.
In this section we will explain how the STFT of the sound signal
is lifted into the **contact space** and explore the properties of this space.

### The sound chirpiness

The 3D representation of the image in the sub-Riemannian model of V1
was obtained by considering the *sensitivity to directions*
represented by an angle $\theta\in P^1=\R/\pi\Z$.

We transpose this concept of sensitivity to directions
for sound signals to sensitivity to *instantaneous chirpiness*
that is the time derivative of the frequency $\nu=\dw/\dtau$.
The time derivative of the frequency is indeed the slope
of the tangent line in the sound spectrogram.
Hence establishing the bridge with the visual model.

### Single time-varying frequency

As we study the sound through its instantaneous frequency and chirpiness,
we consider both the frequency and the chirpiness functions of time.
To properly define the lift, we consider the following single
time-varying frequency sound signal

\begin{equation}
s(t) = A\cdot\sin(\w(t)t),\quad A\in\R
\end{equation}

The STFT of this signal can be therefore expressed as

\begin{equation}
S(\tau,\w) = \frac{A}{2i}\pp{\delta_0(\w-\w(\tau)) - \delta_0(\w+\w(\tau))}
\end{equation}

supposing the FT is normalized, where $\delta_0$ is the Dirac delta
distribution centered at 0.
Which means that $S$ is concentrated on the curves $\tau\mapsto(\tau,\w(\tau))$
and $\tau\mapsto(\tau,-\w(\tau))$.

![The STFT the single time-varying frequency sound signal](img/single_freq.png){width=25%}

So far, the sound signal is represented in the 2-dimensional space
by the parametric curve $t\mapsto(t,\w(t))$.
Nevertheless, we aim to lift our signal into the 3-dimensional *augmented space*
by adding the sensitivity to frequency variations $\nu(t)=\dw(t)/\dt$.
Similarly, the lifted curve is parameterized as $t\mapsto(t,\w(t),\nu(t))$ [@boscain2021].

### Control system

We define the control to the chirpiness variable $u(t)=\dnu/\dt$,
we can therefore say that the curve $t\mapsto(\tau(t),\w(t),\nu(t))$
in the contact space is a lift of a planar curve if there exists
a control $u(t)$ such that

\begin{equation}
\ddt\pmat{\tau\\\w\\\nu} = \pmat{1\\\nu\\0} + u(t)\pmat{0\\0\\1}
\end{equation}

We define the system state vector as $q=(\tau,\w,\nu)$, the control system
can be written as
\begin{equation}
\ddt q(t) = X_0(q(t)) + u(t) X_1(q(t))
\end{equation}

where $X_0(q(t))$ and $X_1(q(t))$ are two vector fields in $\R^3$ defined as

\begin{equation}
X_0\pmat{\tau\\\w\\\nu} = \pmat{1\\\nu\\0},\quad 
X_1\pmat{\tau\\\w\\\nu} = \pmat{0\\0\\1}
\end{equation}

The vector fields $X_0$ and $X_1$ generate the Heisenberg group,
and the space $\sset{X_0+uX_1\vert u\in\R}$ is a line in the $\R^3$ [@boscain2021].

### Lift to the contact space

In the case of a general sound signal, each level line of the spectrogram
$\abs{S}(\tau,\w)$ is lifted to the contact space.
This yeilds by the implicit function theorem the following subset
of the contact space, which is a well-defined surface
if $\abs{S}\in\Cl^2$ and $\mathrm{Hess}\abs{S}$ is non-degenerate [@boscain2021].

\begin{equation}
\Sigma = \sset{(\tau,\w,\nu)\in\R^3 \vert\nu\partial_\w\abs{S}(\tau,\w) + \partial_\tau\abs{S}(\tau,\w) = 0}
\end{equation}

Which allows to finally define the sound lift in the contact space as

\begin{equation}
L(\tau,\w,\nu) = S(\tau,\w)\cdot\delta_\Sigma (\tau,\w,\nu) =
\begin{cases}
S(\tau,\w) & \text{if}~(\tau,\w,\nu)\in\Sigma\\
0          & \text{otherwise}
\end{cases}
\end{equation}

The time-frequency representation is obtained from the lifted sound
by applying the projection operator defined as

\begin{equation}
\Proj\sset{L(\tau,\w,\nu)}(\tau,\w) = \int_\R L(\tau,\w,\nu)\dnu
\end{equation}

### Lift implementation

We have seen that the lift to the contact space is defined through
the surface $\Sigma$ which is defined with respect to $\nabla\abs{S}$.
The chirpiness is numerically calculated by numerically approximating
the gradient of the spectrum $\abs{S}$.

The discretization of the time and frequency domains is determined by
the sampling rate of the original signal and the window size
chosen in the STFT procedure. 
That is, by the Nyquist-Shannon sampling theorem,
for a temporal sampling rate $\delta t$ and a window size of $T_w$,
we consider the frequencies $\omega$ such that $|\omega|<1/(2\delta t)$,
with a finest discretization rate of $1/(2T_w)$.

The frequency domain is therefore bounded.
Nevertheless, the chirpiness $\nu$ defined as
$\nu\partial_\omega\abs{S}(\tau,\omega) + \partial_\tau\abs{S}(\tau,\omega)=0$ is unbounded,
and since generically there exists points such that
$\partial_\omega\abs{S}(\tau_0,\omega_0)=0$,
chirpiness values stretch over the entire real line.

\begin{figure}[H]
    \begin{minipage}{.47\linewidth}
    \centering
    \includegraphics[width=\textwidth]{img/cauchy_dist_pdf.png}
    \caption{Chirpiness of a speech signal compared to Cauchy distribution}
    \label{fig:cauchy_dist}
    \end{minipage}%
    \hfill
    \begin{minipage}{.47\linewidth}
    \centering
        \includegraphics[width=.27\textwidth]{img/cauchy_pt_estimate_iqr_2.png}\hspace{0.1\linewidth}%
        \includegraphics[width=.27\textwidth]{img/cauchy_values_percentage_iqr_2.png}
    \caption{Box plots for estimated Cauchy distributions of speech signals chirpiness.
    \emph{Left:} Kolmogorov-Smirnov statistic values.
    \emph{Right:} percentage of values falling in $I_{0.95}$}
    \end{minipage}
\end{figure}

To overcome this problem, a natural strategy is to model the chirpiness values as
a random variable $X$, and considering only chirpinesses falling inside the confidence
interval $I_p$ for some reasonable $p$-value (e.g., $p=0.95$).
The best fit for the chirpiness values was the random variable $X$
following a Cauchy distribution $\Cd(x_0,\gamma)$ [@asswad2021] where

- $x_0$ is the location parameter that corresponds to the location of the peak
- $\gamma$ is the scale parameter that determines the shape of the distribution

`r if (is_html_output()) '
![Chirpiness of a speech signal compared to Cauchy distribution](img/cauchy_dist_pdf.png)
'`

The Cauchy distribution's probability density function (PDF) is given as
\begin{equation}
f_X(x)=\frac{1}{\pi\gamma\pp{1+\pp{\frac{x-x_0}{\gamma}}^2}}
\end{equation}
and it's cumulative distribution function (CDF) is
\begin{equation}
F_X(x)=\frac{1}{\pi} \arctan\pp{\frac{x-x_0}{\gamma}} + \frac{1}{2}
\end{equation}

The Cauchy parameters were estimated as follows:

- $x_0$: the chirpiness samples median
- $\gamma$: half the interquartile range which is the difference between
  the 75^th^ and the 25^th^ percentile.

Although statistical tests on a library of real-world speech
signals^[The speech material used in the current study is part of an ongoing
psycholinguistic project on spoken word recognition.
Speech material comprises 49 Italian words and 118 French words.
The two sets of words were produced by two (40-year-old) female speakers
(a French monolingual speaker and an Italian monolingual speaker) and recorded
using a headset microphone AKG C 410 and a Roland Quad Capture audio interface.
Recordings took place in the soundproof cabin of the Laboratoire de Phonétique
et Phonologie (LPP) of Université de Paris Sorbonne-Nouvelle.
Both informants were told to read the set of words as fluently and naturally as possible]
rejected the assumption that $X\sim \Cd(x_0,\gamma)$,
the fit is quite good according to the Kolmogorov-Smirnov statistic
\begin{equation}
D_n=\sup_x\abs{F_n(x)-F_X(x)}
\end{equation}
where $F_n$ is the empirical distribution function
evaluated over the chirpiness values [@asswad2021].

`r if (is_html_output()) '
<img src="img/cauchy_pt_estimate_iqr_2.png" width="100" class="inline" style="margin-left: 300px;">
<img src="img/cauchy_values_percentage_iqr_2.png" width="100" class="inline">

<p class="caption">
    Box plots for estimated Cauchy distributions of speech signals chirpiness.
    <em>Left:</em> Kolmogorov-Smirnov statistic values.
    <em>Right:</em> percentage of values falling in <span class="math inline">\\(I_{0.95}\\)</span>
</p>
'`

## Cortical activations in A1

In this model, we consider the primary auditory cortex (A1) as the space of $(\w,\nu)\in\R^2$.
When hearing a sound signal $s$, A1 receives its lift to the contact space $L(t,\w,\nu)$
at every instant $t$.
The *neuron* receives an external charge $S(t,\w)$ if $(t,\w,\nu)\in\Sigma$ and no charge otherwise.

The Wilson-Cowan equations (WC) [@wilson1972] have been successfully applied to describe
the evolution of neural activations in V1 as well as A1
[@ermentrout1979;@bressloff2002a;@loebel2007;@rankin2015;@zulfiqar2019;@boscain2017;@bertalmio2018].

The WC equations have the advantage of being flexible as they can be applied independently
of the underlying geometric structure, which is only encoded in the kernel of the integral term.
They allow as well for a natural implementation of delay terms in the interactions
and can be easily tuned via few parameters with clear effect on the results.

In this model, the resulting activation $a:[0,T]\times\R\times\R\rightarrow\C$ is the solution
to the WC differo-integral equation with a delay $\delta$.

\begin{equation}
\dtfrac a(t,\w,\nu) = -\alpha a(t,\w,\nu) + \beta L(t,\w,\nu)
+ \gamma\int_{\R^2} k_\delta(\w,\nu\Vert\w',\nu') \sigma(a(t-\delta,\w',\nu')) \dw'\dnu'
\end{equation}

where

- $\alpha,\beta,\gamma>0$ are parameters
- $\sigma:\C\rightarrow\C$ is a non-linear sigmoid where $\sigma(\rho e^{i\theta})=\tilde\sigma(\rho)e^{i\theta}$
  with $\tilde\sigma(x)=\min\sset{\max\sset{0,\kappa x}, 1},\forall x\in\R$ given a fixed $\kappa>0$.
- $k_\delta(\w,\nu\Vert\w',\nu')$ is a weight modeling the interaction
  between $(\w,\nu)$ and $(\w',\nu')$ after a delay $\delta>0$ via the kernel of the transport-diffusion
  operator associated to the contact structure of A1.

When $\gamma=0$, the WC equation becomes a standard low-pass filter $\partial_t a=-\alpha a + L$
whose solution is simply

\begin{equation}
a(t,\w,\nu) = \int_0^t e^{-\alpha(s-t)}L(t,\w,\nu)\ds
\end{equation}

With $\gamma\neq0$, a non-linear delayed interaction term is added on top
of the low-pass filter, encoding the inhibitory and excitatory interconnections between neurons [@boscain2021].

In the scope of the internship, no work was carried on the integral kernel.
Hence, no further explanation on the WC model is needed.

\pagebreak

