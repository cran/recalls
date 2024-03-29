# Access U.S. Federal Government Recall Data #

[![Build Status](https://travis-ci.org/rOpenGov/recalls.png?branch=master)](https://travis-ci.org/rOpenGov/recalls)

**recalls** provides access to U.S. Federal Government recall data on drugs and food from the Food and Drug Administration, car safety from the National Highway Traffic Safety Administration, and product safety recalls from the Consumer Product Safety Commission.

## Installation ##

**recalls** is [available on GitHub](http://github.com/rOpenGov/recalls) and can (soon) be installed from within R from your favorite CRAN mirror:

```
install.packages("recalls")
```

And the latest development version, available here, can be installed directly using  [devtools](http://cran.r-project.org/web/packages/devtools/index.html):

```
# install.packages("devtools")
library("devtools")
install_github("rOpenGov/recalls")
```

## Code Examples ##

### Tracking Vehicle Recalls by Make and Year ###


```r
library("recalls")
dat <- expand.grid(year = 1990:2010, make = c("ford", "gmc", "chrysler", "honda", 
    "toyota"), stringsAsFactors = FALSE)
dat$count <- sapply(1:nrow(dat), function(d) {
    r <- recalls(make = dat$make[d], year = dat$year[d], page = 1, per_page = 1)
    return(attr(r, "total"))
})

plot(NA, xlim = c(1990, 2010), ylim = c(0, max(dat$count)), xlab = "Year", ylab = "Recalls")
x <- mapply(function(z, col) lines(count ~ year, data = dat, subset = make == 
    z, col = col, lwd = 2), unique(dat$make), rainbow(5))
legend("topleft", legend = unique(dat$make), fill = rainbow(5))
```

![plot of chunk vehicles](inst/figure/vehicles.png) 



### Are consumer recalls increasing or decreasing? ##


```r
require("recalls")
consum <- data.frame(start = paste(1990:2010, "-01-01", sep = ""), end = paste(1990:2010, 
    "-12-31", sep = ""), stringsAsFactors = FALSE)
consum$count <- sapply(1:nrow(consum), function(d) {
    r <- recalls(organization = "CPSC", start_date = consum$start[d], end_date = consum$end[d])
    return(attr(r, "total"))
})

with(consum, plot(count, type = "l", lwd = 2, ylim = c(0, max(consum$count)), 
    xaxt = "n", xlab = "Year", ylab = "Recalls"))
axis(1, 1:21, 1990:2010)
```

![plot of chunk recalls](inst/figure/recalls.png) 

