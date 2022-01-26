---
layout: post
title: Creating Waffle Charts in Tableau
subtitle: I sincerely say that I am, in fact, not waffling.
cover-img: /assets/img/waffle.jpeg
thumbnail-img: /assets/img/waffle.jpeg
share-img: /assets/img/waffle.jpeg
tags: [Football, Tableau, Data Analysis, Tutorial]
---

Hey there! This tutorial is attempting to take its readers through every step of the process in creating waffle charts in tableau using football data. If you are familiar with the sports analytics arena, you might have seen these quite frequently.

What does it do?

A waffle chart shows the completion percentage, or the components of a whole in % form. It is basically a fancier pie chart, only less cluttered.

![image](https://user-images.githubusercontent.com/87293901/150641590-f5de8ccf-37af-4db0-a942-b8bd22ee2910.png){: .mx-auto.d-block :}

Now, some of you may ask me, Harsh, this looks nothing like a waffle. More like a large square with smaller squares inside it. Why in god’s name is it called a waffle chart?

Well, yes, but also no. As it turns out, you learn something new everyday, and what I learnt today was that square waffles are a very real thing. You can think of the name serving a dual purpose, it sort of looks like a waffle, but in some ways isn’t a waffle. The name waffle chart itself is waffle.
(Okay, I’ve realized I’m wasting your time, so I’ll get straight to the tutorial)

Let’s get waffling!!

### Getting the data 

The data for this visualization is from Football Reference. For this tutorial, I shall be using the Goal-Creating Actions (GCA) for teams in the German Bundesliga.

Once you have the data opened up in Excel, you can go ahead and trim out all the excess and (for the sake of this tutorial) unnecessary data. Your data should now look like this.

![image](https://user-images.githubusercontent.com/87293901/150641787-c731f971-2b29-494c-99ce-74c8d24f175f.png){: .mx-auto.d-block :}

(Note: I have copy pasted this sheet onto a new worksheet before saving it, because I did not extract the data in the form of a CSV. The dataset is saved in the form of an excel workbook named “Bundesliga GCA 3.0”)

### Tableau Data Prep

Open Tableau and load up your data. What you have to do now, is select all the columns except the column named “Squads” and “GCA”. If you’re on Windows, you can simply Hold down the ctrl key and select the columns.

![image](https://user-images.githubusercontent.com/87293901/150641842-53d4c82f-b2d0-41a5-ad8c-e28da9bd470c.png){: .mx-auto.d-block :}

You will see that I have right-clicked and chosen the pivot option. Tableau will now provide you with the pivot fields that you require to make the waffle chart. Your table will now look like this.

![image](https://user-images.githubusercontent.com/87293901/150641847-a90062bc-822f-4e53-bb17-f2b5c11e7211.png){: .mx-auto.d-block :}

Rename “Pivot Field Names” to “GCA Types” and “Pivot Field Values” to “Values”.

Cool? Cool. Now open up the Worksheet. Now you want to open up a new data source on the same sheet. The new dataset that you will enter is the template for your waffle chart. Click on the link below to download it.

[Waffle Template](https://docs.google.com/spreadsheets/d/e/2PACX-1vThwvmaCKGZy4OlhLNFwRYYL_3yMM-95h2M_zWVgFeL9h8WgGK-_I8gLimltuBnt-NRAQeQL4ol0ark/pub?output=ods)

You can make this yourself (quite easy to do) or you can copy paste these values to a new Excel worksheet and save it.

Okay. Convert ‘Rows’ and ‘Columns’ into Dimensions and drag both of them to their respective drop-boxes. It is recommended you right-click on ‘Rows’ and sort it in the descending order. Change the mark type to ‘Square’ and drag on the table from the bottom and right-hand side to enlarge the viz. Adjust the size of the squares through the size slider. Hide both the headers. You should also click on borders under Format and change Pane under ‘Row Divider’ to None. All in all it should look like this.

![image](https://user-images.githubusercontent.com/87293901/150641852-a6914272-942d-439b-b708-d47b881c6e4d.png){: .mx-auto.d-block :}

### Calculated Fields

Click on the original data source (Bundesliga GCA 3.0) and start creating calculated fields.

I’ve named my calculated fields as GCA 1 through to 6 (6 fields in total).

```
SUM(IF [GCA Types] = "Pass Live" THEN [Values] END) / SUM([Values])
```

What we are doing here is creating the value ratios of all the categories available to us.

Use this as an example to create the other 6 calculated fields, keeping everything the same apart from the section within the quotation marks which while include **“Pass Dead”, “Sh”, “Def”, “Drib”, “Fld” and “Blanks”.**

Reference Photo.

![image](https://user-images.githubusercontent.com/87293901/150641883-8320806b-3b9f-42ad-a719-5f1f8afd4779.png){: .mx-auto.d-block :}

Great. Now switch over to the “Waffle Template” dataset and create a new calculated field. I have named this field “Final”. Copy paste this onto there.

```
IF AVG([Percentage]) <= [Sheet1 (Bundesliga GCA 3.0)].[GCA 1] THEN "Pass Live"
ELSEIF AVG([Percentage]) <= [Sheet1 (Bundesliga GCA 3.0)].[GCA 1] + [Sheet1 (Bundesliga GCA 3.0)].[GCA 2] THEN "Pass Dead"
ELSEIF AVG([Percentage]) <= [Sheet1 (Bundesliga GCA 3.0)].[GCA 1] + [Sheet1 (Bundesliga GCA 3.0)].[GCA 2] + [Sheet1 (Bundesliga GCA 3.0)].[GCA 3] THEN "Sh"
ELSEIF AVG([Percentage]) <= [Sheet1 (Bundesliga GCA 3.0)].[GCA 1] + [Sheet1 (Bundesliga GCA 3.0)].[GCA 2] + [Sheet1 (Bundesliga GCA 3.0)].[GCA 3] + [Sheet1 (Bundesliga GCA 3.0)].[GCA 4] THEN "Def"
ELSEIF AVG([Percentage]) <= [Sheet1 (Bundesliga GCA 3.0)].[GCA 1] + [Sheet1 (Bundesliga GCA 3.0)].[GCA 2] + [Sheet1 (Bundesliga GCA 3.0)].[GCA 3] + [Sheet1 (Bundesliga GCA 3.0)].[GCA 4] + [Sheet1 (Bundesliga GCA 3.0)].[GCA 5] THEN "Drib"
ELSEIF AVG([Percentage]) <= [Sheet1 (Bundesliga GCA 3.0)].[GCA 1] + [Sheet1 (Bundesliga GCA 3.0)].[GCA 2] + [Sheet1 (Bundesliga GCA 3.0)].[GCA 3] + [Sheet1 (Bundesliga GCA 3.0)].[GCA 4] + [Sheet1 (Bundesliga GCA 3.0)].[GCA 5] + [Sheet1 (Bundesliga GCA 3.0)].[GCA 6] THEN "Fld"
ELSE "Fld" END
```

Now, drag Final to “Color” under Marks and Drag squad to filters. Select the squad of your choice and THAT IS IT!!

![image](https://user-images.githubusercontent.com/87293901/150641908-a915061f-5205-4c24-9457-9564fa2c4e23.png){: .mx-auto.d-block :}

Adjust the Colours and the background to your liking and your waffle chart is good to go.

### Version 2.0

Now, these graphs are great for showing the percentage of each category in proportion. But, what if you wanted to show the data disproportionately? Maybe I’m not wording this as well as I could but what I mean to say is, if you wanted to show the top GCA teams, then you might need a different type of graph. Something like this.

![image](https://user-images.githubusercontent.com/87293901/150641923-b23c1e02-0fe1-4202-99e1-420f9e58e77e.png){: .mx-auto.d-block :}

To do this, you need to make a small modification in the early steps of this process. This brings us back to our Excel sheet. Create a new Colum called Blanks. In this column, you need to calculate the highest GCA — Every other teams GCA.

![image](https://user-images.githubusercontent.com/87293901/150641929-cccd1eea-5497-4e8d-9cf2-34e601b28d21.png){: .mx-auto.d-block :}

We see that Bayern Munich has the highest GCA in the league. Thus we subtract every other teams GCA from it.

We then do all the same steps done before, until the part to the creation of calculated fields, where we now will make one extra field namely “GCA 7” with it referencing the category Blanks, with the same formula we had used before.

Then in the next formula we add another line with the same cumulative pattern going up to GCA 7, and now end with
```
ELSE “Blanks” END
```

We then edit the colours of the chart so that the colour of the legend “Blanks” is the same as the background, hiding it, and making it, well blank.

And that’s the whole thing.

***

*This tutorial references a guide written by “Recnac”. You can find it [here](https://www.pluralsight.com/guides/tableau-playbook-waffle-chart).*

*Thanks to Nandy for the help in my understanding of version 2.0.*
