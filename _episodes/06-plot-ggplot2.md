---
title: Creating Publication-Quality Graphics
teaching: 60
exercises: 20
questions:
- "How can I create and save publication-quality graphics in R?"
objectives:
- "To be able to use ggplot2 to generate publication quality graphics."
- "To understand the basic grammar of graphics, including the aesthetics and geometry layers, adding statistics, transforming scales, and coloring or panelling by groups."
- "To understand how to save plots in a variety of formats"
- "To be able to find extensions for ggplot2 to produce custom graphics"
keypoints:
- "Use `ggplot2` to create plots."
- "Think about graphics in layers: aesthetics, geometry, statistics, scale transformation, and grouping."
source: Rmd
---



Plotting our data is one of the best ways to
quickly explore it and the various relationships
between variables.

There are three main plotting systems in R,
the [base plotting system][base], the [lattice][lattice]
package, and the [ggplot2][ggplot2] package.

[base]: http://www.statmethods.net/graphs/
[lattice]: http://www.statmethods.net/advgraphs/trellis.html
[ggplot2]: http://www.statmethods.net/advgraphs/ggplot2.html

Today we'll be learning about the ggplot2 package, because
it is the most effective for creating publication quality
graphics.  It is also part of the tidyverse, which we introduced in [episode 4]({{ page.root }}/05-dplyr).

ggplot2 is built on the grammar of graphics, the idea that any plot can be
expressed from the same set of components: a **data** set, a
**coordinate system**, and a set of **geoms**--the visual representation of data
points.

The key to understanding ggplot2 is thinking about a figure in layers.
This idea may be familiar to you if you have used image editing programs like Photoshop, Illustrator, or
Inkscape.

Let's start off with an example, using our gapminder data:



~~~
library("tidyverse")
ggplot(data = gapminder, aes(x = gdpPercap, y = lifeExp)) +
  geom_point()
~~~
{: .r}

<img src="../fig/rmd-06-lifeExp-vs-gdpPercap-scatter-1.png" title="plot of chunk lifeExp-vs-gdpPercap-scatter" alt="plot of chunk lifeExp-vs-gdpPercap-scatter" style="display: block; margin: auto;" />

So the first thing we do is call the `ggplot` function. This function lets R
know that we're creating a new plot, and any of the arguments we give the
`ggplot` function are the *global* options for the plot: they apply to all
layers on the plot.

We've passed in two arguments to `ggplot`. First, we tell `ggplot` what data we
want to show on our figure, in this example the gapminder data we read in
earlier. For the second argument we passed in the `aes` function, which
tells `ggplot` how variables in the **data** map to *aesthetic* properties of
the figure, in this case the **x** and **y** locations. Here we told `ggplot` we
want to plot the "gdpPercap" column of the gapminder data frame on the x-axis, and
the "lifeExp" column on the y-axis. 

By itself, the call to `ggplot` isn't enough to draw a figure:


~~~
ggplot(data = gapminder, aes(x = gdpPercap, y = lifeExp))
~~~
{: .r}

<img src="../fig/rmd-06-unnamed-chunk-2-1.png" title="plot of chunk unnamed-chunk-2" alt="plot of chunk unnamed-chunk-2" style="display: block; margin: auto;" />

We need to tell `ggplot` how we want to visually represent the data, which we
do by adding a new **geom** layer. In our example, we used `geom_point`, which
tells `ggplot` we want to visually represent the relationship between **x** and
**y** as a scatter-plot of points:


~~~
ggplot(data = gapminder, aes(x = gdpPercap, y = lifeExp)) +
  geom_point()
~~~
{: .r}

<img src="../fig/rmd-06-lifeExp-vs-gdpPercap-scatter2-1.png" title="plot of chunk lifeExp-vs-gdpPercap-scatter2" alt="plot of chunk lifeExp-vs-gdpPercap-scatter2" style="display: block; margin: auto;" />

## Combining `dplyr` and `ggplot2`

As `gplot2` is part of the tidyverse, we can use it with pipes.  As we will see later in the 
episode, this will be particularly useful if we need to modify the data before plotting it.

We can repeat the above plot, using a pipe, as follows:


~~~
gapminder %>% ggplot(aes(x = gdpPercap, y = lifeExp)) + geom_point()
~~~
{: .r}

<img src="../fig/rmd-06-unnamed-chunk-3-1.png" title="plot of chunk unnamed-chunk-3" alt="plot of chunk unnamed-chunk-3" style="display: block; margin: auto;" />
Note that the `ggplot2` commands are joined by the `+` symbol and not the `%>%` symbol.

