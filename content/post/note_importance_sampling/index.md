+++
title = "A note on importance sampling for state space models"
subtitle = "Soome tools to solve nonlinearities in state space models"

date = 2020-08-03T00:00:00
lastmod = 2020-08-03T00:00:00
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

tags = []
summary = "Soome tools to solve nonlinearities in state space models"

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

\begin{equation}
\begin{aligned}
\boldsymbol{v}\_t &= \boldsymbol{y}\_t - \boldsymbol{Z} \boldsymbol{a}\_t \\\\\\
\boldsymbol{F}\_t &= \boldsymbol{Z} \boldsymbol{P}\_t \boldsymbol{Z}' + \boldsymbol{H} \\\\\\
\boldsymbol{K}\_t &= \boldsymbol{T} \boldsymbol{P}\_t \boldsymbol{Z}' \boldsymbol{F}\_t^{-1} \\\\\\
\boldsymbol{a}\_{t|t} &= \boldsymbol{a}\_t + \boldsymbol{P}\_t \boldsymbol{Z}' \boldsymbol{F}\_t^{-1} \boldsymbol{v}\_t \\\\\\
\boldsymbol{P}\_{t|t} &= \boldsymbol{P}\_t - \boldsymbol{P}\_t \boldsymbol{Z}' \boldsymbol{F}\_t^{-1} \boldsymbol{Z} \boldsymbol{P}\_t \\\\\\
\boldsymbol{a}\_{t+1} &= \boldsymbol{T} \boldsymbol{a}\_t + \boldsymbol{K}\_t \boldsymbol{v}\_t \\\\\\
\boldsymbol{P}\_{t+1} &= \boldsymbol{T} \boldsymbol{P}\_t \left( \boldsymbol{T} - \boldsymbol{K}\_t \boldsymbol{Z} \right)' + \boldsymbol{Q},
\end{aligned}
\tag{4}
\label{eq:KF}
\end{equation}

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

\begin{equation} 
\begin{aligned}
\boldsymbol{y}\_t &\sim p(\boldsymbol{y}\_t | \boldsymbol{\theta}\_t) \\\\\\
\boldsymbol{\alpha}\_{t+1} &\sim p(\boldsymbol{\alpha}\_{t+1} | \boldsymbol{\alpha}\_t),
\end{aligned}
\tag{5}
\label{eq:nonnormal_nonlinear_ssm}
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
\tag{6}
\label{eq:ssm_approx}
\end{equation}

for $t=1, \dots, T$. Notice that the transition equation is the same as the one in \eqref{eq:normal_linear_ssm}. This shows the advantage of having nonlinearity and non-Gaussianity only in the observation equation. The advantage is not just in terms of efficiency but also on the use of the the first and second derivatives of the density $p (\boldsymbol{y}\_t|\boldsymbol{\theta}\_t)$ which is known, instead of $p (\boldsymbol{\theta}\_t|\boldsymbol{y}\_t)$, which is unknown.

The algorithm stops once $\boldsymbol{g}\_t^+ = \boldsymbol{g}\_t$, for $t=1, \dots, T$. Remember that $\boldsymbol{g}\_t^+$ is an estimate for $\boldsymbol{\theta}\_t$, not $\boldsymbol{\alpha}\_t$.

Later in this note it will become clearer why we can use the approximate linear Gaussian state space model \eqref{eq:ssm_approx} in order to estimate the signal vector.



### Importance sampling

Importance sampling is an alternative and more accurate way for state estimation than mode estimation, but it may employ the latter method. Below I explain how it works.



#### Monte Carlo integration

Let $\boldsymbol{\theta}$ be a stochastic variable with its density $p(\boldsymbol{\theta})$. The expectation of $\boldsymbol{\theta}$ can be obtained as (Durbin and Koopman, 2012, Chapter 11[^durbinkoopman2012]):
\begin{equation*}
\text{E} (\boldsymbol{\theta}) = \int_{\boldsymbol{\theta} \in \Theta} \boldsymbol{\theta} p(\boldsymbol{\theta}) d \boldsymbol{\theta},
\end{equation*}

which can be estimated by its Monte Carlo estimator 
\begin{equation*}
\hat{\boldsymbol{\theta}}\_{\text{M}} = \frac{1}{S} \sum_{i=1}^S \boldsymbol{\theta}^{(i)}, \quad \boldsymbol{\theta}^{(i)} \sim p(\boldsymbol{\theta}).
\end{equation*}

It can be shown that $\hat{\boldsymbol{\theta}}\_{\text{M}}$ is an unbiased estimator of $\text{E} (\boldsymbol{\theta})$.

In the case of the nonlinear non-Gaussian state space model, I wish to draw $\boldsymbol{\theta}$ from the conditional distribution $p(\boldsymbol{\theta}|Y_T)$, which I do not know, but I can instead sample from a linear Gaussian distribution $g(\boldsymbol{\theta}|Y_T)$, which should resemble $p(\boldsymbol{\theta}|Y_T)$ as much as possible:
\begin{equation*}
\text{E} (\boldsymbol{\theta}) = \int_{\boldsymbol{\theta} \in \Theta} \frac{\boldsymbol{\theta} p(\boldsymbol{\theta}|Y_T)}{g(\boldsymbol{\theta}|Y_T)} g(\boldsymbol{\theta}|Y_T) d\boldsymbol{\theta} = \text{E}\_g \left[\frac{\boldsymbol{\theta} p(\boldsymbol{\theta}|Y_T)}{g(\boldsymbol{\theta}|Y_T)}\right],
\end{equation*}

where the subscript $g$ in the expectation operator indicates that the expectation is taken with respect to the Gaussian density $g(\boldsymbol{\theta}|Y_T)$, which I refer to as importance density. The Monte Carlo estimator of this expectation is then
\begin{equation*}
\hat{\boldsymbol{\theta}}\_{\text{M}} = \frac{1}{S} \sum_{i=1}^S \frac{\tilde{\boldsymbol{\theta}}^{(i)} p(\tilde{\boldsymbol{\theta}}^{(i)}|Y_T)}{g(\tilde{\boldsymbol{\theta}}^{(i)}|Y_T)}, \quad \tilde{\boldsymbol{\theta}}^{(i)} \sim g(\boldsymbol{\theta}|Y_T).
\end{equation*}

