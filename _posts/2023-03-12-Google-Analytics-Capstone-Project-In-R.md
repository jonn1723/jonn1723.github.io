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

On average, users meet the recommended amount of steps per day to improve functional and cognitive health. However, the recommended steps per day vary drastically between demographics such as in older or diseased populations. Such metadata are important information that should be included in future sessions to improve the analysis.

Users tend to have the most amount of steps on Saturday, which could be due to working professionals having a day off from work. On the other hand, users barely reach the 7100 step threshold on Sundays as they are potentially getting ready for the work week to start on Monday. Knowing if the user is a student, working professional, or retired for example, can be important as it allows Bellabeat to cater services to each group depending on their needs.

Similarly, using the day column created earlier from the weekday function, days of the week can be graphed against the average amount of sleep between all users. Adults should have 7-hours of sleep or more every night as sleep is one the the major contributors to a healthy lifestyle. Thus, the line indicates the minimum recommended amount of sleep users should strive for.

<img width="655" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/b5cc3250-1c9a-4d13-8dcf-b0dca37b4d9f">
<img width="380" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/ca10b1a8-6134-4b8d-88fb-b796e0ecccab">

It seems as if most users are barely getting the minimum recommended amount of sleep per day from Monday to Saturday. In contrast, users get an average of 7.5-hours (453 minutes) on Sunday. From the preceding graph, users just barely maintained an average above the recommended 7100 steps on Sunday. The increase of sleep on Sundays are consistent with the decrease in steps and may have highlighted an important trend in consumers where Sundays may be considered a "rest day".

A trend in our sample may exist since users can be more or less active depending on the day of the week. There may also be trends that happen hourly if we look at each day of the week specifically. In order to examine this hypothesis, the "separate" function was used to separate the date and time in the steps dataframe and days of the week were added like in previous examples. The data was grouped and averaged by date and time which was then made into a heat map to observe which timepoints have the most activity.

<img width="655" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/dc0b1d3b-7351-4a6b-acf0-1354e533cde7">
<img width="481" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/f296edab-a50f-4eb7-a457-31a473994d15">

Most users are not active on any day of the week before 9:00 AM or after 8:00 PM since they are likely sleeping or just getting in/out of bed. Unfortunately, there are no obvious trends where users are active but more total steps are taken around the 4:00 PM to 7:00 PM range on weekdays. This could be due to the the majority of users being working professionals who are getting off work around that time. With a small sample size of 30-users, it is fairly easy for a few individuals that are extremely active or inactive at certain times of the week to skew the data. For example, some users could have weekly jogs on Wednesday from 5:00 PM to 7:00 PM which would increase the total steps for that time frame as seen in the heat map. By having a larger sample size and collecting important metadata to provide context, an improved heatmap could be developed to enlighten Bellabeat what time and day users are most active.

Lastly, each individual user is to be grouped by their activity level to have a better understanding of Bellabeat's consumers. Users are categorized into highly active, active, somewhat active, low active, and sedentary by the average total steps they take daily.

A dataframe called user_averages was created by grouping and taking the average of measurements such as sleep, sedentary minutes, and total steps. After the users are group accordingly, we use the "group" and "summarise" functions to count the number of users in each activity intensity level. The types_total variable is the total number which is used to find the fraction, and subsequently, the percent of users in each level.

<img width="645" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/b4c745ad-e64e-4e24-a5cf-6802cfb065e8">
<img width="644" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/887b7d21-8e37-4b7e-85a9-81d97d7b9232">
<img width="475" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/5735a48f-f147-4bb5-bea1-33cd94b8a8e0">

Bellabeat's products seem to attract users of all fitness levels so adjustments can be in the app to encourage users to reach the next fitness level if wanted. Different recommendations could be suggested such as the recommended amount of daily calorie intake which should vary between the groups.

During data cleaning, sedentary times of 24-hours were noticed. This likely means the FitBit was not used. Similarly, some individuals had less than the 30-31 entries expected in a month. Therefore, user were also categorized into infrequent use, occasional use, moderate use, and frequent use. The categorizations are arbitrary but can give the team a general understanding of their consumer's usage.

<img width="654" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/c343f176-2b11-4ac8-ac37-a7826e64e6b9">
<img width="654" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/e79fc761-7672-4a01-a87c-9b3d7ba2abf9">
<img width="398" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/0c71f342-1fdb-4f10-9db6-5f0cde84b27d">

Thankfully, the majority of consumers are frequent users suggesting that Bellabeat's products are useful and do not get in the way of most lifestyles. Bellabeat could introduce a survey for the less frequent users to find why they are not using Bellabeat's products consistency.

Because users only have so much time in a day, more active users may be neglecting sleep as they are hyperfixated on improving their health through exercise. However, sleep is just as important so trends between activity level and sleep are graphed as follows:

<img width="654" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/f152d2c0-818c-4ee9-8c92-551c28568c80">
<img width="385" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/74513b79-7c1d-4006-8864-cfcb9bbbbedc">