> ## Challenge 1
>
> Modify the example so that the figure shows how life expectancy has
> changed over time.
>
> Hint: the gapminder dataset has a column called "year", which should appear
> on the x-axis.
>
> > ## Solution to challenge 1
> >
> > Here is one possible solution:
> >
> > 
> > ~~~
> > gapminder %>%  ggplot(aes(x = year, y = lifeExp)) + geom_point()
> > ~~~
> > {: .r}
> > 
> > <img src="../fig/rmd-06-ch1-sol-1.png" title="plot of chunk ch1-sol" alt="plot of chunk ch1-sol" style="display: block; margin: auto;" />
> >
> {: .solution}
{: .challenge}

>
> ## Challenge 2
>
> In the previous examples and challenge we've used the `aes` function to tell
> the scatterplot **geom** about the **x** and **y** locations of each point.
> Another *aesthetic* property we can modify is the point *color*. Modify the
> code from the previous challenge to **color** the points by the "continent"
> column. What trends do you see in the data? Are they what you expected?
>
> > ## Solution to challenge 2
> >
> > In the previous examples and challenge we've used the `aes` function to tell
> > the scatterplot **geom** about the **x** and **y** locations of each point.
> > Another *aesthetic* property we can modify is the point *color*. Modify the
> > code from the previous challenge to **color** the points by the "continent"
> > column. What trends do you see in the data? Are they what you expected?
> >
> > 
> > ~~~
> > gapminder %>% ggplot(aes(x = year, y = lifeExp, color = continent)) +
> >   geom_point()
> > ~~~
> > {: .r}
> > 
> > <img src="../fig/rmd-06-ch2-sol-1.png" title="plot of chunk ch2-sol" alt="plot of chunk ch2-sol" style="display: block; margin: auto;" />
> >
> {: .solution}
{: .challenge}


## Layers

Using a scatter-plot probably isn't the best for visualizing change over time.
Instead, let's tell `ggplot` to visualize the data as a line plot:


~~~
gapminder %>%  ggplot(aes(x = year, y = lifeExp, group = country, color = continent)) +
  geom_line()
~~~
{: .r}

<img src="../fig/rmd-06-lifeExp-line-1.png" title="plot of chunk lifeExp-line" alt="plot of chunk lifeExp-line" style="display: block; margin: auto;" />

Instead of adding a `geom_point` layer, we've added a `geom_line` layer. We've
added the **group** *aesthetic*, which tells `ggplot` to draw a line for each
country (try removing this to see the effect)

But what if we want to visualize both lines and points on the plot? We can
simply add another layer to the plot:


~~~
gapminder %>% ggplot(aes(x = year, y = lifeExp, group = country, color = continent)) +
  geom_line() + geom_point()
~~~
{: .r}

<img src="../fig/rmd-06-lifeExp-line-point-1.png" title="plot of chunk lifeExp-line-point" alt="plot of chunk lifeExp-line-point" style="display: block; margin: auto;" />

It's important to note that each layer is drawn on top of the previous layer. In
this example, the points have been drawn *on top of* the lines. Here's a
demonstration:


~~~
gapminder %>% ggplot(aes(x = year, y = lifeExp, group = country)) +
  geom_line(aes(color = continent)) + geom_point()
~~~
{: .r}

<img src="../fig/rmd-06-lifeExp-layer-example-1-1.png" title="plot of chunk lifeExp-layer-example-1" alt="plot of chunk lifeExp-layer-example-1" style="display: block; margin: auto;" />

In this example, the *aesthetic* mapping of **color** has been moved from the
global plot options in `ggplot` to the `geom_line` layer so it no longer applies
to the points. Now we can clearly see that the points are drawn on top of the
lines.

> ## Tip: Setting an aesthetic to a value instead of a mapping
>
> So far, we've seen how to use an aesthetic (such as **color**) as a *mapping* to a variable in the data. For example, when we use `geom_line(aes(color = continent))`, ggplot will give a different colour to each continent. But what if we want to change the colour of all lines to blue? You may think that `geom_line(aes(color = "blue"))` should work, but it doesn't. Since we don't want to create a mapping to a specific variable, we simply move the colour specification outside of the `aes()` function, like this: `geom_line(color = "blue")`.
{: .callout}