In order to compute $\hat{\boldsymbol{\theta}}\_{\text{M}}$ based on the previous formula, I need to evaluate $p(\tilde{\boldsymbol{\theta}}^{(i)}|Y_T)$ and $g(\tilde{\boldsymbol{\theta}}^{(i)}|Y_T)$ for each draw of $\tilde{\boldsymbol{\theta}}^{(i)}$ from $g(\boldsymbol{\theta}|Y_T)$. The problem is that I still do not have an analytical expression for $p(\tilde{\boldsymbol{\theta}}^{(i)}|Y_T)$. I can apply the Bayesian rule in order to solve this issue:
\begin{equation*}
\text{E} (\boldsymbol{\theta}) =  \text{E}\_g \left[\frac{\boldsymbol{\theta} p(\boldsymbol{\theta}|Y_T)}{g(\boldsymbol{\theta}|Y_T)}\right] = \text{E}\_g \left[\frac{\boldsymbol{\theta} g(Y_T)p(\boldsymbol{\theta},Y_T)}{p(Y_T)g(\boldsymbol{\theta},Y_T)}\right] = \frac{g(Y_T)}{p(Y_T)} \text{E}\_g \left[\frac{\boldsymbol{\theta}\ p(\boldsymbol{\theta},Y_T)}{g(\boldsymbol{\theta},Y_T)}\right] =  \frac{g(Y_T)}{p(Y_T)} \text{E}\_g \left[\boldsymbol{\theta} w(\boldsymbol{\theta}, Y_T) \right],
\end{equation*}

where $w(\boldsymbol{\theta}, Y_T)=\frac{ p(\boldsymbol{\theta},Y_T)}{g(\boldsymbol{\theta},Y_T)}$ are called importance weights. For $\text{E} (\boldsymbol{\theta}) =1$, then $p(Y_T) = g(Y_T) \text{E}\_g \left[\boldsymbol{\theta} w(\boldsymbol{\theta}, Y_T) \right]$, which implies that the final expression for $\text{E} (\boldsymbol{\theta})$ is given by
\begin{equation*}
\text{E} (\boldsymbol{\theta}) =  \frac{\text{E}\_g \left[\boldsymbol{\theta} w(\boldsymbol{\theta}, Y_T) \right]}{\text{E}\_g \left[ w(\boldsymbol{\theta}, Y_T) \right]},
\end{equation*}

and its Monte Carlo estimator is 
\begin{equation} 
\hat{\boldsymbol{\theta}}\_{\text{M}} =  \frac{ \sum_{i=1}^S\tilde{ \boldsymbol{\theta}}^{(i)} w(\tilde{\boldsymbol{\theta}}^{(i)}, Y_T)}{\sum_{i=1}^S  w(\tilde{\boldsymbol{\theta}}^{(i)}, Y_T) }, \quad \tilde{\boldsymbol{\theta}}^{(i)} \sim g(\boldsymbol{\theta}|Y_T).
\tag{7}
\label{eq:MC_estimator}
\end{equation}

A few remarks are in place. Since the importance density $g(\boldsymbol{\theta}|Y_T)$ was chosen in order to resemble $p(\boldsymbol{\theta}|Y_T)$ as much as possible, the importance weights should be close to 1. In practice this does not always happen and $g(\tilde{\boldsymbol{\theta}}^{(i)}|Y_T)$ can sometimes get values that are so small to cause numerical problems. A way to deal with this problem is discuss later on in this note. 

I mentioned at the beginning of this section that I wish to draw $\boldsymbol{\theta}$ from the conditional distribution $p(\boldsymbol{\theta}|Y_T)$ instead of $p(\boldsymbol{\theta})$. Since $\boldsymbol{\theta}\_t = \boldsymbol{Z} \boldsymbol{\alpha}\_t$, the distribution $p(\boldsymbol{\theta})$ is implied by the transition equation of the state space model. Suppose that this equation implies a random walk dynamic for $\boldsymbol{\theta}$; if I would draw $\boldsymbol{\theta}$ from $p(\boldsymbol{\theta})$, then I would sample random walks that might be completely unrelated to the data that we have $(Y_T)$. This sampling method would therefore be inefficient. Sampling from $p(\boldsymbol{\theta}|Y_T)$ prevents this from happening.

The importance weights are based on the joint densities $p(\boldsymbol{\theta},Y_T)$ and $g(\boldsymbol{\theta},Y_T)$, which allow both the observation and the transition equation of the state space model to be nonlinear and non-Gaussian. If the transition equation of the original model is assumed to be linear Gaussian, then 
\begin{equation*}
\text{E} (\boldsymbol{\theta}) =  \text{E}\_g \left[\frac{\boldsymbol{\theta} p(\boldsymbol{\theta},Y_T)}{g(\boldsymbol{\theta},Y_T)}\right] = \text{E}\_g \left[\frac{\boldsymbol{\theta} p(Y_T|\boldsymbol{\theta})p(\boldsymbol{\theta})}{g(Y_T|\boldsymbol{\theta})g(\boldsymbol{\theta})}\right] = \text{E}\_g \left[\frac{\boldsymbol{\theta} p(Y_T|\boldsymbol{\theta})}{g(Y_T|\boldsymbol{\theta})}\right] =\frac{\text{E}\_g \left[\boldsymbol{\theta} w(Y_T|\boldsymbol{\theta}) \right]}{\text{E}\_g \left[ w(Y_T|\boldsymbol{\theta}) \right]} ,
\end{equation*}

because the density distribution implied by the transition equation in the original model, $p(\boldsymbol{\theta})$, is actually linear Gaussian and therefore equal to $g(\boldsymbol{\theta})$. The importance weights now depend on the conditional distributions $p(Y_T|\boldsymbol{\theta})$ and $g(Y_T|\boldsymbol{\theta})$. This assumption again simplifies the analysis, and I will keep making it from this point onwards.



#### Choice of the importance density

As already mentioned, in order to compute the Monte Carlo estimator \eqref{eq:MC_estimator}, I need to evaluate $p(Y_T|\tilde{\boldsymbol{\theta}}^{(i)})$ and $g(Y_T|\tilde{\boldsymbol{\theta}}^{(i)})$, for $i=1, \dots, S$. I know the analytical expression for $p(Y_T|\boldsymbol{\theta})$ but not for $g(Y_T|\boldsymbol{\theta})$ yet. I also mentioned that $g(Y_T|\boldsymbol{\theta})$ should resemble $p(Y_T|\boldsymbol{\theta})$ as much as possible.

