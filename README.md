# Final Project - BIOL l668

## Description
This repository contains the code and notebook generated for the final project in the course BIOL 668 taught by Dr. Scott Kelley at San Diego State. RStudio was utilized, alongside many helpful online resources and tutorials, R-related, to generate this code.

- Required files:
  - Test_Growth_Data.csv
  - co_hl.csv
  - co_hs.csv
  - Co-Culture-Haem-andBacillus.csv
  - heterotrophic_coculture.csv
    
- 16S and 18S Sequences:
  - https://www.ncbi.nlm.nih.gov/nuccore/NR102783 
  - https://www.ncbi.nlm.nih.gov/nuccore/MG 66537 
  - https://www.ncbi.nlm.nih.gov/nuccore/NG_063315.1
  - https://www.ebi.ac.uk/ena/browser/view/MG022689

- Resources: 
  - https://r-graph-gallery.com/ggplot2-package.html
  - https://r-graph-gallery.com/line-chart-dual-Y-axis-ggplot2.html
  - https://www.bioconductor.org/packages/release/bioc/html/msa.html 
  - https://bioconductor.org/packages/release/bioc/html/Biostrings.html
  
- Figure Inspiration:
  - https://pmc.ncbi.nlm.nih.gov/articles/PMC7393147/ 

## Instructions 
Install and load in all necessary packages. This project focuses on data visualization, so much of these packages relate to that exactly.

Begin by reading in the Test_Growth_Data.csv, in order to use ``sec.axis`` and ``facet_wrap`` from ggplot. Code is contained within the private notebook that guides you through first creating a quick simplot boxplot with ggplot, so you may recall the format for plotting.

```
ggplot(gdata, aes(x= Media, y= GrowthRate, fill= Microorganism)) + geom_boxplot() + theme_light()
```
You will then create the multi-paneled plot with ``facet_wrap``. You can adjust the number of columns depending on the number of treatments you have. In this case, it is 4. Additionally, ``scales = "free_y"`` grants a y-axis to each plot.

Next, you will manually create a dataset of sequences, using  ``DNAStringSet``, which is a feature from Bioconductor that recognizes the sequence as biological data, and can work with it accordingly. This is so you can relate sequences to construct a phylogenetic tree.

The provided sequences are for 16S and 18S sequences, with only the 16S for *B.subtilis* being complete, and all others partial. Thus, you must use something such as  ``msa`` to trim and align the strings:

 ```
rRNA_sequences_alignment <- msa(rRNA_sequences, method = "Muscle")
 ```
You will create a distance matrix and use those calculations to form a neighbor-joining tree, using ``nj``, and then plot simply, using ``type = "phylogram``. Your output should be a phylogram with four branches.

For the next set of plots (three), you will use three different datasets. These contain OD600 and OD680 reads for each respective heterotroph, and H. lacustris, for their own mini pilot co-culture experiment in a 96-well plate. You will thus use the second through fourth files under "Data." The idea is to use ``sec.axis`` from ggplot to create dual-y-axes, where one reads for OD600, and the other for OD680.

Read in the data, and use the following as an example for how to combine two lines into a single graph.

```
ggplot(data, aes(x=TIme)) + geom_line(aes(y=Heterotroph, color = "Heterotroph (OD600)")) +  geom_line(aes(y=Microalgae, color = "Haematococcus lacustris (OD680)")) +
scale_y_continuous( name = "Optical Density at 600 nm", sec.axis = sec_axis(name = "Optical Density at 680 nm", ~.)) + scale_color_manual(name = "Microorganism",
values = c("Heterotroph (OD600)" = "red", "Haematococcus lacustris (OD680)" = "green")) 
```

Repeat this with each dataset to generate three different stackable plots. Note that  ``sec.axis`` uses the same scale as the previous axis, with the ``~.`` within the function denoting so. If a number n is added, the y-axis scale will be the product of the first scale ticks, multiplied by n.

Here, another plot will be created, for analyzing growth curves from a growth experiment. These include growth of the three heterotrophic pairs in BBMG media, read with Tecan automated plate reading. Use the final provided datset.

Read it in, and ensure you convert the Time numeric into a factor.

```
het_cocultures$Time <- as.factor(het_cocultures$Time)
```
You will calculate a few statistics, such as mean and standard deviation, to provide error bars to your otherwise rather simple ggplot.

Further, the notebook is completed with running some summary statistics. These are based on the data in the latter file (heterotrophic_coculture.csv).

Perform a few one-way anovas to determine the impact of Coculture composition and Time on the Optical Density factor. You can use either ``adonis2`` or ``aov``, which this notebook utilizes for both the one-way and two-way tests.

Based on your results, such as p-values and F-values, run a correlation test with ``cor.test()``. Do not forget to convert Time back into a numeric factor, so that it can be applicable for the correlation test.
