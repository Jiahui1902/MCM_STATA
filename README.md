# MCM_STATA
Implementing Mobility Contrast Model (MCM) in STATA

Luo, Liying. "Heterogeneous effects of intergenerational social mobility: An improved method and new evidence." *American Sociological Review* 87.1 (2022): 143-173.

MCM model supports *generalized linear model*. All the models are under sum-to-zero coding, which means that the omitted coefficient for a categorical variable equals to the negative sum of all the other levels. It is possible to use `lincom` in STATA to recover the omitted coefficients after model estimation.

The below shows examples for linear models, linear models with weighting, linear models with clustering, and logitsitc regression models with MCM. 

# Basic Syntax

```text
xi3: mcm dependentvar independentvars, origin() destination()
```

# Example 1: linear model 

Data Source: National Youth Longitudinal Study (1979 cohort)

Note: *luck143* is the outcome variable representing the Rotter score collected in 2014. *paredef3* is parents' educational level and *red14ef3* is respondent'd educational level.

```text
* run linear model under sum-to-zero coding
xi3: reg luck143 e.paredef3*e.red14ef3 
* extract origin effect, destination effect, immobility effect, and mobility effect
xi3: mcm luck143 e.paredef3*e.red14ef3, origin(paredef3) destination(red14ef3)
```

Output
```text
. xi3: reg luck143 e.paredef3*e.red14ef3 
. xi3: mcm luck143 e.paredef3*e.red14ef3, origin(paredef3) destination(red14ef3)
e.paredef3        _Iparedef3_1-3      (naturally coded; _Iparedef3_1 omitted)
e.red14ef3        _Ired14ef3_1-3      (naturally coded; _Ired14ef3_1 omitted)
(316 missing values generated)
(316 missing values generated)
(316 missing values generated)
(316 missing values generated)

      Source |       SS           df       MS      Number of obs   =     3,864
-------------+----------------------------------   F(8, 3855)      =     10.24
       Model |  14.3994348         8  1.79992935   Prob > F        =    0.0000
    Residual |  677.573391     3,855  .175764823   R-squared       =    0.0208
-------------+----------------------------------   Adj R-squared   =    0.0188
       Total |  691.972826     3,863  .179128353   Root MSE        =    .41924

------------------------------------------------------------------------------
     luck143 |      Coef.   Std. Err.      t    P>|t|     [95% Conf. Interval]
-------------+----------------------------------------------------------------
_Iparedef3_2 |   .0010803   .0167979     0.06   0.949    -.0318533    .0340139
_Iparedef3_3 |    .043264   .0259892     1.66   0.096    -.0076899    .0942178
_Ired14ef3_2 |  -.0265313   .0157429    -1.69   0.092    -.0573965    .0043339
_Ired14ef3_3 |   .0557952   .0154307     3.62   0.000     .0255422    .0860483
   _Ipa2Xre2 |  -.0044744   .0188698    -0.24   0.813    -.0414701    .0325212
   _Ipa2Xre3 |  -.0174969   .0187949    -0.93   0.352    -.0543458    .0193521
   _Ipa3Xre2 |  -.0134211   .0285653    -0.47   0.638    -.0694258    .0425835
   _Ipa3Xre3 |  -.0044618   .0272711    -0.16   0.870     -.057929    .0490055
       _cons |   .7681171   .0141526    54.27   0.000     .7403698    .7958644
------------------------------------------------------------------------------


origin[3,3]
         estimates     Std Err       P>|t|
Orig 1  -.04434426   .01562588   .00456528
Orig 2   .00108031   .01679789   .94872515
Orig 3   .04326395   .02598919   .09605579

destination[3,3]
          estimates     Std Err       P>|t|
Desti 1  -.02926393     .026755   .27412235
Desti 2  -.02653132   .01574291   .09201419
Desti 3   .05579525   .01543068   .00030318

immobility[3,3]
                  estimates     Std Err       P>|t|
Orig 1 :Desti 1  -.11346242   .02471486   4.555e-06
Orig 2 :Desti 2  -.02992544   .01961592    .1271993
Orig 3 :Desti 3   .09459744   .02033632   3.403e-06

mobility_effect[3,3]
           Desti 1     Desti 2     Desti 3
Orig 1           0   .05774979   .06181288
Orig 2   .02644575           0  -.01302246
Orig 3   .02234467  -.00895937           0

mobility_effect_se[3,3]
          Desti 1    Desti 2    Desti 3
Orig 1          0  .04398869  .04455394
Orig 2  .04823156          0  .02085512
Orig 3  .07521516  .02502905          0

mobility_effect_p[3,3]
          Desti 1    Desti 2    Desti 3
Orig 1          .  .18931743  .16540863
Orig 2  .58351219          .  .53238557
Orig 3  .76642391   .7203936          .
```

