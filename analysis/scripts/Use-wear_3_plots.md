Plots - use-wear archaeology
================
Lisa Schunk
2020-09-11 14:49:06

  - [Goal of the script](#goal-of-the-script)
  - [Load packages](#load-packages)
  - [Get name, path and information of the
    file](#get-name-path-and-information-of-the-file)
  - [Load data into R object](#load-data-into-r-object)
  - [Prepare variables](#prepare-variables)
      - [Define numeric variables](#define-numeric-variables)
  - [Plot each of the selected numeric
    variables](#plot-each-of-the-selected-numeric-variables)
      - [Plot of all samples with raw material, variables and use-wear
        type as
        information](#plot-of-all-samples-with-raw-material-variables-and-use-wear-type-as-information)
      - [Boxplot of all the variables combined with the use-wear
        type](#boxplot-of-all-the-variables-combined-with-the-use-wear-type)
      - [Scatterplots of selected variables combined with the use-wear
        type](#scatterplots-of-selected-variables-combined-with-the-use-wear-type)
  - [Show plot files information](#show-plot-files-information)
  - [sessionInfo() and RStudio
    version](#sessioninfo-and-rstudio-version)

# Goal of the script

This script plots all variables to see which ones should be used for
further analysis.  
Scatterplot of each variable will be plotted.

``` r
dir_in <- "analysis/derived_data/"
dir_out <- "analysis/plots"
```

Raw data must be located in \~/analysis/derived\_data/.  
Formatted data will be saved in \~/analysis/plots. The knit directory
for this script is the project directory.

-----

# Load packages

``` r
library(R.utils)
library(ggplot2)
library(tools)
library(tidyverse)
library(patchwork)
library(doBy)
library(ggrepel)
library(openxlsx)
library(wesanderson)
```

-----

# Get name, path and information of the file

``` r
data_file <- list.files(dir_in, pattern = "\\.Rbin$", full.names = TRUE)
md5_in <- md5sum(data_file)
info_in <- data.frame(file = basename(names(md5_in)), checksum = md5_in, row.names = NULL)
```

The checksum (MD5 hashes) of the imported file is:

``` 
           file                         checksum
1 Use-wear.Rbin 2b7acbe46dcf8faa0145bc7f141abc26
```

# Load data into R object

``` r
imp_data <- loadObject(data_file)
str(imp_data)
```

    'data.frame':   150 obs. of  57 variables:
     $ Sample                  : chr  "MU-232" "MU-232" "MU-232" "MU-003" ...
     $ Site                    : Factor w/ 3 levels "Balve","Buhlen",..: 1 1 1 1 1 1 1 1 1 1 ...
     $ Tool.type               : Factor w/ 4 levels "Keilmesser","Pradnick scraper",..: 1 1 1 1 1 1 1 1 1 4 ...
     $ Raw.material            : Factor w/ 2 levels "flint","lydite": 2 2 2 2 2 2 2 2 2 2 ...
     $ Location                : chr  "B" "B" "B" "D" ...
     $ Sublocation             : chr  "2" "2" "2" "1" ...
     $ Area                    : chr  "01" "01" "01" "01" ...
     $ Spot                    : chr  "a" "b" "c" "a" ...
     $ Usewear.type            : Factor w/ 11 levels "A","B","B2","C",..: 9 9 9 2 2 2 4 4 4 3 ...
     $ Objective               : Factor w/ 3 levels "20x07","50x075",..: 1 1 1 3 3 3 2 2 2 3 ...
     $ Analysis.date           : Date, format: "2020-09-07" "2020-09-07" ...
     $ Analysis.time           : 'times' num  0.631 0.631 0.631 0.631 0.632 ...
      ..- attr(*, "format")= chr "h:m:s"
     $ Acquisition.date.time   : chr  "07.07.2020 16:58" "07.08.2020 10:35" "07.08.2020 12:10" "07.03.2020 10:44" ...
     $ Axis.length.X           : num  255 255 255 255 255 ...
     $ Axis.size.X             : num  1198 1198 1198 1198 1198 ...
     $ Axis.spacing.X          : num  0.213 0.213 0.213 0.213 0.213 ...
     $ Axis.length.Y           : num  255 255 255 255 255 ...
     $ Axis.size.Y             : num  1198 1198 1198 1198 1198 ...
     $ Axis.spacing.Y          : num  0.213 0.213 0.213 0.213 0.213 ...
     $ Axis.length.Z           : num  249564 99661 162726 38576 39610 ...
     $ Axis.size.Z             : num  65505 35461 32419 65340 66654 ...
     $ Axis.spacing.Z          : num  3.81 2.81 5.019 0.59 0.594 ...
     $ NM.points.ratio.Z       : num  0 0 0 0 0 0 0 0 0 0 ...
     $ Sq                      : num  3243 2493 4332 1912 1936 ...
     $ Ssk                     : num  0.0634 -0.9445 0.1816 -0.058 -0.2928 ...
     $ Sku                     : num  3.46 7.36 3.08 3.75 3.47 ...
     $ Sp                      : num  10477 7460 12748 6231 5796 ...
     $ Sv                      : num  10005 12962 16115 6843 6575 ...
     $ Sz                      : num  20482 20422 28864 13075 12371 ...
     $ Sa                      : num  2506 1813 3409 1464 1495 ...
     $ Smr                     : num  0.551 0.697 0.388 0.784 0.586 ...
     $ Smc                     : num  3754 2956 5778 2454 2429 ...
     $ Sxp                     : num  6582 4878 7854 3949 4400 ...
     $ Sal                     : num  25.9 20.5 23.4 24.4 24.9 ...
     $ Str                     : num  0.321 0.215 0.241 0.784 0.767 ...
     $ Std                     : num  42.5 93 51 103.7 106.7 ...
     $ Sdq                     : num  0.603 0.376 0.557 0.301 0.298 ...
     $ Sdr                     : num  9.99 5.11 10.54 4.13 4.09 ...
     $ Vm                      : num  0.2094 0.1157 0.2311 0.0944 0.0828 ...
     $ Vv                      : num  3.96 3.07 6.01 2.55 2.51 ...
     $ Vmp                     : num  0.2094 0.1157 0.2311 0.0944 0.0828 ...
     $ Vmc                     : num  2.78 1.82 3.63 1.59 1.6 ...
     $ Vvc                     : num  3.56 2.73 5.53 2.31 2.24 ...
     $ Vvv                     : num  0.403 0.342 0.48 0.238 0.275 ...
     $ Maximum.depth.of.furrows: num  12698 14381 16377 7155 7130 ...
     $ Mean.depth.of.furrows   : num  2586 2471 3670 2350 2229 ...
     $ Mean.density.of.furrows : num  2987 1790 1901 2032 2098 ...
     $ First.direction         : num  44.9809 90.00638 89.98321 0.01527 0.00574 ...
     $ Second.direction        : num  26.5 135 63.5 116.5 135 ...
     $ Third.direction         : num  63.5 116.4 45 135 90 ...
     $ Isotropy                : num  13.5 64.5 14.9 87 86.3 ...
     $ epLsar                  : num  0.00368 0.0024 0.00301 0.00161 0.00236 ...
     $ NewEplsar               : num  0.0181 0.0177 0.0179 0.0171 0.0171 ...
     $ Asfc                    : num  12.8 6.85 12.12 5.51 5.36 ...
     $ Smfc                    : num  2.51 67.38 48.16 94.68 55.32 ...
     $ HAsfc9                  : num  0.629 0.444 0.496 0.666 0.75 ...
     $ HAsfc81                 : num  0.81 2.106 1.515 0.845 0.704 ...
     - attr(*, "comment")= Named chr [1:44] "µm" "points" "µm" "µm" ...
      ..- attr(*, "names")= chr [1:44] "Axis length - X" "Axis size - X" "Axis spacing - X" "Axis length - Y" ...

The imported file is: “\~/analysis/derived\_data/Use-wear.Rbin”

-----

# Prepare variables

## Define numeric variables

``` r
num.var <- 24:length(imp_data)
```

The following variables will be used:

    [24] Sq
    [25] Ssk
    [26] Sku
    [27] Sp
    [28] Sv
    [29] Sz
    [30] Sa
    [31] Smr
    [32] Smc
    [33] Sxp
    [34] Sal
    [35] Str
    [36] Std
    [37] Sdq
    [38] Sdr
    [39] Vm
    [40] Vv
    [41] Vmp
    [42] Vmc
    [43] Vvc
    [44] Vvv
    [45] Maximum.depth.of.furrows
    [46] Mean.depth.of.furrows
    [47] Mean.density.of.furrows
    [48] First.direction
    [49] Second.direction
    [50] Third.direction
    [51] Isotropy
    [52] epLsar
    [53] NewEplsar
    [54] Asfc
    [55] Smfc
    [56] HAsfc9
    [57] HAsfc81

-----

# Plot each of the selected numeric variables

## Plot of all samples with raw material, variables and use-wear type as information

``` r
# splits the data in the individual 35 samples
imp_data[["Sample_material"]] <- paste(imp_data$Raw.material, imp_data$Sample, sep = " ")

sp <- split(imp_data, imp_data[["Site"]])

usewear <- levels(imp_data$Usewear.type)


for (i in num.var){
  
  # gets the min/max range of the data set 
  range_var <- range(imp_data[[i]]) 
 
   # plot
  p <- vector(mode = "list", length = length(sp))
  names(p) <- names(sp)
  
  for (j in seq_along(sp)) {
    p[[j]] <- ggplot(data = sp[[j]], aes_string(x = "Location", y = names(imp_data)[i],
                                                colour = "Usewear.type", shape =
                                               "Sublocation")) + 
         # avoids overplotting
         geom_jitter(size = 3,  position = position_jitter(width = 0.4, seed = 1)) + 
         coord_cartesian(ylim = range_var) + 
         theme_classic() +
         labs(colour = "Use-wear type") +
         facet_wrap(~ Sample_material, nrow = 3) +
         labs(y = gsub("\\.", " ", names(imp_data)[i])) +
         scale_colour_hue(h = c(25,225),limits = levels(sp[[j]][["Usewear.type"]])) +
         if(j != 1) ylab(NULL) 
  }
  p_all <- wrap_plots(p) + plot_layout(width = c(8/13, 2/13, 3/13), guides = "collect")  
  
  print(p_all)
  
  # saves the plots 
  file_out <- paste0(file_path_sans_ext(info_in[["file"]]), "_plot_", 
                       names(imp_data)[i], ".pdf")
    ggsave(filename = file_out, plot = p_all, path = dir_out, device = "pdf", width = 38,
           height = 26, units = "cm" )
}
```

![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-2.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-3.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-4.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-5.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-6.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-7.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-8.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-9.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-10.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-11.png)<!-- -->

    Warning: Removed 5 rows containing missing values (geom_point).

    Warning: Removed 4 rows containing missing values (geom_point).

    Warning: Removed 5 rows containing missing values (geom_point).

    Warning: Removed 4 rows containing missing values (geom_point).

![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-12.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-13.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-14.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-15.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-16.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-17.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-18.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-19.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-20.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-21.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-22.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-23.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-24.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-25.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-26.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-27.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-28.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-29.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-30.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-31.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-32.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-33.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-8-34.png)<!-- -->

## Boxplot of all the variables combined with the use-wear type

``` r
# plot
for (i in num.var){
  
  p2 <- ggplot(data = imp_data, aes_string(x = "Usewear.type", y = names(imp_data)[i],
                                           fill = "Tool.type")) +
        geom_boxplot() +
        theme_classic() +
        labs( x = "Use-wear type", title = " ") +
        labs(y = gsub("\\.", " ", names(imp_data)[i])) +
        labs(fill = "Tool type") +
        scale_fill_manual(values=wes_palette(n = 4,name = "Royal1"))
        
  print(p2)

  # saves the plots 
  file_out <- paste0(file_path_sans_ext(info_in[["file"]]), "_boxplot_", 
                       names(imp_data)[i], ".pdf")
    ggsave(filename = file_out, plot = p2, path = dir_out, device = "pdf", width = 17,
           height = 25, units = "cm")
      
}  
```

![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-2.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-3.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-4.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-5.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-6.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-7.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-8.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-9.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-10.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-11.png)<!-- -->

    Warning: Removed 9 rows containing non-finite values (stat_boxplot).
    
    Warning: Removed 9 rows containing non-finite values (stat_boxplot).

![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-12.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-13.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-14.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-15.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-16.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-17.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-18.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-19.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-20.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-21.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-22.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-23.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-24.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-25.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-26.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-27.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-28.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-29.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-30.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-31.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-32.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-33.png)<!-- -->![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-9-34.png)<!-- -->

## Scatterplots of selected variables combined with the use-wear type

``` r
# plot 
# plots Sa against Sq
p3 <- ggplot(data = imp_data) +  
      geom_point(mapping = aes(x = Sa, y = Sq, colour = Usewear.type)) +
      theme_classic() +
      labs(colour = "Use-wear type") +
      scale_colour_hue(h = c(25, 230)) 
        
print(p3)
```

![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
# saves the plot
file_out <- paste0(file_path_sans_ext(info_in[["file"]]), "_SA_scatterplot_", ".pdf")
ggsave(filename = file_out, plot = p3, path = dir_out, device = "pdf")

 
# plots epLsar against Asfc 
p4 <- ggplot(data = imp_data) +  
      geom_point(mapping = aes(x = Asfc, y = epLsar, colour = Usewear.type)) +
      theme_classic() +
      labs(colour = "Use-wear type") +
      scale_colour_hue(h = c(25, 230)) 
        
print(p4) 
```

![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-10-2.png)<!-- -->

``` r
# saves the plot
file_out <- paste0(file_path_sans_ext(info_in[["file"]]), "_Asfc_scatterplot_", ".pdf")
ggsave(filename = file_out, plot = p4, path = dir_out, device = "pdf")

  
# plots Sq against Vmc 
p5 <- ggplot(data = imp_data) +  
      geom_point(mapping = aes(x = Sq, y = Vmc, colour = Usewear.type)) +
      theme_classic() +
      labs(colour = "Use-wear type") +
      scale_colour_hue(h = c(25, 230)) 
       
print(p5)
```

![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-10-3.png)<!-- -->

``` r
# saves the plot
file_out <- paste0(file_path_sans_ext(info_in[["file"]]), "_Sq_scatterplot_", ".pdf")
ggsave(filename = file_out, plot = p5, path = dir_out, device = "pdf")


# plots Mean depth of furrows against mean density of furrows  
p6 <- ggplot(data = imp_data) +  
      geom_point(mapping = aes(x = Mean.depth.of.furrows, y = Mean.density.of.furrows,
                               colour = Usewear.type)) +
      theme_classic() +
      labs(colour = "Use-wear type") +
      scale_colour_hue(h = c(25, 230)) +
      labs(x = "Mean depth of furrows", y = "Mean density of furrows")
  
print(p6)
```

![](Use-wear_3_plots_files/figure-gfm/unnamed-chunk-10-4.png)<!-- -->

``` r
# saves the plot
file_out <- paste0(file_path_sans_ext(info_in[["file"]]), "_furrows_scatterplot_", ".pdf")
ggsave(filename = file_out, plot = p6, path = dir_out, device = "pdf")
```

The files will be saved as “\~/analysis/plots.\[ext\]”.

-----

# Show plot files information

``` r
info_out <- list.files(path = dir_out, pattern = "\\.pdf$", 
                       full.names = TRUE) %>% 
            md5sum()
```

The checksum (MD5 hashes) of the exported files are:

``` 
               analysis/plots/Use-wear_Asfc_scatterplot_.pdf 
                          "3a4ab05621f0b0810f814bc515316fd0" 
                    analysis/plots/Use-wear_boxplot_Asfc.pdf 
                          "964605f1bf4a15c7c5e808a1eb55f5ce" 
                  analysis/plots/Use-wear_boxplot_epLsar.pdf 
                          "8fa486c93e83b69d3dd91b6bcb853ffb" 
         analysis/plots/Use-wear_boxplot_First.direction.pdf 
                          "6c737ed19dade33487c0f03ab63bce4e" 
                 analysis/plots/Use-wear_boxplot_HAsfc81.pdf 
                          "dd731e9f4b213b579e10eba2624c39e4" 
                  analysis/plots/Use-wear_boxplot_HAsfc9.pdf 
                          "44e6985019d10a84d6a4db405badad74" 
                analysis/plots/Use-wear_boxplot_Isotropy.pdf 
                          "41ecfb6a3f8bacda14c2f33876cca85c" 
analysis/plots/Use-wear_boxplot_Maximum.depth.of.furrows.pdf 
                          "4e2062c9ad0e09ad51b071d73af9a72a" 
 analysis/plots/Use-wear_boxplot_Mean.density.of.furrows.pdf 
                          "0912dd25d02080feb4d1c2919b08131d" 
   analysis/plots/Use-wear_boxplot_Mean.depth.of.furrows.pdf 
                          "c70d170d80c80b8ec0f7e6f639a6fe19" 
               analysis/plots/Use-wear_boxplot_NewEplsar.pdf 
                          "ef507399ea9ef47f60dc00b48ed45fa1" 
                      analysis/plots/Use-wear_boxplot_Sa.pdf 
                          "f0b31d2d83c7c9fda441134b0ea3acb7" 
                     analysis/plots/Use-wear_boxplot_Sal.pdf 
                          "fc08f06de3c3adc164b51d957296613f" 
                     analysis/plots/Use-wear_boxplot_Sdq.pdf 
                          "5a59468cff61739c34ea0fe61e0df15b" 
                     analysis/plots/Use-wear_boxplot_Sdr.pdf 
                          "dfe7dfcaf8e33d4314885ae47590c293" 
        analysis/plots/Use-wear_boxplot_Second.direction.pdf 
                          "530f698f6f287da39be3cad1f3ea23c6" 
                     analysis/plots/Use-wear_boxplot_Sku.pdf 
                          "1242f35cad08f6523de1f0dd167194ad" 
                     analysis/plots/Use-wear_boxplot_Smc.pdf 
                          "c201ddf0d9a64597de2caace33a558e6" 
                    analysis/plots/Use-wear_boxplot_Smfc.pdf 
                          "dc2dccb700056672bb480dafae4eff5d" 
                     analysis/plots/Use-wear_boxplot_Smr.pdf 
                          "082371ad3d613cb715ca0a4a081e320b" 
                      analysis/plots/Use-wear_boxplot_Sp.pdf 
                          "0b58a89a31a00dbf6376ff506e723551" 
                      analysis/plots/Use-wear_boxplot_Sq.pdf 
                          "ab03f62bd57ea5c470ab7c545e33527f" 
                     analysis/plots/Use-wear_boxplot_Ssk.pdf 
                          "f8f18c80e93561cb9b39df515d5a3fa1" 
                     analysis/plots/Use-wear_boxplot_Std.pdf 
                          "23bf399a23fa08c90f02c2792afed108" 
                     analysis/plots/Use-wear_boxplot_Str.pdf 
                          "43a6990479db760fcb6b54c3e547ca64" 
                      analysis/plots/Use-wear_boxplot_Sv.pdf 
                          "ce2490b9b83e4e29ecd9e312ab81a861" 
                     analysis/plots/Use-wear_boxplot_Sxp.pdf 
                          "bcac1b584ace161b5d52bb7a2d7e8530" 
                      analysis/plots/Use-wear_boxplot_Sz.pdf 
                          "82261a5aee45c1898799bc5a6663756f" 
         analysis/plots/Use-wear_boxplot_Third.direction.pdf 
                          "a8f8941685dacf42578147b08ac2e77e" 
                      analysis/plots/Use-wear_boxplot_Vm.pdf 
                          "bb8b0c24b782061f1785a381e726e339" 
                     analysis/plots/Use-wear_boxplot_Vmc.pdf 
                          "ec22c28846abff40d301cfb2844e16dd" 
                     analysis/plots/Use-wear_boxplot_Vmp.pdf 
                          "f3321181bafecac2144efd795c4d0453" 
                      analysis/plots/Use-wear_boxplot_Vv.pdf 
                          "1c3ab30236556762863f8f275776f874" 
                     analysis/plots/Use-wear_boxplot_Vvc.pdf 
                          "8bc99300dc981eded390e91bab061e88" 
                     analysis/plots/Use-wear_boxplot_Vvv.pdf 
                          "72a7be9b4f554270831413f98119ef8a" 
            analysis/plots/Use-wear_furrows_scatterplot_.pdf 
                          "6ea7ecf5b5d57fb21d0be9f6a5b7a1d5" 
                       analysis/plots/Use-wear_plot_Asfc.pdf 
                          "60890de0252b21f268610a29d196d234" 
                     analysis/plots/Use-wear_plot_epLsar.pdf 
                          "f1ab791126e75ad44f58157c0a2bb3ac" 
            analysis/plots/Use-wear_plot_First.direction.pdf 
                          "bdaf0482d18b3386670369d4e13bdd94" 
                    analysis/plots/Use-wear_plot_HAsfc81.pdf 
                          "b3c9beff1fca4c13fbe5295f1059cce8" 
                     analysis/plots/Use-wear_plot_HAsfc9.pdf 
                          "ad16ddf81b73e506081a5e741ad3a53c" 
                   analysis/plots/Use-wear_plot_Isotropy.pdf 
                          "a063838f2fab9897b6d5c1323dcc1abb" 
   analysis/plots/Use-wear_plot_Maximum.depth.of.furrows.pdf 
                          "473359032cf4210d8ca977e4dbf7d267" 
    analysis/plots/Use-wear_plot_Mean.density.of.furrows.pdf 
                          "ee20c36df30b2e3bc2b426e20e5ee46d" 
      analysis/plots/Use-wear_plot_Mean.depth.of.furrows.pdf 
                          "eb4f4af0ddcdb2f562a651070aed29ea" 
                  analysis/plots/Use-wear_plot_NewEplsar.pdf 
                          "95e515c078f279416b27a2905da8aa44" 
                         analysis/plots/Use-wear_plot_Sa.pdf 
                          "b3817b7365015e863c9b085f512ae856" 
                        analysis/plots/Use-wear_plot_Sal.pdf 
                          "6f8821f47054d3975fe30b921745c8f0" 
                        analysis/plots/Use-wear_plot_Sdq.pdf 
                          "6aebdd2da30aba32cc5464684d303cbc" 
                        analysis/plots/Use-wear_plot_Sdr.pdf 
                          "70eb71ee16e7e38d9105f576e0f4ec22" 
           analysis/plots/Use-wear_plot_Second.direction.pdf 
                          "262b36d9256d5bf372b21b8a9e150fbf" 
                        analysis/plots/Use-wear_plot_Sku.pdf 
                          "1bb6d7eaf171e8e149b535a6a4ed03f6" 
                        analysis/plots/Use-wear_plot_Smc.pdf 
                          "068df15a81cb3ce86002f4ba622e16d9" 
                       analysis/plots/Use-wear_plot_Smfc.pdf 
                          "0ca639270b774b8708896f698a1dd5df" 
                        analysis/plots/Use-wear_plot_Smr.pdf 
                          "5d2062c73a2afa9d04548656147be75a" 
                         analysis/plots/Use-wear_plot_Sp.pdf 
                          "ace8136cbd8c24c1bcd7ea2365c8f701" 
                         analysis/plots/Use-wear_plot_Sq.pdf 
                          "3a2d16726e55cc76c57c0a79c29f0baf" 
                        analysis/plots/Use-wear_plot_Ssk.pdf 
                          "72769aac2b747958b203ef37477c3640" 
                        analysis/plots/Use-wear_plot_Std.pdf 
                          "1519628611203db1574ca8d5d87fcaa4" 
                        analysis/plots/Use-wear_plot_Str.pdf 
                          "961486927e6d8bc5fa7f574331041307" 
                         analysis/plots/Use-wear_plot_Sv.pdf 
                          "20747fca10d520071e6d6757ce66b05a" 
                        analysis/plots/Use-wear_plot_Sxp.pdf 
                          "d1675647fc5e0933151a77d151525b8c" 
                         analysis/plots/Use-wear_plot_Sz.pdf 
                          "47a382af5e81dbbb455b8b320a012e20" 
            analysis/plots/Use-wear_plot_Third.direction.pdf 
                          "2a8448e5ec5454ca79527a0ac6af2608" 
                         analysis/plots/Use-wear_plot_Vm.pdf 
                          "75d0dd3edd938d19bfdbd512375c4acf" 
                        analysis/plots/Use-wear_plot_Vmc.pdf 
                          "dd854596eaca0538de6d1f7f94be8be5" 
                        analysis/plots/Use-wear_plot_Vmp.pdf 
                          "9eba72304bf7e5805ee7af2d2c9ffa72" 
                         analysis/plots/Use-wear_plot_Vv.pdf 
                          "be0f22f2fbbe2e0fc575661e3e27f3ae" 
                        analysis/plots/Use-wear_plot_Vvc.pdf 
                          "acac776bf1565acf8354822bc8c298ba" 
                        analysis/plots/Use-wear_plot_Vvv.pdf 
                          "79e531008ac0665be4c4e9a270c52b9f" 
                 analysis/plots/Use-wear_SA_scatterplot_.pdf 
                          "acaaab2a5ab62a87f79e6965f64a2c98" 
                 analysis/plots/Use-wear_Sq_scatterplot_.pdf 
                          "2e4fd80b633e020c4cf2b0071f45b3ca" 
```

-----

# sessionInfo() and RStudio version

``` r
sessionInfo()
```

``` 
R version 4.0.2 (2020-06-22)
Platform: x86_64-w64-mingw32/x64 (64-bit)
Running under: Windows 10 x64 (build 18362)

Matrix products: default

locale:
[1] LC_COLLATE=German_Germany.1252  LC_CTYPE=German_Germany.1252   
[3] LC_MONETARY=German_Germany.1252 LC_NUMERIC=C                   
[5] LC_TIME=German_Germany.1252    

attached base packages:
[1] tools     stats     graphics  grDevices utils     datasets  methods  
[8] base     

other attached packages:
 [1] wesanderson_0.3.6 openxlsx_4.1.5    ggrepel_0.8.2     doBy_4.6.7       
 [5] patchwork_1.0.1   forcats_0.5.0     stringr_1.4.0     dplyr_1.0.2      
 [9] purrr_0.3.4       readr_1.3.1       tidyr_1.1.2       tibble_3.0.3     
[13] tidyverse_1.3.0   ggplot2_3.3.2     R.utils_2.10.1    R.oo_1.24.0      
[17] R.methodsS3_1.8.1

loaded via a namespace (and not attached):
 [1] Rcpp_1.0.5       lubridate_1.7.9  lattice_0.20-41  assertthat_0.2.1
 [5] rprojroot_1.3-2  digest_0.6.25    R6_2.4.1         cellranger_1.1.0
 [9] backports_1.1.9  reprex_0.3.0     evaluate_0.14    httr_1.4.2      
[13] pillar_1.4.6     rlang_0.4.7      readxl_1.3.1     rstudioapi_0.11 
[17] blob_1.2.1       Matrix_1.2-18    rmarkdown_2.3    labeling_0.3    
[21] munsell_0.5.0    broom_0.7.0      compiler_4.0.2   Deriv_4.0.1     
[25] modelr_0.1.8     xfun_0.16        pkgconfig_2.0.3  htmltools_0.5.0 
[29] tidyselect_1.1.0 fansi_0.4.1      crayon_1.3.4     dbplyr_1.4.4    
[33] withr_2.2.0      MASS_7.3-51.6    grid_4.0.2       jsonlite_1.7.1  
[37] gtable_0.3.0     lifecycle_0.2.0  DBI_1.1.0        magrittr_1.5    
[41] scales_1.1.1     zip_2.1.1        cli_2.0.2        stringi_1.5.3   
[45] farver_2.0.3     fs_1.5.0         xml2_1.3.2       ellipsis_0.3.1  
[49] generics_0.0.2   vctrs_0.3.4      glue_1.4.2       hms_0.5.3       
[53] yaml_2.2.1       colorspace_1.4-1 rvest_0.3.6      knitr_1.29      
[57] haven_2.3.1     
```

RStudio version 1.3.1073.

-----

END OF SCRIPT