> ## Challenge 3
>
> Switch the order of the point and line layers from the previous example. What
> happened?
>
> > ## Solution to challenge 3
> >
> > Switch the order of the point and line layers from the previous example. What
> > happened?
> >
> > 
> > ~~~
> > gapminder %>% ggplot(aes(x = year, y = lifeExp, group = country)) +
> >  geom_point() + geom_line(aes(color = continent))
> > ~~~
> > {: .r}
> > 
> > <img src="../fig/rmd-06-ch3-sol-1.png" title="plot of chunk ch3-sol" alt="plot of chunk ch3-sol" style="display: block; margin: auto;" />
> >
> > The lines now get drawn over the points.
> >
> {: .solution}
{: .challenge}

## Transformations and statistics

Ggplot also makes it easy to overlay statistical models over the data. To
demonstrate we'll go back to our first example:


~~~
gapminder %>% ggplot(aes(x = gdpPercap, y = lifeExp, color= continent)) +
  geom_point()
~~~
{: .r}

<img src="../fig/rmd-06-lifeExp-vs-gdpPercap-scatter3-1.png" title="plot of chunk lifeExp-vs-gdpPercap-scatter3" alt="plot of chunk lifeExp-vs-gdpPercap-scatter3" style="display: block; margin: auto;" />

Currently it's hard to see the relationship between the points due to some strong
outliers in GDP per capita. We can change the scale of units on the x axis using
the *scale* functions. These control the mapping between the data values and
visual values of an aesthetic. We can also modify the transparency of the
points, using the *alpha* function, which is especially helpful when you have
a large amount of data which is very clustered.


~~~
gapminder %>% ggplot(aes(x = gdpPercap, y = lifeExp)) +
  geom_point(alpha = 0.5) + scale_x_log10()
~~~
{: .r}

<img src="../fig/rmd-06-axis-scale-1.png" title="plot of chunk axis-scale" alt="plot of chunk axis-scale" style="display: block; margin: auto;" />

The `log10` function applied a transformation to the values of the gdpPercap
column before rendering them on the plot, so that each multiple of 10 now only
corresponds to an increase in 1 on the transformed scale, e.g. a GDP per capita
of 1,000 is now 3 on the y axis, a value of 10,000 corresponds to 4 on the y
axis and so on. This makes it easier to visualize the spread of data on the
x-axis.

> ## Tip Reminder: Setting an aesthetic to a value instead of a mapping
>
> Notice that we used `geom_point(alpha = 0.5)`. As the previous tip mentioned, using a setting outside of the `aes()` function will cause this value to be used for all points, which is what we want in this case. But just like any other aesthetic setting, *alpha* can also be mapped to a variable in the data. For example, we can give a different transparency to each continent with `geom_point(aes(alpha = continent))`.
{: .callout}

We can fit a simple relationship to the data by adding another layer,
`geom_smooth`:


~~~
gapminder %>% ggplot(aes(x = gdpPercap, y = lifeExp)) +
  geom_point() + scale_x_log10() + geom_smooth(method = "lm")
~~~
{: .r}

<img src="../fig/rmd-06-lm-fit-1.png" title="plot of chunk lm-fit" alt="plot of chunk lm-fit" style="display: block; margin: auto;" />

We can make the line thicker by *setting* the **size** aesthetic in the
`geom_smooth` layer:


~~~
gapminder %>% ggplot(aes(x = gdpPercap, y = lifeExp)) +
  geom_point() + scale_x_log10() + geom_smooth(method = "lm", size = 1.5)
~~~
{: .r}

<img src="../fig/rmd-06-lm-fit2-1.png" title="plot of chunk lm-fit2" alt="plot of chunk lm-fit2" style="display: block; margin: auto;" />

There are two ways an *aesthetic* can be specified. Here we *set* the **size**
aesthetic by passing it as an argument to `geom_smooth`. Previously in the
lesson we've used the `aes` function to define a *mapping* between data
variables and their visual representation.

> ## Challenge 4a
>
> Modify the color and size of the points on the point layer in the previous
> example.
>
> Hint: do not use the `aes` function.
>
> > ## Solution to challenge 4a
> >
> > Modify the color and size of the points on the point layer in the previous
> > example.
> >
> > Hint: do not use the `aes` function.
> >
> > 
> > ~~~
> > gapminder %>% ggplot(aes(x = gdpPercap, y = lifeExp)) +
> >  geom_point(size = 3, color = "orange") + scale_x_log10() +
> >  geom_smooth(method = "lm", size = 1.5)
> > ~~~
> > {: .r}
> > 
> > <img src="../fig/rmd-06-ch4a-sol-1.png" title="plot of chunk ch4a-sol" alt="plot of chunk ch4a-sol" style="display: block; margin: auto;" />
> {: .solution}
{: .challenge}


