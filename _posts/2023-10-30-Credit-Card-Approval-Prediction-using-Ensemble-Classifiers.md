The Credit Card Approval Outcome Prediction project is a data-driven initiative that aims to develop a machine learning model capable of assessing credit card applications and predicting whether an applicant is likely to be approved or denied. This project is highly relevant in the financial industry as it can streamline and enhance the credit assessment process, reducing the risk of lending to high-risk individuals while facilitating access to credit for creditworthy applicants. By leveraging historical data and advanced algorithms, this project can help financial institutions make more informed and efficient credit decisions, significantly reducing the time and resources required for manual evaluations, ultimately benefiting both lenders and applicants by improving efficiency in the credit approval process.

The dataset is taken from https://www.kaggle.com/datasets/samuelcortinhas/credit-card-approval-clean-data where feature names are inferred for easier understanding of the context. The raw dataset can be found in the UC Irvine Machine Learning Repository, https://github.com/uci-ml-repo/ucimlrepo.

We start off by loading in the packages and libraries needed for the project followed by the dataset we will be using for this project. With any unfamiliar dataset, I like to preform some Exploratory Data Analysis (EDA) to better understand our data and what we're working with.

<img width="1202" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/bb168fec-de4c-4ae4-af64-ac9fdfa6c60e">

Just going over all the functions quickly:
1. 'df.head()' shows the initial rows of the data allowing us to quickly inspect the data and understand its structure
2. 'df.describe()' provides statistical insights which offers insights into the data's distribution and central tendencies
3. 'df.nunique()' counts unique values in each column to understand the distinct values present in the data
4. 'print(df['Approved'].value_counts())' shows us the distribution of approved/declined cards in the 'Approved' column
5. 'df.isnull().values.any()' checks for missing data to identify data gaps
6. 'df.info()' provides metadata about the DataFrame to get an overview of the data's structure 

<img width="903" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/65ddef56-c765-44a4-8e90-c5794c1eb462">
<img width="906" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/6377346d-5732-4b59-afac-e24aaf6af577">

From our initial EDA, we learned about two key factors that have to be considered in this project. Initially, we're working with a relatively small dataset of 690 unique data points, which increases the risk of overfitting and constrains us to employ simpler models. Moreover, we encounter a data type issue, as three of our columns are of type "Object," whereas machine learning algorithms typically demand that all columns have a numeric data type.

While little can be done about a small dataset, we can easily overcome the latter issue with a label encoder. A Label Encoder is a data preprocessing technique that transforms categorical data into numerical values. It assigns a unique integer to each category, making the data suitable for use in algorithms that require numerical input.

<img width="902" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/303ae951-3b91-46d5-a749-61cff615693a">
<img width="903" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/f672e1fa-c9c9-4707-a394-7b38566c6307">

