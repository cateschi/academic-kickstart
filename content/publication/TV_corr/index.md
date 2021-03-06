---
title: "Time-varying state correlations in state space models and their estimation via indirect inference"
authors: ["Caterina Schiavoni, Siem Jan Koopman, Franz Palm, Stephan Smeekes and Jan van den Brakel"]
date: "2021-02-23T00:00:00"
doi: ""

# Schedule page publish date (NOT publication's date).
publishDate: ""

# Publication type.
# Legend: 0 = Uncategorized; 1 = Conference paper; 2 = Journal article;
# 3 = Preprint / Working Paper; 4 = Report; 5 = Book; 6 = Book section;
# 7 = Thesis; 8 = Patent
publication_types: ""

# Publication name and optional abbreviated publication name.
publication: ""
publication_short: ""

abstract: "Statistics Netherlands uses a state space model to estimate the Dutch unemployment by using monthly series about the labour force surveys (LFS). More accurate estimates of this variable can be obtained by including auxiliary information in the model, such as the univariate administrative series of claimant counts. Legislative changes and economic crises may affect the relation between survey-based and auxiliary series. This time-changing relationship is captured by a time-varying correlation parameter in the covariance matrix of the transition equation's error terms. We treat the latter parameter as a state variable, which makes the state space model become nonlinear and therefore its estimation by Kalman filtering and maximum likelihood infeasible. We therefore propose an indirect inference approach to estimate the static parameters of the model, which employs cubic splines for the auxiliary model, and a bootstrap filter method to estimate the time-varying correlation together with the other state variables of the model. We conduct a Monte Carlo simulation study that shows that our proposed methodology is able to correctly estimate both the time-constant parameters and the state vector of the model. Empirically we find that the financial crisis of 2008  triggered a deeper and more prolonged deviation between the survey-based and the claimant counts series, than a legislative change in 2015. Promptly tackling such changes, which our proposed method does, results in more realistic real-time unemployment estimates."

# Summary. An optional shortened abstract.
summary: ""

tags: ""
featured: false

links:
url_pdf: ''
url_code: 'https://github.com/cateschi/Time_varying_correlation'
url_dataset: ''
url_poster: ''
url_project: ''
url_slides: ''
url_source: ''
url_video: ''
url_preprint: "https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3792448"

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com/photos/s9CC2SKySJM)'
  focal_point: ""
  preview_only: false

# Associated Projects (optional).
#   Associate this publication with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `internal-project` references `content/project/internal-project/index.md`.
#   Otherwise, set `projects: []`.
projects:
- internal-project

# Slides (optional).
#   Associate this publication with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides: "example"` references `content/slides/example/index.md`.
#   Otherwise, set `slides: ""`.
slides: example
---


