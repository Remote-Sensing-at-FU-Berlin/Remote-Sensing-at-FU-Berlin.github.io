Radiative Transfer Modeling with PROSAIL
================
Fabian Fassnacht

## Radiative transfer modeling with PROSAIL

### Overview

In this tutorial we will get to know the PROSAIL radiative transfer
model for vegetation surfaces. This model is available in R and we will
learn how to conduct some forward simulations with it. Radiative
transfer models like PROSAIL summarize the current knowledge about the
interaction between the electromagnetic radiation in the wavelengths
from 400 to 2500 nm and vegetation. They are an interesting tool for
scientific studies, but also for understanding better how certain plant
traits influence the reflective properties of vegetation.

In today’s theoretical part you already learned a bit more about PROSAIL
and in case you are interested in an even more detailed explanation of
its functionalities, I have uploaded a comprehensive Review-paper on
PROSAIL (Jacquemoud et al. 2009) to ILIAS. This review was written by
the main developers of the model.

### Learning objectives

The learning objectives of this Tutorial include:

- running PROSAIL in forward mode
- understanding how the plant traits implemented in PROSAIL influence
  the reflective properties of vegetation
- understanding NDVI much better

### Datasets used in this Tutorial

We do not use any external datasets in this tutorial.

### Step 1: Running PROSAIL in forward mode

To run PROSAIL in forward mode, we will first have to download and
install a package called “hsdar”. We can accomplish this by running:

``` r
library(hsdar)
```

If the package `hsdar` is not yet installed on your system, run the
following command:

``` r
install.packages("hsdar")
```

We can then have a closer look at the help page of the main function of
the hsdar package which is called “PROSAIL”.

``` r
?PROSAIL
```

If you read through the function-call you will see that it is actually
quite straightforward to simulate a spectrum using the PROSAIL function.
You basically only have to define values for all of the plant traits and
additionally for the sun-sensor geometry and run the function. In case
you do not specify all traits, default values will be used for the
remaining undefined traits.

Now we are already ready to run a first PROSAIL simulation. For this, we
have to define the vegetation traits within the PROSAIL function:

``` r
# simulate simple vegetation spectrum
veg_spectrum = spectra(PROSAIL(LAI=7, Cab = 40, Car=10, Cw = 0.1, Cm = 0.005, N=2, TypeLidf = 2, lidfa = 55))
```

As you an see, we call the PROSAIL function, nested in another function
called `spectra`. This function directly extracts the resulting spectrum
from the `speclib`-object that is created by the `PROSAIL` function.
These speclib-objects contain a lot more detailed information about the
function call etc. and it would also be possible to directly plot these
objects. However, using the `spectra`-function will give us some
advantages for some of the other plots that will follow below and we
hence already introduce this approach here. To plot the spectrum, we run
the following code:

``` r
plot(400:2500, veg_spectrum[1,], type="l", ylab="reflectance", xlab="wavelength [nm]", ylim=c(0,0.5), xlim=c(400,2500))
grid()
```

<img src="../radiative-transfer-model_files/figure-gfm/unnamed-chunk-5-1.png" width="100%" />

One important thing to consider is, that the variable **veg_spectrum**
is only containing the reflectance values (plotted on the y-axis) while
the corresponding wavelengths are not available anymore after applying
the `spectra` function. We hence have to know in this case, that PROSAIL
simulates the reflectance of the vegetation layer with the defined
traits for the wavelengths between 400 and 2500 nm (at steps of 1 nm).
We can then plot the reflectance values in the veg_spectrum variable
over the wavelengths by providing a sequence (**400:2500**) as x-values
in the plot.

You can now play around a bit with this simple way of calling `PROSAIL`
and check how the spectrum changes if you change one or more of the
vegetation traits during the call of the `PROSAIL`-function.

### Step 2: Examining the influence of individual traits on the PROSAIL signal

The hsdar package provides a nice tools to simulate several spectra at
the same time. To make use of this tool, we have to define a dataframe
in which value ranges for all of the to be varied plant traits are
stored. In the example below, we will only vary a single parameter and
leave the remaining parameters at their default value.

For this, we first create a dataframe called **parameter** in which we
add one column named “LAI” and in which we store 10 different values for
the LAI parameter:

``` r
# simulate and plot multiple vegetation spectra
#define parameter space
parameter <- data.frame(LAI = seq(1,5, length.out=10))
parameter
```

            LAI
    1  1.000000
    2  1.444444
    3  1.888889
    4  2.333333
    5  2.777778
    6  3.222222
    7  3.666667
    8  4.111111
    9  4.555556
    10 5.000000

It is of course als possible to vary more than one parameter at the same
time. To see how this works, please have a look at the help of the
“PROSAIL” function. In our case we will now call the PROSAIL function
again and make use of the additional parameter “**parameterList**” which
we will set to the just created dataframe named “**parameter**”:

``` r
veg_spectra = spectra(PROSAIL(parameterList = parameter))
```

This will results in a total of 10 spectra. We will then plot all of
these spectra, one after the other, using a for-loop:

``` r
# plot spectrum
plot(400:2500, ylab="reflectance", xlab="wavelength [nm]", ylim=c(0,0.5), xlim=c(400,2500))
for(i in 1:nrow(veg_spectra)){
  lines(400:2500, veg_spectra[i,])
}
grid()
```

<img src="../radiative-transfer-model_files/figure-gfm/unnamed-chunk-8-1.png" width="100%" />

This plot, hence shows how the spectra of vegetation change if the LAI
of the vegetation changes. You can play around some more with this code
to vary other plant traits and maybe to adjust the colors of the plot to
understand which LAI value produces which curve.

Please use this part of code also to work on exercises 1 and 2 of
today’s theoretical lecture. Remember that meaningful value ranges for
all plant traits considered in PROSAIL are given in the powerpoint
slides of today’s theoretical lecture.

### Step 3: Simulating NDVI values with PROSAIL

In this last step of this short practical on radiative transfer models,
we will first simulate a spectrum and then calculate the NDVI from the
spectrum. Then, once we learned how to do this, your task is to work on
exercise 3 of today’s theoretical lecture and try to find out some plant
trait combinations that will produce NDVI values of 0.40, 0.60 and 0.80.
Please also check whether you can find more than one trait combination
that leads to these NDVI values and think about what this means in terms
of using NDVI as a general and reliable vegetation proxy.

First, we calculate a spectrum:

``` r
# simulate and calculate NDVI
veg_spectrum_1 = spectra(PROSAIL(LAI=5, Cab = 25, Car=10, Cw = 0.05, Cm = 0.005, N=2, TypeLidf = 2, lidfa = 55))[1,]
```

To visualize the locations of the two bands from which NDVI is
calculated, we define the wavelength of the NIR (900nm) and RED (650nm)
band. In both cases we substract 399, as the spectra simulated by
`PROSAIL` start at a wavelength of 400 nm and to hence later select the
correct bands, we will have to substract 399:

``` r
nir_band = 900-399
red_band = 650-399
```

Next, we plot the spectrum and add the positions of the NDVI bands to
the plot. For the plots, we actually add the 399 again - I hope it is
clear to everyone why:

``` r
plot(400:2500, veg_spectrum_1, type="l", ylab="reflectance", xlab="wavelength [nm]", ylim=c(0,0.5), xlim=c(400,2500), lwd=2)
grid()
abline(v=nir_band+399, lty=2, col="darkred", lwd=2)
abline(v=red_band+399, lty=2, col="red", lwd=2)
```

<img src="../radiative-transfer-model_files/figure-gfm/unnamed-chunk-11-1.png" width="100%" />

We can now easily also calculate the NDVI by running the following code:

``` r
ndvi_1 = (veg_spectrum_1[nir_band] - veg_spectrum_1[red_band]) / (veg_spectrum_1[nir_band] + veg_spectrum_1[red_band])
```

To check the NDVI value of the simulated spectrum we run:

``` r
round(ndvi_1,2)
```

    [1] 0.87

Now try to apply what you have just learned about the effects of the
traits considered in PROSAIL on the simulated vegetation spectra to
adjust the spectra in a way that they reach the following three NDVI
values: 0.20; 0.40; and 0.80. In the next class we will compare the
parameter values that each of you found to reach these NDVI values and
discuss whether they are matching and what this means.

With this we are already at the end of today’s short excursion into the
world of radiative transfer models. I hope that all of you see the
benefits of using such models for better understanding how certain
plants traits affect the reflectance of electromagnetic radiation in
different wavelengths. You can maybe imagine that these models can be a
very powerful tool when combined with high quality and fine spectral
resolution remote sensing data which allow for an inversion of the
models as we discussed at the end of today’s theoretical lecture.