Alternatively, use `lincom` to extract main effects:
```text
* origin
lincom -_Iparedef3_2 -_Iparedef3_3
lincom _Iparedef3_2
lincom _Iparedef3_3

* destinatioin
lincom -_Ired14ef3_2 -_Ired14ef3_3
lincom _Ired14ef3_2
lincom _Ired14ef3_3
```

Use `lincom` to extract immobility effects:
```text
* origin == 1 & destination == 1
lincom -_Iparedef3_2-_Iparedef3_3 + _Ipa2Xre2 + _Ipa2Xre3 + _Ipa3Xre2 + _Ipa3Xre3
* origin == 2 & destination == 2
lincom _Iparedef3_2+_Ired14ef3_2 + _Ipa2Xre2
* origin == 3 & destination == 3
lincom _Iparedef3_3+_Ired14ef3_3 + _Ipa3Xre3
```

# Example 2: linear model + weighting
```text
xi3: reg luck143 e.paredef3*e.red14ef3 [aw=weight]
xi3: mcm luck143 e.paredef3*e.red14ef3, origin(paredef3) destination(red14ef3)
```

Output
```text
. xi3: reg luck143 e.paredef3*e.red14ef3 [aw=weight]
. xi3: mcm luck143 e.paredef3*e.red14ef3, origin(paredef3) destination(red14ef3)
e.paredef3        _Iparedef3_1-3      (naturally coded; _Iparedef3_1 omitted)
e.red14ef3        _Ired14ef3_1-3      (naturally coded; _Ired14ef3_1 omitted)
(316 missing values generated)
(316 missing values generated)
(316 missing values generated)
(316 missing values generated)
(sum of wgt is   1.1480e+09)

      Source |       SS           df       MS      Number of obs   =     3,864
-------------+----------------------------------   F(8, 3855)      =      8.48
       Model |  11.3259035         8  1.41573793   Prob > F        =    0.0000
    Residual |  643.263583     3,855  .166864743   R-squared       =    0.0173
-------------+----------------------------------   Adj R-squared   =    0.0153
       Total |  654.589486     3,863  .169451071   Root MSE        =    .40849

------------------------------------------------------------------------------
     luck143 |      Coef.   Std. Err.      t    P>|t|     [95% Conf. Interval]
-------------+----------------------------------------------------------------
_Iparedef3_2 |   .0031781    .015474     0.21   0.837    -.0271599    .0335161
_Iparedef3_3 |   .0386188   .0226057     1.71   0.088    -.0057015    .0829392
_Ired14ef3_2 |  -.0274769   .0142736    -1.93   0.054    -.0554615    .0005077
_Ired14ef3_3 |   .0445164   .0142643     3.12   0.002     .0165501    .0724828
   _Ipa2Xre2 |  -.0073068   .0173625    -0.42   0.674    -.0413475    .0267338
   _Ipa2Xre3 |  -.0201436   .0175538    -1.15   0.251    -.0545593     .014272
   _Ipa3Xre2 |  -.0203045   .0249549    -0.81   0.416    -.0692305    .0286215
   _Ipa3Xre3 |   .0055419   .0239311     0.23   0.817    -.0413768    .0524607
       _cons |   .7771642   .0127686    60.87   0.000     .7521304     .802198
------------------------------------------------------------------------------

origin[3,3]
         estimates     Std Err       P>|t|
Orig 1  -.04179696   .01509156   .00564009
Orig 2   .00317813     .015474   .83728185
Orig 3   .03861883   .02260573   .08764919

destination[3,3]
          estimates     Std Err       P>|t|
Desti 1  -.01703957   .02389582   .47584109
Desti 2  -.02747687   .01427365   .05430194
Desti 3   .04451644   .01426435   .00181683

immobility[3,3]
                  estimates     Std Err       P>|t|
Orig 1 :Desti 1  -.10104952   .02848051   .00039276
Orig 2 :Desti 2  -.03160555   .01766992   .07374741
Orig 3 :Desti 3   .08867722   .01765602   5.332e-07

mobility_effect[3,3]
           Desti 1     Desti 2     Desti 3
Orig 1           0   .06982428   .05681469
Orig 2   .03475727           0  -.01283684
Orig 3   .00922059  -.02584643           0

mobility_effect_se[3,3]
          Desti 1    Desti 2    Desti 3
Orig 1          0  .04138926  .04287663
Orig 2  .04414669          0  .01980183
Orig 3  .06529925  .02280814          0

mobility_effect_p[3,3]
          Desti 1    Desti 2    Desti 3
Orig 1          .  .09168152  .18522532
Orig 2  .43114699          .  .51685188
Orig 3  .88771532  .25719621          .
```

