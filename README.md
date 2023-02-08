# MCM_STATA
Implementing Mobility Contrast Model in STATA

Luo, Liying. "Heterogeneous effects of intergenerational social mobility: An improved method and new evidence." *American Sociological Review* 87.1 (2022): 143-173.

# Example
Syntax --

xi3: mcm luck143 e.paredef3\*e.red14ef3, origin(paredef3) destination(red14ef3)

Data Source: National Youth Longitudinal Study (1979 cohort)

Note: *luck143* is the outcome variable representing the Rotter score collected in 2014. *paredef3* is parents' educational level and *red14ef3* is respondent'd educational level.

```text
origin[3,3]
         estimates     Std Err       P>|t|
Orig 1  -.11681631   .01974687   3.477e-09
Orig 2  -.01822826   .02157382    .3981845
Orig 3   .13504457    .0317395   .00002123

destination[3,3]
          estimates     Std Err       P>|t|
Desti 1  -.13242618   .03338915   .00007385
Desti 2  -.01990937   .01960914   .30999642
Desti 3   .15233555   .01906333   1.577e-15

immobility[3,3]
                  estimates     Std Err       P>|t|
Orig 1 :Desti 1  -.29479119   .03394925   4.827e-18
Orig 2 :Desti 2  -.01044716   .02465609   .67178802
Orig 3 :Desti 3   .23547976   .02396604   1.265e-22

mobility_effect[3,3]
           Desti 1     Desti 2     Desti 3
Orig 1           0   .05836619   .07827991
Orig 2  -.07455011           0  -.00852132
Orig 3   .14430869   .01139239           0

mobility_effect_se[3,3]
          Desti 1    Desti 2    Desti 3
Orig 1          0  .05587926  .05622258
Orig 2  .06240739          0  .02537405
Orig 3  .09182403  .03011419          0

mobility_effect_p[3,3]
          Desti 1    Desti 2    Desti 3
Orig 1          .  .29629129  .16387371
Orig 2  .23229892          .  .73701154
Orig 3  .11609804  .70521559          .
```
