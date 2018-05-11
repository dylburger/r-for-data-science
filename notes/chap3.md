### dplyr

Let's learn how to munge data using `dplyr`!

We're going to use the **nycflights13** data package here:

    library("nycflights13")

The `flights` object that we'll be using is a "tibble", which is a lot like a data frame in R.

We've got a few different data types here, including `chr` (a character vector, aka a string) and `dttm` (a date time object).

There are **5 key things we're going to use in `dplyr`**:

* Select observations by values (using `filter()`)
* Re-order rows (using `arrange()`)
* Pick variables by their name (`select()`)
* Create new variables with functions of existing variables (`mutate()`)
* Collapse many values down into a single summary value (`summarize()`)

We'll also use `group_by()` to operate on groups of rows. The above functions can work on groups.

All of these functions work similarly:

* First argument is a data frame or tibble.
* Subsequent args determine what to do with the data.
* Returns a new data frame (or tibble).

#### filter

One example of `filter()`:

    filter(flights, month == 1, day == 1)

`dplyr` never modifies the original data frame, it just returns a new one, so we'll need to assign the resulting value to a new variable if we want to save it.

You might expect this to work:

    sqrt(2) ^ 2 == 2

but it returns `FALSE`. Why? Computers represent floating point numbers with finite precision, which means that the left number is near 2 (it even prints as 2), but isn't exactly equal to 2, which is what the `==` operator tests for. We can use `near()` to test for equality, since it includes some buffer / tolerance for equality.

    near(sqrt(2) ^ 2, 2)

The following two lines both return the same subset of our data frame (the set of flights in Nov or Dec):

    filter(flights, month == 11 | month == 12)
    filter(flights, month %in% c(11, 12))

#### Missing values

R uses `NA` to represent unknown or missing values.

Use `is.na()` to test for the presence of `NA` values.

`filter()` will only return rows where the given conditions are true. If we want to include `NA` values, we must ask for them explicitly:

    x <- tibble(x = c(1, NA, 2))
    filter(x, is.na(x) | x > 1)

#### EXERCISES

_Find all flights with an arrival delay of 2 or more hours_

    filter(flights, arr_delay >= 120)

_Find all flights that flew to Houston_

    filter(flights, dest == "HOU")

_Find all flights that were operated by American, United or Delta_

    filter(flights, carrier == "AA" | carrier == "DL" | carrier == "UA")

We can use `between()` to filter rows of a data frame that are between given values (i.e. shortening the expression `x >= left & x <= right`):
    
    filter(flights, between(dep_time, 0, 600))

We can find flights that were completely cancelled using:

    filter(flights, is.na(dep_time))

#### Arranging rows

`arrange()` orders rows of a data frame by column names some more complex criteria, e.g.

    arrange(flights, year, month, day)

`desc()` arranges in descending order

    arrange(flights, desc(arr_delay))o

By default, NA values appear last in sorted lists. It appears we can sort with NA values first using something like:

    arrange(flights, desc(is.na(dep_time))

#### Selecting variables

We often need to select a handful out of hundreds or thousands of variables. `select()` can help!

This returns the year, month and day variables from the `flights` tibble:

    select(flights, year, month, day)

We can rename columns of a tibble using `rename()`, e.g. renaming `dep_delay` to `departure_delay`:
    
    rename(flights, departure_delay = dep_delay)

This will move the `dep_delay` variable to the front of the data frame and keep everything else:

    select(flights, dep_delay, everything())

We can use a number of helper functions that allow us to match specific columns with select, e.g.

    select(flights, starts_with("dep"))
    select(flights, ends_with("delay"))

We can select multiple columns whose names are already contained in a vector:

    vars <- c("year", "month", "day")
    select(flights, one_of(vars))

#### Mutations

`mutate()` allows you to create new columns from existing columns.

`mutate()` will always add column at the end of your data frame.

The `View()` function in RStudio allows you to view a whole data frame in a spreadsheet-like form!

Usage:
    
    flights_with_gain <- mutate(flights, gain = arr_delay - dep_delay)

`transmute` creates a new data frame that _only_ has the new columns you specify.

You can use any function with `mutate()` that takes a vector as an input and returns a vector of the same length.

#### Other functions we can use with mutations

* Arithmetic operators can be applied to vectors!
* Proportion of total: `x / sum(x)`
* Distance from mean: `x - mean(x)`
* Integer division: `x %/% y`
* Modulo: `x %% y`
* Logarithm: `log(), log2(), log10()`
* `lead()` and `lag()` to compute running differences, e.g. `x - lag(x)`
* Cumulative sums, products, etc: `cumsum()`, `cumprod()`
* Ranking, e.g. `min_rank()`

We can combine all of these functions to mutate, arrange, and select only variables we care about using code like this:

    select(arrange(mutate(flights, top_delays = min_rank(desc(dep_delay))), top_delays), dep_delay, top_delays)

#### summarize

`summarize()` collapses a data frame into a single row based on some summarization.

We can combine `summarize` with `group_by` to summarize groups of observations and get summary stats for each group, e.g. let's get the average departure delay for our flights:

    by_day <- group_by(flights, year, month, day)
    summarize(by_day, delay = mean(dep_delay, na.rm = TRUE))

This code creates a new variable on the grouped data frame, `delay`, which gives us the average departure delay for the grouping.

#### The pipe operator

It's a pain to store the results of intermediate operations in their own data frames, when those data frames aren't used beyond the life of these intermediate operations. They're just to store data frames / groups / summaries so that we don't have one big line of code that we can't easily make sense of!

The pipe operator, `%>%`, can facilitate this process. It helps you focus on the transformations (the verbs), not what's being transformed (the nouns). We can pronounce `%>%` as "then".

Here's an example for our flights data:

    delays <- flights %>% 
        group_by(dest) %>% 
        summarize(
            count = n(), 
            dist = mean(distance, na.rm = TRUE), 
            delay = mean(arr_delay, na.rm = TRUE)
        ) %>% 
        filter(count > 20, dest != "HNL")

Behind the scenes, `x %>% f(y)` is transformed to `f(x, y)`.

#### Missing values

Aggregation functions work like other functions with `NA` values: if the input contains `NA` values, the output will be `NA`.

All aggregation functions have an `na.rm` argument that lets you remove missing values before the aggregate computation is applied.

We can pipe the output of one function to the data tied to a ggplot object, like so:

    delays %>% filter(count > 25) %>% ggplot(aes(count, delay)) + geom_point()

### Other stats functions we can apply to data frames

* `quantile(x, 0.25)` will calculate the value that is greater than 25% of the values, and less than the remaining 75%.
* `mad(x)` calculates the median absolute deviation, which is a more robust statistic to quantify the average spread of a value compared to its mean (useful when we have a lot of outliers).
* `first()` and `last()` will get the first and last value, respectively, of a group.
* You can count the number of non-missing values using `sum(is.na(x))`.
* You can count the number of distinct values of a variable using `n_distinct(x)`.

If all you need is a count, `dplyr` provides a helper:

    df %>% count(x)

### Multiple levels of grouping / summarization

    daily <- group_by(flights, year, month, day)
    per_day <- summarize(daily, flights = n())
    per_month <- summarize(per_day, flights = sum(flights))
    per_year <- summarize(per_month, flights = sum(flights))

N.B. "The sum of groupwise sums is the overall sum, but the median of groupwise medians is not the overall median"

To find out more about window functions, run

    vignette("window-functions")
