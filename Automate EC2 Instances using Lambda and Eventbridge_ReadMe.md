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
	4.3 Click on "Create Policy", Select JSON and provide the policy details in there
	{
	    "Version": "2012-10-17",
	    "Statement": [
	      {
		"Effect": "Allow",
		"Action": [
		  "logs:CreateLogGroup",
		  "logs:CreateLogStream",
		  "logs:PutLogEvents"
		],
		"Resource": "arn:aws:logs:*:*:*"
	      },
	      {
		"Effect": "Allow",
		"Action": [
		  "ec2:Start*",
		  "ec2:Stop*"
		],
		"Resource": "*"
	      }
	    ]
	  }
	4.4 Click "Next"
	4.5 Under Policy Name , type "LambdaStartAndStop" and click on "CreatePolicy"
	4.6 Go back to previous tab where we create IAM role and select "LambdaStartAndStop" policy we just create. Click on Next 
	4.7 Name the role as "EC2StartStopLambdaRole". So now Lambda can assume this role and can stop/ start Lambda function 

5. Create a Lambda function to ***Stop instances***
	5.1 Click on "Create Function" and "Author From Scratch" 
	5.2 Select Function Name as "EC2Stop"
	5.3 Select Python(latest function) under Runtime 
	5.4 Under "Change default Execution Role", select "Use an existing Role" and select "EC2StartStopLambdaRole" which we have created earlier
	5.5 Create Function
	5.6 Under lambda_function.py, right click and select Open -- and paset the below Lambda 
		import boto3
		import os
		import json

		region = 'us-east-1'
		ec2 = boto3.client('ec2', region_name=region)

		def lambda_handler(event, context):
    			instances=os.environ['EC2_INSTANCES'].split(",")
    			ec2.stop_instances(InstanceIds=instances)
    			print('stopped instances: ' + str(instances))
	5.7 Click on Deploy
	5.8 Now Click on Configuration --> Environment Variables 
	5.9 Under Key, Enter "EC2_INSTANCES" and Value as "EC2 Instance Ids of the 2 EC2 instances followed by comma". Click on "Save"
	5.10 Click on "Test" to test the Lambda Function . Give the event name as "TestEC2Stop" and click on Test -- ***This will stop the 2 EC2 instances***
	5.11 Go to EC2 instance and check the statuses - it should eventually Stopped for both the instances 

5. Create a Lambda function to ***Start instances***
	5.1 Click on "Create Function" and "Author From Scratch" 
	5.2 Select Function Name as "EC2Start"
	5.3 Select Python(latest function) under Runtime 
	5.4 Under "Change default Execution Role", select "Use an existing Role" and select "EC2StartStopLambdaRole" which we have created earlier
	5.5 Create Function
	5.6 Under lambda_function.py, right click and select Open -- and paset the below Lambda 
		import boto3
		import os
		import json

		region = 'us-east-1'
		ec2 = boto3.client('ec2', region_name=region)

		def lambda_handler(event, context):
    			instances=os.environ['EC2_INSTANCES'].split(",")
    			ec2.start_instances(InstanceIds=instances)
    			print('started instances: ' + str(instances))
	5.7 Click on Deploy
	5.8 Now Click on Configuration --> Environment Variables 
	5.9 Under Key, Enter "EC2_INSTANCES" and Value as "EC2 Instance Ids of the 2 EC2 instances followed by comma". Click on "Save"
	5.10 Click on "Test" to test the Lambda Function . Give the event name as "TestEC2Start" and click on Test -- ***This will start the 2 EC2 instances***
	5.11 Go to EC2 instance and check the statuses - it should eventually Start for both the instances and the Instance State should be in "Running"

6. Create an event which will get executed in case an Instance gets Stopped and will get restart on its own 
	6.1 Click on "Create Function" and "Author From Scratch" 
	6.2 Select Function Name as "EC2Protect"
	6.3 Select Python(latest function) under Runtime 
	6.4 Under "Change default Execution Role", select "Use an existing Role" and select "EC2StartStopLambdaRole" which we have created earlier
	6.5 Create Function
	6.6 Under lambda_function.py, right click and select Open -- and paset the below Lambda 
	6.1 Click on "Create Function" and "select From Scratch" 
	6.2 Select Function Name as "EC2Start"
	6.3 Select Python(latest function) under Runtime 
	6.4 Under "Change default Execution Role", select "Use an existing Role" and select "EC2StartStopLambdaRole" which we have created earlier
	6.5 Create Function
	6.6 Under lambda_function.py, right click and select Open -- and paset the below Lambda 
		import boto3
		import os
		import json

		region = 'us-east-1'
		ec2 = boto3.client('ec2', region_name=region)

		def lambda_handler(event, context):
    			print("Received event: " + json.dumps(event))
    			instances=[ event['detail']['instance-id'] ]
    			ec2.start_instances(InstanceIds=instances)
    			print ('Protected instance stopped - starting up instance: '+str(instances))
	6.7 Click on Deploy
	6.8 Create an event in ***EventBridge*** 
	6.9 Under "Amazon EventBridge" , select "Create Rule"  
	6.10 Name the Rule as "EC2Protect" , provide the Description as "Start Protected Instance" 
	6.11 Under Rule type, select "Rule with an event pattern" , click Next 
	6.12 EventSource should be "AWS events or EventBridge partner events
	6.13 Under "Event Pattern" under
		Event Source --> AWS services
		AWS Service --> EC2
		Event type --> EC2 Instance State-change Notification
		Specific states --> Stopped
		Specific instance Id(s) -- (Provide any one of the Instance Id" 	
	6.14 Under Sample events , select "EC2 Instance State-change Notification
	6.15 Click Next
	6.16 Under Target types --> AWS Service --> select "Lambda function" . Function --> EC2Protect 
	6.17 Click Next. Click on "Create Rule"
	
###### 7. Testing
	7. Go to EC2 instabce and stop the instance whose Instance Id is been provided in the Eventbridge 
	Note that the event will not allow the instane to be in Stopped state and will restart immediately and will be in Running state 

###### Check the CloudWatch Logs 
	Go to CloudWatch , under "og groups", select /aws/lambda/Ec2Protect 
	check the Log evens and it will detail you the event which got generated 
	

## Create a Scheduled event which will get executed in case an Instance gets Stopped and will get restart on its own 

	7.
