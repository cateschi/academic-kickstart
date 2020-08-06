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
\label{eq:normal_linear_ssm}
\end{equation}

for $t=1, \dots, T$, where $\boldsymbol{y}\_t$ is a $n \times 1$ vector, and $\boldsymbol{\alpha}\_t$ is a $m \times 1$ vector. The observation equation of the linear Gaussian state space model \eqref{eq:normal_linear_ssm} implies that $\boldsymbol{y}\_t \sim g(\boldsymbol{y}\_t | \boldsymbol{\alpha}\_t) = g_{\boldsymbol{\varepsilon}\_t}(\boldsymbol{y}\_t - \boldsymbol{Z} \boldsymbol{\alpha}\_t) = NID(\boldsymbol{0}, \boldsymbol{H})$, where $g$ indicates a linear Gaussian density. The log-likelihood of $\boldsymbol{y}\_t | \boldsymbol{\alpha}\_t$ takes the form:

\begin{equation*}
\log g(\boldsymbol{y}\_t | \boldsymbol{\alpha}\_t) = - \frac{n}{2} \log(2 \pi) - \frac{1}{2} \log(\det\boldsymbol{H}) - \frac{1}{2} (\boldsymbol{y}\_t - \boldsymbol{Z} \boldsymbol{\alpha}\_t)' \boldsymbol{H}^{-1} (\boldsymbol{y}\_t - \boldsymbol{Z} \boldsymbol{\alpha}\_t),
\end{equation*}

for $t=1, \dots, T$. The transition equation of the linear Gaussian state space model \eqref{eq:normal_linear_ssm} implies that $\boldsymbol{\alpha}\_{t+1} \sim g(\boldsymbol{\alpha}\_{t+1} | \boldsymbol{\alpha}\_t) = g_{\eta_t}(\boldsymbol{\alpha}\_{t+1} -  \boldsymbol{T} \boldsymbol{\alpha}\_t) = NID(\boldsymbol{0}, \boldsymbol{Q})$. The log-likelihood of $\boldsymbol{\alpha}\_{t+1} | \boldsymbol{\alpha}\_t$ takes the form:
\begin{equation} \label{eq:gaussian_state}
\log g(\boldsymbol{\alpha}\_{t+1} | \boldsymbol{\alpha}\_t ) = - \frac{n}{2} \log(2 \pi) - \frac{1}{2} \log(\det \boldsymbol{Q}) - \frac{1}{2} (\boldsymbol{\alpha}\_{t+1} -  \boldsymbol{T} \boldsymbol{\alpha}\_t)' \boldsymbol{Q}^{-1} (\boldsymbol{\alpha}\_{t+1} -  \boldsymbol{T} \boldsymbol{\alpha}\_t).
\end{equation}

for $t=1, \dots, T$. The initial value of the state vector $\boldsymbol{\alpha}\_1 \sim g(\boldsymbol{\alpha}\_1) = N(\boldsymbol{0}, \boldsymbol{P}\_1)$. Finally, we define the the joint density:
\begin{equation*}
g(\boldsymbol{\alpha}, Y_T) = g(\boldsymbol{\alpha}\_1) \prod_{t=1}^T g(\boldsymbol{y}\_t | \boldsymbol{\alpha}\_t) g(\boldsymbol{\alpha}\_{t+1}|\boldsymbol{\alpha}\_t) = g(\boldsymbol{\alpha}\_1) \prod_{t=1}^T g_{\varepsilon_t}(\boldsymbol{y}\_t - \boldsymbol{Z} \boldsymbol{\alpha}\_t)g_{\eta_t}(\boldsymbol{\alpha}\_{t+1} -  \boldsymbol{T} \boldsymbol{\alpha}\_t),
\end{equation*}

where $\boldsymbol{\alpha}$ if the $mT \times 1$ state vector, and $Y_T$ is the $nT \times 1$ vector of observed series.

The parameters of the linear Gaussian state space model \eqref{eq:normal_linear_ssm} can be estimated by maximizing the following log-likelihood
\begin{equation} \label{eq:logl_y}
\log g(\boldsymbol{y}\_t) = - \frac{n}{2} \log(2 \pi) - \frac{1}{2} \log(\det \boldsymbol{F}\_t) - \frac{1}{2} \boldsymbol{v}\_t' \boldsymbol{F}\_t^{-1} \boldsymbol{v}\_t,
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
\boldsymbol{P}\_{t+1} &= \boldsymbol{T} \boldsymbol{P}\_t \left( \boldsymbol{T} - \boldsymbol{K}\_t \boldsymbol{Z} \right)' + \boldsymbol{R} \boldsymbol{Q}\_t \boldsymbol{R}',
\end{aligned}
\end{equation*}

for $t = 1, \dots, T$. The $m \times 1$ vector $\boldsymbol{a}\_{t|t}$ is the filter estimate of the state vector $\boldsymbol{\alpha}\_t$, and $\boldsymbol{P}\_{t|t}$ is the respective covariance matrix. 

Let us define $\boldsymbol{\theta}\_t = \boldsymbol{Z} \boldsymbol{\alpha}\_t$ the $n \times 1$ signal vector (this will simplify some derivations). More accurate estimates of the signal vector can be efficiently obtained by applying an additional Kalman smoother; we define the estimate from the Kalman filter smoother (KFS) as $\hat{\boldsymbol{\theta}}\_t = \E (\boldsymbol{\theta}\_t | \boldsymbol{y}\_t) = \argmax_{\boldsymbol{\alpha}\_t} g(\boldsymbol{\theta}\_t|\boldsymbol{y}\_t)$, for $t=1, \dots, T$, which implies that $\hat{\boldsymbol{\theta}}\_t$ is the mode of $g(\boldsymbol{\theta}\_t|\boldsymbol{y}\_t)$. The Kalman smoother recursions of \cite{durbinkoopman2012} are:

\vspace{0.5em}
\begin{minipage}{.46\textwidth}
\begin{equation*}
\begin{aligned}
\vr_{t-1} &= \boldsymbol{Z}'\mF_t^{-1} \vv_t + \left(\mT - \boldsymbol{K}\_t \boldsymbol{Z} \right)'\vr_t \\
\hat{\boldsymbol{\alpha}}\_t &= \va_t + \mP_t \vr_{t-1}
\end{aligned}
\end{equation*} 
\end{minipage}
\begin{minipage}{.5\textwidth}
\begin{equation} \label{eq:KFS}
\begin{aligned}
\mN_{t-1} &= \boldsymbol{Z}' \mF_t^{-1} \boldsymbol{Z} + \left(\mT - \boldsymbol{K}\_t \boldsymbol{Z} \right)' \mN_t \left(\mT - \boldsymbol{K}\_t \boldsymbol{Z} \right) \\
\mV_t &= \mP_t - \mP_t \mN_{t-1} \mP_t,
\end{aligned}
\end{equation}
\end{minipage}  \vspace{0.5em}

for $t=T, \dots, 1$, with $\vr_T= \vzeros$, $\mN_T = \vzeros$, and $\mV_t$ being the smoothed covariance matrix of $\hat{\boldsymbol{\alpha}}\_t$.




