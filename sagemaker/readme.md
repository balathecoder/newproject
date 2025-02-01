# Sagemaker : 
	- fully managed machine learning service that provides the ability build, train, deploy machine learning models quickly.
	- Create endpoints and that can be exposed so that application can use that.

In this exercise, going to train a model with simple csv and that will get deployed in sagemaker.

### 1. Download mobile_price_classification_dataset.csv from below link.
https://github.com/arpita-maji/Mobile-Price-Classification/blob/master/mobile_price_range_data.csv

### 2. Do aws configure and aws login steps
###	3. Setup VSCode
		a. # virtualenv venv
		b. Create requirements.txt with below packages
		 
		
		sagemaker
		scikit-learn
		pandas
		numpy
		Ipykernel

Troubleshooting : 
ValueError: Must setup local AWS configuration with a region supported by SageMaker.
Fixed with adding below entry,

os.environ['AWS_DEFAULT_REGION'] = 'us-east-1'
###	4. Try below code to connect to AWS to create sagemaker session,
	
	import sagemaker
	from sklearn.model_selection import train_test_split
	import boto3
	import pandas as pd
	import os
	os.environ['AWS_DEFAULT_REGION'] = 'us-east-1'
	#sm_boto3 = boto3.client('sagemaker',region_name='us-east-1')
	sm_boto3 = boto3.client('sagemaker')
	sess = sagemaker.Session()
	region = sess.boto_session.region_name
	print('region: ', region)
	bucket = "mobbucket1sagemaker"
	print("using bucket" + bucket)
	print(os.environ['AWS_DEFAULT_REGION'])
	
###	5. Load csv file with pandas,

	
	df = pd.read_csv('mobile_price_range_data.csv')
	features = list(df.columns)
	label = features.pop(-1)
	x = df[features]
	y = df[label]
	
	Note : in above features is input, label is output.
	
###	6. train_test_split() in sklearn package is used to split our data into train and test sets. First, we need to divide our data into features(X) and labels(Y). 
	The dataframe gets divided into X_train, X_test, y_train and y_test. 
	X_train and Y_train sets are used for training and fitting the model. 
	The X_test and y_test sets are used for testing the model if its predicting the right outputs/labels.
	
	X_train, X_test, y_train, y_test = train_test_split(x,y,test_size=0.15,random_state=0)
	
	For example, in this csv file if there are 2000 records, 1700 records will go to train dataset, 300 records will go test dataset.

	Note : X_train is features with all records, y_train is output with all records, X_test is features with all test records, y_test is output with all records 
	X_train : 
	battery_power	blue	clock_speed	dual_sim	fc	four_g	int_memory	m_dep	mobile_wt	n_cores	pc	px_height	px_width	ram	sc_h	sc_w	talk_time	three_g	touch_screen	wifi	
	1452	1450	0	2.1	0	1	0	31	0.6	114	5	4	1573	1639	794	11	5	9	0	1	1
	1044	1218	1	2.8	1	3	0	39	0.8	150	7	14	1122	1746	1667	10	0	12	0	0	0
	
	y_train :
	1452    1
	1044    1
	1279    3
	674     0
	1200    0
	       ..
	835     3
	1216    1
	1653    3
	559     0
	684     1
	
	X_test
	battery_power	blue	clock_speed	dual_sim	fc	four_g	int_memory	m_dep	mobile_wt	n_cores	pc	px_height	px_width	ram	sc_h	sc_w	talk_time	three_g	touch_screen	wifi	
	405	1454	1	0.5	1	1	0	34	0.7	83	4	3	250	1033	3419	7	5	5	1	1	0
	1190	1092	1	0.5	1	10	0	11	0.5	167	3	14	468	571	737	14	4	11	0	1	0
	
	y_test
	405     3
1190    0
1132    2
731     2
1754    2
	
	Name: price_range, Length: 1700, dtype: int64
	
###	7. Save train and test data to a csv
	
	trainX = pd.DataFrame(X_train) # saving train data to trainX
	trainX[label] = y_train # adding output to label
	testX = pd.DataFrame(X_test) # saving test data to testX
	testX[label] = y_test # adding output to label
	
###	8. Now the trainX and testX datas are,
	trainX.head()
	battery_power	blue	clock_speed	dual_sim	fc	four_g	int_memory	m_dep	mobile_wt	n_cores	pc	px_height	px_width	ram	sc_h	sc_w	talk_time	three_g	touch_screen	wifi	price_range	
	1452	1450	0	2.1	0	1	0	31	0.6	114	5	4	1573	1639	794	11	5	9	0	1	1	1
	1044	1218	1	2.8	1	3	0	39	0.8	150	7	14	1122	1746	1667	10	0	12	0	0	0	1
	
	testX.head()
	battery_power	blue	clock_speed	dual_sim	fc	four_g	int_memory	m_dep	mobile_wt	n_cores	pc	px_height	px_width	ram	sc_h	sc_w	talk_time	three_g	touch_screen	wifi	price_range	
	405	1454	1	0.5	1	1	0	34	0.7	83	4	3	250	1033	3419	7	5	5	1	1	0	3
	1190	1092	1	0.5	1	10	0	11	0.5	167	3	14	468	571	737	14	4	11	0	1	0	0
###	9. Save trainX and testX to csv files,
	
	trainX.to_csv('train-V-1.csv',index=False)
	testX.to_csv('test-V-1.csv', index=False)
	
###	10. send data to S3. Sagemaker will take training data from s3
	# send data to S3. Sagemaker will take training data from s3
	sk_prefix = 'sagemaker/mobile_price_classification/sklearncontainer'
	trainpath = sess.upload_data(
	    path="train-V-1.csv", bucket=bucket, key_prefix=sk_prefix
	)
	testpath = sess.upload_data(
	    path="test-V-1.csv",bucket=bucket, key_prefix=sk_prefix
	)
![image](https://github.com/user-attachments/assets/619d36e3-06f3-4b61-9081-e1fc63406f7e)
