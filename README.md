# gganimate
Intro to gganimate Package

## Introduction
Data visualizations play an important role in helping us understand communicate data effectively. Our brains are so good a processing visual information, a good visualization can help people identify trends and relationships more easily than with plain data. This R Markdown document seeks to enhance the reader's abilit to create powerful and memorable visualizations by demonstrating how to create animated charts using the **`gganimate`** package in R. **`gganimate`** is a powerful graphics package built on top of the **`ggplot2`** package to create animated graphs. It provides a variety of additional functions that can be added plots that can customize how the visualizations change over time. 
<br>
<br>
In addition to some general familiarity with the **`ggplot2`** package that **`gganimate`** extends, it is important to understand how the concepts of **Layers** and **Transitions** function in the package. The **`gganimate`** animations are made up of layers with each layer containing a different type of data (e.g., data points, lines, or text). These **layers** can be leveraged to create vibrant illustrations to show how different datasets interact and change over time. To create the animations, **`gganimate`** provides a variety of **transitions** that can be employed to create simple and complex visual effects to transform static plots and graphs into dynamic and compelling visualizations. 
<br>

In this demonstration, we will create two animated charts to illustrate a few of the essential functions including: 
<br>
<br>
 *    **`transition_*()`** specifies the distribution of data and its relationship to itself across different states and time.
<br>
  *   **`view_*()`** specifies the scale and position of data points and how they should change over the course of the animation. 
<br>
  *   **`shadow_*()`** specifies how prior data points should be visually represented across different states and times. 
<br>
  *   **`enter_*()`/`exit_*()`** specifies how new data is introduced into the visualization adn how old data is removed from the animation. 



## Load Packages

```{r, echo=FALSE}
library(ggplot2)
library(gganimate)
```


```{r}
# Load diamonds dataset
data(diamonds)
```

```{r, echo=FALSE, results='asis'}
knitr::kable(head(diamonds, 5))
```

## Static Bar Chart
First, we will create a static bar chart using the **`diamonds`** dataset from the **`ggplot2`** package.The graph will plot the levels of clarity of the diamonds in the dataset for each type of cut (ranging from: Fair, Good, Very Good, Premium, Ideal).

```{r}

# Create static bar chart
bar_chart <- ggplot(diamonds, aes(x = cut, fill = clarity)) +
  geom_bar() +
  labs(title = "Static Bar Chart", x = "Cut", y = "Count") 
bar_chart
```

## Animated Bar Chart
To add animation to our chart, we are going to add an additional layer using the **`transition_states()`** function to our plot. **The `transitition_states()`** function will split the data into subsets based on the variable passed to it ("clarity" in this example) and calculate the intermediary data states for the animation. 
<br>
After adding our transition layer, we will pass our animated object to the **`animate()`** function to render the animation in our document. 
```{r pressure}
# Add transition_state animation
animated_bar_chart <- bar_chart +
  transition_states(clarity, transition_length = 2, state_length = 1)+ 
  labs(title = "Animated Bar Chart", x = "Cut", y = "Count", 
       subtitle = "Clarity: {closest_state}") #Creating a dynamic Label: the {closest_state}
                                              # object will pass the current transition that will 
                                              # pass the current transition state as the text for the subtitle

# Render animated chart
animate(animated_bar_chart)
```

### It Works! Adding Additional Effects
Now that we have a working animation, we can look into some other functions to polish how our data transforms. The **`enter_*()`/`exit_*()`** functions control what happens to the data that doesn't persist in the visualization. 
<br>
There are several built-in effects such as:  
   `enter/exit_fade()` <br>
   `enter/exit_grow()` <br>
   `enter/exit_recolor()` <br>
   `enter/exit_drift() ` 

or you can specify your own transformations using:  **`enter/exit_manual()`**.

```{r}
# Add enter_fade()/exit_fade() for a smoother effect 
animated_bar_chart_fade <- animated_bar_chart +
  enter_fade() +
  exit_fade()

# Render animated chart
animate(animated_bar_chart_fade)
```

