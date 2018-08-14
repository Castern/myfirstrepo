
The zip contains five files:<br>
**A) query.txt <br>
B) unique_query.txt <br>
C) logistic_regression.ipnyb <br>
D) data_file.csv <br>
E) unique_data_file.csv** <br>
							
A) The **query.txt**  
This SQL query returns the following data
Note that it contains one to many values for respondents in terms of COMPANY_ID, ENVIRONMENT, DEVICE_TYPE and ARCHIVE_FLAG

	1] RESPONDENT_ID     		- Every respondent’s unique ID
        2] AVG_DURATION_FOR_COMPLETES 	- The average duration of events that have been marked Complete
        3] MEDIAN_DURATION_FOR_COMPLETES- The median duration of events that have been marked Complete
        4] AVG_DURATION_FOR_QCFS   	- The average duration of events that have been marked as Quality Check Fails
        5] MEDIAN_DURATION_FOR_QCFS  	- The median duration of events that have been marked as Quality Check Fails
        6] DISTINCT_EVENT_COUNT 	- The total number of distinct events for a respondent
        7] 10TH_PERCENTILE_DURATION	- The 10th percentile of duration of the survey
        8] 20TH_PERCENTILE_DURATION 	- The 20th percentile of duration of the survey
        9] 30TH_PERCENTILE_DURATION	- The 30th percentile of duration of the survey
        10] 40TH_PERCENTILE_DURATION	- The 40th percentile of duration of the survey
        11] 50TH_PERCENTILE_DURATION	- The 50th percentile of duration of the survey
        12] 60TH_PERCENTILE_DURATION	- The 60th percentile of duration of the survey
        13] 70TH_PERCENTILE_DURATION 	- The 70th percentile of duration of the survey
        14] 80TH_PERCENTILE_DURATION	- The 80th percentile of duration of the survey
        15] 90TH_PERCENTILE_DURATION 	- The 90th percentile of duration of the survey
        16] TOTAL_SESSION_COUNT 	- The total count of sessions for all surveys taken by a respondent
        17] TOTAL_INTERVIEW_COUNT 	- The total surveys taken by a respondent
        18] ALL_COMPLETE_COUNTS		- The count of events marked as complete for a respondent
        19] ALL_QCF_COUNTS		- The count of events that failed the quality checks for a respondent
        20] ALL_SCREENOUT_COUNT		- The count of events that are marked as screen out for a respondent
	21] EMAIL_ADDRESS		- The email address of the respondent
	22] FIRST_NAME			- The first name of the respondent
	23] LAST_NAME			- The last name of the respondent
	24] DOMAIN_NAME			- The domain of the respondent
	25] PANEL_ID			- 
	26] BRAND_ID			- The brand code the respondent is affiliated to
	27] GENDER			- The gender of the respondent
	28] AGE				- The age of the respondent
	29] COUNTRY_CODE		- The country code for the respondent
	30] IS_BAD_EMAIL		- Whether the email is flagged as bad
	31] IS_RESTING			- 
	32] IS_SEED			-
	33] IS_VERIFIED			- Whether the email is flagged as verified
	34] IS_GHOST			- 
	35] IS_ACTIVE			- Whether the email is flagged as active
	36] COMPANY_ID			- The code of the company affiliated with the respondent
	37] DEVICE_TYPE			- The type of device used by the respondent
	38] ENVIRONMENT			- The browser used by the respondent
	39] ARCHIVE_FLAG		- Whether the respondent was archived
	40] IS_QUARANTINED		- Whether the respondent was quarantined
	


	The above features have been acquired as a result of querying the following tables:
	A] ERI_STG.PUBLIC.NOVA_RESPONDENT<br>	
		- RESPONDENT ID, IS_QUARANTINED, 
	B] ERI_STG.PUBLIC.STS_QP_SURVEY_TRANS and
	C] ERI_STG.PUBLIC.STS_QM_EVENTS<br>	
		- These two tables were queried to get aggregate values like - COMPLETE_AVERAGE, COMPLETE_MEDIAN, QCF_AVERAGE, 			QCF_MEDIAN, COMPLETE_COUNT, QCF_COUNT, SCREENOUT_COUNT AND PERCENTILES
	D] ERI_STG.PUBLIC.RESPONDENT_SESSION    - TOTAL_SESSIONS
	       


**B) unique_query.txt**
Unlike the previous query in order to have one-to-one values, 
This SQL Query returns the above data but 36, 37, 38 and 39 and in addition to that returns the count of the count of COMPANY_ID, ENVIRONMENT, DEVICE_TYPE and ARCHIVE_FLAG is recorded in this query for each respondent along with the most frequent occurrence of COMPANY_ID, ENVIRONMENT and DEVICE_TYPE per respondent.


**C) data_file.csv**
This CSV contains the data returned by **A) query.txt**

**D) unique_data_file.csv**
This CSV contains the data returned by **B) unique_query.txt**

**E) logistic_regression.ipnyb**

- This is a jupyter notebook that contains the python code for logistic regression.
- In order to view the data in a tabular format, the pandas library is used.
#### import pandas as pd 
- This gives a row-column structure to the data called as data frame.

- To ensure whether null values are present or not
#### data.isnull() 
- method is used chained with
#### data.isnull() .sum() 
- to give a total count of null values for every feature.



- The data frame is then divided on the basis of whether the respondent is quarantined or not.

#### pos_data = data[data.IS_QUARANTINED == 1].reset_index().fillna(0)
#### neg_data = data[data.IS_QUARANTINED == 0].reset_index().fillna(0)

- pos_data contains all quarantined  respondent. 
- neg_data contains all non-quarantined respondents.
#### p = pos_data['IS_QUARANTINED'].count()
- This is done to get a count of quarantined respondents.

#### p_data = pos_data.sample(n = p, axis = 0).reset_index().fillna(0)
#### n_data = neg_data.sample(n = p, axis = 0).reset_index().fillna(0)
#### merge = pd.concat([p_data, n_data])
- Equal number of quarantined and non-quarantined respondents are then merged into a data frame - merge.
#### features = merge[['TOTAL_SESSIONS','COMPLETE_AVGERAGE','PERCENTILE40','QCF_COUNT', 'SCREENOUT_COUNT','EVENT_COUNT']]
#### target = merge['IS_QUARANTINED']
  - The target variable “IS_QUARANTINED” is then separated from the features into variables target and features respectively.

#### from sklearn.model_selection import train_test_split
#### X_train, X_test, Y_train, Y_test = train_test_split(features,target, test_size=0.3, random_state=0)

 - In order to perform logistic regression, the machine learning library - sklearn is used.
 - The data is split into training and test sets using the train test split method from the sklearn library.
 - The test size is set to 0.3 which would result in 70% of the data to be considered as training whereas 30% as test data.

#### logisticRegr = LogisticRegression(solver = 'lbfgs' )
 - The regressor is then defined using the logistic regression classifier

#### logisticRegr.fit(X_train, Y_train)
 - The fit method trains the training data. This is where the regressor learns about the data
#### Y_pred = logisticRegr.predict(X_test)
 - Based on what the regressor learned above, it is uses to now predict on some unseen data using the features of test set

#### score = logisticRegr.score(X_test, Y_test)
 - This method returns the score / accuracy of the model

#### cm = metrics.confusion_matrix(Y_test, Y_pred)
- We get the values for the confusion matrix 

#### def plot_confusion_matrix
- This method returns a visual representation of the confusion matrix with the normalized form too.
