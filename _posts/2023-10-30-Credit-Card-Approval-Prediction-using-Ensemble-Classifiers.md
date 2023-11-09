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

While little can be done about a small dataset, we can easily overcome the latter issue with a label encoder. A Label Encoder is a data preprocessing technique from the scikit-learn (sklearn) library that transforms categorical data into numerical values. It assigns a unique integer to each category, making the data suitable for use in algorithms that require numerical input.

<img width="902" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/303ae951-3b91-46d5-a749-61cff615693a">
<img width="903" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/f672e1fa-c9c9-4707-a394-7b38566c6307">

Features (x) are the measured inputs of the problem domain like independent variables while the target (y), is the dependent variable that we are attempting to predict and model. Therefore, features (x) are all the measurements besides the "Approval" column, which is why we drop that column specifically. The "axis = 1" indicates that we are dropping a column, not a row. Conversely, the target (y), is the "Approval" column only.

After encoding our data, we quickly check to confirm that all our features now have a numerical dtype. Moving on, we use the train_test_split function from scikit-learn to split a dataset into training and testing sets. We set the test size as 25%, meaning that 25% of the data is allocated to the testing set to test against our trained model, and the remaining 75% is used for training. Lastly, random_state is a parameter that sets the seed for the random number generator, ensuring reproducibility of the split.

<img width="904" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/bd403b8e-59cf-4005-a74f-49b7d31ccbf9">

The Gradient Boost Classifier is first initialized with a set random state for reproducibility while x_train are the feature variables and y_train is the corresponding target label used for training the model

Keeping in mind that the dataset is split 75:25 for train to test data points, predict_train uses the previously trained model to make predictions on the training data while predict_test makes predictions on the testing data. Afterwards, the accuracy score is calculated by comparing the actual training labels (y_train) with the predicted label we just declared (predict_train). The accuracy score measures the fraction of correctly predicted labels in the training set.

<img width="902" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/db731e50-a3b6-4677-9856-bacf78c5958e">
<img width="904" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/6cf1e967-f22c-4cc1-b48c-bd3661623d8f">

A higher training accuracy (0.98) compared to the test accuracy (0.86), is often an indication of overfitting. Overfitting occurs when a model learns the training data too well, capturing noise or random fluctuations in the data to the extent that it performs poorly on new, unseen data. To address this overfitting, we can use a specific library designed for gradient boosting where the eXtreme Gradient Boost (XGBoost) gamma parameter penalizes overcomplex models that may result in overfitting of our model. 

While loss refers to how bad a model's prediction is, loss reduction indicates if the model is making substantial improvements during training. We can prevent further splits in tree-based models and end training early if no improvement is seen with training. This is called tree-pruning and helps prevent the creation of overly complex trees that may capture noise in the training data and result in overfitting. More specifically, "gamma" is the minimum loss reduction required to create new nodes which we have set at an experimental value of gamma = 5. Although this is a relatively high gamma value, we have less data; therefore, a less complex model is preferred for reasons stated previously.

<img width="901" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/8e1e2e25-d5ee-442a-9810-b467fc0ac99b">
<img width="902" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/2f5ec2b0-6df9-463e-be89-917a76194d92">

Avoiding dataset memorization by our model may be another reason for tree-pruning and simpler models. With small datasets, there's a risk that an overly complex model memorizes the individual data points rather than learning the underlying patterns. When comparing gamma restricted accuracies to a lack of, we can see the plain Gradient Boost has a training accuracy of near 100%, suggesting potential memorization. While the test accuracy only increased by a mere 0.01, regulating your model complexity accordingly may pay dividends with larger or different data sets.

Using the Pyplot library from the Matplotlib library, we can also visualize the importance and contribution of each individual feature on our machine learning algorithm.

<img width="903" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/d5645928-1d5b-40ab-a2d0-5996b3e4b598">
<img width="997" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/d79dc0fe-db7b-4860-a4c2-f366a3bcc068">
