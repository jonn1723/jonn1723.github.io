Bellabeat Case Study Capstone Project:

The complete project with all code outputs can be found here: https://www.kaggle.com/code/jonathanliu9/bellabeat-case-study-with-r

This case study is an optional capstone project provided by the Google Data Analytics Certificate, which consists of 8-courses that provided me with the necessary skills needed to flouish as a data analyst. In this course, I learnt both hard skills working with Microsoft Excel, SQL, Tableau, and R programming, as well as soft skills including problem solving, critical thinking, and presentation skills. This capstone project helped consolidate what I've learnt and visits each phase of data analysis in-depth.

Case Study Introduction:

Bellabeat is a health-product manufacturer catered towards women and are looking to become a larger player in the smart device market. Their products provide the user insights on their activity, sleep, stress, menstrual cycle, and mindfulness habits through the Bellabeat app.

As part of the Bellabeat marketing analytics team, we were tasked to analyze smart device data to potentially unlock growth opportunities for the company. We were asked to focus on one of Bellabeat's products and provide high-level recommendations to guide Bellabeat's marketing strategies.

Ask Phase:

In the ask phase, there are two main goals; that is, to identify the problem that needs solving, as well as fully understanding the shareholder's expectations. The problem can be defined by looking at the ideal state, and compare how it differs from the current situation. In this case, although Bellabeat is a successful small company, they wish to become a larger contributor and competitor in the global market. The shareholders wish for the Bellabeat Marketing Analysis Team to analyze the smart device data to provide high-level insights for their marketing strategy to achieve this.

The Prepare Phase:

The prepare phase follows the ask phase, and is where data analysts collect and store data for future use. FitBit Fitness Tracker Data (CC0:Public Domain by Mobius) is used for our analysis and contains fitness information from 30 consenting users.

The data contains various metrics such as, but not limited to the total steps taken in a day or hour, calories burnt in a day, sleep per night. Although the dataset provided consists of 18 CSV-files, many files are subsets of larger files that provide redundant information. Similarly, some files contain metrics gathered from a limited sample size and will not be included in our analysis.

Bellabeat's cofounder and chief creative officer informs us that there are limitations to the provided dataset. These limitations should be addressed in case they negatively influence the reliability and validity of our findings:

1.  Small sample size: The sample size should be representative of the whole population which is unlikely considering that the target population is women across the globe.
2.  Lack of demographics: Demographics are a major health determinant which is needed in order to select an appropriate sample size. Biobehavioral, behavioral, and social factors are all variables that influence an individual's health and lifestyle.
3.  Short data collection duration: Data was collected over one month and may not be representative of an individual's actual lifestyle. For example, a user based in Toronto may be less inclinded to be active in the harsh, cold winter compared to a user in California.

Considering the limitations, the data can still provide valueable insights to improve Bellabeat's marketing strategies.

The Process Phase:

This phase requires us to find and remove and errors or inconsistencies that may impede the analysis step. Here, we can remove any missing points, typos, nulls, or outliers to ensure we are working with clean data. The process phase also includes the transformation of data into a more useful format. Data cleaning, transformation, analysis, and visualization will all be performed in R Studio.

<img width="584" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/dc8413be-1380-425b-95a4-614189a9d18a">

The clean_names function from the janitor package changes all column names in a dataframe resulting in unique names in the snake_case format.

<img width="584" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/b9a79889-bc3b-4499-94d3-8f038a03d823">

The remove_empty function removes all rows and/or columns that consist entirely of "NA" values.

Next, the is.na function checks each cell for "NA" and returns FALSE if that cell is not a "NA". The sum function then obtains the total number of "FALSE". There are no "NA" values in our datasets.

<img width="584" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/e0304e6e-31a3-4577-996a-61f895e23b2f">


We then check for duplicates with the get_dupes function and remove and duplicated values if they exist. Three duplicates were found in the sleep table and were removed.

<img width="583" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/ae9e6f04-a713-46a7-a7d7-525e692f6742">
<img width="585" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/e10922ba-f425-4ae2-9c57-9233449b534c">

Although the names were cleaned, we want to align variables across different dataframes if we want to merge/join our tables in the future. The column for dates are named differently across the dataframes so they will all be named "date" for consistency.

Dates in the sleep table are in the datetime format and is converted to a date format. All the times in sleep are "12: 00:00 AM" which signals the start of the day and is irrelevant. Time in steps are useful and are renamed as "date_time".

