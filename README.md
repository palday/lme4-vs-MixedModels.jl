# Model Fits in lme4 and MixedModels.jl
Phillip Alday  
June 2015  




R session info:

```r
> sessionInfo()
```

```
R version 3.2.0 (2015-04-16)
Platform: x86_64-pc-linux-gnu (64-bit)
Running under: Ubuntu 13.10

locale:
 [1] LC_CTYPE=en_US.UTF-8       LC_NUMERIC=C               LC_TIME=en_AU.UTF-8        LC_COLLATE=en_US.UTF-8    
 [5] LC_MONETARY=en_AU.UTF-8    LC_MESSAGES=en_US.UTF-8    LC_PAPER=en_AU.UTF-8       LC_NAME=C                 
 [9] LC_ADDRESS=C               LC_TELEPHONE=C             LC_MEASUREMENT=en_AU.UTF-8 LC_IDENTIFICATION=C       

attached base packages:
[1] stats     graphics  grDevices utils     datasets  methods   base     

loaded via a namespace (and not attached):
 [1] magrittr_1.5    formatR_1.2     tools_3.2.0     htmltools_0.2.6 yaml_2.1.13     stringi_0.4-1   rmarkdown_0.6.1
 [8] knitr_1.10.5    stringr_1.0.0   digest_0.6.8    evaluate_0.7   
```

Julia session info:
```
julia> println(versioninfo())
Julia Version 0.3.8
Commit 79599ad (2015-04-30 23:40 UTC)
Platform Info:
  System: Linux (x86_64-linux-gnu)
  CPU: Intel(R) Core(TM) i5 CPU       M 480  @ 2.67GHz
  WORD_SIZE: 64
  BLAS: libblas.so.3
  LAPACK: liblapack.so.3
  LIBM: libopenlibm
  LLVM: libLLVM-3.3
nothing

julia> println(Pkg.installed("MixedModels"))
0.3.22
```


# Models fit by Maximum Likelihood

## R


```r
> load("aldayetal2014.RData")
> library(lme4)
> d.small <- subset(d,chan %in% c("CZ","CPZ","PZ"))
> n400 <- subset(d.small, win=="N400")
> 
> system.time(m0 <- lmer(meanuv ~ wordOrder*ambiguity*np1type*np2type + (1|subj) + (1|item),data=n400,REML=FALSE)
+ )
```

```
   user  system elapsed 
  8.137   0.335   8.503 
```

```r
> print(summary(m0),correlation=FALSE)
```

```
Linear mixed model fit by maximum likelihood  ['lmerMod']
Formula: meanuv ~ wordOrder * ambiguity * np1type * np2type + (1 | subj) +      (1 | item)
   Data: n400

      AIC       BIC    logLik  deviance  df.resid 
 316096.0  316264.4 -158029.0  316058.0     52172 

Scaled residuals: 
    Min      1Q  Median      3Q     Max 
-8.5341 -0.6319 -0.0037  0.6272  8.6941 

Random effects:
 Groups   Name        Variance Std.Dev.
 item     (Intercept)  0.2548  0.5048  
 subj     (Intercept)  1.4689  1.2120  
 Residual             24.8331  4.9833  
Number of obs: 52191, groups:  item, 60; subj, 37

Fixed effects:
                                                                Estimate Std. Error t value
(Intercept)                                                     -0.17014    0.22706  -0.749
wordOrdersubject                                                 0.81033    0.12345   6.564
ambiguityunambig                                                 0.87272    0.12344   7.070
np1typepronoun                                                   0.09963    0.12355   0.806
np2typepronoun                                                   1.46224    0.12378  11.814
wordOrdersubject:ambiguityunambig                               -0.62653    0.17474  -3.585
wordOrdersubject:np1typepronoun                                 -0.20188    0.17472  -1.155
ambiguityunambig:np1typepronoun                                 -0.11287    0.17476  -0.646
wordOrdersubject:np2typepronoun                                 -0.30049    0.17489  -1.718
ambiguityunambig:np2typepronoun                                 -0.10815    0.17483  -0.619
np1typepronoun:np2typepronoun                                   -0.43252    0.17523  -2.468
wordOrdersubject:ambiguityunambig:np1typepronoun                 0.02693    0.24721   0.109
wordOrdersubject:ambiguityunambig:np2typepronoun                -0.06017    0.24732  -0.243
wordOrdersubject:np1typepronoun:np2typepronoun                   0.88005    0.24781   3.551
ambiguityunambig:np1typepronoun:np2typepronoun                   0.44676    0.24746   1.805
wordOrdersubject:ambiguityunambig:np1typepronoun:np2typepronoun -0.85064    0.35031  -2.428
```

