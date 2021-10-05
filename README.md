# MCM_STATA
Implementing Mobility Contrast Model in STATA

Luo, Liying. Forthcoming. “Heterogeneous Effects of Intergenerational Social Mobility: An Improved Method and New Evidence.” *American Sociological Review*

# Example
xi3: mcm luck143 e.paredef3\*e.red14ef3, origin(paredef3) destination(red14ef3)

Note: *paredef3* is parents' educational level and *red14ef3* is respondent'd educational level

```text
Estimates:
mobility_effect[3,3]

            r1          r2          r3 
r1           0   .05836619    .0782799
r1  -.07455011           0  -.00852132
r1   .14430869   .01139239           0

Standard Errors: 
mobility_effect_se[3,3]
           r1         r2         r3
c1          0  .05587926  .05622257
c1  .06240739          0  .02537405
c1  .09182403  .03011419          0

Confidence Interval (lower bound)
ci_lower[3,3]
            r1          r2          r3
c1           0  -.05115715  -.03191634
c1  -.19686859           0  -.05825446
c1  -.03566642  -.04763143           0

Confidence Interval (upper bound)
ci_upper[3,3]
           r1         r2         r3
c1          0  .16788953  .18847615
c1  .04776838          0  .04121182
c1   .3242838  .07041621          0
```