Since $g(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t)$ is Gaussian, it can take the general expression
\begin{equation*}
g(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t) = \exp \left( d_t + \boldsymbol{b}\_t' \boldsymbol{\theta}\_t - \frac{1}{2} \boldsymbol{\theta}\_t' \boldsymbol{C}\_t \boldsymbol{\theta}\_t \right),
\end{equation*}

for $t=1, \dots, T$. If I consider the linear Gaussian state space model
\begin{equation*}
\begin{aligned}
\boldsymbol{y}\_t^* &= \boldsymbol{\theta}\_t + \boldsymbol{\varepsilon}\_t, \quad \boldsymbol{\varepsilon}\_t \sim N (\boldsymbol{0}, \boldsymbol{C}\_t^{-1}) \\\\\\
\boldsymbol{\theta}\_t &= \boldsymbol{Z} \boldsymbol{\alpha}\_t \\\\\\
\boldsymbol{\alpha}\_{t+1} &= \boldsymbol{T} \boldsymbol{\alpha}\_t + \boldsymbol{\eta}\_t, \quad \boldsymbol{\eta}\_t \sim N (\boldsymbol{0}, \boldsymbol{Q}),
\end{aligned}
\end{equation*}

for $t=1, \dots, T$, with $\boldsymbol{y}\_t^* = \boldsymbol{C}\_t^{-1} \boldsymbol{b}\_t$, it is possible to show that $g(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t) = g(\boldsymbol{y}\_t^* |\boldsymbol{\theta}\_t)$ for $t=1, \dots, T$:
\begin{equation} 
\begin{aligned}
\log g(\boldsymbol{y}\_t^* |\boldsymbol{\theta}\_t) &= - \frac{\dim(\boldsymbol{y}\_t^* )}{2} \log(2 \pi) + \frac{1}{2}\log (\det \boldsymbol{C}\_t)- \frac{1}{2}( \boldsymbol{C}\_t^{-1} \boldsymbol{b}\_t - \boldsymbol{\theta}\_t)' \boldsymbol{C}\_t (\boldsymbol{C}\_t^{-1} \boldsymbol{b}\_t - \boldsymbol{\theta}\_t) \\\\\\
&= d_t + \boldsymbol{b}\_t ' \boldsymbol{\theta}\_t -  \frac{1}{2} \boldsymbol{\theta}\_t' \boldsymbol{C}\_t \boldsymbol{\theta}\_t \\\\\\
&= \log g(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t),
\end{aligned}
\tag{8}
\label{eq:logl_ystar}
\end{equation}