As expected, those who are too active are not getting the minimum recommended amount of sleep per night (7 hours/420 minutes). Conversely, those who are considered sedentary are getting more sleep. However, users should strive for a healthy balance between sleep and activity level since only sedentary users seem to be getting enough sleep nightly.

Most users are frequent users as seen previously, so it can be assumed that frequent users have a similar distribution of activity intensity levels. This can be confirmed by simply merging two dataframes created just prior. Only the activeness of frequent users were investigated as the other user frequencies have too small of a sample size.

<img width="655" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/fe612b85-1426-4c8b-84c3-4ff9a8de18fa">
<img width="655" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/5dfde356-4fa0-4b13-8085-04ad012cc1de">
<img width="394" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/98a85f3e-4f96-4427-a737-12fe41016e04">

Surprisingly, the amount of sedentary users went down by a lot and the two categories at the extremes sides of the spectrum make up less than 10% of frequent users. The Bellabeat app seems to be effective in reducing sedentary users when used frequently.

The Act Phase - Insights and Recommendations:

The act phase is where the Bellabeat market analyst team should provide possible solutions for the business problem at hand. The shareholders wish to push Bellabeat to the global market, so the team should provide data-driven insights to help achieve this.

Since the stakeholders requested the Bellabeat marketing analysis team to suggest improvements for one product specifically, the team decided that improving the app would be the wisest decision. The Bellabeat app connects to their whole line of products and is likely used by the most consumers.

Trends in Data and Why it Matters:

1. Product utilization rate

From the 'User Usage Frequency' Chart, it appears as if a little over 85% of users regularly use their Bellabeat products. Regular users consists of both frequent and moderate users in the 'User Usage Frequency' Chart. In other words, consumers are using their Bellabeat productAlthough 87% sounds relatively high, when put into perspective, 150,000 out of 1,000,000 users would rarely be actively using their Bellabeat product after purchase. This is especially relevant since Bellabeat wishes to expand and be a strong player in the smart technology global market. Bellabeat should consider ways to improve user retention especially since word-of-mouth advertising is so important in today's society.

In order to resolve the problems Bellabeat consumers may have that lead to less often use, the problems first have to be identified. Bellabeat could send out surveys on the Bellabeat app to identify common complaints a user may have. Unfortunately, survey response rates range from 15-30% depending on the length and complexity of questions asked. Response rates from customers that are already rarely using their products are expected to be even lower Therefore, it is imperative that Bellabeat should offer a small incentive to participate in the survey while keeping the survey at a tolerable length. For example, the survey should consist mainly of short and easy to answer, closed-ended questions that are convenient for the consumer to answer, and for the data analytics team to process. A small discount, voucher, or token of appreciation could be provided as well and may result in an increased profit as well as giving the customers an incentive to participate in the survey. From this survey, important demographic and context details could also be gathered to make more informed decisions.

2. Activeness of Frequent Users

This section investigates how active users are when they are frequent users of Bellabeat products. Ideally, all frequency groups would be analyzed, but the lack of data and samples meant that some categories had either no users, or very few users. Including these frequency groups would have been misleading since it is highly unlikely that there are no active users in the moderate use category when looking at the target population. Similarly, frequency groups with a few users would skew the data heavily as well.

Nevertheless, having this visualization allows the Bellabeat marketing analysis team to have a general idea regarding the activeness of their frequent users. The survey mentioned early could include a section that inquires their user as to why they use Bellabeat products. Although the products are targeted towards the more active population, around 30% of frequent users are considered low active or sedentary. Perhaps, these users like Bellabeat products for their design, aesthetics, quality, or ease-of-use. Finding the general consensus may allow for Bellabeat to open a new product line that targets the less active population. This allows for a larger target audience and would be a great way for Bellabeat to enter the global market.

However, for more active users, their main goal is likely to stay fit and improve their health and lifestyle through Bellabeat's help. The app can introduce push notifications that notify and encourage the user to achieve the minimum 7100 steps daily to stay fit. In addition, the app can also provide the benefits to doing so such as improving cardiovascular health, easing joint and muscle pain, lowering mortality rate, and can improve mental health. Similar to targeted products for the less active group, products such as their water bottle (Spring) could be recommended to encourage users to stay hydrated. All these claims should be endorsed by researchers and Bellabeat could look into partnering with physiologists or kinesiologists since studies have found that scientists are one of the most trusted groups in society.

Bellabeat could introduce a reward system that encourages users to use their products frequently and stay healthy simultaneously. For example, if users are consecutively active on their app and achieve the minimum steps per day for a "streak" of one month, a small discount can be applied on their next purchase of a Bellabeat product. Regardless of how it is done, it is important to help users meet their fitness goals. By doing so, Bellabeat's customers could recommend the product to family and friends and would be a huge first step into becoming a larger player in the global smart device market.

3. User Metrics - Trends

Total steps daily and very active minutes daily have a positive correlation with the daily calories burnt as expected. However, there is no negative correlation between sedentary minutes and calories burnt. This means that individuals may still burn high amounts of calories regardless of their daily sedentary minutes. However, sedentary minutes has a slight negative correlation with total steps daily meaning that the more sedentary minutes spent daily increases the liklihood of that user taking less total steps. Thus, decreasing sedentary minutes could help to increase the chance of a user meeting the minimum amount of steps suggested in a day.