> ## Challenge 4b
>
> Modify your solution to Challenge 4a so that the
> points are now a different shape and are colored by continent with new
> trendlines.  Hint: The color argument can be used inside the aesthetic.
>
> > ## Solution to challenge 4b
> >
> > Modify Challenge 4 so that the points are now a different shape and are
> > colored by continent with new trendlines.
> >
> > Hint: The color argument can be used inside the aesthetic.
> >
> >
> >~~~
> > gapminder %>% ggplot(aes(x = gdpPercap, y = lifeExp, color = continent)) +
> > geom_point(size = 3, shape = 17) + scale_x_log10() +
> > geom_smooth(method = "lm", size = 1.5)
> >~~~
> >{: .r}
> >
> ><img src="../fig/rmd-06-ch4b-sol-1.png" title="plot of chunk ch4b-sol" alt="plot of chunk ch4b-sol" style="display: block; margin: auto;" />
> {: .solution}
{: .challenge}


## Multi-panel figures

Earlier we visualized the change in life expectancy over time across all
countries in one plot. Alternatively, we can split this out over multiple panels
by adding a layer of **facet** panels. 



~~~
gapminder %>% ggplot(aes(x = year, y = lifeExp, group = country)) +
  geom_line() + facet_wrap( ~ continent)
~~~
{: .r}

<img src="../fig/rmd-06-unnamed-chunk-4-1.png" title="plot of chunk unnamed-chunk-4" alt="plot of chunk unnamed-chunk-4" style="display: block; margin: auto;" />

The `facet_wrap` layer took a "formula" as its argument, denoted by the tilde
(~). This tells R to draw a panel for each unique value in the country column
of the gapminder dataset.

> ## Challenge 5
> 
> Modify the plot so that each country has its own colour.   Although we would 
> usually use a legend when plotting multiple series, you will find that this takes
> up all of the plotting space; hide the legend.
> 
> Hint: There is a cheatsheet for `ggplot2` included with Rstudio
> 
> > ## Solution to challenge 5
> > We can colour each country's line using the `colour` aesthetic:
> > 
> > 
> > ~~~
> >  gapminder %>% ggplot(aes(x = year, y = lifeExp, colour = country)) +
> >  geom_line() + facet_wrap( ~ continent)
> > ~~~
> > {: .r}
> >
> > This will produce a plot that only shows (part of) the legend.
> > we can hide the legend using `guides(colour = "none")`
> >
> > 
> > ~~~
> >  gapminder %>% ggplot(aes(x = year, y = lifeExp, colour = country)) +
> >  geom_line() + facet_wrap( ~ continent) + guides(colour = "none")
> > ~~~
> > {: .r}
> > 
> > <img src="../fig/rmd-06-noLegend-1.png" title="plot of chunk noLegend" alt="plot of chunk noLegend" style="display: block; margin: auto;" />
> {: .solution}
{: .challenge}


> ## Challenge 6
>
> Create a density plot of GDP per capita, filled by continent.
>
> Advanced:
>
>  - Transform the x axis to better visualise the data spread.
>  - Add a facet layer to panel the density plots by year.
>
> > ## Solution to challenge 6
> >
> > Create a density plot of GDP per capita, filled by continent.
> >
> > Advanced:
> >  - Transform the x axis to better visualise the data spread.
> >  - Add a facet layer to panel the density plots by year.
> >
> > 
> > ~~~
> >  gapminder %>%  ggplot(aes(x = gdpPercap, fill = continent)) +
> >  geom_density(alpha = 0.6) + facet_wrap( ~ year) + scale_x_log10()
> > ~~~
> > {: .r}
> > 
> > <img src="../fig/rmd-06-ch5-sol-1.png" title="plot of chunk ch5-sol" alt="plot of chunk ch5-sol" style="display: block; margin: auto;" />
> {: .solution}
{: .challenge}

The figure we produced in challenge 6 is visually rather complicated; it's difficult
to get a feel for how the different continents' GDPs are changing over time.  Let's focus on 
just a couple of continents.  

When we want to start subsetting and mutating the data before plotting, the usefulness of
"piped" data-analysis becomes apparent; we can perform our data transformations and then
send the result to `ggplot2` without making an intermediate data set.


