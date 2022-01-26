---
layout: post
title: Some Useful R Tricks
subtitle: Basic data manipulation techniques using R
cover-img: /assets/img/dplyr.png
thumbnail-img: /assets/img/dplyr.png
share-img: /assets/img/dplyr.png
tags: [Data Analysis, R]
---

R is one of the most popular programming languages for statistical analysis. It is especially useful for manipulation of datasets and has excellent libraries for the same, along with a large range of graphing libraries that allow for data visualizations to be made with high customizability.

In this tutorial, I’ll go through some basic techniques which I hope can be greatly useful for beginners with the language. These methods will help you to reshape datasets to the form you want, and we shall also go through some basic visualizations that you can make with R package [ggshakeR](https://abhiamishra.github.io/ggshakeR/), made by Abhi A. Mishra.

All the code will be within the RStudio IDE and it is recommended that you download it after downloading Base R.

### Dataset

Let’s first load in our dataset. We shall be using data from Understat and the package which will help us in extracting the data will be worldfootballR by Jason Zivkovic. Let’s install and load the package with these lines.

```r
install.packages("devtools")
devtools::install_github("JaseZiv/worldfootballR")
library(worldfootballR) 
```

Now, to load in the dataset of all Premier League shots from the 2021/22 season, we write

```r
data <- understat_league_season_shots(league = "EPL", season_start_year = 2021)
```

### Tidyverse

The tidyverse is a collection of packages that make our job of data manipulation and plotting easier. The two packages within the tidyverse which we shall use extensively are dplyr and tidyr.

```r
install.packages("tidyverse")
library(tidyverse)
```

This concludes the packages part, for now. Let’s move onto working with the data.

### Filtering

Let’s first learn to filter a dataset by a specific column value.

```r
data1 <- data %>%
  filter(situation == "OpenPlay")
```

This piece of code will filter the data for only open play shots.

![image](https://user-images.githubusercontent.com/87293901/150677560-861fd83c-226e-43ca-ae9f-4fd04f76b3ef.png)

Keep in mind the I am calling the filtered data to a new data object so as to not disturb our original dataset. The “%>%” is known as a pipe operator, which is what tells R to apply the certain function on the specific dataset that is called.

Now, what if you wanted to filter for two or more variables at the same time?

```r
data2 <- data %>%
  filter(situation == "OpenPlay" | situation == "FromCorner")
```

What about filtering with a not clause? If you wanted only non-penalty goals, you’d write

```r
data3 <- data %>%
  filter(!situation == "Penalty")
```

Note that the “==” operator is only for variables that are characters. You can also filter for numeric variables within the dataset by using some different operators such as

```r
data$minute <- as.numeric(data$minute)
data4 <- data %>%
  filter(minute >= 45)
data5 <- data %>%
  filter(minute <= 45)
data6 <- data %>%
  filter(minute > 20)
```

If you noticed, I first made sure that the class of the minute column was a numeric so that the filtering worked correctly. There are many other functions like it :- as.vector(), as.Date(), as.data.frame() etc.

You can check the class of a column or vector by running

```r
x <- 1 ## numeric
class(x)
```

The great thing about dplyr (tidyverse) is that you can add as many pipe operators (%>%) to continue filtering (also using different dplyr functions : soon to come) without breaks in the code. An example is the following where all second half open-play goals are filtered out from the dataset.

```r
data7 <- data %>%
  filter(minute >= 45) %>%
  filter(situation == "OpenPlay") %>%
  filter(result == "Goal")
```

### Some stuff on Columns

The mutate() function within dplyr allows you to create new columns which are resulted of calculations on already existing columns.

```r
data8 <- data %>%
  mutate(xGmean = mean(xG)) %>%
  mutate(meandiff = xGmean - xG)
```

This calculates the mean of the xG values and subtracts the singular values from the mean. The new columns would appear as such

![image](https://user-images.githubusercontent.com/87293901/150677623-84c893fe-d307-43e4-86df-55fdfb28e0f8.png)

A new column can be created without any calculations as well

```r
data9 <- data %>%
  mutate(newcol = 90) %>%
  mutate(newcol2 = "Hi")
```

![image](https://user-images.githubusercontent.com/87293901/150677635-c2a8a8ed-8a8d-43da-a676-ab9f965d616f.png)

You can even rename columns using the rename() function, such as

```r
data10 <- data %>%
  rename(xGoals = xG)
```

The new column name is to be used first.

We can also sort the values of a column in ascending or descending order.

```r
# Ascending
data11 <- data[order(as.numeric(data$xG),decreasing = FALSE),]
# Descending
data12 <- data[order(as.numeric(data$xG),decreasing = TRUE),]
```

### Data Cleaning

You might have noticed that this data frame does not have any missing values. This would not be the case with every dataset. Therefore, cleaning a dataset by either removing or replacing NA values is a very useful skill.

Let us first create some dummy NA values.

```r
df <- data ## to keep original dataset intact
df$minute[df$minute == 10] <- NA
```
This replaces all the rows with 10 in the minutes column with NA’s.

Next, Let’s learn to replace those values with 0’s as well removing rows that have missing values from the dataset completely.

```r
## Replace with 0's
df[is.na(df)] <- 0
```
or

```r
## Removing rows with NA's
df <- df %>%
  na.omit()
```

### Grouping

Another extremely useful function is group_by() which allows you to group column variables as well as the summarise() function that allows to operate a different function of a column based on the grouping.

```r
df1 <- data %>%
  group_by(player) %>%
  summarise(no = n(), xGsum = sum(xG))
```

![image](https://user-images.githubusercontent.com/87293901/150677699-3559b5fc-2587-41ed-9eed-c10386261753.png)

This creates a new data frame of players, their respective shots taken, and the sum of the xG of all their shots.

### Some concluding basic stuff

Some of the other stuff you can do which will be extremely useful are :-

1. Selecting specific rows and columns

```r
# dataframe <- dataframe[rows, columns]

## This selects the first 3 rows and all the columns
dataframe <- dataframe[c(1:3),] 
```

2. Binding together two or more datasets

```r
# Binding together by rows
df <- rbind(data1, data2) ## number of rows will increase
# Binding together by columns 
df <- cbind(data1, data2) ## number of columns will increase
```

A very important thing to make sure of is that while using rbind(), the number of columns of the datasets used within the function have to be the same. Similarly, while using cbind(), the number of rows have to be same.

You can check the number of rows and column of the dataset in the environment or you can run

```r
nrow(data) ## row
ncol(data) ## column 
```

3. Saving dataset as a csv file

```r
write.csv(df, "df.csv", row.names = FALSE)
```

### Plotting

Now that we have learn so many different methods of working with the data, lets make good use of it by plotting it in a graph. For this purpose we shall install and load in R analysis package ggshakeR (as mentioned before in the tutorial).

```r
devtools::install_github("abhiamishra/ggshakeR")
library(ggshakeR)
```

ggshakeR has 10 different plotting functions that allow you to make graphs with extreme ease and allow for considerable customization as well. For the purpose of this tutorial, we shall only be using two of those functions, plot_shot (Shot maps) and plot_trendline (xG vs xGA Trendlines).

Lets filter shots for their situation and result and plot it using plot_shot.

```r
df <- data %>%
  filter(!situation == "Penalty") %>%
  filter(player == "Mohamed Salah")
plot_shot(df)
```

![image](https://user-images.githubusercontent.com/87293901/150677768-11936412-89a7-4c05-9d0a-71799d10e709.png)

And here we are, Mo Salah’s non penalty shot map for the 2021/22 PL season. Feel free to go through the ggshakeR docs to learn more about how you can create different types of shot maps using the function.

Now let’s go onto out final part for the day, plotting a trendline using the plot_trendline function. While the function works directly on FBref (StatsBomb) data, we can manipulate the dataset we have right now to mimic a dataframe that requires the minimum number of columns for it to work.

The current dataset in out hands (3127 rows & 21 columns as of 22/11/2021) has to be converted to into something that looks like the one below.

![image](https://user-images.githubusercontent.com/87293901/150677779-dc183724-874a-44d4-8306-28ed93333eb9.png)

This is achievable using all the information that has been shared in this guide so far. If you’re feeling confident, give it a go! (consider it practice).

The solution is in the following box.

```r
df1 <- data %>%
  filter(h_a == "h") %>%
  filter(home_team == "Manchester United") %>%
  group_by(date) %>%
  summarise(sum(xG))
df2 <- data %>%
  filter(h_a == "a") %>%
  filter(away_team == "Manchester United") %>%
  group_by(date) %>%
  summarise(sum(xG))
df <- rbind(df1, df2)
df3 <- data %>%
  filter(h_a == "a") %>%
  filter(home_team == "Manchester United") %>%
  group_by(date) %>%
  summarise(sum(xG))
df4 <- data %>%
  filter(h_a == "h") %>%
  filter(away_team == "Manchester United") %>%
  group_by(date) %>%
  summarise(sum(xG))
dfa <- rbind(df3, df4)
dfa <- dfa[, 2]
dfa <- dfa %>%
  rename(Away_xG = 'sum(xG)')
df <- cbind(df, dfa)
df <- df %>%
  rename(Home_xG = 'sum(xG)') %>%
  rename(Date = date) %>%
  mutate(Home = "Manchester United") %>%
  mutate(Away = "Misc")
df$Date <- as.Date(df$Date)
```

Then plot the data.

```r
plot_trendline(data = df, team = "Manchester United",
               colour_xg = "#08519c", colour_xga = "#cb181d",
               roll_avg = 2, theme = "dark")
```

The resulting plot :

![image](https://user-images.githubusercontent.com/87293901/150677813-f4bb8126-f085-4c69-b884-7a4017d7ad7c.png)

That just about concludes this tutorial. I hope this helped you in starting out with the R language. All of the code used in this tutorial can be found in this [GitHub file](https://github.com/harshkrishna17/Miscellaneous-R/blob/main/R%20Tips%20and%20Tricks.R).

***

*You can reach out to me on my twitter account.*

*Harsh.*