with $d_t = \frac{1}{2}\left( \log (\det \boldsymbol{C}\_t) - \dim(\boldsymbol{y}\_t^* ) \log(2 \pi) - \boldsymbol{b}\_t'\boldsymbol{y}\_t^* \right)$, and $\dim(\boldsymbol{y}\_t^* )$ equal to the dimension of the vector $\boldsymbol{y}\_t^* $. I now have to choose $\boldsymbol{b}\_t$ and $\boldsymbol{C}\_t$ (and therefore $d_t$) such that:
\begin{equation*}
\begin{aligned}
\left. \frac{\partial \log g(\boldsymbol{y}\_t^* |\boldsymbol{\theta}\_t)}{\partial \boldsymbol{\theta}\_t} \right\vert_{\boldsymbol{\theta}\_t = \hat{\boldsymbol{\theta}}\_t} &= \left. \frac{\partial \log p(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t)}{\partial \boldsymbol{\theta}\_t} \right\vert_{\boldsymbol{\theta}\_t = \hat{\boldsymbol{\theta}}\_t} \\\\\\
\left. \frac{\partial^2 \log g(\boldsymbol{y}\_t^* |\boldsymbol{\theta}\_t)}{\partial \boldsymbol{\theta}\_t \partial \boldsymbol{\theta}\_t'} \right\vert_{\boldsymbol{\theta}\_t = \hat{\boldsymbol{\theta}}\_t} &= \left. \frac{\partial^2 \log p(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t)}{\partial \boldsymbol{\theta}\_t \partial \boldsymbol{\theta}\_t'} \right\vert_{\boldsymbol{\theta}\_t = \hat{\boldsymbol{\theta}}\_t},
\end{aligned}
\end{equation*}

for $t=1, \dots, T$, where $\hat{\boldsymbol{\theta}}\_t$ is the mode of $\log p(\boldsymbol{\theta}\_t|\boldsymbol{y}\_t)$ calculated as described in the "Mode estimation" section. I am now making sure that $g(Y_T|\boldsymbol{\theta})$ resembles $p(Y_T|\boldsymbol{\theta})$ as much as possible, since the first and second derivatives of the two distributions are the same, at the mode. This is a second-order approximation and as such is a stronger approximation than the one used in the extended Kalman filter (which is instead a first-order approximation). Now I get an expression for $\boldsymbol{b}\_t$ and $\boldsymbol{C}\_t$:
\begin{equation*}
\begin{aligned}
& \left. \frac{\partial \log g(\boldsymbol{y}\_t^* |\boldsymbol{\theta}\_t)}{\partial \boldsymbol{\theta}\_t} \right\vert_{\boldsymbol{\theta}\_t = \hat{\boldsymbol{\theta}}\_t} = \left. \frac{\partial \log p(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t)}{\partial \boldsymbol{\theta}\_t} \right\vert_{\boldsymbol{\theta}\_t = \hat{\boldsymbol{\theta}}\_t} \\\\\\
\implies & \left. \boldsymbol{C}\_t(\boldsymbol{C}\_t^{-1}\boldsymbol{b}\_t - \boldsymbol{\theta}\_t) \right\vert_{\boldsymbol{\theta}\_t = \hat{\boldsymbol{\theta}}\_t} = \left. \dot{p}(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t) \right\vert_{\boldsymbol{\theta}\_t = \hat{\boldsymbol{\theta}}\_t} \\\\\\
\implies & \boldsymbol{b}\_t - \boldsymbol{C}\_t \hat{\boldsymbol{\theta}}\_t = \left. \dot{p}(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t) \right\vert_{\boldsymbol{\theta}\_t = \hat{\boldsymbol{\theta}}\_t} \\\\\\
\implies & \boldsymbol{b}\_t  = \boldsymbol{C}\_t \hat{\boldsymbol{\theta}}\_t + \left. \dot{p}(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t) \right\vert_{\boldsymbol{\theta}\_t = \hat{\boldsymbol{\theta}}\_t} ,
\end{aligned}
\end{equation*}

for  $t=1, \dots, T$.
\begin{equation*}
\begin{aligned}
& \left. \frac{\partial^2 \log g(\boldsymbol{y}\_t^* |\boldsymbol{\theta}\_t)}{\partial \boldsymbol{\theta}\_t \partial \boldsymbol{\theta}\_t'} \right\vert_{\boldsymbol{\theta}\_t = \hat{\boldsymbol{\theta}}\_t} = \left. \frac{\partial^2 \log p(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t)}{\partial \boldsymbol{\theta}\_t \partial \boldsymbol{\theta}\_t'} \right\vert_{\boldsymbol{\theta}\_t = \hat{\boldsymbol{\theta}}\_t} \\\\\\
\implies & - \boldsymbol{C}\_t = \left. \ddot{p}(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t) \right\vert_{\boldsymbol{\theta}\_t = \hat{\boldsymbol{\theta}}\_t} \\\\\\
\implies & \boldsymbol{C}\_t = - \left. \ddot{p}(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t) \right\vert_{\boldsymbol{\theta}\_t = \hat{\boldsymbol{\theta}}\_t} \\\\\\
\end{aligned}
\end{equation*}

for  $t=1, \dots, T$. Notice that $\boldsymbol{C}\_t = \boldsymbol{A}\_t^{-1}$, with $\boldsymbol{A}\_t$ defined in the "Mode estimation" section and evaluated at the mode $\hat{\boldsymbol{\theta}}\_t$, and $\boldsymbol{C}\_t^{-1}\boldsymbol{b}\_t =\hat{\boldsymbol{\theta}}\_t  + \boldsymbol{C}\_t^{-1} \left. \dot{p}(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t) \right\vert_{\boldsymbol{\theta}\_t = \hat{\boldsymbol{\theta}}\_t} = \boldsymbol{z}\_t$, with $\boldsymbol{z}\_t$ also defined in the "Mode estimation" section and evaluated at the mode $\hat{\boldsymbol{\theta}}\_t$. This implies that the linear Gaussian model \eqref{eq:ssm_approx}  evaluated at the mode $\hat{\boldsymbol{\theta}}\_t$ can actually be used as approximate model for the original nonlinear non-Gaussian state space model \eqref{eq:nonnormal_nonlinear_ssm}. I therefore conclude that $g(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t) = g(\boldsymbol{y}\_t^* |\boldsymbol{\theta}\_t) =g(\boldsymbol{z}\_t|\boldsymbol{\theta}\_t)$, with
\begin{equation} 
\log g(\boldsymbol{z}\_t|\boldsymbol{\theta}\_t) = - \frac{\dim(\boldsymbol{z}\_t)}{2} \log (2 \pi) + \frac{1}{2} \log (\det\boldsymbol{A}\_t^{-1}) - \frac{1}{2}(\boldsymbol{z}\_t - \boldsymbol{\theta}\_t)' \boldsymbol{A}\_t^{-1} (\boldsymbol{z}\_t - \boldsymbol{\theta}\_t),
\tag{9}
\label{eq:approx_logl}
\end{equation}

for  $t=1, \dots, T$, which can be used in order to evaluate the importance weights.

The method described above to choose $\boldsymbol{C}\_t$ and $\boldsymbol{b}\_t$, which is based on mode estimation, is called SPDK in the literature (after Shephard and Pitt (1997)[^ShephardPitt1997] and Durbin and Koopman (1997)[^DurbinKoopman1997]).



#### Simulation smoothing

The only ingredient that I am now missing in order to evaluate the importance weights, is the draw $\tilde{\boldsymbol{\theta}}\_t^{(i)}$ from $g(\boldsymbol{\theta}\_t|\boldsymbol{y}\_t)$. I do not have an expression for $g(\boldsymbol{\theta}\_t|\boldsymbol{y}\_t)$, but I can still compute $\tilde{\boldsymbol{\theta}}\_t^{(i)}$ by means of simulation smoothing.

I first discuss what simulation smoothing is for general multivariate normal distributions, and I will then extend it to the case of state space models. Suppose that I want to draw samples from the conditional Gaussian density $g(x|y)$. Let $x^+$ and $y^+$ be draws from the joint Gaussian distribution $g(x,y)$, and let $\hat{x} = \text{E} (x|y)$, and $\hat{x}^+ = \text{E} (x|y^+)$. It is possible to show that $\tilde{x} = \hat{x} + x^+ - \hat{x}^+$ is a draw from $g(x|y)$. This result holds for linear and Gaussian distributions.

In the case of a nonlinear non-Gaussian state space model, I wish to draw samples from $g(\boldsymbol{\theta}\_t|\boldsymbol{y}\_t)$ for $t=1,\dots,T$. Durbin and Koopman (2002)[^DurbinKoopman2002] show that the simulation smoothing works as follows:

1. Compute $\hat{\boldsymbol{\theta}}\_t = \text{E}(\boldsymbol{\theta}\_t|\boldsymbol{y}\_t)$ by mode estimation, i.e. using the (modified) Newton-Raphson method discussed in the "Mode estimation" section.
2. Initialize $ \boldsymbol{\theta}\_1^+ = \boldsymbol{Z} \boldsymbol{\alpha}\_1^+, \boldsymbol{\alpha}\_1^+  \sim  N(\boldsymbol{0}, \boldsymbol{P}\_1)$ (the diffuse elements of $\boldsymbol{P}\_1$ could be set equal to 0).
3. Draw $\boldsymbol{\theta}\_t^+$ from $g(\boldsymbol{\theta}\_t)$, i.e. the (approximate) linear Gaussian model for $\boldsymbol{\theta}\_t$, by following the steps:
    + Draw $\boldsymbol{\theta}\_t^+ \sim N(\boldsymbol{0}, \boldsymbol{Q})$, for $t=1,\dots, T$.
    + Obtain recursively $\boldsymbol{\alpha}\_{t+1}^+ = \boldsymbol{T} \boldsymbol{\alpha}\_t^+ + \boldsymbol{\eta}\_t^+$, and $\boldsymbol{\theta}\_t^+ = \boldsymbol{Z} \boldsymbol{\alpha}\_t^+$, for $t=1,\dots, T$.
4. Use $\boldsymbol{\theta}\_t^+$ to generate $\boldsymbol{y}\_t^+ \sim g(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t^+)$, i.e. the approximate linear Gaussian model for $\boldsymbol{y}\_t$, by following the steps:
    + Draw $\boldsymbol{\varepsilon}\_t^+ \sim N(\boldsymbol{0}, \boldsymbol{A}\_t)$, for $t=1,\dots,T$, with $\boldsymbol{A}\_t$ evaluated at the mode $\hat{\boldsymbol{\theta}}\_t$.
    + Obtain recursively $\boldsymbol{y}\_t^+ = \boldsymbol{\theta}\_t^+ + \boldsymbol{\varepsilon}\_t^+$, for $t=1,\dots,T$.
5. Compute $\hat{\boldsymbol{\theta}}\_t^+ = \text{E}(\boldsymbol{\theta}\_t|\boldsymbol{y}\_t^+)$ by KFS applied to the approximate linear Gaussian model \eqref{eq:ssm_approx}, with $\boldsymbol{A}\_t$ evaluated at the mode $\hat{\boldsymbol{\theta}}\_t$, and with $\boldsymbol{z}\_t = \boldsymbol{y}\_t^+$.
6. Compute $\tilde{\boldsymbol{\theta}}\_t = \hat{\boldsymbol{\theta}}\_t + \boldsymbol{\theta}\_t^+ - \hat{\boldsymbol{\theta}}\_t^+$; $\tilde{\boldsymbol{\theta}}\_t$ is a draw from $g(\boldsymbol{\theta}\_t|\boldsymbol{y}\_t)$.

For each draw $\tilde{\boldsymbol{\theta}}^{(i)}$, evaluate $g(Y_T | \tilde{\boldsymbol{\theta}}^{(i)}) = \prod_{t=1}^T g(\boldsymbol{z}\_t | \tilde{\boldsymbol{\theta}}\_t^{(i)})$, with $\boldsymbol{z}\_t | \tilde{\boldsymbol{\theta}}\_t^{(i)} \sim N(\tilde{\boldsymbol{\theta}}\_t^{(i)}, \boldsymbol{A}\_t)$, and $p(Y_T | \tilde{\boldsymbol{\theta}}^{(i)}) = \prod_{t=1}^T p(\boldsymbol{y}\_t | \tilde{\boldsymbol{\theta}}\_t^{(i)})$, in order to compute the importance weights.

Notice that the algorithm above is not valid when $\boldsymbol{A}\_t$ is not positive definite, since it would not be possible to implement step 4. In this case Jungbacker and Koopman (2007)[^JungbackerKoopman2007] show that, as long as $\boldsymbol{A}\_t$ is invertible, the sample $\tilde{\boldsymbol{\theta}}\_t^{(i)}$ is generated by the following simulation smoothing recursions:
\begin{equation} 
\begin{aligned}
\boldsymbol{B}\_t &= \boldsymbol{A}\_t^{-1} - \boldsymbol{F}\_t^{-1} - \boldsymbol{K}\_t' \boldsymbol{N}\_t \boldsymbol{K}\_t \\\\\\
\boldsymbol{R}\_t &= \boldsymbol{B}\_t^{-1} (\boldsymbol{A}\_t^{-1} \boldsymbol{Z} -\boldsymbol{K}\_t' \boldsymbol{N}\_t \boldsymbol{T} ) \\\\\\
\boldsymbol{w}\_t &\sim N(\boldsymbol{0}, \boldsymbol{B}\_t) \\\\\\
\boldsymbol{u}\_t &= \boldsymbol{A}\_t (\boldsymbol{w}\_t + \boldsymbol{F}\_t^{-1} \boldsymbol{v}\_t - \boldsymbol{K}\_t' \boldsymbol{r}\_t) \\\\\\
\boldsymbol{r}\_{t-1} &= \boldsymbol{Z}' \boldsymbol{A}\_t^{-1} \boldsymbol{u}\_t - \boldsymbol{R}\_t' \boldsymbol{w}\_t + \boldsymbol{T}' \boldsymbol{r}\_t \\\\\\
\boldsymbol{N}\_{t-1} &= \boldsymbol{R}\_t' \boldsymbol{B}\_t \boldsymbol{R}\_t - \boldsymbol{Z}' \boldsymbol{A}\_t^{-1} \boldsymbol{Z} +\boldsymbol{T}'\boldsymbol{N}\_t \boldsymbol{T},
\end{aligned}
\tag{10}
\label{eq:JK_smoothing}
\end{equation}

for $t=T,\dots,1$, and with the initializations $\boldsymbol{r}\_T=\boldsymbol{0}$ and $\boldsymbol{N}\_T=\boldsymbol{0}$. The elements $\boldsymbol{F}\_t$ and $\boldsymbol{v}\_t$ come from the Kalman filter recursions \eqref{eq:KF}. The simulated $\tilde{\boldsymbol{\theta}}^{(i)}$ from $g(\boldsymbol{\theta}|Y_T)$ is obtained as $\tilde{\boldsymbol{\theta}}^{(i)} = \hat{\boldsymbol{\theta}} + (\boldsymbol{u}\_1', \dots, \boldsymbol{u}\_T')' $. Moroever, in this case the log-likelihood of the approximating model takes the following form (instead of \eqref{eq:approx_logl}):
\begin{equation*}
\log g(\boldsymbol{z}\_t|\boldsymbol{\theta}\_t) = - \frac{\dim(\boldsymbol{z}\_t)}{2} \log (2 \pi) - \log(|(\det \boldsymbol{A}\_t)|) - \log(\det \boldsymbol{B}\_t^* ) - \frac{1}{2}\boldsymbol{b}\_t^{'(i)}\boldsymbol{b}\_t^{(i)},
\end{equation*}

for $t=1, \dots, T$, and with $\boldsymbol{B}\_t^* $ and $\boldsymbol{b}\_t^{(i)}$ such that, respectively, $\boldsymbol{B}\_t^* = \boldsymbol{B}\_t \boldsymbol{B}\_t'$ and $\boldsymbol{w}\_t^{(i)} = \boldsymbol{B}\_t^* \boldsymbol{b}\_t^{(i)}$. This method assumes that $\boldsymbol{B}\_t$ is positive definite because it is needed in order to generate $\boldsymbol{w}\_t^{(i)}$, and this is guaranteed if $\boldsymbol{b}\_t$ and $\boldsymbol{A}\_t$ are evaluated at the mode estimate $\hat{\boldsymbol{\theta}}\_t$, for $t=1,\dots,T$ (which implies that these modified smoothing recursions can only be employed with the SPDK method).



#### Evaluation and maximization of the log-likelihood

So far I have implicitly assumed that the matrices $\boldsymbol{Z}, \boldsymbol{T}$, $\boldsymbol{H}$, and $\boldsymbol{Q}$ were known. In practice, they may depend on parameters that need to be estimated by maximum likelihood. Let me define with $\boldsymbol{\beta}$ the vector that contains these parameters.

The likelihood of the nonlinear non-Gaussian model \eqref{eq:nonnormal_nonlinear_ssm} was expressed in the "Monte Carlo integration" section as
\begin{equation*}
p(Y_T; \boldsymbol{\beta}) = g(Y_T; \boldsymbol{\beta})\text{E}[w(Y_T|\boldsymbol{\theta}; \boldsymbol{\beta})],
\end{equation*}

where $g(Y_T)$ is the likelihood of the linear Gaussian approximating model \eqref{eq:ssm_approx}. 

In practice I evaluate the likelihood as Durbin and Koopman (2012, Chapter 11)[^durbinkoopman2012]:
\begin{equation*}
\hat{p}(Y_T; \boldsymbol{\beta}) = g(Y_T; \boldsymbol{\beta})\frac{1}{S}\sum_{i=1}^S w(Y_T|\tilde{\boldsymbol{\theta}}^{(i)};\boldsymbol{\beta}).
\end{equation*}

It is numerically more stable to maximize the log-likelihood
\begin{equation} 
\log \hat{p}(Y_T; \boldsymbol{\beta}) = \log g(Y_T; \boldsymbol{\beta}) + \log \left[ \frac{1}{S}\sum_{i=1}^S w(Y_T|\tilde{\boldsymbol{\theta}}^{(i)}; \boldsymbol{\beta}) \right] =  \log g(Y_T; \boldsymbol{\beta})+ \log \bar{w},
\tag{11}
\label{eq:logl_y_imp}
\end{equation}

where $\log g(Y_T; \boldsymbol{\beta})$ takes expression \eqref{eq:logl_y}, and is evaluated via the Kalman filter recursions with the difference that $\boldsymbol{y}\_t=\boldsymbol{z}\_t$ and $\boldsymbol{H}=\boldsymbol{A}\_t$, for $t=1,\dots,T$. Notice that irrespectively of how the diffuse initializations are dealt with in step 2 of the simulation smoothing, they have to be dealt with as usual when applying the Kalaman filter to the approximate linear Gaussian model \eqref{eq:ssm_approx}, and evaluating $\log g(Y_T; \boldsymbol{\beta})$. The maximization of $\hat{p}(Y_T; \boldsymbol{\beta})$ yields the maximum likelihood estimate of $\boldsymbol{\beta}$.

To start the maximization, an initial value for $\boldsymbol{\beta}$ can be obtained by maximizing the approximate log-likelihood
\begin{equation} 
\log \hat{p}(Y_T; \boldsymbol{\beta}) \approx \log g(Y_T; \boldsymbol{\beta}) + \log w(Y_T|\hat{\boldsymbol{\theta}}; \boldsymbol{\beta}),
\tag{12}
\label{eq:logl_y_imp_approx}
\end{equation}

where $\hat{\boldsymbol{\theta}}$ is the mode of $\log p(\boldsymbol{\theta}|Y_T)$.



#### Implementation remarks

I mentioned in the "Monte Carlo integration" section that the denominator of the importance weights can in practice be so small to cause numerical problems. I here report a way to implement importance weights that are numerically more stable, suggested in Koopman et al. (2018)[^Koopmanetal2018]. Recall the expression for the importance weights for a draw $i$, $w(Y_T|\boldsymbol{\theta}^{(i)}) = \frac{ p(Y_T|\boldsymbol{\theta}^{(i)})}{g(Y_T|\boldsymbol{\theta}^{(i)})}$. After some manipulation I can rewrite $w(Y_T|\boldsymbol{\theta}^{(i)}) = \exp(\bar{m}) \exp(m_i - \bar{m})$, where $m_i = \sum_{t=1}^T \log p(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t^{(i)}) - \sum_{t=1}^T \log g(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t^{(i)})$ and $\bar{m} = \frac{1}{S} \sum_{i=1}^S m_i$. The Monte Carlo estimator of $\boldsymbol{\theta}$ then becomes
\begin{equation*}
\hat{\boldsymbol{\theta}}\_{\text{M}} = \frac{\sum_{i=1}^S \tilde{\boldsymbol{\theta}}^{(i)} \exp(m_i - \bar{m})}{\sum_{i=1}^S  \exp(m_i - \bar{m})}.
\end{equation*}

The log-likelihood \eqref{eq:logl_y_imp} becomes (I omit $\boldsymbol{\beta}$ in the formula for simplicity)
\begin{equation*}
\log \hat{p}(Y_T) = \log g(Y_T) + \bar{m} - \log S + \log \left( \sum_{i=1}^S \exp(m_i - \bar{m}) \right).
\end{equation*}

In practice, if the values of the log-lieklihood are too large, maximize the average log-likelihood function expressed above: $\log \hat{p}(Y_T)/T$. The log-likelihood \eqref{eq:logl_y_imp_approx} stays the same.

The same random seed and number of draws $S$ need to be used when implementing simulation smoothing, if I am maximizing the log-likelihood.

If the state space model has a linear and a nonlinear part, then the importance sampling can only be done for the nonlinear part of the model by conditioning on the state variables that are linear; I only have to simulate the part of $\boldsymbol{\theta}^{(i)}$ that is nonlinear, and keep constant the rest. 



### Modified efficient importance sampling

So far I discussed how importance sampling works when $\boldsymbol{b}\_t$ and $\boldsymbol{C}\_t$ in equation \eqref{eq:logl_ystar} are chosen by mode estimation. There are also other ways to chose these importance parameters. Remember that the choice of $\boldsymbol{b}\_t$ and $\boldsymbol{C}\_t$ is fundamental in order to get an expression for the approximate linear Gaussian state space model \eqref{eq:ssm_approx}. The modified efficient importance sampling (MEIS) method of Koopman et al. (2018)[^Koopmanetal2018] and Scharth (2012, Chapter 5)[^Scharth2012], suggests to choose $\boldsymbol{b}\_t$ and $\boldsymbol{C}\_t$ such that the criterion
\begin{equation*}
I_t = \int \lambda^2(\boldsymbol{\theta}\_t, \boldsymbol{y}\_t ; \boldsymbol{\beta}) p(\boldsymbol{\theta}\_t, \boldsymbol{y}\_t ; \boldsymbol{\beta}) d \boldsymbol{\theta}\_t
\end{equation*}

is minimized for each $t$ separately, and where $\lambda(\boldsymbol{\theta}\_t, \boldsymbol{y}\_t ; \boldsymbol{\beta}) = \log w(\boldsymbol{\theta}\_t, \boldsymbol{y}\_t ; \boldsymbol{\beta})$. The criterion $I_t$ can be interpreted as the variance of the logged importance weight function with respect to $p(\boldsymbol{\theta}\_t, \boldsymbol{y}\_t ; \boldsymbol{\beta}\_t)$. The variables $\boldsymbol{b}\_t$ and $\boldsymbol{C}\_t$ determine $g(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t; \boldsymbol{\beta})$ that is part of $w(\boldsymbol{\theta}\_t, \boldsymbol{y}\_t ; \boldsymbol{\beta})$ and hence of $\lambda(\boldsymbol{\theta}\_t, \boldsymbol{y}\_t ; \boldsymbol{\beta})$. The function $I_t$ cannot be evaluated analytically but it is possible to approximate it as
\begin{equation*}
I_t^* = \int \lambda^2(\boldsymbol{\theta}\_t, \boldsymbol{y}\_t ; \boldsymbol{\beta}) w(\boldsymbol{\theta}\_t, \boldsymbol{y}\_t ; \boldsymbol{\beta}) g(\boldsymbol{\theta}\_t|\boldsymbol{y}\_t; \boldsymbol{\beta}) d \boldsymbol{\theta}\_t.
\end{equation*}

The evaluation and minimization of $I_t^* $ can be done via importance sampling, by minimzing
\begin{equation*}
I_t^* = \frac{1}{S} \sum_{i=1}^S \lambda^2(\boldsymbol{\theta}\_t^{(i)}, \boldsymbol{y}\_t ; \boldsymbol{\beta}) w(\boldsymbol{\theta}\_t^{(i)}, \boldsymbol{y}\_t ; \boldsymbol{\beta}), \quad \boldsymbol{\theta}\_t^{(i)} \sim g(\boldsymbol{\theta}\_t|\boldsymbol{y}\_t),
\end{equation*}

for $t=1, \dots, T$. It is possible to show that this minimization leads to the weighted least squares (WLS) solution 
\begin{equation}
(a_t, \boldsymbol{b}\_t', \text{vech} (\boldsymbol{C}\_t^* )')' = (\boldsymbol{X}\_t' \boldsymbol{W}\_t \boldsymbol{X}\_t)^{-1}\boldsymbol{X}\_t' \boldsymbol{W}\_t \boldsymbol{e}\_t,
\tag{13}
\label{eq:MEIS_WLS}
\end{equation}

for $t=1, \dots, T$, with $\boldsymbol{e}\_t = (\log p(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t^{(1)}; \boldsymbol{\beta}), \dots, \log p(\boldsymbol{y}\_t|\boldsymbol{\theta}\_t^{(S)}; \boldsymbol{\beta}))$, $\boldsymbol{W}\_t$ a $S \times S$ diagonal matrix whose diagonal elements correspond to the importance weights $m_i$, $i=1, \dots, S$ (defined in the "Implementation remarks" section), and 
\begin{equation*}
\boldsymbol{X}\_t = \left[ \begin{array}{ccc} 
1 & \boldsymbol{\theta}\_t^{(1)'} & -(1/2) \text{vech} (\boldsymbol{\theta}\_t^{(1)} \boldsymbol{\theta}\_t^{(1)'})' \\\\\\
1 & \boldsymbol{\theta}\_t^{(2)'} & -(1/2) \text{vech} (\boldsymbol{\theta}\_t^{(2)} \boldsymbol{\theta}\_t^{(2)'})' \\\\\\
\vdots & \vdots & \vdots \\\\\\
1 & \boldsymbol{\theta}\_t^{(S)'} & -(1/2) \text{vech} (\boldsymbol{\theta}\_t^{(S)} \boldsymbol{\theta}\_t^{(S)'})'
\end{array}    \right].
\end{equation*}