~~~
gapminder %>% 
  filter(continent %in% c("Europe", "Africa")) %>% 
  ggplot(aes(x = gdpPercap, fill = continent)) +
  facet_wrap( ~ year) + 
  scale_x_log10() + 
  geom_density(alpha = 0.6) 
~~~
{: .r}

<img src="../fig/rmd-06-unnamed-chunk-5-1.png" title="plot of chunk unnamed-chunk-5" alt="plot of chunk unnamed-chunk-5" style="display: block; margin: auto;" />



## Modifying text

To clean this figure up for a publication we need to change some of the text
elements.  For example the axis labels should be "human readable" rather than 
the variable name from the data-set.  We may also wish to change the text size, etc.

We can do this by adding a couple of different layers. The **theme** layer
controls the axis text, and overall text size. Labels for the axes, plot 
title and any legend can be set using the `labs` function. Legend titles
are set using the same names we used in the `aes` specification. Thus below
the colour legend title is set using `color = "Continent"`, while the title 
of a fill legend would be set using `fill = "MyTitle"`. 


~~~
gapminder %>% 
  filter(continent %in% c("Europe", "Africa")) %>% 
  ggplot(aes(x = gdpPercap, fill = continent)) +
  facet_wrap( ~ year) + 
  scale_x_log10() + 
  geom_density(alpha = 0.6) +
  labs(
    x = "GDP per capita (log scale)",              # x axis title
    y = "Density",   # y axis title
    title = "Figure 1",      # main title of figure
    fill = "Continent"      # title of legend
  ) 
~~~
{: .r}

<img src="../fig/rmd-06-theme-1.png" title="plot of chunk theme" alt="plot of chunk theme" style="display: block; margin: auto;" />


 RStudio provides a really useful [cheat sheet][cheat] of the different layers available, and more
extensive documentation is available on the [ggplot2 website][ggplot-doc].

[cheat]: http://www.rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf
[ggplot-doc]: http://docs.ggplot2.org/current/

## Saving plots

Having produced a plot, we can save it, or copy it to the clipboard using the "Export" command at the top of RStudio's plot window.

It's a better idea to save your plots as part of your scripts; this way if you modify your analysis code, you _know_ the plot will reflect the results of the code.  If you manually save the plot, you have to remember to do this after running the script.  

> ## Reproducibile research
> 
> You still have to remember to update the figure in your report, thesis or paper manuscript. Although we don't have time to consider it in this course, we can actually take this one step further, and write our reports, presentations or even web pages in R.  This way the text, analysis and figures are all produced from the same source.  This is referred to as reproducible research.
{: .callout }


We can save the most recently produced ggplot using the `ggsave()` function:


~~~
ggsave("plots/myplot.png")
ggsave("plots/myplot.pdf", width = 20, height = 20, units = "cm")
~~~
{: .r}

The help for the `ggsave()` function lists the image formats that are available, as well as the options for setting the
resolution and size of the saved image.


## ggplot themes and extensions

ggplot is very flexible, and its capabilities can be extended.  

The _theme_ of a plot affects the background, axes etc.  The [ggplot2 themes package](https://cran.r-project.org/web/packages/ggthemes/) contains many useful (and not so useful) themes we can apply to our data.  The [cowplot package](https://cran.r-project.org/web/packages/cowplot/index.html) makes it easy to plot subplots, and to overlay plots within plots.  

The [ggplot2 exensions](http://www.ggplot2-exts.org/) pages lists R packages that can extend its capabilities. If you have a specialised plotting need (for example plotting ROC curves, survival data, or time series) there are packages that will allow you to make these plots with minimal effort. [The top 50 ggplot2 visualisations page](http://r-statistics.co/Top50-Ggplot2-Visualizations-MasterList-R-Code.html) provides examples (with full code) of almost any type of graph you might want to make. 

As an example of how easy it can be to extend ggplot, we will use the `ggridges` plot to produce a stacked density plot, to better visualise the previous figure:


~~~
library(ggridges)

gapminder %>% 
  filter(continent %in% c("Europe", "Africa")) %>% 
  ggplot(aes(x = gdpPercap, y = factor(year), fill = continent)) +
  scale_x_log10() + 
  geom_density_ridges(alpha = 0.6) 
~~~
{: .r}



~~~
Picking joint bandwidth of 0.128
~~~
{: .output}

<img src="../fig/rmd-06-ridgesplot-1.png" title="plot of chunk ridgesplot" alt="plot of chunk ridgesplot" style="display: block; margin: auto;" />
