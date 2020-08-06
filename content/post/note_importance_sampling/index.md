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


## Short intro

Let us compare the performance of C++, Matlab, Python and R when performing typical econometrical/statistical tasks. The word ‘typical’ is quite ambiguous since computational requirements can vary substantially between subfields. As such, I will consider a setting that is standard yet also computationally demanding: _bootstrap inference on the Durbin-Watson test statistic_. This setting is easy to understand and the computational performance of the programming languages is probably representative for a wide range of Monte Carlo (MC) simulations. MC simulations are very often needed in research when relying on frequentist statistics.

## The Durbin-Watson test statistic
Consider a standard multivariate regression model $y_t^{} = \mathbf{x}_t' \boldsymbol \beta + u_t^{}$ for $t = 1, 2, . . . , T$. The presence of serial correlation in $u_t$ will render standard ordinary least squares (OLS) inference invalid. The Durbin-Watson test statistic was one of the first test statistics to test for the presence of serial correlation.[^1] It is computed as follows:

1. Calculate the OLS estimator $\widehat{\\boldsymbol \\beta}=(\mathbf{X}'\mathbf{X})^{-1} \mathbf{X}'\mathbf{y}$.
2. Obtain the residuals $\widehat{u}_t=y\_t- \mathbf{x}\_t'\widehat{\boldsymbol \beta}$ for $t = 1, 2, . . . , T$.
3. The Durbin-Watson test statisic is now computed as $DW= \frac{\\sum\_{t=2}^T \left(\widehat{u}\_t-\widehat{u}\_{t-1}\right)^2  }{\sum_{t=1}^T \widehat{u}_t^2}$.


## Linear Gaussian state space model

The linear Gaussian state space model takes the following form:
$$
\mathbf{y}\_t &= \mathbf{Z} \boldsymbol{\alpha}\_t + \boldsymbol{\varepsilon}\_t, \\qquad  \boldsymbol{\varepsilon}_\t \sim N \left( \mathbf{0}, \mathbf{H} \right)
$$




