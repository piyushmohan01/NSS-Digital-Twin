### Project Notes

#### Introduction :
1) For the analysis, we have chosen the level-3 data of the year 2022. The dataset consists of 213920 rows.
2) Each entity (particular course of a specific provider) had 35 rows, wherein, there were 27 rows for 27 survey questions and 8 rows pertaining to different scales.
3) The aim is to build a neural network that takes 26 sets of inputs to predict the actual agree percentage of the 27th question (overall satisfaction)

#### Data Analysis :
1) First we begin by extracting the scale rows from the dataset. This left us with 165024 rows with sets of 27 rows per entity. The reduced data is saved as a CSV for further use.
2) Then percentage values in the columns- Answered 1-5 and Actual value, were converted to numericals using Pandas.
3) Once we have all the required columns in the right datatype, we define a function that takes 2 inputs- column name (for example, Subject Code) and an empty dictionary. This function calculates the completion rate and stores it along with the actual value percentages of every single entity (unique subjects).
4) We run this function for 2 different columns- 'Subject Code' and 'Provider'. The dictionaries returned by the function are then converted to dataframes and the values are plotted (completion rate of entities vs. actual agree value)
5) The results of both the columns can be seen below:

<p float="left">
  <img src="./images/2022/level-3/subject-comp-rate-vs-actual-agree.png" width="300" /> 
  <img src="./images/2022/level-3/provider-comp-rate-vs-actual-agree.png" width="300" />
</p>

Inference: 
There seems to be a very weak linearity between completion rate and actual agree values. This busts the myth that students who are not satisfied with the course do not complete the survey, conversely, a good completion rate does not directly indicate a student's satisfaction towards the course either.

#### Building Neural Networks :

##### A) First Neural Network :
1) In the first model, we aim to pass 26 inputs to get a prediction on 1 output. The inputs here are the actual agree values for the first 26 questions of the survey which will be used to predict the 27th actual value. The predictions will be checked against the true values of the 27th question rows.
2) Since we do not need other columns (except for 'Actual value') for this network, we extract a subset of the dataset containing just the one column.
3) This sampled dataset is reshaped into (6112, 27) indicating 6112 different sets of 27 questions. This reshaped data is split into train and test sets with a specified random_state using Scikit-Learn and a network is built.
4) Once the network trains over the specified epochs, we plot the predictions across true values to gain insight into the performance of the network.
5) The results of the first network can be seen below (Test Loss: 40.66653823852539):

<p float="left">
  <img src="./images/2022/level-3/neural-network-01.png"/> 
</p>

<p float="left">
  <img src="./images/2022/level-3/sample-test-01.png"/ width=600> 
</p>

##### B) Second Neural Network :
1) In the second model, we aim to pass 26x5 inputs to get a prediction on 1 output. The inputs here are the percentage values across the 5 different options (1 to 5) for the first 26 questions of the survey which are used to predict the 27th actual value.
2) For this network we require 6 columns- 'Answered 1', 'Answered 2', 'Answered 3', 'Answered 4', 'Answered 5', and 'Actual value'. We extract another subset with the mentioned columns to be used for the network.
3) This sampled dataset is reshaped into (6112, 27, 6) indicating 6112 different sets of 27 questions across 6 different columns. The data is split into train set using the first 26 sets of 5 values (options) for 6112 different entities. The test set consists of the 27th values in the last column of the dataset.
4) The split is performed using the same specified random_state from the previous network. Once the network trains over the specified epochs, we plot the predictions across true values to gain insight into the performance of the network.
5) The results of the second network can be seen below (Test Loss: 39.72053146362305):

<p float="left">
  <img src="./images/2022/level-3/neural-network-02.png"/> 
</p>

<p float="left">
  <img src="./images/2022/level-3/sample-test-02.PNG"/ width=600> 
</p>

#### Next steps:
1) Analysing difference in performance after removing outliers.
2) Include Provider and Subject names as inputs (encoding)
3) Bagging multiple networks and find average performance scores.

#### 2023-Data:
1) Identified and removed "Theme" rows from dataset
2) Split "Questions" into QNo. and Question-Text
3) Added row_index column to make subsets based on countries
4) Extracted rows for Non-English universities (Scotland, Wales, Northern Ireland) - Rows that have Q28 - Overall Satisfaction
5) Did the same for English universities - Rows that have Q27 but no Q28
6) Made third subset of rows that had neither Q27 or Q28

