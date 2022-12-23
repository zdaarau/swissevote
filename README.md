# swissevote: Provides Functions Around the Swiss E-Voting Trial Data Collected by the Centre for Democracy Studies Aarau (ZDA)

<a href="https://cran.r-project.org/package=swissevote" class="pkgdown-release"><img src="https://r-pkg.org/badges/version/swissevote" alt="CRAN Status" /></a>

swissevote can parse cantonal voting register data, scrape authority websites for ballot data and provides other auxiliary functions around the Swiss e-voting trial data collected by the Centre for Democracy Studies Aarau (ZDA) at the University of Zurich, Switzerland.

## Details

### Raw data files

The **cantonal raw data files** are *not* included in this package due to legal restrictions and/or concerns regarding voter privacy and thus **have to be provided by the user**. The path to the folder holding these files must be set in the [R option](https://rdrr.io/r/base/options.html) **`swissevote.path_raw_data`** and the files under this path are expected to be organized and named in the following way:

``` fs
[swissevote.path_raw_data]/
├──BE/
|  ├──BE_YYYY-MM-DD.EXT
|  └──...
├──GE/
|  ├──vYYYYNN.EXT
|  └──...
└──NE/
   ├──NE_YYYY-MM-DD.EXT
   └──...
```

The Genevan raw data files must be named according to the column `filename` in `swissevote:::metadata_geneva_raw_datasets` (which has been defined by the Geneva State Chancellery). Raw data files from the other cantons must be named by the canton abbreviation and the ballot date (e.g. `NE_2019-02-10`).

### Caching

Time-consuming operations like reading in the raw datasets or scraping ballot dates from cantonal websites are cached to the local filesystem by default (using [pkgpins](https://pkgpins.rpkg.dev)). The maximum cache lifespan for all affected functions can be set in the global option **`swissevote.global_cache_lifespan`**[^1] (defaults to *30 days* if unset).

## Documentation

[![Netlify Status](https://api.netlify.com/api/v1/badges/ce410db6-b85a-4707-9358-f0d3449398c3/deploy-status)](https://app.netlify.com/sites/swissevote-rpkg-dev/deploys)

The documentation of this package is found [here](https://pal.rpkg.dev).

## Installation

To install the latest development version of swissevote, run the following in R:

``` r
if (!("remotes" %in% rownames(installed.packages()))) {
  install.packages(pkgs = "remotes",
                   repos = "https://cloud.r-project.org/")
}

remotes::install_gitlab(repo = "zdaarau/rpkgs/swissevote")
```

## Development

### R Markdown format

This package’s source code is written in the [R Markdown](https://rmarkdown.rstudio.com/) file format to facilitate practices commonly referred to as [*literate programming*](https://en.wikipedia.org/wiki/Literate_programming). It allows the actual code to be freely mixed with explanatory and supplementary information in expressive Markdown format instead of having to rely on [`#` comments](https://cran.r-project.org/doc/manuals/r-release/R-lang.html#Comments) only.

All the `.gen.R` suffixed R source code found under [`R/`](R/) is generated from the respective R Markdown counterparts under [`Rmd/`](Rmd/) using [`pkgpurl::purl_rmd()`](https://pkgpurl.rpkg.dev/dev/reference/purl_rmd.html)[^2]. Always make changes only to the `.Rmd` files – never the `.R` files – and then run `pkgpurl::purl_rmd()` to regenerate the R source files.

### Coding style

This package borrows a lot of the [Tidyverse](https://www.tidyverse.org/) design philosophies. The R code adheres to the principles specified in the [Tidyverse Design Guide](https://principles.tidyverse.org/) wherever possible and is formatted according to the [Tidyverse Style Guide](https://style.tidyverse.org/) (TSG) with the following exceptions:

-   Line width is limited to **160 characters**, double the [limit proposed by the TSG](https://style.tidyverse.org/syntax.html#long-lines) (80 characters is ridiculously little given today’s high-resolution wide screen monitors).

    Furthermore, the preferred style for breaking long lines differs. Instead of wrapping directly after an expression’s opening bracket as [suggested by the TSG](https://style.tidyverse.org/syntax.html#long-lines), we prefer two fewer line breaks and indent subsequent lines within the expression by its opening bracket:

    ``` r
    # TSG proposes this
    do_something_very_complicated(
      something = "that",
      requires = many,
      arguments = "some of which may be long"
    )

    # we prefer this
    do_something_very_complicated(something = "that",
                                  requires = many,
                                  arguments = "some of which may be long")
    ```

    This results in less vertical and more horizontal spread of the code and better readability in pipes.

-   Usage of [magrittr’s compound assignment pipe-operator `%<>%`](https://magrittr.tidyverse.org/reference/compound.html) is desirable[^3].

-   Usage of [R’s right-hand assignment operator `->`](https://rdrr.io/r/base/assignOps.html) is not allowed[^4].

-   R source code is *not* split over several files as [suggested by the TSG](https://style.tidyverse.org/package-files.html) but instead is (as far as possible) kept in the single file [`Rmd/swissevote.Rmd`](Rmd/swissevote.Rmd) which is well-structured thanks to its [Markdown support](#r-markdown-format).

As far as possible, these deviations from the TSG plus some additional restrictions are formally specified in the [lintr configuration file](https://github.com/jimhester/lintr#project-configuration) [`.lintr`](.lintr), so lintr can be used right away to check for formatting issues:

``` r
pkgpurl::lint_rmd()
```

---

[^1]: Its value must be a valid [lubridate duration](https://lubridate.tidyverse.org/reference/as.duration.html#details) which can be a natural-language string like `"2 days 1 minute 30 seconds"` (or short `2d1min30s`).

[^2]: This naming convention as well as the very idea to leverage the R Markdown format to author R packages was originally proposed by Yihui Xie. See his excellent [blog post](https://yihui.name/rlp/) for more detailed information about the benefits of literate programming techniques and some practical examples. Note that using `pkgpurl::purl_rmd()` is a less cumbersome alternative to the Makefile approach outlined by him.

[^3]: The TSG [explicitly instructs to avoid this operator](https://style.tidyverse.org/pipes.html#assignment-2) – presumably because it’s relatively unknown and therefore might be confused with the forward pipe operator `%>%` when skimming code only briefly. I don’t consider this to be an actual issue since there aren’t many sensible usage patterns of `%>%` at the beginning of a pipe sequence inside a function – I can only think of creating side effects and relying on [R’s implicit return of the last evaluated expression](https://rdrr.io/r/base/function.html). Therefore – and because I really like the `%<>%` operator – it’s usage is welcome.

[^4]: The TSG [explicitly accepts `->` for assignments at the end of a pipe sequence](https://style.tidyverse.org/pipes.html#assignment-2) while Google’s R Style Guide [considers this bad practice](https://google.github.io/styleguide/Rguide.html#right-hand-assignment) because it “makes it harder to see in code where an object is defined”. I second the latter.
