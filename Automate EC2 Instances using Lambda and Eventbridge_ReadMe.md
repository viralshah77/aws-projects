# Automate EC2 Instances using Lambda and Eventbridge : 

## Story : 
In this project , will  create 2 EC2 instances , 3 Lambda functions and 2 Eventbridge events which will interact with each other in different ways 
1. One Lambda function is to stop instanes manually 

2. Second Lambda function is to start instances manually 

3. Third Lambda function is going to protect EC2 instance against instance stop - as soon as an Instance is stopped , an event created in EventBridge will invoke a Lambda function and will start that instance automatically 

4. Will also create another event via EventBridge which will stop a particular instance at a scheduled time 

## Steps : 
1. Create 1 EC2 instance named "TestEC2Instance_1"
	1.1 Go to "EC2 console and select "Launch instance"
	1.2 Enter the name as "TestEC2Instance_1" and select "Amazon Linux 2" free tier AMI 
	1.3 Keep rest all configuration as it is and click "Launch Instance"

2. Create 1 EC2 instance named "TestEC2Instance_2"
	2.1 Go to "EC2 console and select "Launch instance"
	2.2 Enter the name as "TestEC2Instance_2" and select "Amazon Linux 2" free tier AMI 
	2.3 Keep rest all configuration as it is and click "Launch Instance"

3. Copy the Instance Id of both the instances and ensure both the Instances are in "Running" state 

4. Create an IAM role named "EC2StartStopLambdaRole" for Lambda function to stop/ start EC2 instances as well as ability to write logging information into CloudWatch Logs 
	4.1 Under IAM Console, click on Roles and "Create Role"
	4.2 Select "AWS Service" and then select Lambda. Click on Next 
	4.3 Click on "Create Policy", Select JSON and provide the policy details in there . Click "Next"
	4.4 Under Policy Name , type "LambdaStartAndStop" and click on "CreatePolicy"
	4.5 Go back to previous tab where we create IAM role and select "LambdaStartAndStop" policy we just create. Click on Next 
	4.6 Name the role as "EC2StartStopLambdaRole". So now Lambda can assume this role and can stop/ start Lambda function 
