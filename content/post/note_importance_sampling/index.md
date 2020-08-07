+++
title = "A note on importance sampling for state space models"
subtitle = ""

date = 2020-08-03T00:00:00
lastmod = 2020-08-03T00:00:00
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

tags = []
summary = ""

# Link to related code
# url_code = "https://github.com/HannoReuvers/Software_comparison_DW"

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["deep-learning"]` references 
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
# projects = ["internal-project"]

# Featured image
# To use, add an image named `featured.jpg/png` to your project's folder. 
[image]
  # Caption (optional)
  # caption = "Image credit: [**Unsplash**](https://unsplash.com/photos/CpkOjOcXdUY)"

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = ""

# Show image only in page previews?
  preview_only = false

# Set captions for image gallery.

[[gallery_item]]
album = "gallery"
image = "theme-default.png"
caption = "Default"

[[gallery_item]]
album = "gallery"
image = "theme-ocean.png"
caption = "Ocean"

[[gallery_item]]
album = "gallery"
image = "theme-forest.png"
caption = "Forest"

[[gallery_item]]
album = "gallery"
image = "theme-dark.png"
caption = "Dark"

[[gallery_item]]
album = "gallery"
image = "theme-apogee.png"
caption = "Apogee"

[[gallery_item]]
album = "gallery"
image = "theme-1950s.png"
caption = "1950s"

[[gallery_item]]
album = "gallery"
image = "theme-coffee-playfair.png"
caption = "Coffee theme with Playfair font"

[[gallery_item]]
album = "gallery"
image = "theme-cupcake.png"
caption = "Cupcake"
+++


## Linear Gaussian state space model

The linear Gaussian state space model takes the following form:

\begin{equation}
\begin{aligned}
\boldsymbol{y}\_t &= \boldsymbol{Z} \boldsymbol{\alpha}\_t + \boldsymbol{\varepsilon}\_t, \\qquad  \boldsymbol{\varepsilon}\_t \sim NID \left( \boldsymbol{0}, \boldsymbol{H} \right) \\\\\\
\boldsymbol{\alpha}\_{t+1} &= \boldsymbol{T} \boldsymbol{\alpha}\_t + \boldsymbol{\eta}\_t, \\qquad \boldsymbol{\eta}\_t \sim NID \left( \boldsymbol{0}, \boldsymbol{Q} \right),
\end{aligned}
\tag{1}
\label{eq:normal_linear_ssm}
\end{equation}

for $t=1, \dots, T$, where $\boldsymbol{y}\_t$ is a $n \times 1$ vector, and $\boldsymbol{\alpha}\_t$ is a $m \times 1$ vector. The observation equation of the linear Gaussian state space model \eqref{eq:normal_linear_ssm} implies that $\boldsymbol{y}\_t \sim g(\boldsymbol{y}\_t | \boldsymbol{\alpha}\_t) = g_{\boldsymbol{\varepsilon}\_t}(\boldsymbol{y}\_t - \boldsymbol{Z} \boldsymbol{\alpha}\_t) = NID(\boldsymbol{0}, \boldsymbol{H})$, where $g$ indicates a linear Gaussian density. The log-likelihood of $\boldsymbol{y}\_t | \boldsymbol{\alpha}\_t$ takes the form:

\begin{equation*}
\log g(\boldsymbol{y}\_t | \boldsymbol{\alpha}\_t) = - \frac{n}{2} \log(2 \pi) - \frac{1}{2} \log(\det\boldsymbol{H}) - \frac{1}{2} (\boldsymbol{y}\_t - \boldsymbol{Z} \boldsymbol{\alpha}\_t)' \boldsymbol{H}^{-1} (\boldsymbol{y}\_t - \boldsymbol{Z} \boldsymbol{\alpha}\_t),
\end{equation*}

for $t=1, \dots, T$. The transition equation of the linear Gaussian state space model \eqref{eq:normal_linear_ssm} implies that $\boldsymbol{\alpha}\_{t+1} \sim g(\boldsymbol{\alpha}\_{t+1} | \boldsymbol{\alpha}\_t) = g_{\eta_t}(\boldsymbol{\alpha}\_{t+1} -  \boldsymbol{T} \boldsymbol{\alpha}\_t) = NID(\boldsymbol{0}, \boldsymbol{Q})$. The log-likelihood of $\boldsymbol{\alpha}\_{t+1} | \boldsymbol{\alpha}\_t$ takes the form:

\begin{equation} 
\log g(\boldsymbol{\alpha}\_{t+1} | \boldsymbol{\alpha}\_t ) = - \frac{n}{2} \log(2 \pi) - \frac{1}{2} \log(\det \boldsymbol{Q}) - \frac{1}{2} (\boldsymbol{\alpha}\_{t+1} -  \boldsymbol{T} \boldsymbol{\alpha}\_t)' \boldsymbol{Q}^{-1} (\boldsymbol{\alpha}\_{t+1} -  \boldsymbol{T} \boldsymbol{\alpha}\_t),
\tag{2}
\label{eq:gaussian_state}
\end{equation}

for $t=1, \dots, T$. The initial value of the state vector $\boldsymbol{\alpha}\_1 \sim g(\boldsymbol{\alpha}\_1) = N(\boldsymbol{0}, \boldsymbol{P}\_1)$. Finally, I define the the joint density:

\begin{equation*}
g(\boldsymbol{\alpha}, Y_T) = g(\boldsymbol{\alpha}\_1) \prod_{t=1}^T g(\boldsymbol{y}\_t | \boldsymbol{\alpha}\_t) g(\boldsymbol{\alpha}\_{t+1}|\boldsymbol{\alpha}\_t) = g(\boldsymbol{\alpha}\_1) \prod_{t=1}^T g_{\varepsilon_t}(\boldsymbol{y}\_t - \boldsymbol{Z} \boldsymbol{\alpha}\_t)g_{\eta_t}(\boldsymbol{\alpha}\_{t+1} -  \boldsymbol{T} \boldsymbol{\alpha}\_t),
\end{equation*}

where $\boldsymbol{\alpha}$ if the $mT \times 1$ state vector, and $Y_T$ is the $nT \times 1$ vector of observed series.

The parameters of the linear Gaussian state space model \eqref{eq:normal_linear_ssm} can be estimated by maximizing the following log-likelihood

\begin{equation} 
\log g(\boldsymbol{y}\_t) = - \frac{n}{2} \log(2 \pi) - \frac{1}{2} \log(\det \boldsymbol{F}\_t) - \frac{1}{2} \boldsymbol{v}\_t' \boldsymbol{F}\_t^{-1} \boldsymbol{v}\_t,
\tag{3}
\label{eq:logl_y}
\end{equation}

where the prediction errors $\boldsymbol{v}\_t$ and their covariance matrix $\boldsymbol{F}\_t$ are obtained via the Kalman filter recursions

\begin{equation*}
\begin{aligned}
\boldsymbol{v}\_t &= \boldsymbol{y}\_t - \boldsymbol{Z} \boldsymbol{a}\_t \\\\\\
\boldsymbol{F}\_t &= \boldsymbol{Z} \boldsymbol{P}\_t \boldsymbol{Z}' + \boldsymbol{H} \\\\\\
\boldsymbol{K}\_t &= \boldsymbol{T} \boldsymbol{P}\_t \boldsymbol{Z}' \boldsymbol{F}\_t^{-1} \\\\\\
\boldsymbol{a}\_{t|t} &= \boldsymbol{a}\_t + \boldsymbol{P}\_t \boldsymbol{Z}' \boldsymbol{F}\_t^{-1} \boldsymbol{v}\_t \\\\\\
\boldsymbol{P}\_{t|t} &= \boldsymbol{P}\_t - \boldsymbol{P}\_t \boldsymbol{Z}' \boldsymbol{F}\_t^{-1} \boldsymbol{Z} \boldsymbol{P}\_t \\\\\\
\boldsymbol{v}\_{t+1} &= \boldsymbol{T} \boldsymbol{a}\_t + \boldsymbol{K}\_t \boldsymbol{v}\_t \\\\\\
\boldsymbol{P}\_{t+1} &= \boldsymbol{T} \boldsymbol{P}\_t \left( \boldsymbol{T} - \boldsymbol{K}\_t \boldsymbol{Z} \right)' + \boldsymbol{Q},
\end{aligned}
\end{equation*}

for $t = 1, \dots, T$. The $m \times 1$ vector $\boldsymbol{a}\_{t|t}$ is the filter estimate of the state vector $\boldsymbol{\alpha}\_t$, and $\boldsymbol{P}\_{t|t}$ is the respective covariance matrix. 

I define with $\boldsymbol{\theta}\_t = \boldsymbol{Z} \boldsymbol{\alpha}\_t$ the $n \times 1$ signal vector (this will simplify some derivations). More accurate estimates of the signal vector can be efficiently obtained by applying an additional Kalman smoother; I define the estimate from the Kalman filter smoother (KFS) as $\hat{\boldsymbol{\theta}}\_t = \text{E} (\boldsymbol{\theta}\_t | \boldsymbol{y}\_t) = \arg \max_{\boldsymbol{\alpha}\_t} g(\boldsymbol{\theta}\_t|\boldsymbol{y}\_t)$, for $t=1, \dots, T$, which implies that $\hat{\boldsymbol{\theta}}\_t$ is the mode of $g(\boldsymbol{\theta}\_t|\boldsymbol{y}\_t)$. The Kalman smoother recursions of Durbin and Koopman (2012)[^durbinkoopman2012] are:

\begin{equation*}
\begin{aligned}
\boldsymbol{r}\_{t-1} &= \boldsymbol{Z}'\boldsymbol{F}\_t^{-1} \boldsymbol{v}\_t + \left(\boldsymbol{T} - \boldsymbol{K}\_t \boldsymbol{Z} \right)'\boldsymbol{r}\_t \\\\\\
\hat{\boldsymbol{\alpha}}\_t &= \boldsymbol{a}\_t + \boldsymbol{P}\_t \boldsymbol{r}\_{t-1} \\\\\\
\boldsymbol{N}\_{t-1} &= \boldsymbol{Z}' \boldsymbol{F}\_t^{-1} \boldsymbol{Z} + \left(\boldsymbol{T} - \boldsymbol{K}\_t \boldsymbol{Z} \right)' \boldsymbol{N}\_t \left(\boldsymbol{T} - \boldsymbol{K}\_t \boldsymbol{Z} \right) \\\\\\
\boldsymbol{V}\_t &= \boldsymbol{P}\_t - \boldsymbol{P}\_t \boldsymbol{N}\_{t-1} \boldsymbol{P}\_t,
\end{aligned}
\end{equation*}

for $t=T, \dots, 1$, with $\boldsymbol{r}\_T= \boldsymbol{0}$, $\boldsymbol{N}\_T = \boldsymbol{0}$, and $\boldsymbol{V}\_t$ being the smoothed covariance matrix of $\hat{\boldsymbol{\alpha}}\_t$.



## Nonlinear non-Guassian state space model

Suppose now that either the observation or the transition equation (or both) of the state space model are nonlinear and non-Gaussian:

\begin{equation} \label{eq:nonnormal_nonlinear_ssm}
\begin{aligned}
\boldsymbol{y}\_t &\sim p(\boldsymbol{y}\_t | \boldsymbol{\theta}\_t) \\\\\\
\boldsymbol{\alpha}\_{t+1} &\sim p(\boldsymbol{\alpha}\_{t+1} | \boldsymbol{\alpha}\_t),
\end{aligned}
\end{equation}

for $t=1, \dots, T$, where $p$ indicates that the distribution is either nonlinear or non-Gaussian ($p$ need not be the same in the observation and transition equation).
In this case it is not possible to employ the Kalman filter for likelihood evaluation and state estimation. Specifically, in case of nonlinearity it is not possible to apply the usual Kalman filter; in case of linearity and non-Gaussianity in the observation equation, it is possible to apply the usual Kalman filter if a density that belongs to the family of exponential distributions is assumed. The Kalman filter would then still be the best _linear_ unbiased estimator, and one can rely on quasi maximum likelihood results for the estimation of the parameters.



### Mode estimation
Shephard and Pitt (1997)[^ShephardPitt1997] and Durbin and Koopman (1997)[^DurbinKoopman1997] argue that it is still possible estimate the signal vector of a nonlinear non-Gaussian model by mode estimation, i.e. find
\begin{equation*}
\hat{\boldsymbol{\theta}}\_t = \arg \max_{\boldsymbol{\theta}\_t} p(\boldsymbol{\theta}\_t|\boldsymbol{y}\_t),
\end{equation*}

for $t=1, \dots, T$. We can employ the Newton-Raphson algorithm in order to do it: for an initial guess $\boldsymbol{g}\_t$ of $\hat{\boldsymbol{\theta}}\_t$ (for instance $\boldsymbol{g}\_t=\boldsymbol{y}\_t$, or $\boldsymbol{g}\_t=\bar{y} \boldsymbol{1}\_n$, or $\boldsymbol{g}\_t=\boldsymbol{0}\$), compute
\begin{equation*}
\boldsymbol{g}\_t^+ = \boldsymbol{g}\_t - \left[ \left. \ddot p(\boldsymbol{\theta}\_t | \boldsymbol{y}\_t) \right\vert_{\boldsymbol{\theta}\_t = \boldsymbol{g}\_t} \right]^{-1} \left. \dot p(\boldsymbol{\theta}\_t | \boldsymbol{y}\_t) \right\vert_{\boldsymbol{\theta}\_t = \boldsymbol{g}\_t},
\end{equation*}

for $t=1, \dots, T$, where $\dot p(\boldsymbol{\theta}\_t | \boldsymbol{y}\_t) = \frac{\partial \log p(\boldsymbol{\theta}\_t | \boldsymbol{y}\_t)}{\partial \boldsymbol{\theta}\_t}$ and $\ddot p(\boldsymbol{\theta}\_t | \boldsymbol{y}\_t) = \frac{\partial^2 \log p(\boldsymbol{\theta}\_t | \boldsymbol{y}\_t)}{\partial \boldsymbol{\theta}\_t \partial \boldsymbol{\theta}\_t'}$. 

If the transition equation of the state space model is linear Gaussian, it is possible to show that the Newton-Raphson updating step can be more efficiently computed (it takes less iterations than for the Newton-Raphson method), by applying the KFS to a linear Gaussian state space model with $\boldsymbol{H} =\boldsymbol{A}\_t = - \left[ \left. \ddot p (\boldsymbol{y}\_t|\boldsymbol{\theta}\_t) \right\vert_{\boldsymbol{\theta}\_t = \boldsymbol{g}\_t} \right]^{-1}$ and $ \boldsymbol{y}\_t=\boldsymbol{z}\_t = \boldsymbol{g}\_t + \boldsymbol{A}\_t \left. \dot p (\boldsymbol{y}\_t|\boldsymbol{\theta}\_t) \right\vert_{\boldsymbol{\theta}\_t = \boldsymbol{g}\_t}$, for $t=1, \dots, T$. This state space model takes the form
\begin{equation} 
\begin{aligned}
\boldsymbol{z}\_t &= \boldsymbol{\theta}\_t + \boldsymbol{\varepsilon}\_t, \quad \boldsymbol{\varepsilon}\_t \sim N (\boldsymbol{0}, \boldsymbol{A}\_t) \\\\\\
\boldsymbol{\theta}\_t &= \boldsymbol{Z} \boldsymbol{\alpha}\_t \\\\\\
\boldsymbol{\alpha}\_{t+1} &= \boldsymbol{T} \boldsymbol{\alpha}\_t + \boldsymbol{\eta}\_t, \quad \boldsymbol{\eta}\_t \sim N (\boldsymbol{0}, \boldsymbol{Q}),
\end{aligned}
\tag{4}
\label{eq:ssm_approx}
\end{equation}

for $t=1, \dots, T$. Notice that the transition equation is the same as the one in \eqref{eq:normal_linear_ssm}. This shows the advantage of having nonlinearity and non-Gaussianity only in the observation equation. The advantage is not just in terms of efficiency but also on the use of the the first and second derivatives of the density $p (\boldsymbol{y}\_t|\boldsymbol{\theta}\_t)$ which is known, instead of $p (\boldsymbol{\theta}\_t|\boldsymbol{y}\_t)$, which is unknown.

The algorithm stops once $\boldsymbol{g}\_t^+ = \boldsymbol{g}\_t$, for $t=1, \dots, T$. Remember that $\boldsymbol{g}\_t^+$ is an estimate for $\boldsymbol{\theta}\_t$, not $\boldsymbol{\alpha}\_t$.

Later in this note it will become clearer why we can use the approximate linear Gaussian state space model \eqref{eq:ssm_approx} in order to estimate the signal vector.



### Importance sampling



#### Monte Carlo integration



#### Choice of the importance density



#### Simulation smoothing



#### Evaluation and maximization of the log-likelihood



#### Implementation remarks



### Modified efficient importance sampling



## Examples



### Poisson model



### Stochastic volatility model



## Acknowledgments

I am grateful to Siem Jan Koopman for his guidance in exploring these methods, to Ilka van de Werve for providing materials that helped me undertsand this topic, and to Franz Palm, Stephan Smeekes and Jan van den Brakel for regular and useful discussions around it. All errors are my own.



## References

[^DurbinKoopman1997]: Durbin, J. and Koopman, S. J. (1997). _Monte Carlo Maximum Likelihood Estimation for Non-Gaussian State Space Models_. Biometrika, 84(3):669–684.

[^durbinkoopman2012]: Durbin, J. and Koopman, S. J. (2012), _Time Series Analysis by State Space Methods: Second Edition_, Oxford Statistical Science Series. OUP Oxford.

[^ShephardPitt1997]: Shephard, N. and Pitt, M. (1997). _Likelihood Analysis of Non-Gaussian Measurement Time Series_. Biometrika, 84(3):653–667.
