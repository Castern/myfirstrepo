
The zip contains three files:
**A) Query.txt
B) result_july24.csv
C) code.ipnyb**


A) The **query.txt** consist of the Snowflake SQL query that retrieves 
The following features:
	      1] “RESPONDENT_ID"      - Every respondent’s unique ID
        2] “COMPLETE_AVGERAGE" 	- The average duration of events that have been marked Complete
        3] ”COMPLETE_MEDIAN”  	- The median duration of events that have been marked Complete
        4] "QCF_AVERAGE”    	- The average duration of events that have been marked as Quality Check Fails
        5] “QCF_MEDIAN"  	- The median duration of events that have been marked as Quality Check Fails
        6] “EVENT_COUNT” 	- The total number of distinct events for a respondent
        7] “PERCENTILE10” 	- The 10th percentile of duration of the survey
        8] “PERCENTILE20” 	- The 20th percentile of duration of the survey
        9] “PERCENTILE30” 	- The 30th percentile of duration of the survey
        10] “PERCENTILE40” 	- The 40th percentile of duration of the survey
        11] “PERCENTILE50” 	- The 50th percentile of duration of the survey
        12] “PERCENTILE60” 	- The 60th percentile of duration of the survey
        13] “PERCENTILE70” 	- The 70th percentile of duration of the survey
        14] “PERCENTILE80” 	- The 80th percentile of duration of the survey
        15] “PERCENTILE90” 	- The 90th percentile of duration of the survey
        16] “TOTAL_SESSIONS” 	- The total count of sessions for all surveys taken by a respondent
        17] “TOTAL_INTERVIEWS” 	- The total surveys taken by a respondent
        18] “COMPLETE_COUNT” 	- The count of events marked as complete for a respondent
        19] “QCF_COUNT” 	- The count of events that failed the quality checks for a respondent
        20] “SCREENOUT_COUNT” 	- The count of events that are marked as screen out for a respondent
        21] ”IS_QUARANTINED" 	- The respondents flagged as Quarantined or not Quarantined.


	The above features have been acquired as a result of querying three tables:
	1] ERI_STG.PUBLIC.NOVA_RESPONDENT	- RESPONDENT ID, IS_QUARANTINED
	2] ERI_STG.PUBLIC.STS_QP_SURVEY_TRANS and
	3] ERI_STG.PUBLIC.STS_QM_EVENTS. 	- These two tables were queried to get aggregate values like - COMPLETE_AVERAGE,
						  COMPLETE_MEDIAN, QCF_AVERAGE, QCF_MEDIAN, COMPLETE_COUNT, QCF_COUNT, SCREENOUT_COUNT AND PERCENTILES
	4] ERI_STG.PUBLIC.RESPONDENT_SESSION    - TOTAL_SESSIONS
	       



**B) result_july24.csv**
The features reurned by the above query are stored in this csv. You may re-run the query to get updated results to date and store in a new file.



**C) code.ipnyb**

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
