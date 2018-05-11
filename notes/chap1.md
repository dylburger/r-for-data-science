* ggplot() creates an empty coordinate system. You use it to create the initial `ggplot` object, with the input data frame tied to the graphic.
* data is the _default_ dataset for the plot. If not specified, it must be specified in each layer of the plot.
* You add layers to your plot. `geom_point()` adds a layer of points.
* Each `geom` function takes a mapping argument. This defines how variables in your dataset are mapped to visual properties.
* The mapping argument is always paired with `aes()`. The `x` and `y` axes specify which variables are mapped to the x and y axes. ggplot looks for the mapped variables in the data frame you passed to the `data` argument of `ggplot()`.
* In general, the template we use looks something like: `ggplot(data = <DATA FRAME>) + GEOM_FUNC(mapping = aes(<MAPPINGS>)`.
* We can add another variable to our plot by mapping it to an aesthetic. An aesthetic is a visual property of object in our chart.
* Aesthetics can include size, shape, color, etc.
* We'll use the word "level" to describe aesthetic properties.
* If we want to associate a variable with an aesthetic, we can say `color = class`. ggplot2 will automatically assign a unique level of the aesthetic (a unique color, in this case) to each unique value of the variable. This is known as *scaling*.
* If we map a discrete variable to the size aesthetic, ggplot2 will warn us that this isn't a good idea. We are technically mapping an unordered variable to an ordered aesthetic.
* The `alpha` aesthetic controls the transparency of our geometric layer.
* We can set the aesthetics manually, outside of the mapping argument (e.g. `geom_point(mapping = aes(x = displ, y = hwy), color = 'blue')`)
* You can map the same variable to different aesthetics!
* You can pass any expression to an aesthetic, e.g. `color = displ < 5` to color points based on the boolean value that results.
* `facet_wrap` allows you to facet plots (split the plot into multiple plots, based on some discrete / categorical variable).
* `facet_wrap` takes a "formula" as its first argument. If we want to facet by a single variable, that will be something like `~ class`.
* `facet_grid` lets you facet a plot by more than one variable and also takes a formula as its first argument, e.g. `facet_grid(drv ~ cyl)`
* If you only want to facet by rows, and not columns, replace the variable in the column (latter) part of the formula with a `.`, e.g. `facet_grid(drv ~ .)`
* A "geom" is the geometric object used to represent data (e.g. bar geoms, line geoms, etc.)
* Every geom takes a mapping argument but not every geom takes the same aesthetics.
* [`ggplot2` cheatsheet](https://www.rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf)
* We can add a mapping to our original ggplot object and add multiple geometry layers to our chart, all tied to that original, default mapping, like so: `ggplot(data = mpg, mapping = aes(x = displ, y = hwy)) + geom_point() + geom_smooth()`.
* Bar charts and histograms generate counts of observations at particular bins. These are not data that come directly from the dataset (like for scatterplots). They are a statistic calculated from the data! Smoothers plot predictions from your data. Boxplots generate boxes!
* We can generate a basic bar plot using just one aesthetic, therefore, e.g. `ggplot(data = diamonds) + geom_bar(mapping = aes(x = cut))`.
* The algorithm used to generate this new value from data is called a "stat": a statistical transformation.
* This stat can be varied using the "stat" argument.
* The function used to generate this count is called `stat_count`. Generally you can replace this with the original geom to get the same result, e.g. `ggplot(data = diamonds) + stat_count(mapping = aes(x = cut))`
* *Every geom has a default stat, and every stat has a default geom*
* The "identity" stat is used when the value of the y axis is already present in the data, so you specify the `y` aesthetic and add `stat = "identity"` to use that value, instead of the default stat "count". `geom_col` is a geometry that uses stat = identity by default.
* We can fill bars with a set of stacked bars tied to the values of another variable (e.g. clarity), _and_ make all stacked bars the same height so that the height of each stacked bar represents the propotion, and makes it easier to compare across groups! e.g. `ggplot(data = diamonds) + geom_bar(mapping = aes(x = cut, fill = clarity), position = "fill")`
* `position = "dodge"` makes it easier to compare individual values, side by side.
* `geom_jitter()` adds a bit of random noise to the points of our plot so that overlapping values (on an overplotted plot) will show up at different points, since no two points are likely to receive the same random noise. This reduces the accuracy of the (x, y) point at small scales (but makes it a lot easier to identify clusters of points at larger scales).
* `coord_flip()` can be useful for flipping the x and y axes (e.g. to make horizontal boxplots).
* `coord_quickmap()` sets the correct aspect ratio for maps.


### Questions

* I don't quite understand the `mapping` argument.
* What's the difference between the `group` aesthetic and faceted plots?
* `geom_bar(mapping = aes(x = cut, y = ..prop.., group = 1))`: what's up with the `..prop..` and `group = 1`?