<img width="582" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/9f1dc3fa-7f3c-4ca4-8f70-bd80df10230f">

During the data cleaning process, a few data points seemed suspicious. For example, some users had days where they were sedentary for 1440 minutes (24-hours) or totaled zero steps which may be attributed to the FitBit not being used. These extreme data points can influence the analysis are should be removed.

Logically speaking, most people would take at least 100-steps per day even if they are mostly sedentary. Any less than that may mean that the FitBit wasn't used or the battery died early so any data points with less than 100-steps were removed.

Likewise, sedentary time exceeding 23-hours or under 3-hours were removed. Users are considered sedentary when sleeping so any less than 3-hours of sedentary time seems unlikely. Sedentary time at the high-end of the spectrum may be plausible since user could be working an office/desk job at home so I was a bit more conservative in removing these values. Still, 24-hours of sedentary time likely indicates that the FitBit wasn't used.

Furthermore, some values of calories burnt were "0". Since calories burnt vary drastically, only data points where calories burnt equal to "0" were removed. Calories are burnt just from sitting due to the muscles needed to keep one upright.

The subset function creates a new dataframe called "activity_cleaned" which includes all specified values. Essentially, the arbitrary outliers were excluded from the new dataframe.

<img width="584" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/2ff5b451-3682-4f3b-9779-90996d424f48">

Data points outside the upper and lower limit (fences) of a data set are considered outliers by definition. Outliers will be identified and then deleted if they do not make logical sense.

The interquartile range (IQR) is a measure of statistical dispersion that indicates the middle 50% of data, or the difference between the 75th and 25th percentiles. The upper fence represents values exceeding the 75th percentile by more than 1.5-times the difference between the percentiles. The same logic can be applied to the lower fence.

The summary function can easily help us identify the different percentiles (quartiles) to find our upper and lower fences. All outliers are listed in a different table for us to evaluate using the subset function again.

<img width="583" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/7b613ab9-b2e7-4d2c-88b0-8da5d62466a7">
<img width="583" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/70ae46bb-0ec4-44fe-bd38-2296f0f6ed70">
<img width="582" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/0ad2eb91-8992-431d-ad5d-ab0f5a6f9650">

Not many outliers were expected since most of the illogical data were removed beforehand.

1. Outliers for calories burnt were high but the total_steps and amount of activity were just as high so no data points were removed.
2. No outliers were found for sedentary minutes.
3. One outlier was removed from row 137 since high calories were burnt, but the total amount of steps and activity were too low to make sense.
4. The lowest amount of sleep was 58-minutes while the highest amount of sleep was 796. While unhealthy, both may be possible so no outliers were removed. The same logic is applied to total steps taken.

Finally, all the dataframes have been cleaned and are ready for use. The activity_cleaned and sleep tables are then joined to find any existing correlations.

<img width="584" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/2eacb88c-0e9a-4379-9a5e-00db1226302b">

The Analyze and Share Phase:

Using the clean and processed data, we can create visualization that help us understand possible trends. Analysis helps with making predictions, drawing conclusions, and in return, drives informed decision making.

Variables in the cleaned activity table are plotted against each other to find trends.

<img width="656" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/b60e893e-0439-45d8-a510-383d6451c96c">
<img width="429" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/bef46c90-d7cb-4148-8cef-4a8896cac1af">

As expected, there is a positive correlation between calories, total steps, and very active minutes. The more steps and active minutes an individual has, the more calories they burn in a day. There is also a negative correlation between sedentary minutes and total steps in a day. The less sedentary time a user has, the more time there is for to be active. However, there is no correlation between calories burnt and sedentary minutes. Applying the same thought process, it would be reasonable for calories burnt and sedentary minutes to have a negative correlation.

The same trends are seen both before and after removing the outliers but stronger correlations are seen after removing the outliers. The same graphs plotted before removing the outliers can be seen as follows:

<img width="343" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/b1dccbaa-9eb1-4411-a06e-a5e5def79361">

Now, trends within the merged datasets of cleaned activity and sleep are investigated. First, a new column called day is created, while the day of the week corresponding to the date is added using the "weekday" function. A new table is created afterwards which gets the average amount of steps taken per day of the week. The seven days of the week are plotted against the average steps per day of the week and the line at the y-intercept indicates the recommended amount of steps per day.

<img width="655" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/f7ca8f0c-438c-4f30-a15d-72f558ba1d33">
<img width="380" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/de497d2f-6102-4922-a969-4bfd210f1cd7">