## Static Line Chart
To test some additional features, let's create an animated line chart to use implement some additional functionality using the **`airquality`** dataset and creating an initial static chart as before.

```{r}
# Load airquality dataset
data(airquality)
```

```{r, echo=FALSE, results='asis'}
knitr::kable(head(airquality, 5))
```

```{r}
# Create static line chart
line_chart <- ggplot(airquality, aes(Day, Temp, group = Month, color = factor(Month))) +
  geom_line() +
  labs(title = "Static Line Chart", subtitle = "Boring", x = "Day of Month", y = "Temperature") 
  
line_chart
```

## Animated Line Chart 
As with our bar chart, we will add the **`transition_reveal()`** function to our chart which will, in this case, render our data gradually and illustrate how our plots change over time.  

```{r}
# Add transition_reveal animation
animated_line_chart <- line_chart +
  labs(title = "Animated Line Chart", subtitle = "Exciting", x = "Day of Month", y = "Temperature")+
  transition_reveal(Day)

# Render animated chart
animate(animated_line_chart)


```

### Changing the Perspective
Using the **`view_*()`** functions allows you to define how the positional scales should change along the animation and allow you to follow specific data or groups of data in a graphic. Below, I have added the `view_follow()` function to scale the animation in a manner that follows the data points as they are revealed along the time series. 

```{r}
# Adding Zoom
animated_line_chart_zoom <- animated_line_chart +
  labs(title = "Animated Line Chart", subtitle = "With Zoom", x = "Day of Month", y = "Temperature")+
  view_follow()
  
 

# Render animated chart
animate(animated_line_chart_zoom)

```

### Adding Shadows to Data 
For the final illustration, I have converted the line chart to a scatter plot, but maintained the same time-series transition along the `Day` variable. The plot movement on this graph will be emphasized with the `shadow_wake()` function that is meant to draw a small wake after a data point by showing the most recent frames up to the current point. 
<br>
You can gradually diminish the size of the preceding data in the wake (as is shown here with the `wake_length=0.1` proportion argument) and/or the opacity of the shadow (using the `alpha=` argument).
```{r}

# Changing to scatter plot / adding shadow_wake effect
scatter_chart_shadow <- ggplot(airquality, aes(Day, Temp, group = Month, color = factor(Month)))+
  geom_point() +
  labs(title = "Animated Scatter Chart", x = "Day of Month", y = "Temperature")+ 
  transition_time(Day) +
  shadow_wake(wake_length = 0.5, alpha = FALSE)

# Render animated chart
animate(scatter_chart_shadow )
```


## Summary & Resources
In this document, we have demonstrated how to create animated charts using the **`gganimate`** package in R, and highlighted some of the essential functionality of the package as we created dynamic a dynamic bar graph, line graph, and scatter plot.
<br>
<br>
You can learn more about **`gganimate`** features or the **`ggplot2`** package it is built on in their reference manuals at cran.r-project.org: 
<br>
[gganimate](https://cran.r-project.org/web/packages/gganimate/gganimate.pdf) 
<br>
[ggplot2](https://cran.r-project.org/web/packages/ggplot2/ggplot2.pdf)
<br>
I hope this can serve as a starting point for you to explore more advanced animation techniques and customization options in the future. Happy coding!

### Works Cited
This code through references and cites the following sources:
<br>
<br>
*Package 'gganimate': A Grammar of Animated Graphics*, Version 1.0.8, accessed: October 1, 2023 [Online] Available: (https://cran.r-project.org/web/packages/gganimate/gganimate.pdf) 
<br>
<br>
*Package 'ggplot2': Create Elegant Data Visualizations Using the Grammar of Graphics*. Version 3.4.3, accessed: October 1, 2023 [Online] Available: (https://cran.r-project.org/web/packages/ggplot2/ggplot2.pdf)
<br>
<br>
*'gganimate'*, https://gganimate.com/index.html, 1 October, 2023.