The matrix $\boldsymbol{C}\_t$ is retrieved by multiplying the off-diagonal elements of $\boldsymbol{C}\_t^* $ by 1/2.

Notice that in the WLS problem $S$ defines the "sample size". This also means that simulation smoothing, which samples $\boldsymbol{\theta}^{(i)}$, is done prior to the WLS estimation, for initial values of $\boldsymbol{A}\_t$ (for instance $\boldsymbol{A}\_t = \boldsymbol{I}$) and $\boldsymbol{z}\_t$ (for instance $\boldsymbol{z}\_t = \boldsymbol{0}$). The MEIS (simulation smoothing + WLS) is iterated until convergence (see Koopman et al. (2018)[^Koopmanetal2018] for a choice of convergence criterion).

Richard and Zhang (2007)[^RichardZhang2007] advise (I quote) _to set all weights equal to one during the initial iteration(s) to avoid numerical instability of WLS computations under high variance weights. Actually, for most problems, the OLS version of \eqref{eq:MEIS_WLS}, whereby all weights remain set equal to one, is essentially as efficient as its WLS counterpart. We generally recommend against presetting a number S of EIS iterations. Instead, we prefer using a stopping rule based upon a relative change threshold of the order of $10^{-3}-10^{-5}$. Occasional failure to converge is a clear indicator of potential pathologies (e.g. bimodality of the target density) which, in extreme cases, might require extensions of the class S of samplers_.