A) Imported Non-English and English subsets
B) Converted "Option" values to percentages by dividing # by Responses (0-100)
C) Dropped sets that fell below the Response and Population thresholds
D) Build 2 models: One using only Positivity values for 26 questions and other using Option values for the 26 questions
E) Evaluated performance for both models
F) Used the models to predict Q28 Positivity values for English subset
G) Found unique sets of Provider-Level-Subject and assigned predicted values

#### Poster Presentation :

Good afternoon everyone! I'm Piyush Mohan and my Dissertation is based on building a Digital Twin for the National Student Survey, also known as the NSS.

I'll start with a brief introduction on NSS and a little about what my project aims to achieve.

The NSS is an yearly survey for all Undergraduate students across UK. It has been running since 2005 and is structured with questions related to different aspects of Undergraduate courses. One question that summarises a student's responses is the one on "Overall Satisfaction". The aim for building this Digital Twin is to understand how "Overall Satisfaction" relates to responses to the other questions. Recently a Two-Stage review was conducted and the governing bodies from all countries in the UK, decided to make certain changes to the 2023 survey. The idea behind this project is to build a Digital Twin that can be used by Universities to assess and improve their courses.

Moving onto the Data Exploration part

I started with the 2022 data which had 27 questions with 5 options- Mostly Disagree, Disagree, Neutral, Agree, and Mostly Agree. The data was initially split across 3 tables, indicating 3 levels, each with different granularity on the courses. I decided to use the Level-3 data, as it had no aggregation on the courses and was the most granular type available.

After some data processing, I was left with almost 165 thousand instances with sets of 27 questions spread across 422 unique providers and 162 unique courses.

  In the selected dataset, the questions were grouped into sections and the data initially consisted of rows summarising sections for each course. Once, we drop all the rows indicating sections, we were left with almost 165 thousand instances with sets of 27 questions spread across 422 unique providers and 162 unique courses. 

The survey's main purpose is to understand a student's satisfaction and one common misconception built on that idea is that unsatisfied students do not complete their surveys. To check if it really is a misconception, I created a completion rate metric using Response counts and Sample Sizes. Then, I plotted a scatterplot for the completion rates against Actual Agree percentages, which are basically the amount of students that responded positively for the questions. The scatterplot does not show a strong correlation, indicating that even unsatisfied students often complete the survey.

  As the plot clearly shows, there does not seem to be a strong correlation between both the variables. This indicates that a fair amount of students that are not satisfied with their courses have completed the survey.

  we use the response count and sample sizes for different providers and create a new column indicating completion rates. Next, we plot a scatterplot for the completion rates against Actual Agree percentages, which are basically the amount of students that responded positively for the questions. As the plot clearly shows, there does not seem to be a strong correlation between both the variables. This indicates that a fair amount of students that are not satisfied with their courses have completed the survey.

  students do not complete the survey if they are not satisfied with their courses. To check if it really is a misconception, we use the response count and sample sizes for different providers and create a new column indicating completion rates. Next, we plot a scatterplot for the completion rates against Actual Agree percentages, which are basically the amount of students that responded positively for the questions. As the plot clearly shows, there does not seem to be a strong correlation between both the variables. This indicates that a fair amount of students that are not satisfied with their courses have completed the survey.

After a careful analysis on the features, the columns selected for building the Digital Twin were: Response percentages for the 5 options and Actual Agree percentages. 

Next, I started working on the Digitl Twin by assigning one of the features as the target and choosing our inputs for the model. The idea here is to use the responses from the first 26 questions to predict the value for the 27th question which indicates "Overall Satisfaction".

  Now that we have selected our features for the Digital Twin, we begin by assigning one of the features as the target and choosing our inputs for the model. The idea here is to use the responses from the first 26 questions to predict the value for the 27th question which indicates "Overall Satisfaction".

For this task, I built two models- first, where I used the Actual Agree percentages for the 26 questions as inputs and second, where instead of using Actual Agree percentages, I used the response percentages across the 5 options to predict the Agreement percentage for Overall Satisfaction.

  predict the Agreement value for Overall Satisfaction. In the second model, instead of using Actual Agree percentages as inputs, I used the response percentages across the 5 options for the first 26 questions to predict the Agreement percentage for Overall Satisfaction.

