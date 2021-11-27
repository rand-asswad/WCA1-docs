# Image reconstruction model

The geometric model for sound reconstruction was inspired by
a sub-Riemannian model of the visual cortex V1 for image reconstruction.
Therefore, a preliminary study of the image reconstruction was needed
in order to understand the sound model and the challenges that are
faced in adapting the initial model for sound reconstruction.

## Neoro-geometric model of V1

My supervisors and their colleagues have presented a cortical-inspired
image reconstruction algorithm [@boscain2017;@bertalmio2018] based
on the Citti-Petitot-Sarti (CPS) model of the human primary visual cortex V1 [@petitot1999;@citti2006].

The CPS model is based on the work of Hubel and Wiesel [@hubel1959] who observed that there are
groups of neurons sensitive to positions and directions.
This has inspired Hoffman [@hoffman1989] to model V1 as a contact space
(a three-dimensional manifold endowed with a smooth map).
Since an image can be seen as a function $f:\R^2\rightarrow\R_+$ representing
the grey level at given coordinates, the visual cortex adds
the angle $\theta\in P^1$ of the tangent line to the curve,
where $P^1=\R/\pi\Z$ is the projective line of non-directed angles.
The model therefore lifts the image into $\R^2\times P^1$.

## Wilson-Cowan model in V1

The Wilson-Cowan (WC) model [@wilson1972] describes the evolution of neural activations,
more precisely, it describes the evolution of excitatory and inhibitory activity in
a synaptically coupled neuronal network.

The interaction between the hypercolumns in V1 can be described through
the WC equations [@bressloff2002].
An integro-differential equation describing the state $a(x,\theta,t)$ of a population
of neurons with V1 coordinates $x\in\R^2$ and orientation $\theta\in P^1$ at time $t>0$ [@bertalmio2018].

\begin{equation}
\dtfrac a(x,\theta,t) = -\alpha a(x,\theta,t) + \nu
\int_{\R^2\times P^1} \omega(x,\theta\| x',\theta') \sigma(a(x',\theta',t)) \dx'\dtheta'
+ h(x,\theta,t)
\end{equation}

\begin{figure}[H]
    \centering
    \begin{minipage}{0.3\linewidth}
        \centering
        \includegraphics[width=\textwidth]{img/img_original.jpg}\\original
    \end{minipage}\hfill%
    \begin{minipage}{0.3\linewidth}
        \centering
        \includegraphics[width=\textwidth]{img/img_corrupted.png}\\corrupted
    \end{minipage}\hfill%
    \begin{minipage}{0.3\linewidth}
        \centering
        \includegraphics[width=\textwidth]{img/img_reconstructed.png}\\reconstructed
    \end{minipage}
    \caption{Reconstruction of a 97\% corrupted image}
    \label{fig:img-reconstruct}
\end{figure}

`r if (is_html_output()) '
<img src="img/img_original.jpg" width="250" class="inline">
<img src="img/img_corrupted.png" width="250" class="inline">
<img src="img/img_reconstructed.png" width="250" class="inline">

<p class="caption">Reconstruction of a 97% corrupted image
(left: original, middle: corrupted, right: reconstructed)</p>
'`

\pagebreak