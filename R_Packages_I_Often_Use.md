# R packages I often use

Because i often break my R or RStudio installation on my local machine (e.g., switching compilers all of a sudden and then re-installing Rcpp), I decided to make this document to remind myself what packages I need to install on a fresh R installation. This is mostly focused on my genomic work, as that's what I am doing the most at the moment.

## 1. Utility packages - Rcpp, Devtools & BiocManager

So, as mentioned above, I installed Rcpp, devtools and BiocManager. We need BiocManager for packages like SNPRelate and co.

```{r eval=FALSE}
install.packages("Rccp")
install.packages("devtools")
install.packages("BiocManager")
```

Then, I tried to open this markdown file and it prompted me to install all the required packages (e.g., rlang, knitr, etc.)

## 2. Tidyverse - because it's awesome

Now, let's install the [tidyverse](https://www.tidyverse.org/) because it is awesome.

```{r eval=FALSE}
install.packages("tidyverse")
```

## 3. Core genomic packages

Now, let's install the main genetic/genomic packages. For me those are adegenet for data manipulation, and dartR and radiator for filtering DArTseq data. Install SNPRelate before dartR, just to make things easier (i.e., I don't like warning messages). For now [radiator](https://thierrygosselin.github.io/radiator/) is only on github (as of Oct 2020), but it should be on CRAN soonish.

```{r eval=FALSE}
install.packages("adegenet")
BiocManager::install("SNPRelate")
install.packages("dartR")
devtools::install_github("thierrygosselin/radiator")
```

That's it for now. I just reinstalled XQuartz, R and RStudio, and the day is about to be over. I will update this as I get back to my current pop gen project (current covers the last 4 years and It should be published already, but life)