```r
> system.time(m1 <- lmer(meanuv ~ wordOrder*ambiguity*np1type*np2type + (wordOrder+ambiguity|subj) + (wordOrder+ambiguity|item),data=n400,REML=FALSE)
+ )
```

```
   user  system elapsed 
115.335   0.535 116.325 
```

```r
> print(summary(m1),correlation=FALSE)
```

```
Linear mixed model fit by maximum likelihood  ['lmerMod']
Formula: meanuv ~ wordOrder * ambiguity * np1type * np2type + (wordOrder +  
    ambiguity | subj) + (wordOrder + ambiguity | item)
   Data: n400

      AIC       BIC    logLik  deviance  df.resid 
 315834.8  316091.8 -157888.4  315776.8     52162 

Scaled residuals: 
    Min      1Q  Median      3Q     Max 
-8.5577 -0.6295 -0.0039  0.6287  8.7730 

Random effects:
 Groups   Name             Variance Std.Dev. Corr       
 item     (Intercept)       0.3513  0.5927              
          wordOrdersubject  0.2346  0.4843   -0.30      
          ambiguityunambig  0.2492  0.4992   -0.42 -0.05
 subj     (Intercept)       1.3257  1.1514              
          wordOrdersubject  0.1984  0.4454    0.16      
          ambiguityunambig  0.3374  0.5808   -0.11  0.02
 Residual                  24.5828  4.9581              
Number of obs: 52191, groups:  item, 60; subj, 37

Fixed effects:
                                                                Estimate Std. Error t value
(Intercept)                                                     -0.17634    0.22188  -0.795
wordOrdersubject                                                 0.82441    0.15615   5.279
ambiguityunambig                                                 0.86707    0.16846   5.147
np1typepronoun                                                   0.11830    0.12309   0.961
np2typepronoun                                                   1.48138    0.12331  12.014
wordOrdersubject:ambiguityunambig                               -0.64326    0.17399  -3.697
wordOrdersubject:np1typepronoun                                 -0.22234    0.17409  -1.277
ambiguityunambig:np1typepronoun                                 -0.11949    0.17412  -0.686
wordOrdersubject:np2typepronoun                                 -0.32068    0.17426  -1.840
ambiguityunambig:np2typepronoun                                 -0.11235    0.17418  -0.645
np1typepronoun:np2typepronoun                                   -0.46886    0.17478  -2.683
wordOrdersubject:ambiguityunambig:np1typepronoun                 0.05819    0.24633   0.236
wordOrdersubject:ambiguityunambig:np2typepronoun                -0.03085    0.24644  -0.125
wordOrdersubject:np1typepronoun:np2typepronoun                   0.91838    0.24725   3.714
ambiguityunambig:np1typepronoun:np2typepronoun                   0.45214    0.24688   1.831
wordOrdersubject:ambiguityunambig:np1typepronoun:np2typepronoun -0.90358    0.34954  -2.585
```

```r
> system.time(m2 <- lmer(meanuv ~ wordOrder*ambiguity*np1type*np2type + (wordOrder+ambiguity+np1type+np2type|subj) + (wordOrder+ambiguity+np1type+np2type|item),data=n400,REML=FALSE)
+ )
```

```
    user   system  elapsed 
1019.460   12.402 1038.711 
```

```r
> print(summary(m2),correlation=FALSE)
```

