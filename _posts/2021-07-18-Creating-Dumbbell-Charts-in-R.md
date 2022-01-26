---
layout: post
title: Creating Dumbbell Charts in R
subtitle: And otheR stuff
cover-img: /assets/img/dumb.jpeg
thumbnail-img: /assets/img/dumb.jpeg
share-img: /assets/img/dumb.jpeg
tags: [Football, Data Analytics, R]
---

This is the first tutorial I’m writing for R, in which I’m hoping to tackle a variety of topics, including creating “dumbbell charts” and other stuff in R that can be used for any visualization that you hope to make using ggplot2.

### What is R?

R is a programming language which is used (primarily) by statisticians for statistical (duh) and graphical purposes. 
It can create powerful plots which are highly customizable. 
To get started with R, [download the base version of it](https://cran.r-project.org/bin/windows/base/) from the internet. Now, the usage of the base version of R is quite cumbersome and tedious, thus most people make use of IDE’s (Integrated Development Environment’s) which are wrappers around base R, that make the usage of the language easier. RStudio is by far the most popular of these IDE’s and you can [download it here](https://www.rstudio.com/products/rstudio/download/).

### What are Dumbbell Charts?

Next up, lets talk about what “dumbbell charts” are and what they are useful for. A dumbbell plot (can also be called a barbell plot) is ideal for illustrating change and comparing the distance between two groups of data points. It looks like a dumbbell, hence the name.

![image](https://user-images.githubusercontent.com/87293901/150669958-07283dad-e967-4508-93bd-bc8f2b082eec.png){: .mx-auto.d-block :}

In the sphere of football analytics, it has various uses, including showing the differences between team performances home and away, which is what we will be using as an example in this tutorial.

Now that we have the basics out of the way, let’s go about making the chart.

### Getting the Data

Once you have opened up RStudio, you will see something like this

![image](https://user-images.githubusercontent.com/87293901/150669975-82454d48-d246-41ac-93cd-61dc752061ee.png){: .mx-auto.d-block :}

*Cool tip: In case you wanted to change the appearance of your RStudio window (like I have), you can do so by going to Tools > Global Options > Appearance. You can change the layout to your liking.*

Now, the data for the chart is from FBref. You can extract the data from the website directly in the form of an excel sheet, before reading the data into R, but that is kinda tedious, and unnecessary. Instead, we can use specific packages that will get the data into RStudio for us. All we need to do is write the code. The package in this case is [WorldFootballR created by Jason Zivkovic](https://jaseziv.github.io/worldfootballR/). Let’s get to the coding part (pew pew!!) (sorry wont do that again).

The Rscript (where you write your code) is done on the pane on the top right.

```r
install.packages("devtools")
remotes::install_github("JaseZiv/worldfootballR")
library(worldfootballR)
```

*When writing code in R, the usage of “” is very important. The parts within the quotation marks will be highlighted differently to the other parts of the code. Make sure not to miss out on writing any (or don’t even write any extra ones) (yeah, I know, its confusing that way).*

Next up, to extract the data from FBref, we go

```r
league_table <- get_season_team_stats(country = "FRA", gender = "M", season_end_year = "2021", tier = "1st", stat_type = "league_table_home_away")
```

Now, this part is pretty self explanatory. The “<-” is important in getting the data into your environment, which is to the right hand side of where you write your code.

You can also check out the package to see how you can get the data for other metrics as well. It is all well specified and easy to use.

### Other Packages

Now, the WorldFootballR package is for getting the data, but there are several other packages that shall be used for the creation of plots in R. They go as follows

```r
install.packages("ggplot2")
install.packages("tidyverse")
installed.packages("ggtext")

library(tidyverse)
library(ggplot2)
library(ggtext)
library(dplyr)
library(ggalt)
```

Let me go over what each of these packages and what they do

ggplot2 :- Creating graphics.
tidyverse :- A collection of packages that include design and grammar.
dplyr :- Manipulation of datasets. Included in tidyverse.
ggtext :- Text stuff.
ggalt :- Includes different functions such as the geom() set of functions. Comes within the ggplot2 package.

### Data Manipulation

Okay, so now that we have got the data, let’s manipulate it a bit.

```r
data1 <- league_table[, c(5, 14, 19, 27, 32)]

data1$xGDiff_Home <- data1$GD_Home - data1$xGD_Home
data1$xGDiff_Away <- data1$GD_Away - data1$xGD_Away
```

So what I’ve done here is select specific columns and rows from the dataset “league_table” and called it “data1”. In the next two lines, I have created two new columns (the columns that we shall use to create the dumbbell plot). You can also add another line to have only the columns that we need.

```r
data1 <- data1[, c(1, 6, 7)]
```

I’ve filtered down the dataset “data1” to the same name, to avoid confusion. Your dataset should now look like this

![image](https://user-images.githubusercontent.com/87293901/150670053-2328c217-7109-458c-bd33-4a8b48495589.png){: .mx-auto.d-block :}

*Tip for later use: When selecting specific rows and columns, we should note that the code is written in the form of :- dataset[rows, columns].*

For example, to select the first two rows and all columns of a dataset, we use

```r
dataset[c(1, 2),] #example
```

*Another tip for use: A ‘#’ before anything in R means that R will not read it as part of the code. Thus, it can be used to explain stuff in the script itself so you don’t forget steps.*

And finally let’s add another line to transform the column listing club names to characters

```r
data1$Squad <- factor(data1$Squad, levels=as.character(data1$Squad))
```

### Plotting

The main event. Let’s begin. Enter this code.

```r
ggplot(data1, aes(x=xGDiff_Home, xend=xGDiff_Away, y=Squad)) + 
  geom_dumbbell()
```

And lo and behold! Its a dumbbell chart!

![image](https://user-images.githubusercontent.com/87293901/150670120-8f4d07ae-3fa8-4f8f-b256-3033218d47b6.png){: .mx-auto.d-block :}

But really, this is just the bare bones of the chart and there is a lot of work yet to be done. The next part of code is long, and it is applicable for the creation of dumbbell charts with any datasets. Lets go step by step.

```r
p1 <- ggplot(data1, aes(x=xGDiff_Home, xend=xGDiff_Away, y=Squad))
```

This is the same line as before. It concerns stuff that make the majority of out plot. Now, if your wondering why I have defined this as ‘p1’, we will get to that soon. Next, we take care of the bar and circles that give our plot it’s name.

```r
p2 <- geom_segment(aes(x=xGDiff_Home, 
                   xend=xGDiff_Away, 
                   y=Squad, 
                   yend=Squad), 
               color="#b2b2b2", size=3)
p3 <- geom_dumbbell(color="light blue",         
                size_x=10,                  #size of circle1
                size_xend =10,              #size of circle2
                colour_x="#238b45",         #colour of circle1
                colour_xend = "#fe9929")    #colour of cirlce2
```

Next, the title and the subtitle.

```r
TS <- labs(x=NULL, y=NULL, 
       title="<b style='color:#ffffff'>Overperformance or Underperformance ? (2020/21)</b>", 
       subtitle="<b style='color:#ffffff'>How have Ligue 1 teams performed on their Goal Difference</b> 
<b style='color:#238b45'>Home</b> <b style='color:#ffffff'>&</b> 
<b style='color:#fe9929'>Away</b> <b style='color:#ffffff'>?</b>")
TS1 <- theme_classic(base_size = 24) +
  theme(plot.title = element_markdown(lineheight = 1.1),
        plot.subtitle = element_markdown(lineheight = 1.1))
```

Let’s add a caption to the chart as well.

```r
TS2 <- labs(fill="",                                                                       
       caption = "Data from FBref via StatsBomb, WorldFootballR
       Created by Harsh Krishna")
```
Let’s put together all that we have so far.

```r
plotlight <- p1 + p2 + p3 + TS + TS1
plotlight
```

Lets take out an image of it. Click on ‘export’ on the panel above where our plot lies and set the dimensions as 2000x1000.

![image](https://user-images.githubusercontent.com/87293901/150670170-f7f9155b-abfc-42c7-a356-d950597a6c57.png){: .mx-auto.d-block :}

Check out the image. You might notice that the title is not visible at all. That is because I prefer my plots to be of a dark theme. I have already coded the title and subtitle of the plot to be white in colour. Lets take a look at that specific section of code in more detail.

```r
labs(x=NULL, y=NULL, 
       title="<b style='color:#ffffff'>Overperformance or Underperformance ? (2020/21)</b>", 
       subtitle="<b style='color:#ffffff'>How have Ligue 1 teams performed on their Goal Difference</b> 
<b style='color:#238b45'>Home</b> <b style='color:#ffffff'>&</b> 
<b style='color:#fe9929'>Away</b> <b style='color:#ffffff'>?</b>") +
  theme_classic(base_size = 24) +
  theme(plot.title = element_markdown(lineheight = 1.1),
        plot.subtitle = element_markdown(lineheight = 1.1)) +
  labs(fill="",                                                                       
       caption = "Data from FBref via StatsBomb, WorldFootballR
       Created by Harsh Krishna")
```

Any text can have its colour altered by putting it between “”<b style=’color:#ffffff’>” and “</b>”. If you like you plots with a white background, you can go ahead remove only that specific section of code (except for ‘Home’ and ‘Away’).

For those of you that like a plot with a dark background, let’s go forward. To change the plot and panel background colour, as well as change the caption text colour, we do

```r
themes1 <- theme(plot.background = element_rect(fill = "#000000", colour = "#000000")) +
          theme(panel.background = element_rect(fill = "#000000", colour = "#000000")) +
          theme(plot.caption = element_text(color = "white"))
```

Let’s do some stuff for the axes.

```r
themes2 <- labs(x = "GD - xGD")
themes3 <- theme(axis.title.x = element_text(colour = "#ffffff")) 
```

```r
themes4 <- theme(axis.text.x = element_text(colour = "#ffffff"),
                 axis.text.y = element_text(colour = "#ffffff"))
```

And finally, let’s add some gridlines and axis lines.

```r
themes5 <- theme(panel.grid.major = element_line(colour="grey", size = (0.1)), panel.grid.minor = element_blank()) +
          theme(panel.grid.major.y = element_blank()) + 
          theme(axis.line = element_line(size = 0.8, colour = "#ffffff"))
```

Let’s bring it all together once again.

```r
DBplot <- p1 + p2 + p3 + TS + TS1 + TS2 + themes1 + themes2 + themes3 + themes4 + themes5
DBplot
```

Now, our plot looks complete.

![image](https://user-images.githubusercontent.com/87293901/150670233-a08713f8-76e7-4e44-af0f-de41734c4256.png){: .mx-auto.d-block :}

Buuuuut, maybe just add one teeny-tine last thing. We can highlight a section of the panel (less than 0) as the section where teams that underperform lie. We want it to be red in colour (some might argue underperformance might be good, but whatever dude), and we also want it to be kinda transparent. Lets do this using a function called geom_rect().

```r
DBplot + geom_rect(aes(xmin = 0, xmax = -Inf, ymin = -Inf, ymax = Inf), 
fill = "red", alpha = 0.01)
```

You can play around with this and see what look suits your plot. ‘alpha’ is for setting the transparency. ‘Inf’ stands for infinity, and make careful use of the minus sign before it.

Phew! It looks good, EXCEPT (I’m so sorry, just one last ride, the fast and furious franchise are furious at me for stealing their flow, huh?) the rectangle appears above the plot. Now, I’m sure there is some fancy function to fix this, but I don’t know of that and truly speaking, I don’t care. To fix, just add all the stuff you had defined beforehand, except for p1. (now you see why we defined all that stuff earlier, it’s all just to make our code cleaner).

```r
DBplot + geom_rect(aes(xmin = 0, xmax = -Inf, ymin = -Inf, ymax = Inf),
fill = "red", alpha = 0.01) + 
  p2 + p3 + TS + TS1 + TS2 + themes1 + themes2 + themes3 + themes4 + themes5
```

And believe it or not, we are done!

All of this will lead to this final plot.

![image](https://user-images.githubusercontent.com/87293901/150670263-56c0075e-edbc-41dd-8036-fa2a0a9a0048.png){: .mx-auto.d-block :}

To check out the code in its full glory, you can click [here](https://github.com/harshkrishna17/R-Code/blob/main/Dumbell%20Chart.R).

***

*If you have any doubts or questions, you can reach out to me on my twitter account.*

*Hope you found this helpful!*

*Harsh.*