## Examples



### Poisson model

Suppose that the observed univariate series $y_t$ is Poisson-distributed with stochastic intensity $\lambda_t = \exp(\theta_t)$:
\begin{equation} 
\begin{aligned}
y_t &\sim P \left(\exp(\theta_t)\right) \\\\\\
\theta_t &= \alpha_t \\\\\\
\alpha_{t+1} &= 0.5 \alpha_t + \eta_t, \quad \eta_t \sim N(0, 0.2),
\end{aligned}
\tag{14}
\label{eq:Possion_model}
\end{equation}

for $t=1,\dots, T$. In this case $\boldsymbol{Z}=1, \boldsymbol{T}=0.5, \boldsymbol{Q}=0.2$. The density for this model is $p(Y_T|\boldsymbol{\theta}) = \prod_{t=1}^T p(y_t|\theta_t)$, where $p(y_t|\theta_t) = \exp (\theta_t y_t - \exp \theta_t - \log y_t!)$, and therefore $\dot{p}(y_t|\theta_t) = y_t - \exp \theta_t$ and $\ddot{p}(y_t|\theta_t) = - \exp \theta_t$.

I can now estimate the mode $\hat{\boldsymbol{\theta}}$ by applying the KFS to the approximate linear Gaussian model

\begin{equation*} 
\begin{aligned}
g_t + \frac{1}{\exp(g_t)}(y_t - \exp(g_t)) &= \theta_t + \varepsilon_t, \quad \varepsilon_t \sim N \left(0, \frac{1}{\exp(g_t)}\right) \\\\\\
\theta_t &= \alpha_t \\\\\\
\alpha_{t+1} &= \boldsymbol{T} \alpha_t + \eta_t, \quad \eta_t \sim N (0, \boldsymbol{Q}),
\end{aligned}
\end{equation*}

