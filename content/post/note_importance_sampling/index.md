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

for $t=1, \ldots, T$, where $\boldsymbol{y}\_t$ is a $n \times 1$ vector, and $\boldsymbol{\alpha}\_t$ is a $m \times 1$ vector. The observation equation of the linear Gaussian state space model \eqref{eq:normal_linear_ssm} implies that $\boldsymbol{y}\_t \sim g(\boldsymbol{y}\_t | \boldsymbol{\alpha}\_t) = g_{\boldsymbol{\varepsilon}\_t}(\boldsymbol{y}\_t - \boldsymbol{Z} \boldsymbol{\alpha}\_t) = NID(\mathbf{0}, \boldsymbol{H})$, where $g$ indicates a linear Gaussian density. The log-likelihood of $\boldsymbol{y}\_t | \boldsymbol{\alpha}\_t$ takes the form:

\begin{equation*}
\log g(\vy_t | \valpha_t) = - \frac{n}{2} \log(2 \pi) - \frac{1}{2} \log(\det\mH) - \frac{1}{2} (\vy_t - \mZ \valpha_t)' \mH^{-1} (\vy_t - \mZ \valpha_t),
\end{equation*}

for $t=1, \dots, T$. The transition equation of the linear Gaussian state space model \eqref{eq:normal_linear_ssm} implies that $\valpha_{t+1} \sim g(\valpha_{t+1} | \valpha_t) = g_{\veta_t}(\valpha_{t+1} -  \mT \valpha_t) = NID(\vzeros, \mQ)$. The log-likelihood of $\valpha_{t+1} | \valpha_t$ takes the form:
\begin{equation} \label{eq:gaussian_state}
\log g(\valpha_{t+1} | \valpha_t) = - \frac{n}{2} \log(2 \pi) - \frac{1}{2} \log(\det \mQ) - \frac{1}{2} (\valpha_{t+1} -  \mT \valpha_t)' \mQ^{-1} (\valpha_{t+1} -  \mT \valpha_t).
\end{equation}

for $t=1, \dots, T$. The initial value of the state vector $\valpha_1 \sim g(\valpha_1) = N(\vzeros, \mP_1)$. Finally, we define the the joint density:
\begin{equation*}
g(\valpha, Y_T) = g(\valpha_1) \prod_{t=1}^T g(\vy_t | \valpha_t) g(\valpha_{t+1}|\valpha_t) = g(\valpha_1) \prod_{t=1}^T g_{\vvarepsilon_t}(\vy_t - \mZ \valpha_t)g_{\veta_t}(\valpha_{t+1} -  \mT \valpha_t),
\end{equation*}

where $\valpha$ if the $mT \times 1$ state vector, and $Y_T$ is the $nT \times 1$ vector of observed series.

The parameters of the linear Gaussian state space model \eqref{eq:normal_linear_ssm} can be estimated by maximizing the following log-likelihood
\begin{equation} \label{eq:logl_y}
\log g(\vy_t) = - \frac{n}{2} \log(2 \pi) - \frac{1}{2} \log(\det \mF_t) - \frac{1}{2} \vv_t' \mF_t^{-1} \vv_t,
\end{equation}

where the prediction errors $\vv_t$ and their covariance matrix $\mF_t$ are obtained via the Kalman filter recursions

\vspace{0.5em}
\begin{minipage}{.46\textwidth}
\begin{equation*}
\begin{aligned}
\vv_t &= \vy_t - \mZ \va_t \\
\mF_t &= \mZ \mP_t \mZ' + \mH \\
\mK_t &= \mT \mP_t \mZ' \mF_t^{-1} \\
\va_{t|t} &= \va_t + \mP_t \mZ' \mF_t^{-1} \vv_t \\
\end{aligned}
\end{equation*} 
\end{minipage}
\begin{minipage}{.5\textwidth}
\begin{equation} \label{eq:KF}
\begin{aligned}
\mP_{t|t} &= \mP_t - \mP_t \mZ' \mF_t^{-1} \mZ \mP_t \\
\va_{t+1} &= \mT \va_t + \mK_t \vv_t \\
\mP_{t+1} &= \mT \mP_t \left( \mT - \mK_t \mZ \right)' + \mR \mQ_t \mR',
\end{aligned}
\end{equation}
\end{minipage}  \vspace{0.5em}

for $t = 1, \dots, T$. The $m \times 1$ vector $\va_{t|t}$ is the filter estimate of the state vector $\valpha_t$, and $\mP_{t|t}$ is the respective covariance matrix. 

Let us define $\vtheta_t = \mZ \valpha_t$ the $n \times 1$ signal vector (this will simplify some derivations). More accurate estimates of the signal vector can be efficiently obtained by applying an additional Kalman smoother; we define the estimate from the Kalman filter smoother (KFS) as $\hat{\vtheta}_t = \E (\vtheta_t | \vy_t) = \argmax_{\valpha_t} g(\vtheta_t|\vy_t)$, for $t=1, \dots, T$, which implies that $\hat{\vtheta}_t$ is the mode of $g(\vtheta_t|\vy_t)$. The Kalman smoother recursions of \cite{durbinkoopman2012} are:

\vspace{0.5em}
\begin{minipage}{.46\textwidth}
\begin{equation*}
\begin{aligned}
\vr_{t-1} &= \mZ'\mF_t^{-1} \vv_t + \left(\mT - \mK_t \mZ \right)'\vr_t \\
\hat{\valpha}_t &= \va_t + \mP_t \vr_{t-1}
\end{aligned}
\end{equation*} 
\end{minipage}
\begin{minipage}{.5\textwidth}
\begin{equation} \label{eq:KFS}
\begin{aligned}
\mN_{t-1} &= \mZ' \mF_t^{-1} \mZ + \left(\mT - \mK_t \mZ \right)' \mN_t \left(\mT - \mK_t \mZ \right) \\
\mV_t &= \mP_t - \mP_t \mN_{t-1} \mP_t,
\end{aligned}
\end{equation}
\end{minipage}  \vspace{0.5em}

for $t=T, \dots, 1$, with $\vr_T= \vzeros$, $\mN_T = \vzeros$, and $\mV_t$ being the smoothed covariance matrix of $\hat{\valpha}_t$.