High sedentary minutes are historically associated with numerous health deficits such as cardiovascular disease, type 2 diabetes, various cancers, and obesity. Reducing sedentary minutes would serve to prevent and lower these risks while allowing for users to meet the recommended daily steps. The Bellabeat app could introduce a feature to encourage users to move around if prolonged sedentary behavior is observed outside of working hours. Over the years, sedentary behavior has only increased due to more work-from-home jobs and increased screentime and technology usage. Decreasing sedentary minutes would be a great start for users to improve fitness while decreasing disease risks.

Users seem to meet the recommended step quota on average all days of the week which may be attributed to the successful workings of Bellabeat's products. They also tend to have higher total steps on Monday, Tuesdays, and Saturdays. It might be possible that users lose motivation near the middle of the week but start exercising more frequently when they have time on Saturday. Of all days, Sundays are the day where Bellabeat consumers have a lot lower total step count than the rest of the week. The Bellabeat app could motivate users to keep up and maintain their physical activity throughout the week. However, taking a rest on Sundays may be a good idea as long as users maintain their daily step count.

Looking into each hour of the day, there isn't a clear or obvious trend as to when the sample population are more active. Rather than pooling the whole sample, it may be beneficial to consider the demographics of each individual such as if they are currently working or in school. After sorting individuals by their demographics, there may be a clearer trend to provide insight on consumer tendencies. From the heat map currently, users are on average the most active on Wednesdays from 4:00 PM to 6:00 PM. Since the sample size is small, it could be influenced by a few individuals who happen to have their weekly run at that time. Having a larger sample size with important metadata and demographics would allow for targeted responses to each individual.

Finally, sleep is one of the main determinants of health and should be prioritized equally to exercise. However, users are barely able to get the recommended 7-hours of sleep each night. The Bellabeat app could remind users to sleep within the next hour in order to get sufficient sleep and should they get at least 7-8 hours of sleep daily for a month, a discount or alternative form of incentive could be provided. The data provided suggests that users are able to get more sleep on Sundays which aligns with the data found in steps taken per day of the week. Users take noticably less steps on Sundays while getting more sleep. Sunday could be considered a rest day where the Bellabeat app could applaud the user for their hard work during the week and suggest healthy methods such as meditation or light yoga as a break.

After noticing a trend between exercise and sleep in the two different visuals, a new graph was made to better understand the relationship between activity and sleep. Using the activity level category labels from before, each activity level was sorted to show the amount of sleep each group averaged. Although the sample population is not the largest, highly active and low active groups averaged around 5-hours of sleep nightly. For highly active groups, studies have shown that intense exercise before bedtime is correlated with disrupted sleep. The Bellabeat app could suggest users stray from intense exercise at night to promote better sleep. As for the low active group, 29 out of 33 studies found a correlation between regular exercise and improved sleep quality/duration. Bellabeat can encourage this group to increase their exercise frequency to hopefully improve their sleep. In contrast, the least active user are getting the most sleep since sleep counts as sedentary hours.

Increase the Bellabeat App/Product Usage:

1. Bellabeat could introduce a socialization platform similar to popular social media such as Instagram for users to share their physical feats and acheivements with their social circle. This would greatly increase app usage and encourage friends and family to start using Bellabeat products as well. Similarly, users can keep each other in check to make sure they are reaching their fitness goals.
2. Users can also be introduced to other consumers with similar fitness goals and lifestyles through the app. For example, the app could introduce users who all play badminton casually to each other to form a weekly group. This would allow for consistent exercise and may also attract new users as well.
3. The app can include locations of healthy eating restaurants, gyms, physicians, etc., that other Bellabeat users recommend. It can also provide resources such as fitness classes, drop-in sports, or cooking classes to promote a healthy lifestyle.
4. A minigame could be introduced for those who like video games. Each step, could equate to in-game currency which users can use to buy new characters, accessories, or upgrades. Since the gaming community is also often the most sedentary, promoting exercise through an alternative gaming method would be great for motivating these users.
5. Increase the battery life and make the FitBit waterproof so users don't need to take it off. Improve the aesthetics of their other products to attract new users. This would increase app usage as all their products are connected to their app so the consistency and reliability of data collected would improve overall.


Conclusion and Key Takeaways:

1. Improve data gathering and increase sample size so individualized recommendations can be made to each individual
2. Include a short questionnaire/survey for individuals that are less frequent users of their Bellabeat product to increase user retention
3. Add incentives for the user stay health and to use Bellabeat products daily such as small discounts or freebies
4. Partner with researchers and scientists that advocate for a healthy lifestyle to increase trust and confidence in Bellabeat's products
5. Open a new product line targeting less active individuals - a sizeable portion of active users (30%) are low active or sedentary users suggesting that they may be using Bellabeat products for design, quality, or ease-of-use
6. Add features to the Bellabeat app that would encourage users to use it more frequently

Thank you for reading to the end of my first project in R