```
Linear mixed model fit by maximum likelihood  ['lmerMod']
Formula: meanuv ~ wordOrder * ambiguity * np1type * np2type + (wordOrder +  
    ambiguity + np1type + np2type | subj) + (wordOrder + ambiguity +      np1type + np2type | item)
   Data: n400

      AIC       BIC    logLik  deviance  df.resid 
 314563.6  314980.1 -157234.8  314469.6     52144 

Scaled residuals: 
    Min      1Q  Median      3Q     Max 
-8.6729 -0.6352 -0.0042  0.6270  8.9977 

Random effects:
 Groups   Name             Variance Std.Dev. Corr                   
 item     (Intercept)       0.6196  0.7872                          
          wordOrdersubject  0.2356  0.4853   -0.21                  
          ambiguityunambig  0.2519  0.5019   -0.33 -0.05            
          np1typepronoun    0.3034  0.5508   -0.22  0.11 -0.34      
          np2typepronoun    0.6197  0.7872   -0.61 -0.10  0.24 -0.12
 subj     (Intercept)       2.0216  1.4218                          
          wordOrdersubject  0.2011  0.4485    0.11                  
          ambiguityunambig  0.3308  0.5752   -0.24  0.01            
          np1typepronoun    0.1660  0.4074    0.03  0.08 -0.33      
          np2typepronoun    2.0205  1.4214   -0.55  0.01  0.41 -0.50
 Residual                  23.8151  4.8801                          
Number of obs: 52191, groups:  item, 60; subj, 37

Fixed effects:
                                                                Estimate Std. Error t value
(Intercept)                                                     -0.16342    0.26886  -0.608
wordOrdersubject                                                 0.81019    0.15508   5.224
ambiguityunambig                                                 0.84761    0.16674   5.083
np1typepronoun                                                   0.12962    0.15573   0.832
np2typepronoun                                                   1.45031    0.28229   5.138
wordOrdersubject:ambiguityunambig                               -0.59836    0.17153  -3.488
wordOrdersubject:np1typepronoun                                 -0.23019    0.17169  -1.341
ambiguityunambig:np1typepronoun                                 -0.10120    0.17154  -0.590
wordOrdersubject:np2typepronoun                                 -0.29046    0.17172  -1.691
ambiguityunambig:np2typepronoun                                 -0.09670    0.17158  -0.564
np1typepronoun:np2typepronoun                                   -0.47714    0.17205  -2.773
wordOrdersubject:ambiguityunambig:np1typepronoun                 0.01327    0.24290   0.055
wordOrdersubject:ambiguityunambig:np2typepronoun                -0.06206    0.24292  -0.255
wordOrdersubject:np1typepronoun:np2typepronoun                   0.92902    0.24336   3.818
ambiguityunambig:np1typepronoun:np2typepronoun                   0.45835    0.24301   1.886
wordOrdersubject:ambiguityunambig:np1typepronoun:np2typepronoun -0.91021    0.34404  -2.646
```

## Julia 



