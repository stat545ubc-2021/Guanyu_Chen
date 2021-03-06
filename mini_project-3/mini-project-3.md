Mini Data-Analysis Deliverable 3
================

# Welcome to your last milestone in your mini data analysis project!

In Milestone 1, you explored your data and came up with research
questions. In Milestone 2, you obtained some results by making summary
tables and graphs.

In this (3rd) milestone, you’ll be sharpening some of the results you
obtained from your previous milestone by:

-   Manipulating special data types in R: factors and/or dates and
    times.
-   Fitting a model object to your data, and extract a result.
-   Reading and writing data as separate files.

**NOTE**: The main purpose of the mini data analysis is to integrate
what you learn in class in an analysis. Although each milestone provides
a framework for you to conduct your analysis, it’s possible that you
might find the instructions too rigid for your data set. If this is the
case, you may deviate from the instructions – just make sure you’re
demonstrating a wide range of tools and techniques taught in this class.

## Instructions

**To complete this milestone**, edit [this very `.Rmd`
file](https://raw.githubusercontent.com/UBC-STAT/stat545.stat.ubc.ca/master/content/mini-project/mini-project-3.Rmd)
directly. Fill in the sections that are tagged with
`<!--- start your work here--->`.

**To submit this milestone**, make sure to knit this `.Rmd` file to an
`.md` file by changing the YAML output settings from
`output: html_document` to `output: github_document`. Commit and push
all of your work to your mini-analysis GitHub repository, and tag a
release on GitHub. Then, submit a link to your tagged release on canvas.

**Points**: This milestone is worth 40 points (compared to the usual 30
points): 30 for your analysis, and 10 for your entire mini-analysis
GitHub repository. Details follow.

**Research Questions**: In Milestone 2, you chose two research questions
to focus on. Wherever realistic, your work in this milestone should
relate to these research questions whenever we ask for justification
behind your work. In the case that some tasks in this milestone don’t
align well with one of your research questions, feel free to discuss
your results in the context of a different research question.

# Setup

Begin by loading your data and the tidyverse package below:

``` r
library(datateachr) # <- might contain the data you picked!
library(tidyverse)
```

From Milestone 2, you chose two research questions. What were they? Put
them here.

<!-------------------------- Start your work below ---------------------------->

1.  *What is the distribution of the range of the flow in a year?*
2.  *Does the maximum of flow relate to month?*
    <!----------------------------------------------------------------------------->

# Exercise 1: Special Data Types (10)

For this exercise, you’ll be choosing two of the three tasks below –
both tasks that you choose are worth 5 points each.

But first, tasks 1 and 2 below ask you to modify a plot you made in a
previous milestone. The plot you choose should involve plotting across
at least three groups (whether by facetting, or using an aesthetic like
colour). Place this plot below (you’re allowed to modify the plot if
you’d like). If you don’t have such a plot, you’ll need to make one.
Place the code for your plot below.

<!-------------------------- Start your work below ---------------------------->

To conduct this project, I provide and revise the code from mini project
2 to get the selected dataset. **flow\_sample** is used as mentioned
before. A new factor about month is created for conduting the following
analysis.

``` r
flow_sample_q4 <- flow_sample %>%
  select(-c(station_id,sym))%>%
  filter(extreme_type=="maximum")%>%
  select(-extreme_type)%>%
  drop_na()%>%
  mutate(month_new=as_factor(month))

# There is no to save the dataset in the current assignment. 
# save(flow_sample_q4,file = "flow_sample_q4.Rdata")
# For simplified the repository structure, flow_sample_q4.Rdata is not saved from assignment 2 and loaded here.
```

Here is the boxplot of flows across different months.

``` r
flow_sample_q4 %>%
  ggplot(aes(month_new,flow))+
  geom_boxplot(aes(colour=month_new))+
  geom_jitter(aes(colour=month_new))
```

![](mini-project-3_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

<!----------------------------------------------------------------------------->

Now, choose two of the following tasks.

1.  Produce a new plot that reorders a factor in your original plot,
    using the `forcats` package (3 points). Then, in a sentence or two,
    briefly explain why you chose this ordering (1 point here for
    demonstrating understanding of the reordering, and 1 point for
    demonstrating some justification for the reordering, which could be
    subtle or speculative.)

2.  Produce a new plot that groups some factor levels together into an
    “other” category (or something similar), using the `forcats` package
    (3 points). Then, in a sentence or two, briefly explain why you
    chose this grouping (1 point here for demonstrating understanding of
    the grouping, and 1 point for demonstrating some justification for
    the grouping, which could be subtle or speculative.)

3.  If your data has some sort of time-based column like a date (but
    something more granular than just a year):

    1.  Make a new column that uses a function from the `lubridate` or
        `tsibble` package to modify your original time-based column. (3
        points)
        -   Note that you might first have to *make* a time-based column
            using a function like `ymd()`, but this doesn’t count.
        -   Examples of something you might do here: extract the day of
            the year from a date, or extract the weekday, or let 24
            hours elapse on your dates.
    2.  Then, in a sentence or two, explain how your new column might be
        useful in exploring a research question. (1 point for
        demonstrating understanding of the function you used, and 1
        point for your justification, which could be subtle or
        speculative).
        -   For example, you could say something like “Investigating the
            day of the week might be insightful because penguins don’t
            work on weekends, and so may respond differently”.

<!-------------------------- Start your work below ---------------------------->

**Task Number**: 1 `fct_reorder` is used for reorder the levels of month
by the mean of flow as showed by `levels()`. By reordering month, the
difference between June and other months can be easily found, which can
support the hypothesis that the maximum flow in June may significantly
higher than other months.

``` r
flow_sample_q4_reorder <- flow_sample_q4 %>%
  mutate(month_new=fct_reorder(month_new,flow,mean,.desc=T)) #reorder the levels of month

levels(flow_sample_q4$month_new)
```

    ## [1] "5" "6" "7" "8"

``` r
levels(flow_sample_q4_reorder$month_new)
```

    ## [1] "6" "7" "5" "8"

``` r
flow_sample_q4_reorder %>%
  ggplot(aes(month_new,flow))+
  geom_boxplot(aes(colour=month_new))+
  geom_jitter(aes(colour=month_new))
```

![](mini-project-3_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

<!----------------------------------------------------------------------------->
<!-------------------------- Start your work below ---------------------------->

**Task Number**: 2 `fct_lump`is used for lumping together the levels
month into “other” (except for June). `table()` show that the
conversations in May, July, and August are much less than June. In this
case, combine all these months in to “other” can be a better solution to
alleviate the effect of unbalanced sample size.

``` r
table(flow_sample_q4$month_new)
```

    ## 
    ##  5  6  7  8 
    ## 16 79 13  1

``` r
flow_sample_q4_lump <- flow_sample_q4 %>%
  mutate(month_new=fct_lump_min(month_new,20))# lump month

flow_sample_q4_lump %>%
  ggplot(aes(month_new,flow))+
  geom_boxplot(aes(colour=month_new))+
  geom_jitter(aes(colour=month_new))
```

![](mini-project-3_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

<!----------------------------------------------------------------------------->

# Exercise 2: Modelling

## 2.0 (no points)

Pick a research question, and pick a variable of interest (we’ll call it
“Y”) that’s relevant to the research question. Indicate these.

<!-------------------------- Start your work below ---------------------------->

**Research Question**: Does the maximum of flow relate to month?
Specifically, does the maximum of flow in June significantly differ to
other months.

**Variable of interest**: Flow, i.e., the maximum of the flow.

<!----------------------------------------------------------------------------->

## 2.1 (5 points)

Fit a model or run a hypothesis test that provides insight on this
variable with respect to the research question. Store the model object
as a variable, and print its output to screen. We’ll omit having to
justify your choice, because we don’t expect you to know about model
specifics in STAT 545.

-   **Note**: It’s OK if you don’t know how these models/tests work.
    Here are some examples of things you can do here, but the sky’s the
    limit.
    -   You could fit a model that makes predictions on Y using another
        variable, by using the `lm()` function.
    -   You could test whether the mean of Y equals 0 using `t.test()`,
        or maybe the mean across two groups are different using
        `t.test()`, or maybe the mean across multiple groups are
        different using `anova()` (you may have to pivot your data for
        the latter two).
    -   You could use `lm()` to test for significance of regression.

<!-------------------------- Start your work below ---------------------------->

Here I store the model object as a “m1” by using `lm()`, and print its
output to screen.

``` r
m1<- lm(flow~month_new,data=flow_sample_q4_lump)#lm()is used
summary(m1)
```

    ## 
    ## Call:
    ## lm(formula = flow ~ month_new, data = flow_sample_q4_lump)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -111.4  -40.5   -5.5   31.5  247.6 
    ## 
    ## Coefficients:
    ##                Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)     218.367      6.875  31.762   <2e-16 ***
    ## month_newOther  -22.867     13.105  -1.745   0.0839 .  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 61.11 on 107 degrees of freedom
    ## Multiple R-squared:  0.02767,    Adjusted R-squared:  0.01858 
    ## F-statistic: 3.045 on 1 and 107 DF,  p-value: 0.08387

<!----------------------------------------------------------------------------->

## 2.2 (5 points)

Produce something relevant from your fitted model: either predictions on
Y, or a single value like a regression coefficient or a p-value.

-   Be sure to indicate in writing what you chose to produce.
-   Your code should either output a tibble (in which case you should
    indicate the column that contains the thing you’re looking for), or
    the thing you’re looking for itself.
-   Obtain your results using the `broom` package if possible. If your
    model is not compatible with the broom function you’re needing, then
    you can obtain your results by some other means, but first indicate
    which broom function is not compatible.

<!-------------------------- Start your work below ---------------------------->

1.P-value is provided as below.

2.“output” is stored as a tibble and printed out. P-value is printed out
by `output$p.value[2]`

3.  The results is obtained by `broom::tidy()`.

``` r
output <- broom::tidy(m1)
print(output)
```

    ## # A tibble: 2 x 5
    ##   term           estimate std.error statistic  p.value
    ##   <chr>             <dbl>     <dbl>     <dbl>    <dbl>
    ## 1 (Intercept)       218.       6.88     31.8  2.71e-56
    ## 2 month_newOther    -22.9     13.1      -1.74 8.39e- 2

``` r
output$p.value[2]
```

    ## [1] 0.08387029

<!----------------------------------------------------------------------------->

# Exercise 3: Reading and writing data

Get set up for this exercise by making a folder called `output` in the
top level of your project folder / repository. You’ll be saving things
there.

## 3.1 (5 points)

Take a summary table that you made from Milestone 2 (Exercise 1.2), and
write it as a csv file in your `output` folder. Use the `here::here()`
function.

-   **Robustness criteria**: You should be able to move your Mini
    Project repository / project folder to some other location on your
    computer, or move this very Rmd file to another location within your
    project repository / folder, and your code should still work.
-   **Reproducibility criteria**: You should be able to delete the csv
    file, and remake it simply by knitting this Rmd file.

<!-------------------------- Start your work below ---------------------------->

Here is the summary table.

``` r
#  A summary table that I made from Milestone 2
table3_1<- flow_sample %>%
  group_by(extreme_type)%>%
  summarise(flow_mean = mean(flow,na.rm=T),flow_range=max(flow,na.rm = T)-min(flow,na.rm = T),flow_median=median(flow,na.rm = T),flow_sd=sd(flow,na.rm = T),flow_n=length(na.omit(flow)))

print(table3_1)
```

    ## # A tibble: 2 x 6
    ##   extreme_type flow_mean flow_range flow_median flow_sd flow_n
    ##   <chr>            <dbl>      <dbl>       <dbl>   <dbl>  <int>
    ## 1 maximum         212.       359         204     61.7      109
    ## 2 minimum           6.27       4.82        6.15   0.965    107

And the csv is created in the output folder in the mini\_project-3
folder.

``` r
dir.create(here::here("output"))
```

    ## Warning in dir.create(here::here("output")): 'D:\Dropbox\00_Course\STAT
    ## 545\Mini_project\Guanyu_Chen\output' already exists

``` r
write_csv(table3_1, here::here("output","table3_1.csv")) # save the table
```

<!----------------------------------------------------------------------------->

## 3.2 (5 points)

Write your model object from Exercise 2 to an R binary file (an RDS),
and load it again. Be sure to save the binary file in your `output`
folder. Use the functions `saveRDS()` and `readRDS()`.

-   The same robustness and reproducibility criteria as in 3.1 apply
    here.

<!-------------------------- Start your work below ---------------------------->

m1 is saved as rds. And m2 stores the output of `readRDS()`.

``` r
saveRDS(m1, here::here("output","model.rds"))
m2<- readRDS(here::here("output","model.rds"))
print(m2) # to show the readRDS() works
```

    ## 
    ## Call:
    ## lm(formula = flow ~ month_new, data = flow_sample_q4_lump)
    ## 
    ## Coefficients:
    ##    (Intercept)  month_newOther  
    ##         218.37          -22.87

<!----------------------------------------------------------------------------->

# Tidy Repository

Now that this is your last milestone, your entire project repository
should be organized. Here are the criteria we’re looking for.

## Main README (3 points)

There should be a file named `README.md` at the top level of your
repository. Its contents should automatically appear when you visit the
repository on GitHub.

Minimum contents of the README file:

-   In a sentence or two, explains what this repository is, so that
    future-you or someone else stumbling on your repository can be
    oriented to the repository.
-   In a sentence or two (or more??), briefly explains how to engage
    with the repository. You can assume the person reading knows the
    material from STAT 545A. Basically, if a visitor to your repository
    wants to explore your project, what should they know?

Once you get in the habit of making README files, and seeing more README
files in other projects, you’ll wonder how you ever got by without them!
They are tremendously helpful.

## File and Folder structure (3 points)

You should have at least four folders in the top level of your
repository: one for each milestone, and one output folder. If there are
any other folders, these are explained in the main README.

Each milestone document is contained in its respective folder, and
nowhere else.

Every level-1 folder (that is, the ones stored in the top level, like
“Milestone1” and “output”) has a `README` file, explaining in a sentence
or two what is in the folder, in plain language (it’s enough to say
something like “This folder contains the source for Milestone 1”).

## Output (2 points)

All output is recent and relevant:

-   All Rmd files have been `knit`ted to their output, and all data
    files saved from Exercise 3 above appear in the `output` folder.
-   All of these output files are up-to-date – that is, they haven’t
    fallen behind after the source (Rmd) files have been updated.
-   There should be no relic output files. For example, if you were
    knitting an Rmd to html, but then changed the output to be only a
    markdown file, then the html file is a relic and should be deleted.

Our recommendation: delete all output files, and re-knit each
milestone’s Rmd file, so that everything is up to date and relevant.

PS: there’s a way where you can run all project code using a single
command, instead of clicking “knit” three times. More on this in STAT
545B!

## Error-free code (1 point)

This Milestone 3 document knits error-free. (We’ve already graded this
aspect for Milestone 1 and 2)

## Tagged release (1 point)

You’ve tagged a release for Milestone 3. (We’ve already graded this
aspect for Milestone 1 and 2)
