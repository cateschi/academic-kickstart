+++
title = "A Spatial Exploration of Regional Housing Prices in California"
subtitle = "A brief introduction to spatial econometrics"

date = 2018-06-01T00:00:00
lastmod = 2020-01-01T00:00:00
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

tags = []
summary = "A brief introduction to spatial econometrics"

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

We will explore the empirical application by Brady (2011)[^1] to ilustrate the use of spatial econometrics. The focus is on data visualization (with Matlab) and the estimation of some basic spatial models. We do not aim to provide a sound and detailed econometric model. Quite the contrary, we aim to display a set of basic tools that can serve as a point of departure for the interested reader. 

## Regional housing prices in California[^2]
The dataset by Brady (2011) contains: (1) monthly average single-family sales prices for 31 counties in California, (2) the monthly unemployment rates in these counties, (3) a construction variable reporting the number of new units of single-family housing built per month, and (4) additional variables that we will omit for ease of exposition. Similarly, we will ignore the time series dimension of the data to avoid difficulties that can arrise from the panel data structure. In conclusion, with $i=\{1,2,\ldots,31\}$ enumerating the counties, we define:
* $HP_i$: the average housing price in county $i$,
* $Unemp_i$: monthly unemployment rate in county $i$,
* $Constr_i$: number of new single housing units in county $i$,

and restrict our attention to one specific month in the sample: .

## A visual inspection of the data

Simple algebraic manipulations show that the Durbin-Watson statistic can be expressed in terms of the first order sample autocorrelation $\widehat{\rho}= \frac{\\sum\_{t=2}^T \widehat{u}\_t\widehat{u}\_{t-1} }{\\sum\_{t=1}^T \widehat{u}\_t^2}$, namely $DW=2(1-\\widehat{\\rho})-\\frac{\\widehat{u}\_1^2+\\widehat{u}\_T^2}{\\sum\_{t=1}^T \\widehat{u}\_t^2}$. Based on this result we would expect outcomes close to the value 2 if first order autocorrelation is absent. Values different from 2 indicate serial correlation. Using the Durbin-Watson statistic to carry out a formal hypothesis test is more complicated because its (asymptotic) distribution is difficult to derive. This motivates the use of a bootstrap approach.[^2] The specific steps are:

1. Estimate $\boldsymbol \beta$ by OLS and compute the residual series $\{\\widehat{u}\_t \}$ as well as the Durbin-Watson statistic $DW$.
2. Compute $\\widehat{\rho}$ (see the definition above) and $\\widehat{\varepsilon\,}\_t=\widehat{u}\_t- \\widehat{\rho} \widehat{u}\_{t-1}$ for $t=2,3,\\ldots,T$.
3. Draw bootstrap errors $\varepsilon\_t^\star$ by resampling with replacement from $\\{\\widehat{\varepsilon\,}\_2,\\widehat{\varepsilon\,}\_3,\ldots,\\widehat{\varepsilon\,}\_T \\}$. Store these in the vector $\boldsymbol \\varepsilon^\star=[\varepsilon\_1^\star,\varepsilon\_2^\star,\ldots,\varepsilon\_T^\star]' $.
4. Set $\mathbf{u}^\star=\boldsymbol \\varepsilon^\star$ (to impose the null hypothesis in the bootstrap sample), construct the bootstrap sample $\mathbf{y}^\star=\mathbf{X}\\widehat{\boldsymbol \\beta}+\mathbf{u}^\star$, and compute the DW statistic from $\mathbf{y}^\star$.
5. Repeat steps 3-4 $B$ times and store these outcomes in a list $\left\\{ DW\_b^\star \right\\}\_{b=1}^B$.
6. For a given significance level $\alpha$, we define $q\_{\alpha/2}^\star$ and $q\_{1-\alpha/2}^\star$ as the $\alpha/2$ and $1-\alpha/2$ empirical quantiles of $\left\\{ DW\_b^\star \right\\}\_{b=1}^B$. We reject the null hypothesis of no serial correlation if either $DW < q\_{\alpha/2}^\star$ or if $DW>q\_{1-\alpha/2}^\star$.




## References

[^1]: R. Brady (2011), _Measuring the Diffusion of Housing Prices across Space and over Time_, Journal of Applied Econometrics
[^2]: The data set is freely available from the Journal of Applied Econometrics Data Archive ([here](http://qed.econ.queensu.ca/jae/)). It can be found in the repository linked to volume 26, number 2, and 2011.
