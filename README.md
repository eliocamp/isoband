
<!-- README.md is generated from README.Rmd. Please edit that file -->

# isoband

Generate contour lines (isolines) and contour polygons (isobands) from
regularly spaced grids containing elevation data.

## Installation

You can install the development version of isoband from github with:

``` r
devtools::install_github("clauswilke/isoband")
```

## Example

Simple isobanding examples:

``` r
library(isoband)
library(grid)

plot_iso <- function(m, vlo, vhi) {
  df1 <- isobands((1:ncol(m))/(ncol(m)+1), (nrow(m):1)/(nrow(m)+1), m, vlo, vhi)[[1]]
  df2 <- isolines((1:ncol(m))/(ncol(m)+1), (nrow(m):1)/(nrow(m)+1), m, vlo)[[1]]
  g <- expand.grid(x = (1:ncol(m))/(ncol(m)+1), y = (nrow(m):1)/(nrow(m)+1))
  grid.newpage()
  grid.points(g$x, g$y, default.units = "npc", pch = 19, size = unit(0.5, "char"))
  grid.path(df1$x, df1$y, df1$id, gp = gpar(fill = "#acd8e6a0", col = NA))
  grid.polyline(df2$x, df2$y, df2$id)
}

m <- matrix(c(0, 1, 0,
              0, 1, 0,
              0, 0, 0), 3, 3, byrow = TRUE)
plot_iso(m, 0.5, 1.5)
```

<img src="man/figures/README-example-1.png" width="100%" />

``` r

m <- matrix(c(0, 0, 0, 0, 0, 0,
              0, 0, 0, 1, 1, 0,
              0, 0, 1, 1, 1, 0,
              0, 1, 1, 0, 0, 0,
              0, 0, 0, 1, 0, 0,
              0, 0, 0, 0, 0, 0), 6, 6, byrow = TRUE)
plot_iso(m, 0.5, 1.5)
```

<img src="man/figures/README-example-2.png" width="100%" />

``` r

m <- matrix(c(0, 0, 1, 1, 0, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0.8, 0), 4, 4, byrow = TRUE)
plot_iso(m, 0.5, 1.5)
```

<img src="man/figures/README-example-3.png" width="100%" />

Isobanding the famous volcano dataset:

``` r
m <- volcano
b <- isobands((1:ncol(m))/(ncol(m)+1), (nrow(m):1)/(nrow(m)+1), m, c(120, 150), c(140, 152))
l <- isolines((1:ncol(m))/(ncol(m)+1), (nrow(m):1)/(nrow(m)+1), m, c(120, 140, 150, 152))

grid.newpage()
grid.path(b[[1]]$x, b[[1]]$y, b[[1]]$id, gp = gpar(fill = "lightblue", col = NA))
grid.path(b[[2]]$x, b[[2]]$y, b[[2]]$id, gp = gpar(fill = "tomato", col = NA))
for (i in seq_along(l)) {
  grid.polyline(l[[i]]$x, l[[i]]$y, l[[i]]$id)
}
```

<img src="man/figures/README-unnamed-chunk-1-1.png" width="100%" />

Isolining is ~25% faster than `grDevices::contourLines()`, isobanding is
a little slower.

``` r
microbenchmark::microbenchmark(
  grDevices::contourLines(1:ncol(volcano), 1:nrow(volcano), volcano, levels = 10*(10:18)),
  isolines(1:ncol(volcano), 1:nrow(volcano), volcano, 10*(10:18)),
  isobands(1:ncol(volcano), 1:nrow(volcano), volcano, 10*(9:17), 10*(10:18))
)
#> Unit: milliseconds
#>                                                                                            expr
#>  grDevices::contourLines(1:ncol(volcano), 1:nrow(volcano), volcano,      levels = 10 * (10:18))
#>                               isolines(1:ncol(volcano), 1:nrow(volcano), volcano, 10 * (10:18))
#>             isobands(1:ncol(volcano), 1:nrow(volcano), volcano, 10 * (9:17),      10 * (10:18))
#>       min       lq     mean   median       uq       max neval
#>  1.636726 1.863497 2.721861 2.010746 3.097843 10.727686   100
#>  1.303393 1.384711 1.669972 1.543951 1.759873  3.141554   100
#>  3.916997 4.264658 4.651490 4.528215 4.850670  7.655414   100
```