The second model uses more features as inputs and although this helps the neural network to learn more about the data, it can also make it vulnerable to overfitting, which is the case where the network learns a bit too much of the training data and eventually fails to apply the learning on any unseen instances.

  To build both these models, the data was initially split into inputs and targets. A ratio of 80 is to 20 was used to make the training and testing subsets. 

I used a ratio of 80 is to 20 to split the data into training and test sets and also defined a similar random state for both models to assess their performance on the same instances. The results of both models indicate that they were able to capture information from the training sets and showed good generalisation on the test sets. The performance in the second model, with more input features, shows an improvement while also mitigating overfitting.

Moving onto the Future Work, now that we have built a reliable Digital Twin that is capable of predicting Overall Satisfaction, the aim will be to map it to the 2023 data. As mentioned, there were crucial changes in the 2023 survey. Some of which were- Dropping the neutral option (leaving us with 4 options) and more importantly dropping the question on Overall Satisfaction for English Providers.

The 2023 data consisted of 2 subsets based on Providers- English Providers without the 27th Question and Non-English Providers, from Scotland, Wales, and Norther Ireland, with the 27th question.

One way to map the Digital Twin would be to use the Non-English provider instances to train a model over the available 27th question and then use the model for predicting Overall Satisfaction for English providers. 

But as there are no true values for the predictions, we have to find a way to assess the robustness of our model and in some way quantify the confidence in our predictions. This can be done by building a dataset using the predicted English percentages along with the true values for Non-English providers. This way, I would have a complete dataset that can further be used for training a new Digital Twin that generalises well over English and Non-English providers.

On its completion, the project will help Universities, English or Non-English, understand more about the Students' needs, ultimately benefitting students.

#### Salutation :
- Good Afternoon
- Name and Topic

#### Introduction :
- NSS (yearly survey) on UG studies
- Overall Satisfaction
- Related to other 26 questions
- Two-Stage review, changes 2023
- Map Twin, accomm changes

#### Data Exploration :
- 2022 data, 5 options
- Level 3, Granularity
- 160k Rows
- Misconception
- Completion Rate
- Scatterplot, weak correlation
- Choosing columns

#### Building Digital Twin :
- Assigning target and inputs
- 2 models, both inputs
- More features, overfitting
- 80:20, random state
- Result, good performance
- 2nd model better, no overf

#### Future Work :
- Changes to 2023 survey
- 2 subsets, english and non
- Explain model
- No true values, robustness
- Complete dataset
- Build better Digital Twin

#### Conclusion :
- students' needs


#### Project Progress :
- Started with 2022 data, built two models (with and without options) and compared their performances.
- Once I had a good working model, I started working on the 2023 dataset.
  - Regional Differences in Survey had to be addressed
  - Sets with Q27 were considered Non-English
  - Sets with Q28 were considered English
  - Sets without either of the questions were left as UK
- After data processing was done to fit the 2023 data on the previously built model for 2022, I used the Non-English data to train and test the model.
- Upon achieving a good accuracy, I ran the model on the English data to get predictions for Overall Satisfaction

- Since there were no true values to check against, I tried 2 approaches:

- Approach 01: (future.ipynb)
  - Try to split the 2022 data as Non-English and English
  - Run the data over the 2023 model and check for both English and Non-English as now we had true values for both regions
  - Result: The predictions were varied with some being good and some being way off. This can be due to the change in question framing in the surveys of both years.

- Approach 02: (2023-05 copy.ipynb)
  - Combine the English predictions for 2023 with True Non-English instances and form a complete dataset where the survey sets have 27 rows (with Overall Satisfaction)
  - Shuffle the sets as we would ideally want a good mix of English and Non-English sets in training and testing
  - I decided to modify the model to predict the 4 options instead of one agreement value. The performance, as expected, declined a bit but upon a closer inspection, the overall predictions across the options were closer.
  - I further changed the model to have question numbers as inputs along with the region of the provider. This was done by assigning a value 'English' or 'Non-English' to the region column.
  - With the addition of Question Numbers and Regions, we encoded the data, resulting in 33 column inputs, predicting the percentages for the 4 options.
  - The Neutral column for Overall Satisfaction was dropped in order to have consistency in the number of options (4).
  - Result : Good train and test performance for both English and Non-English data

#### Next steps :
- Using 2024 data (pipeline copy.ipynb)
- Building a pipeline and predicting for English and Non-English