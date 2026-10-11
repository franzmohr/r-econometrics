[![Netlify Status](https://api.netlify.com/api/v1/badges/5d280351-cbc6-4fff-91bf-9c55c6f1eb58/deploy-status)](https://app.netlify.com/projects/r-econometrics/deploys)

# r-econometrics

Source of [www.r-econometrics.com](https://www.r-econometrics.com/), a site about
using R for econometric analysis. The focus is less on the maths behind the
statistical methods and more on their application, so that beginners become
familiar with the language quickly.

## Building the site

The site is a [Hugo](https://gohugo.io/) project written with
[blogdown](https://github.com/rstudio/blogdown). The
[liquorice](https://github.com/eliasson/liquorice) theme is vendored in
`themes/liquorice`, so a plain clone is enough:

```bash
git clone git@github.com:franzmohr/r-econometrics.git
```

Then serve it locally with:

```bash
hugo server
```

Netlify pins the Hugo version in `netlify.toml`; keep the local version in step
with it to avoid surprises between a local preview and a deploy.

## Writing content

Articles are authored as R Markdown and knitted to `.html` with blogdown, which
is what gets committed. `ignoreFiles` in `config.toml` keeps Hugo from picking up
the `.Rmd` sources directly.

Figures belong in `static/<section>/<page>_files/figure-html/` and are referenced
from the knitted HTML by absolute path, for example
`/timeseries/varintro_files/figure-html/plot-1.png`.

A leading underscore marks a draft, but note that this only hides `.Rmd` files,
via `ignoreFiles`. Hugo has no such convention of its own, so an underscored
`.html` or `.md` file in `content/` is published like any other page. To keep a
draft out of the build, leave it as `.Rmd` only, or set `draft: true` in its
front matter.

## Deployment

Netlify builds from `main` and publishes `public/`. Both the build command and
the Hugo version live in `netlify.toml`; the production branch is configured in
the Netlify UI rather than in the repository.