for $t=1,\dots, T$, and for an initial guess of $g_t$ (for instance $g_t = \bar{y}$ for $t=1, \dots, T$). Once $\hat{\boldsymbol{\theta}}$ has been obtained by applying the KFS, replace $\boldsymbol{g}=\hat{\boldsymbol{\theta}}$ and re-estimate $\boldsymbol{\theta}$ by KFS. Do so until convergence. The final estimate  $\hat{\boldsymbol{\theta}}$ is the mode. Then obtain $z_t = \hat{\theta}\_t + \frac{1}{\exp(\hat{\theta}\_t)}(y_t - \exp(\hat{\theta}\_t))$, and $A_t = \frac{1}{\exp(\hat{\theta}\_t)}$, for $t=1,\dots, T$. I can use these two elements in order to implement the simulation smoothing.

The importance weights are $w(Y_T|\boldsymbol{\theta}^{(i)}) = \frac{ p(Y_T|\boldsymbol{\theta}^{(i)})}{g(Y_T|\boldsymbol{\theta}^{(i)})}$, where  $p(Y_T|\boldsymbol{\theta}^{(i)}) = \prod_{t=1}^T p(y_t|\theta_t^{(i)})$, with 
\begin{equation*} 
p(y_t|\theta_t^{(i)}) = \exp (\theta_t^{(i)} y_t - \exp \theta_t^{(i)} - \log y_t!),
\end{equation*}