# Example 3: linear model + clustering
```text
xi3: reg luck143 e.paredef3*e.red14ef3 [aw=weight], cluster(HHID)
xi3: mcm luck143 e.paredef3*e.red14ef3, origin(paredef3) destination(red14ef3)
```

# Example 4: Binary outcome (logistics regression)
```text
xi3: logit luck143 e.paredef3*e.red14ef3 
xi3: mcm luck143 e.paredef3*e.red14ef3, origin(paredef3) destination(red14ef3)
```

Output
```text
. xi3: logit luck143 e.paredef3*e.red14ef3 
. xi3: mcm luck143 e.paredef3*e.red14ef3, origin(paredef3) destination(red14ef3)
e.paredef3        _Iparedef3_1-3      (naturally coded; _Iparedef3_1 omitted)
e.red14ef3        _Ired14ef3_1-3      (naturally coded; _Ired14ef3_1 omitted)
(316 missing values generated)
(316 missing values generated)
(316 missing values generated)
(316 missing values generated)

Iteration 0:   log likelihood =   -2100.87
Iteration 1:   log likelihood = -2060.4833
Iteration 2:   log likelihood = -2059.7292
Iteration 3:   log likelihood = -2059.7284
Iteration 4:   log likelihood = -2059.7284

Logistic regression                               Number of obs   =       3864
                                                  LR chi2(8)      =      82.28
                                                  Prob > chi2     =     0.0000
Log likelihood = -2059.7284                       Pseudo R2       =     0.0196

------------------------------------------------------------------------------
     luck143 |      Coef.   Std. Err.      z    P>|z|     [95% Conf. Interval]
-------------+----------------------------------------------------------------
_Iparedef3_2 |  -.0143869   .0977362    -0.15   0.883    -.2059463    .1771726
_Iparedef3_3 |   .2546631   .1544784     1.65   0.099     -.048109    .5574352
_Ired14ef3_2 |  -.1677565   .0917548    -1.83   0.068    -.3475926    .0120797
_Ired14ef3_3 |   .3303276   .0918126     3.60   0.000     .1503782    .5102771
   _Ipa2Xre2 |  -.0068398   .1086452    -0.06   0.950    -.2197805    .2061008
   _Ipa2Xre3 |  -.1076951   .1107078    -0.97   0.331    -.3246785    .1092882
   _Ipa3Xre2 |  -.0960851   .1683443    -0.57   0.568    -.4260339    .2338637
   _Ipa3Xre3 |   .0274566   .1637476     0.17   0.867    -.2934829    .3483961
       _cons |   1.225574   .0833271    14.71   0.000     1.062256    1.388892
------------------------------------------------------------------------------


origin[3,3]
         estimates     Std Err       P>|t|
Orig 1  -.24027624   .09079903   .00817223
Orig 2  -.01438687   .09773621   .88298107
Orig 3    .2546631   .15447841   .09932437

destination[3,3]
          estimates     Std Err       P>|t|
Desti 1  -.16257115   .15751794   .30209927
Desti 2   -.1677565   .09175482   .06757989
Desti 3   .33032765   .09181265   .00032489

immobility[3,3]
                  estimates     Std Err       P>|t|
Orig 1 :Desti 1  -.58601092    .1314324   8.483e-06
Orig 2 :Desti 2  -.18898321   .11124056   .08942444
Orig 3 :Desti 3   .61244733   .13110399   3.093e-06

mobility_effect[3,3]
           Desti 1     Desti 2     Desti 3
Orig 1           0   .28608851   .26340208
Orig 2   .12137482           0  -.10085528
Orig 3   .04117197  -.12354172           0

mobility_effect_se[3,3]
          Desti 1    Desti 2    Desti 3
Orig 1          0  .25379124  .26042904
Orig 2  .27903357          0   .1219254
Orig 3  .44871318  .14925759          0

mobility_effect_p[3,3]
          Desti 1    Desti 2    Desti 3
Orig 1          .  .25970306  .31188088
Orig 2  .66359921          .  .40818145
Orig 3  .92689694  .40788705          .
```

