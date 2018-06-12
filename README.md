
<!-- README.md is generated from README.Rmd. Please edit that file -->

sticRs: the [STICS](https://www6.paca.inra.fr/stics_eng/) model R package for developers <img src="man/figures/logo.jpg" alt="logo" width="300" align="right" />
================================================================================================================================================================

<!-- <img src="man/figures/logo.png" alt="logo" style="width:30%;height:auto;" align="right" /> -->
<!-- [![Travis build status](https://travis-ci.com/VEZY/DynACof.svg?branch=master)](https://travis-ci.org/VEZY/DynACof)   -->
[![Project Status: WIP – Initial development is in progress, but there has not yet been a stable, usable release suitable for the public.](http://www.repostatus.org/badges/latest/wip.svg)](http://www.repostatus.org/#wip) [![Travis Build Status](https://travis-ci.com/VEZY/sticRs.svg?branch=master)](https://travis-ci.com/VEZY/sticRs) [![AppVeyor Build status](https://ci.appveyor.com/api/projects/status/cu1nyxrhc6nmpt5i/branch/master?svg=true)](https://ci.appveyor.com/project/VEZY/sticrs/branch/master) [![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

Overview
--------

This package allows the user to programmatically:
\* read ([`read_param`](R/read_param.R)) or set ([`set_param`](R/read_param.R)) parameters

-   set ([`set_usm`](R/set_usm.R)) one or more simulations (USM)

-   call STICS to run them ([`run_stics`](R/run_stics.R))

-   import the results for analyses ([`read_output`](R/read_output.R)), or the observations available in the USM ([`read_obs`](R/read_obs.R))

-   compare observations and simulations ([`eval_output`](R/eval_output.R))

-   make all previous at once in parallel for comparison between STICS versions ([`stics_eval`](R/stics_eval.R))

-   generate automatic reports (only available for one experiment, development terminated but still under evaluation)

-   and run sensitivity analyses ([`sensitive_stics`](R/sensitive_stics.R)).

The package is under intensive development, so you can fill an issue or request me a feature [here](https://github.com/VEZY/sticRs/issues) at any time.

Installation
------------

The development version from [GitHub](https://github.com/) can be installed with:

``` r
devtools::install_github("VEZY/sticRs")
```

Or using the lightweight [remotes](https://github.com/r-lib/remotes#readme) package:

``` r
# install.packages("remotes")
remotes::install_github("VEZY/sticRs")
```

For the moment, there is no [CRAN](https://CRAN.R-project.org) release of sticRs, but we work on that. You will soon be able to run this command to install the package:

``` r
install.packages("sticRs")
```

Example
-------

### Setting a parameter and running the model

This is a basic example using the default dummy simulation (parameters and meteorology) for a mixed crop of wheat-wheat (not a real mixed crop, for testing the model behavior) :

``` r
library("sticRs")
set_usm(plant= c("wheat","wheat"))
# Reading the interrang parameter:
read_param(param='interrang')
# Setting the interrang parameter to 0.01 meter:
set_param(param= "interrang", value= 0.01)
# Running the model:
run_stics(dirpath = "Your_path_goes_here")
```

To use your own data, simply use the folder of your simulation as the reference path; like you would do with javaSTICS.

### Making a sensitivity analysis

Make a sensitivity analysis using the `fast99` algorithm for the interrow (`interrang` parameter) for three main variables: the intercepted radiation (`raint`), the leaf area index (`lai(n)`), and the dry mass (`masec(n)`):

``` r
library("sticRs")
sens= sensitive_stics(dir.orig = "0-DATA/dummy/SC_Wheat",
                      dir.targ = "2-Simulations/Sensitivity2",
                      stics = "0-DATA/stics_executable/EquDens_trg/stics.exe",
                      obs_name = "Wheat_N0.obs",Parameters = "interrang",
                      Vars = c("raint", "lai(n)", "masec(n)"),
                      method= "fast99", n= 10,
                      q= "qunif",q.arg = list(list(min=0.05, max=0.25),
                                              list(min=140, max=280)))
```

NB: `n`, `q`, and `q.arg` are parameters from the [`fast99`](https://cran.r-project.org/web/packages/sensitivity/sensitivity.pdf) function.

The output from [`sensitive_stics`](R/sensitive_stics.R) is a list of two:

-   A list of ggplot objects to plot the sensitivity of each variable to the parameter(s) along the rotation

-   A list of the output from the method function, *e.g.* a list of class `fast99` for the `fast99` method.

Acknowledgments
---------------

The STICS (Simulateur mulTIdisciplinaire pour les Cultures Standard, or multidisciplinary simulator for standard crops) model is a dynamic, generic and robust model aiming to simulate the soil-crop-atmosphere system. It was first developed in 1996 by INRA -the French National Institute for Agricultural research- by Nadine Brisson and Dominique Ripoche. An overview of the model is available [here](https://www6.paca.inra.fr/stics_eng/About-us/Stics-model-overview).

The sticRs package was developed thanks to the European H2020 funded [ReMIX project](https://www.remix-intercrops.eu/).

![ReMIX logo](man/figures/remix_logo.jpg)
-----------------------------------------
