# pkgcheck

<!-- badges: start -->

[![R build
status](https://github.com/ropensci-review-tools/pkgcheck/workflows/R-CMD-check/badge.svg)](https://github.com/ropensci-review-tools/pkgcheck/actions?query=workflow%3AR-CMD-check)
[![gitlab
push](https://github.com/ropensci-review-tools/pkgcheck/workflows/push-to-gitlab/badge.svg)](https://github.com/ropensci-review-tools/pkgcheck/actions?query=workflow%3Apush-to-gitlab)
[![codecov](https://codecov.io/gh/ropensci-review-tools/pkgcheck/branch/main/graph/badge.svg)](https://codecov.io/gh/ropensci-review-tools/pkgcheck)
[![Project Status:
Concept](https://www.repostatus.org/badges/latest/concept.svg)](https://www.repostatus.org/#concept)
<!-- badges: end -->

Check whether a package is ready for submission to
[rOpenSci](https://ropensci.org)’s peer review system. The primary
function collates the output of
[`goodpractice`](https://github.com/mangothecat/goodpractice), including
`R CMD check` results, a number of statistics via the [`pkgstats`
package](https://github.com/ropensci-review-tools/pkgstats), and checks
for package structure expected for rOpenSci submissions. The output of
this function immediately indicates whether or not a package is “Ready
to Submit”.

## Installation

The easiest way to install this package is via the [associated
`r-universe`](https://ropensci-review-tools.r-universe.dev/ui#builds).
As shown there, simply enable the universe with

``` r
options (repos = c (
    ropenscireviewtools = "https://ropensci-review-tools.r-universe.dev",
    CRAN = "https://cloud.r-project.org"
))
```

And then install the usual way with,

``` r
install.packages ("pkgcheck")
```

Alternatively, the package can be installed by first installing either
the [remotes](https://remotes.r-lib.org) or
[pak](https://pak.r-lib.org/) packages and running one of the following
lines:

``` r
remotes::install_github ("ropensci-review-tools/pkgcheck")
pak::pkg_install ("ropensci-review-tools/pkgcheck")
```

The package can then loaded for use with

``` r
library (pkgcheck)
```

## Setup

The [`pkgstats`
package](https://github.com/ropensci-review-tools/pkgstats) also
requires the system libraries [`ctags`](https://ctags.io) and [GNU
`global`](https://www.gnu.org/software/global/) to be installed.
Procedures to install these libraries on various operating systems are
described in a [`pkgstats`
vignette](https://docs.ropensci.org/pkgstats/articles/installation.html).
This package also uses the [GitHub GraphQL
API](https://developer.github.com/v4) which requires a local GitHub
token to be stored with an unambiguous name including `GITHUB`, such as
`GITHUB_TOKEN` (recommended), or `GITHUB_PAT` (for Personal
Authorization Token). This can be obtained from GitHub (via your user
settings), and stored using

``` r
Sys.setenv ("GITHUB_TOKEN" = "<my_token>")
```

This can also be set permanently by putting this line in your
`~/.Renviron` file (or creating this if it does not yet exist). Once
`pkgstats` has been successfully installed, the `pkgcheck` package can
then be loaded via a `library` call:

``` r
library (pkgcheck)
```

## Usage

The package primarily has one function, `pkgcheck`, which accepts the
single argument, `path`, specifying the local location of a git
repository to be analysed. The following code generates a reproducible
report by first downloading a local clone of a repository called
[`srr-demo`](https://github.com/mpadge/srr-demo), which contains the
skeleton of an [`srr` (Software Review Roclets)
package](https://github.com/ropensci-review-tools/srr), generated with
the [`srr_stats_pkg_skeleton()`
function](https://docs.ropensci.org/srr/reference/srr_stats_pkg_skeleton.html):

``` r
mydir <- file.path (tempdir (), "srr-demo")
gert::git_clone ("https://github.com/mpadge/srr-demo", path = mydir)
x <- pkgcheck (mydir)
```

That object has default `print` and `summary` methods. The latter can be
used to simply check whether a package is ready for submission:

``` r
summary (x)
## 
## ── demo 0.0.0.9000 ────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
## 
## ✔ Package name is available
## ✖ does not have a 'codemeta.json' file.
## ✖ does not have a 'contributing' file.
## ✔ uses 'roxygen2'.
## ✔ 'DESCRIPTION' has a URL field.
## ✖ 'DESCRIPTION' does not have a BugReports field.
## ✖ Package has no HTML vignettes
## ✔ All functions have examples.
## ✖ Package has no continuous integration checks.
## ✖ Package coverage failed
## ✔ R CMD check found no errors.
## ✔ R CMD check found no warnings.
## 
## ℹ Current status:
## ✖ This package is not ready to be submitted.
## 
```

A package may only be submitted when the summary contains all ticks and
no cross symbols. (These symbols are colour-coded with green ticks and
red crosses when generated in a terminal; GitHub markdown only renders
them in black-and-white.) The object returned from the `pkgcheck`
function is a complex nested list with around a dozen primary
components. Full information can be obtained by simply calling the
default `print` method by typing the object name (`x`).

## The `pkgcheck` GitHub action

The `pkgcheck` package also has an associated GitHub action in [the
`pkgcheck-action`
repository](https://github.com/ropensci-review-tools/pkgcheck-action).
You can use this action to run `pkgcheck` every time you push commits to
GitHub, just like the `rcmdcheck()` checks which can be installed and
run via [the `usethis::use_github_action_check_standard()`
function](https://usethis.r-lib.org/reference/github_actions.html).
`pkgcheck` includes an analogous function,
[`use_github_action_pkgcheck()`](https://docs.ropensci.org/pkgcheck/reference/use_github_action_pkgcheck.html),
which will download the workflow file defining the action into your
local `.github/workflows` folder. See [the `pkgcheck-action`
repository](https://github.com/ropensci-review-tools/pkgcheck-action)
for more details.

You can also add a `pkgcheck` badge, just like that `rcmdcheck` badge,
that will always reflect the current state of your repository’s
`pkgcheck` results. To add a badge, copy the following line to your
README file, filling in details of the GitHub organization and
repository name (`<org>` and `<repo>`, respectively):

``` markdown
[![pkgcheck](https://github.com/<org>/<repo>/workflows/pkgcheck/badge.svg)](https://github.com/<org>/<repo>/actions?query=workflow%3Apkgcheck)
```

## What is checked?

All current checks are listed in [a separate
vignette](https://docs.ropensci.org/pkgcheck/articles/list-checks.html).

### Summary of Check Results

Calling `summary()` on the object returned by the [`pkgcheck()`
function](https://docs.ropensci.org/pkgcheck/reference/pkgcheck.html)
will generate a checklist like that shown above. This checklist will
also be automatically generated when a package is first submitted to
rOpenSci, and is used by the editors to assess whether to process a
submission. Authors must ensure prior to submission that there are no
red crosses in the resultant list. (In the unlikely circumstances that a
package is unable to pass particular checks, explanations should be
given upon submission about why those checks fail, and why review may
proceed in spite of such failures.)

### Detailed Check Results

Full details of check results can be seen by `print`-ing the object
returned by the [`pkgcheck()`
function](https://docs.ropensci.org/pkgcheck/reference/pkgcheck.html)
(or just by typing the name of this object in the console.)

The package includes an additional function,
[`checks_to_markdown()`](https://docs.ropensci.org/pkgcheck/reference/checks_to_markdown.html),
with a parameter, `render`, which can be set to `TRUE` to automatically
render a HTML-formatted representation of the check results, and open it
in a browser. The formatting differs only slightly from the terminal
output, mostly through the components of
[`goodpractice`](http://mangothecat.github.io/goodpractice/) being
divided into distinct headings, with explicit statements in cases where
components pass all checks (the default screen output inherits directly
from that package, and only reports components which *do not* pass all
checks).

This
[`checks_to_markdown()`](https://docs.ropensci.org/pkgcheck/reference/checks_to_markdown.html)
function returns the report in markdown format, suitable for pasting
directly into a GitHub issue, or other markdown-compatible place. (The
[`clipr` package](https://github.com/mdlincoln/clipr) can be used to
copy this directly to your local clipboard with `write_clip(md)`, where
`md` is the output of `checks_to_markdown()`.)

## Caching and running `pkgcheck` in the background

Running the [`pgkcheck`
function](https://docs.ropensci.org/pkgcheck/reference/pkgcheck.html)
can be time-consuming, primarily because the
[`goodpractice`](https://github.com/mangothecat/goodpractice) component
runs both a full `R CMD check`, and calculates code coverage of all
tests. To avoid re-generating these results each time, the package saves
previous reports to a local cache directory defined in
`Sys.getenv("PKGCHECK_CACHE_DIR")`.

You may manually erase the contents of this `pkgcheck` subdirectory at
any time at no risk beyond additional time required to re-generate
contents. By default checks presume packages use `git` for version
control, with checks updated only when code is updated via `git commit`.
Checks for packages that do not use `git` are updated when any files are
modified.

The first time
[`pkgcheck()`](https://docs.ropensci.org/pkgcheck/reference/pkgcheck.html)
is applied to a package, the checks will be stored in the cache
directory. Calling that function a second time will then load the cached
results, and so enable checks to be returned much faster. For code which
is frequently updated, such as for packages working on the final stages
prior to submission, it may still be necessary to repeatedly call
[`pkgcheck()`](https://docs.ropensci.org/pkgcheck/reference/pkgcheck.html)
after each modification, a step which may still be inconveniently
time-consuming. To facilitate frequent re-checking, the package also has
a [`pkgcheck_bg()`
function](https://docs.ropensci.org/pkgcheck/reference/pkgcheck_bg.html)
which is effectively identical to the main [`pkgcheck()`
function](https://docs.ropensci.org/pkgcheck/reference/pkgcheck.html),
except it runs in the background, enabling you to continue coding while
checks are running.

The [`pkgcheck_bg()`
function](https://docs.ropensci.org/pkgcheck/reference/pkgcheck_bg.html)
returns a handle to the [`callr::r_bg()`
process](https://callr.r-lib.org/reference/r_bg.html) in which the
checks are running. Typing the name of the returned object will
immediately indicate whether the checks are still running, or whether
they have finished. That handle is itself an [`R6`
object](http://r6.r-lib.org/) with a number of methods, notably
including
[`get_result()`](https://callr.r-lib.org/reference/get_result.html)
which can be used to access the checks once the process has finished.
Alternatively, as soon as the background process, the normal
(foreground) [`pkgcheck()`
function](https://docs.ropensci.org/pkgcheck/reference/pkgcheck.html)
may be called to quickly re-load the cached results.

## Prior Work

[The `checklist` package](https://github.com/inbo/checklist) for
“checking packages and R code”.

## Code of Conduct

Please note that this package is released with a [Contributor Code of
Conduct](https://ropensci.org/code-of-conduct/). By contributing to this
project, you agree to abide by its terms.

## Contributors


<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->

All contributions to this project are gratefully acknowledged using the [`allcontributors` package](https://github.com/ropenscilabs/allcontributors) following the [all-contributors](https://allcontributors.org) specification. Contributions of any kind are welcome!

### Code

<table>

<tr>
<td align="center">
<a href="https://github.com/mpadge">
<img src="https://avatars.githubusercontent.com/u/6697851?v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/commits?author=mpadge">mpadge</a>
</td>
<td align="center">
<a href="https://github.com/maelle">
<img src="https://avatars.githubusercontent.com/u/8360597?v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/commits?author=maelle">maelle</a>
</td>
<td align="center">
<a href="https://github.com/assignUser">
<img src="https://avatars.githubusercontent.com/u/16141871?v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/commits?author=assignUser">assignUser</a>
</td>
<td align="center">
<a href="https://github.com/annakrystalli">
<img src="https://avatars.githubusercontent.com/u/5583057?v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/commits?author=annakrystalli">annakrystalli</a>
</td>
<td align="center">
<a href="https://github.com/noamross">
<img src="https://avatars.githubusercontent.com/u/571752?v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/commits?author=noamross">noamross</a>
</td>
</tr>

</table>


### Issue Authors

<table>

<tr>
<td align="center">
<a href="https://github.com/piyalkarum">
<img src="https://avatars.githubusercontent.com/u/48254643?u=370433a2ace6a030f2551575bc08fa53664fbd8f&v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/issues?q=is%3Aissue+author%3Apiyalkarum">piyalkarum</a>
</td>
<td align="center">
<a href="https://github.com/christophsax">
<img src="https://avatars.githubusercontent.com/u/1390827?u=ce6363f6da758d1bb85987d021cacc34a81c8837&v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/issues?q=is%3Aissue+author%3Achristophsax">christophsax</a>
</td>
<td align="center">
<a href="https://github.com/steffilazerte">
<img src="https://avatars.githubusercontent.com/u/14676081?u=0c1a4469750eba5a6f398c35ba34a2dd600a873e&v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/issues?q=is%3Aissue+author%3Asteffilazerte">steffilazerte</a>
</td>
<td align="center">
<a href="https://github.com/phuongquan">
<img src="https://avatars.githubusercontent.com/u/38658964?u=5761c2835f0a4853c9d7fb844061056a23d29564&v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/issues?q=is%3Aissue+author%3Aphuongquan">phuongquan</a>
</td>
<td align="center">
<a href="https://github.com/s3alfisc">
<img src="https://avatars.githubusercontent.com/u/19531450?u=26be80705a31079d973246c98bf3b26d9131e7d3&v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/issues?q=is%3Aissue+author%3As3alfisc">s3alfisc</a>
</td>
<td align="center">
<a href="https://github.com/Bisaloo">
<img src="https://avatars.githubusercontent.com/u/10783929?u=38e3754466eaa200e20f0609709467b6331cdfbe&v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/issues?q=is%3Aissue+author%3ABisaloo">Bisaloo</a>
</td>
<td align="center">
<a href="https://github.com/Robinlovelace">
<img src="https://avatars.githubusercontent.com/u/1825120?u=461318c239e721dc40668e4b0ce6cc47731328ac&v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/issues?q=is%3Aissue+author%3ARobinlovelace">Robinlovelace</a>
</td>
</tr>


<tr>
<td align="center">
<a href="https://github.com/schneiderpy">
<img src="https://avatars.githubusercontent.com/u/77991319?u=4242d4c5942fced6368dd5c68221e6618092cbf8&v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/issues?q=is%3Aissue+author%3Aschneiderpy">schneiderpy</a>
</td>
<td align="center">
<a href="https://github.com/eliocamp">
<img src="https://avatars.githubusercontent.com/u/8617595?v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/issues?q=is%3Aissue+author%3Aeliocamp">eliocamp</a>
</td>
</tr>

</table>


### Issue Contributors

<table>

<tr>
<td align="center">
<a href="https://github.com/dgkf">
<img src="https://avatars.githubusercontent.com/u/18220321?u=bef717254e5b877159fa712e2b8ad6952c816064&v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/issues?q=is%3Aissue+commenter%3Adgkf">dgkf</a>
</td>
<td align="center">
<a href="https://github.com/cboettig">
<img src="https://avatars.githubusercontent.com/u/222586?u=dfbe54d3b4d538dc2a8c276bb5545fdf4684752f&v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/issues?q=is%3Aissue+commenter%3Acboettig">cboettig</a>
</td>
<td align="center">
<a href="https://github.com/jhollist">
<img src="https://avatars.githubusercontent.com/u/5438539?u=815aa29d708acd16180967c0ffaf81fc64a08bf4&v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/issues?q=is%3Aissue+commenter%3Ajhollist">jhollist</a>
</td>
<td align="center">
<a href="https://github.com/ropensci-review-bot">
<img src="https://avatars.githubusercontent.com/u/77673839?u=f26c3eb2a3b6c74a7f100d4a707116d24d723d81&v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/issues?q=is%3Aissue+commenter%3Aropensci-review-bot">ropensci-review-bot</a>
</td>
<td align="center">
<a href="https://github.com/santikka">
<img src="https://avatars.githubusercontent.com/u/8639149?v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/issues?q=is%3Aissue+commenter%3Asantikka">santikka</a>
</td>
<td align="center">
<a href="https://github.com/bnicenboim">
<img src="https://avatars.githubusercontent.com/u/5982330?u=ec5543c6d11255fd330fc03f5880a1d7bdefadd7&v=4" width="100px;" alt=""/>
</a><br>
<a href="https://github.com/ropensci-review-tools/pkgcheck/issues?q=is%3Aissue+commenter%3Abnicenboim">bnicenboim</a>
</td>
</tr>

</table>

<!-- markdownlint-enable -->
<!-- prettier-ignore-end -->
<!-- ALL-CONTRIBUTORS-LIST:END -->
