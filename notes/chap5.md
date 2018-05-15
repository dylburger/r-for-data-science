Two very high-level questions that occur in EDA:

* What type of variation occurs within my variables?
* What type of covariation occurs between my variables?

### Variation

Variation is the tendency of values of a variable to change from measurement to measurement.

The best way to understand and summarize variation is to visualize the distribution.

Visualize the distribution of categorical variables using a bar plot:

    ggplot(diamonds) + geom_bar(aes(cut))

We can generate a table of the count of each cut of diamonds using dplyr, too:

    diamonds %>% count(cut)

A histogram divides the x axis into equally-spaced bins and then counts the number of observations in each bin on the y axis.

    ggplot(diamonds) + geom_histogram(aes(carat))

Different binwidths can reveal different patters and there is no general rule that says you should have a binwidth of x. Experiment here!

If you want to view multiple histograms on the same chart, a better choice is a frequency polygon, which runs the same computation as a histogram but produces lines:

    ggplot(diamonds) + geom_freqpoly(aes(x = carat, color = cut), binwidth = 0.1)

### Typical Values

Some questions arise when we review the most common values of a given variable:

* What values are the most common? Under what conditions? Why?
* What values are the most rare?
* Are there any unusual values (unexpected / bad data)?