for $t=1,\dots, T$, and  $g(Y_T|\boldsymbol{\theta}^{(i)}) = \prod_{t=1}^T g(z_t|\theta_t^{(i)})$, with
\begin{equation*} 
 g(z_t|\theta_t) = \exp \left( - \frac{1}{2} \log (2 \pi) + \frac{1}{2} \log (A_t^{-1}) - \frac{1}{2}(z_t - \theta_t^{(i)})' A_t^{-1} (z_t - \theta_t^{(i)}) \right),
\end{equation*}

for $t=1,\dots, T$.

The following pictures show the results of importance sampling estimation based on the mode estimation (or local approximation) method for choosing $\boldsymbol{b}\_t$ and $\boldsymbol{C}\_t$ discussed in the "Choice of the importance density" section, for simulated series according to model \eqref{eq:Possion_model}.



### Stochastic volatility model



## Acknowledgments

I am grateful to Siem Jan Koopman for his guidance in exploring these methods, to Ilka van de Werve for sharing materials that helped me undertsand this topic, and to Franz Palm, Stephan Smeekes and Jan van den Brakel for regular and useful discussions around it. All errors are my own.



## References

[^DurbinKoopman1997]: Durbin, J. and Koopman, S. J. (1997). _Monte Carlo Maximum Likelihood Estimation for Non-Gaussian State Space Models_. Biometrika, 84(3):669–684.

[^DurbinKoopman2002]: Durbin, J. and Koopman, S. J. (2002). _A Simple and Efficient Simulation Smoother for State Space Time Series Analysis_. Biometrika, 89(3):603–615.

[^durbinkoopman2012]: Durbin, J. and Koopman, S. J. (2012), _Time Series Analysis by State Space Methods: Second Edition_, Oxford Statistical Science Series. OUP Oxford.

[^JungbackerKoopman2007]: Jungbacker, B. and Koopman, S. J. (2007). _Monte Carlo Estimation for Nonlinear Non-Gaussian State Space Models_. Biometrika, 94(4):827–839.

[^Koopmanetal2018]: Koopman, S. J., Lit, R., and Nguyen, T. M. (2018). _Modified Efficient Importance Sampling for Partially Non-Gaussian State Space Models_. Statistica Neerlandica, 73(1):44–62.

[^RichardZhang2007]: Richard, J.-F. and Zhang, W. (2007). _Efficient High-dimensional Importance Sampling_. Journal of Econometrics, 141(2):1385–1411.

[^Scharth2012]: Scharth, M. (2012). _Essays on Monte Carlo Methods for State Space Models_. PhD thesis, Vrije Universiteit Amsterdam.

[^ShephardPitt1997]: Shephard, N. and Pitt, M. (1997). _Likelihood Analysis of Non-Gaussian Measurement Time Series_. Biometrika, 84(3):653–667.
