Mixed first order abs and zero order infusion
================

``` r
library(mrgsolve)
library(tidyverse)
```

Mixed first order abs into gut and zero order infusion
------------------------------------------------------

``` r
code <- '
$PARAM CL = 5, VC = 50, KA = 0.5, GUTBIOAV = 0.5, GUTLAGT=0
BIOAV = 1, LAGT = 5, MODE = 0, DUR2 = 4, RAT2 = 10

$CMT GUT CENT

$MAIN
F_GUT = GUTBIOAV;
ALAG_GUT = GUTLAGT;

F_CENT = 1-F_GUT;
ALAG_CENT = LAGT;

if(MODE==1) R_CENT = RAT2;
if(MODE==2) D_CENT = DUR2;

$ODE

dxdt_GUT = -KA*GUT;
dxdt_CENT = KA*GUT - (CL/VC)*CENT;

$TABLE
double CP = CENT/VC;

$CAPTURE CP GUT CENT
'

mod <- mcode_cache("mixed", code)
```

``` r
mod <- update(mod, end=72)
```

### mixed zero and first-order events

``` r
ev2 <- ev(ID=1,amt = 10, time=0, cmt=1) + ev(ID=1,time = 0, amt = 10, cmt=2, rate=-2, MODE=2)
out <- mod %>% Req(GUT, CENT, CP) %>% mrgsim(ev2)
plot(out)
```

![](mixedzeroandfirst_files/figure-markdown_github/unnamed-chunk-5-1.png)
