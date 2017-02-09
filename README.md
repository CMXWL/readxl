
<!-- README.md is generated from README.Rmd. Please edit that file -->
readxl
======

[![Travis-CI Build Status](https://travis-ci.org/tidyverse/readxl.svg?branch=master)](https://travis-ci.org/tidyverse/readxl) [![AppVeyor Build Status](https://ci.appveyor.com/api/projects/status/github/tidyverse/readxl?branch=master&svg=true)](https://ci.appveyor.com/project/tidyverse/readxl) [![Coverage Status](https://img.shields.io/codecov/c/github/tidyverse/readxl/master.svg)](https://codecov.io/github/tidyverse/readxl?branch=master) [![CRAN\_Status\_Badge](https://www.r-pkg.org/badges/version/readxl)](https://cran.r-project.org/package=readxl)

The readxl package makes it easy to get data out of Excel and into R. Compared to many of the existing packages (e.g. gdata, xlsx, xlsReadWrite) readxl has no external dependencies so it's easy to install and use on all operating systems. It is designed to work with *tabular* data stored in a single sheet.

readxl supports both the legacy `.xls` format and the modern xml-based `.xlsx` format. `.xls` support is made possible the with [libxls](http://sourceforge.net/projects/libxls/) C library, which abstracts away many of the complexities of the underlying binary format. To parse `.xlsx`, we use the [RapidXML](http://rapidxml.sourceforge.net) C++ library.

Installation
------------

The easiest way to install the latest released version from CRAN is to install the whole tidyverse (NOTE: you will still need to load readxl explicitly, i.e. it is not a core tidyverse package loaded via `library(tidyverse)`.)

``` r
install.packages("tidyverse")
```

Alternatively, install just readxl from CRAN:

``` r
install.packages("readxl")
```

Or install the development version from GitHub:

``` r
# install.packages("devtools")
devtools::install_github("tidyverse/readxl")
```

Usage
-----

readxl includes example files `datasets.xlsx` and `datasets.xls`, containing datasets you already know and love. They were created with the help of openxlsx and Excel.

``` r
l <- list("iris" = iris, "mtcars" = mtcars, chickwts = chickwts, quakes = quakes)
openxlsx::write.xlsx(l, file = "inst/extdata/datasets.xlsx")
```

readxl reads both xls and xlsx files.

``` r
library(readxl)

xlsx_example <- system.file("extdata/datasets.xlsx", package = "readxl")
read_excel(xlsx_example)
#> # A tibble: 150 × 5
#>   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
#>          <dbl>       <dbl>        <dbl>       <dbl>   <chr>
#> 1          5.1         3.5          1.4         0.2  setosa
#> 2          4.9         3.0          1.4         0.2  setosa
#> 3          4.7         3.2          1.3         0.2  setosa
#> # ... with 147 more rows

xls_example <- system.file("extdata/datasets.xls", package = "readxl")
read_excel(xls_example)
#> # A tibble: 150 × 5
#>   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
#>          <dbl>       <dbl>        <dbl>       <dbl>   <chr>
#> 1          5.1         3.5          1.4         0.2  setosa
#> 2          4.9         3.0          1.4         0.2  setosa
#> 3          4.7         3.2          1.3         0.2  setosa
#> # ... with 147 more rows
```

List the sheet names with `excel_sheets()`.

``` r
excel_sheets(xlsx_example)
#> [1] "iris"     "mtcars"   "chickwts" "quakes"
```

Specify a worksheet by name or number.

``` r
read_excel(xlsx_example, sheet = "chickwts")
#> # A tibble: 71 × 2
#>   weight      feed
#>    <dbl>     <chr>
#> 1    179 horsebean
#> 2    160 horsebean
#> 3    136 horsebean
#> # ... with 68 more rows
read_excel(xls_example, sheet = 4)
#> # A tibble: 1,000 × 5
#>      lat   long depth   mag stations
#>    <dbl>  <dbl> <dbl> <dbl>    <dbl>
#> 1 -20.42 181.62   562   4.8       41
#> 2 -20.62 181.03   650   4.2       15
#> 3 -26.00 184.10    42   5.4       43
#> # ... with 997 more rows
```

If `NA`s are represented by something other than blank cells, set the `na` argument.

``` r
read_excel(xlsx_example, na = "setosa")
#> # A tibble: 150 × 5
#>   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
#>          <dbl>       <dbl>        <dbl>       <dbl>   <chr>
#> 1          5.1         3.5          1.4         0.2    <NA>
#> 2          4.9         3.0          1.4         0.2    <NA>
#> 3          4.7         3.2          1.3         0.2    <NA>
#> # ... with 147 more rows
```

Features
--------

-   Re-encodes non-ASCII characters to UTF-8.

-   Loads datetimes into POSIXct columns. Both Windows (1900) and Mac (1904) date specifications are processed correctly.

-   Blank columns are automatically dropped (*but this is changing!*). Blank rows that appear before the data are automatically dropped; embedded blank rows are not.

-   It returns a tibble, i.e. a data frame with an additional `tbl_df` class. Among other things, this provide nicer printing.