```
julia> using DataFrames, MixedModels

julia> d = DataFrame(read_rda("aldayetal2014.RData")["d"]);

julia> dsmall = d[bool([x in ["CZ","CPZ","PZ"] for x in d[:chan]]),:];

julia> n400 = dsmall[dsmall[:win] .== "N400",:];

julia> @time m0 = fit(lmm(meanuv ~ 1 + wordOrder*ambiguity*np1type*np2type + (1|item) + (1|subj), n400));
elapsed time: 20.650423511 seconds (1259493348 bytes allocated, 24.05% gc time)

julia> print(m0)
Linear mixed model fit by maximum likelihood
Formula: meanuv ~ 1 + wordOrder * ambiguity * np1type * np2type + (1 | item) + (1 | subj)

 logLik: -158029.004553, deviance: 316058.009106

Variance components:
            Variance   Std.Dev.  
 item      0.25483327 0.50481013
 subj      1.46886921 1.21196915
 Residual 24.83310014 4.98328207
 Number of obs: 52191; levels of grouping factors: 60, 37

  Fixed-effects parameters:
                                     Estimate Std.Error   z value
(Intercept)                         -0.170135  0.227059 -0.749301
wordOrdersubject                     0.810334  0.123448   6.56415
ambiguityunambig                     0.872724  0.123444   7.06978
np1typepronoun                      0.0996264  0.123553  0.806347
np2typepronoun                        1.46224  0.123776   11.8136
wordOrdersubject&ambiguityunambig   -0.626527  0.174739   -3.5855
wordOrdersubject&np1typepronoun     -0.201881  0.174717  -1.15548
wordOrdersubject&np2typepronoun     -0.300485  0.174889  -1.71815
ambiguityunambig&np1typepronoun     -0.112868  0.174758 -0.645855
ambiguityunambig&np2typepronoun     -0.108149   0.17483 -0.618594
np1typepronoun&np2typepronoun       -0.432515   0.17523  -2.46827
wordOrdersubject&ambiguityunambig   0.0269254  0.247212  0.108916
wordOrdersubject&ambiguityunambig  -0.0601712  0.247325 -0.243288
wordOrdersubject&np1typepronoun      0.880053  0.247806   3.55139
ambiguityunambig&np1typepronoun      0.446759   0.24746   1.80538
wordOrdersubject&ambiguityunambig   -0.850636  0.350306  -2.42827

julia> @time m1 = fit(lmm(meanuv ~ 1 + wordOrder*ambiguity*np1type*np2type + (1+wordOrder+ambiguity|item) + (1+wordOrder+ambiguity|subj), n400));
elapsed time: 13.114836832 seconds (1768485232 bytes allocated, 50.79% gc time)

julia> print(m1);
Linear mixed model fit by maximum likelihood
Formula: meanuv ~ 1 + wordOrder * ambiguity * np1type * np2type + ((1 + wordOrder + ambiguity) | item) + ((1 + wordOrder + ambiguity) | subj)

 logLik: -157888.376486, deviance: 315776.752972

Variance components:
            Variance   Std.Dev.    Corr.
 item      0.35132918 0.59273027
           0.23458607 0.48434086 -0.30
           0.24918868 0.49918802 -0.42 -0.42
 subj      1.32575435 1.15141406
           0.19840059 0.44542182  0.16
           0.33737448 0.58083947 -0.11 -0.11
 Residual 24.58280904 4.95810539
 Number of obs: 52191; levels of grouping factors: 60, 37

  Fixed-effects parameters:
ambiguityunambig&np2typepronoun     -0.112348  0.174184 -0.644994
np1typepronoun&np2typepronoun       -0.468859  0.174777  -2.68261
wordOrdersubject&ambiguityunambig   0.0581888  0.246326  0.236226
wordOrdersubject&ambiguityunambig  -0.0308533   0.24644 -0.125196
wordOrdersubject&np1typepronoun      0.918377  0.247248   3.71439
ambiguityunambig&np1typepronoun      0.452145  0.246883   1.83141
wordOrdersubject&ambiguityunambig   -0.903582  0.349536  -2.58509

julia> @time m2 = fit(lmm(meanuv ~ 1 + wordOrder*ambiguity*np1type*np2type +                                  (1+wordOrder+ambiguity+np1type+np2type|item) +                                     (1+wordOrder+ambiguity+np1type+np2type|subj), n400));
elapsed time: 28.146366217 seconds (2426816680 bytes allocated, 37.34% gc time)

julia> print(m2)
Linear mixed model fit by maximum likelihood
Formula: meanuv ~ 1 + wordOrder * ambiguity * np1type * np2type + ((1 + wordOrder + ambiguity + np1type + np2type) | item) + ((1 + wordOrder + ambiguity + np1type + np2type) | subj)

 logLik: -157234.785839, deviance: 314469.571677

Variance components:
            Variance   Std.Dev.    Corr.
 item      0.61958276 0.78713580
           0.23555342 0.48533846 -0.21
           0.25190194 0.50189833 -0.33 -0.33
           0.30335998 0.55078125 -0.22 -0.22 -0.22
           0.61963811 0.78717096 -0.61 -0.61 -0.61 -0.61
 subj      2.02160641 1.42183206
           0.20111041 0.44845335  0.11
           0.33079992 0.57515208 -0.24 -0.24
           0.16601203 0.40744574  0.03  0.03  0.03
           2.02052768 1.42145267 -0.55 -0.55 -0.55 -0.55
 Residual 23.81509690 4.88007140
 Number of obs: 52191; levels of grouping factors: 60, 37

  Fixed-effects parameters:
                                     Estimate Std.Error   z value
(Intercept)                         -0.163416  0.268861 -0.607807
wordOrdersubject                     0.810187  0.155077    5.2244
ambiguityunambig                     0.847608  0.166738   5.08349
np1typepronoun                       0.129621  0.155731  0.832338
np2typepronoun                        1.45031  0.282292   5.13763
wordOrdersubject&ambiguityunambig   -0.598355  0.171535  -3.48825
wordOrdersubject&np1typepronoun     -0.230189   0.17169  -1.34072
wordOrdersubject&np2typepronoun     -0.290458  0.171717  -1.69149
ambiguityunambig&np1typepronoun     -0.101199  0.171542 -0.589941
ambiguityunambig&np2typepronoun    -0.0967029   0.17158 -0.563601
np1typepronoun&np2typepronoun       -0.477141  0.172047  -2.77332
wordOrdersubject&ambiguityunambig   0.0132734    0.2429 0.0546457
wordOrdersubject&ambiguityunambig  -0.0620641  0.242925 -0.255487
wordOrdersubject&np1typepronoun      0.929022  0.243358   3.81751
ambiguityunambig&np1typepronoun       0.45835  0.243008   1.88615
wordOrdersubject&ambiguityunambig   -0.910206  0.344039  -2.64565

```


