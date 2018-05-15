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
* What clusters exist in my data when I focus on a specific variable?

### Unusual Values

Outliers can be difficult to see with histograms. A single value in a small bin is almost invisible to the human eye.

We can use the `coord_cartesian()` function to change the zoom level on a plot (keeping the original scale intact)

    ggplot(diamonds) + geom_histogram(aes(x = y), binwidth = 0.5) + coord_cartesian(ylim=c(0, 50))

After visual examination, we can create a new data frame of just these outlier values:

    unusual <- diamonds %>% filter(y < 3 | y > 20) %>% arrange(y)

and can quickly tell that these values are measurement errors. We should note this, filter these values _out_, and move on.

Aside: remember that we can quickly get a count of observations broken out by some field with:

    target_carats %>% count(field)

We probably do not want to remove rows with bad values completely, because this would prevent us from using the good variables from the observation. Instead, we can `mutate` the values, replacing them with `NA`s:

    diamonds2 <- diamonds %>% mutate(y = ifelse(y < 3 | y > 20, NA, y))

`ifelse` takes three arguments: 1.) the condition we're testing 2.) the value to set for `y` if the condition is TRUE and 3.) the value to set if the condition is FALSE.

### Covariation

Covariation is the tendency for values of two or more variables to vary together. Here again, visualization is key!
