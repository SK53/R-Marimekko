# R-Marimekko
Creating Marimekko plots using ggplot in R 

This is largely based on two existing examples:

* A gist by DS Parks: https://gist.github.com/dsparks/3771604
* An example on StackOverflow by Edwin: http://stackoverflow.com/questions/19233365/how-to-create-a-marimekko-mosaic-plot-in-ggplot2

Although both produce decent plots they proved unable to be readily extended 
for the funtionality I wanted. The first uses geom_rect and the second used geom_bar. 
I preferred the approach in (2), but in practice using geom_rect as in (1) is essential.

== Steps ==

Basically the process of creating a Marimekko can be seen as three steps.

1. Start with data containing for individual data points, such that each data point 
contains columns with the categorical variables which will form the axes of the plot. 
As an aside, continuous variables might be turned into categoricals by using breaks. I actually group base categoricals into a smaller number of groups.

2. Create a cross-tabulation of the data using two variables (as above). The cross-tab 
should (ideally) calculate percentages both across rows & columns & across the entire tabulation. 

2a. Calculate left, right, bottom, top positions for each cell of the cross-tab (assume that the width
& height of the eventual plot a particular number of units ). I use 1 and 100.

3. Create the plot.

I actually do 1 & 2 in PostgreSQL and export the result as a CSV. This is merely because I' m more familiar with using SQL windowing functions than doing the same thing in R (see the gist for one way to do it).

In principle one can start from an existing cross-tab data set.

== The plot: line by line ==

Basically the plot consistes of 3 overlaid plots:

* the main marimekko plot: using geom_rect
* labelling plots above (showing numeric total for columns) and below (labelling with ISO 2 character country codes). Both use geom_text. 

2 R data frames are used:

* r1 for the main plot (this is not actually a cross-tabulation, but each cell in the cross-tab is a row). Basic columns: x-category, y-category, count, pct_of_total, pct_of_column, cum_pct_of_column, cum_pct_of_xaxis, cell_left, cell_right, cell_bottom,cell_top, sort_label
* cc for the labelling plots: contains summary data for each country (to allow correct positioning of labels). Columns pretty much as above without y-category, cell_top and cell_bottom.

The basic plot is fairly self-explanatory. The order of elements must be determined in the cross-tab (step 2) calculation for both data sets. I havent explored alternative ordering methods.

The colour palette is based on one recommended for avoiding problems for colour-blind viewers of the plot. However, as I had a larger number of categories I have changed the luminance for each base colour in order to extend the palette. I dont know if this is adequate, but now know of some more extensive resources for guidance.

The label plots change the size of the labels because category labels get very close together at the right hand end. Jitter is introduced to minimise overlap. These dont look partcularly nice, but do make the overall plot more legible (providing one can recognise ISO codes). The original intention was to place minor countris & retail categories into 'Other' to reduce or eliminate this issue. Time did not permit this as I wanted to finish these for a talk.

Obviously the whole thing really should be re-written to be more legible and so it can be called as a function (see the StackOverflow page referenced above).









