# Automate EC2 Instances using Lambda and Eventbridge : 

## Story : 
In this project , will  create 2 EC2 instances , 3 Lambda functions and 2 Eventbridge evets which will interact with each other in different ways 
1. One Lambda function is to stop instanes manually 
2. Second Lambda function is to start instances manually 
3. Third Lambda function is going to protect EC2 instance against instance stop - as soon as an Instance is stopped , an event created in EventBridge will invoke a Lambda function and will start that instance automatically 
4. Will also create another event via EventBridge which will stop a particular instance at a scheduled time 

## Steps : 
1. Create 1 EC2 instance named "TestEC2Instance_1"
2. Create 1 EC2 instance named "TestEC2Instance_2"
3. Create an Execution role named " " for Lambda function to stop/ start EC2 instances as well as ability to write logging information into CloudWatch Logs 
	3.1 
