# Useful commands for data manipulation

Here I collect a few handy functions for data manipulation in R. They are mostly things I often seem to need, but often forget how to do.

## Adegenet/genind

#### Select random subset of individuals from genind file

```{r}
my.genind[i=sample(nInd(my.genind),5)]
```
This would subsample 5 random individuals from a genind file called my.genind.
