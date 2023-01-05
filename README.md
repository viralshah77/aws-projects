# Building an Event-driven architecture

Steps 

![image](https://user-images.githubusercontent.com/73116712/210787726-5bd9e317-878e-4ed6-a1a6-a6ce2c46114c.png)

## First event bus and targets

In this module, you will create a custom EventBridge event bus, Orders, and an EventBridge rule, OrderDevRule, which matches all events sent to the Orders event bus and sends the events to a CloudWatch Logs log group, /aws/events/orders. See the diagram above:

The technique of logging all events to CloudWatch Logs is useful when implementing EventBridge rules.

## Step 1: Create a custom event bus

1. Open the AWS Management Console for EventBridge  in a new tab or window, so you can keep this step-by-step guide open.
2. On the EventBridge homepage, under Events, select Event buses from the left navigation.
3. Click Create event bus.
4. Name the event bus Orders.
5. Leave Event archive and Schema discovery disabled, Resource-based policy blank.
6. Click Create.


## Step 2: Set up Amazon CloudWatch target 

A simple way to test, and get rapid feedback for, the rules you create for your event bus is to use Amazon CloudWatch as a target. We will create a rule for the Orders bus that will act as a "catch-all" for every event passed to the bus, irrespective of source.

1. From the left-hand menu, select Rules.
2. From the Event bus dropdown, select the **Orders** event bus
3. Click Create rule
4. Define rule detail:
    4.1 Add **OrdersDevRule** as the Name of the rule
    4.2 Add **Catchall rule for development purposes** for Description
    4.3 Select **Rule with an event pattern** for the Rule type
5. In the next step, **Build event pattern**
    5.1 Under Event source, select **Other**
    5.2 Under Event pattern, further down the screen, enter the following pattern to catch all events from **com.aws.orders**:
        {
          "source": ["com.aws.orders"]
        }
    5.3 Click Next
6. Select your rule target:
    6.1 From the Target dropdown, select **CloudWatch log group**
    6.2 Name your log group /aws/events/**orders**
    
7. Skip through the configure tags section, review your rule configuration and click Create.

## Step 3: Test your rule

1. Select the EventBridge tab in the ***Event Generator .***
2. Make sure that the Event Generator is populated with the following (if you clicked the link above then you should see this pre-populated):
   2.1 AWS Region should be to the region in which you are running the workshop
   2.2 Event Bus selected to Orders
   2.3 Source should be com.aws.orders
   2.4 In the Detail Type add Order Notification
   2.5 JSON payload for the Detail Template should be:

        {
            "category": "lab-supplies",
             "value": 415,
             "location": "eu-west"
        }
 3. Click Publish.
 4. Open the AWS Management Console for CloudWatch  in a new tab or window, so you can keep this step-by-step guide open.
 5. Choose Log groups in the left navigation and select the /aws/events/orders log group.
 6. Select the Log stream.
 7. Toggle the log event to verify that you received the event.


## Step 4: Review event structure
In the following sections, you will use event data to implement EventBridge custom rules to route events. Due to the **OrdersDevRule** that you created in this section, all events to the **Orders** event bus will be sent to CloudWatch Logs, which you can use to view sample data in order to implement and troubleshoot rules matching logic.

OK, now that we have our first target configured, let's configure some more targets for our Orders event bus. In the next section, Working with EventBridge Rules, you will look more closely at how to use the event pattern matching and integration with other services.


## Working with EventBridge rules

![image](https://user-images.githubusercontent.com/73116712/210816559-e5a27a8b-56c7-4087-9f88-e2ad3fdaf889.png)


Rules match incoming events and routes them to targets for processing. A single rule can route to multiple targets, all of which are processed in parallel. Rules aren't processed in a particular order. A rule can customize the JSON sent to the target, by passing only certain parts or by overwriting it with a constant. EventBridge supports 28+ AWS service targets!

In this module, you will walk through the steps to create an Orders event bus rule to match an event with a com.aws.orders source and to send the event to an Amazon API Gateway endpoint, invoke a AWS Step Function, and send events to an Amazon Simple Notification Service (Amazon SNS) topic.

The event bus targets (API Gateway endpoint, AWS Step Functions state machine, and Amazon SNS topic) have been provisioned for you. The goal is for you to write event bus rules to match events and verify delivery to the appropriate target.


It is important to remember the following about event pattern matching:

1. For a pattern to match an event, the event must contain all the field names listed in the pattern. The field names must appear in the event with the same nesting structure.
2. Other fields of the event not mentioned in the pattern are ignored; effectively, there is a "*" : "*" wildcard for fields not mentioned.
3. The matching is exact (character-by-character), without case-folding or any other string normalization.
4. The values being matched follow JSON rules: Strings enclosed in quotes, numbers, and the unquoted keywords true, false, and null.
5. Number matching is at the string representation level. For example, 300, 300.0, and 3.0e2 are not considered equal.

## Next steps
OK, now that you have explored EventBridge Rule patterns, let's begin the first challenge.
        

# API Destination Challenge

Challenge goal
Process all events for source com.aws.orders via an Amazon EventBridge API Destination. In this use case we are demonstrating how you might integrate EventBridge with third party public API endpoints using an outbound webhook with Basic Auth configured.

## Step 1: Identify the API URLHeader anchor link
1. Open the AWS Management Console for CloudFormation . You can find the API URL for this challenge in the **Outputs** of the CloudFormation Stack with a name containing *ApiUrl*.

## Step 2: Configure the EventBridge API Destination with basic auth security
1. Open the AWS Management Console for EventBridge  in a new tab or window, so you can keep this step-by-step guide open.
2. On the EventBridge homepage, select API destinations from the left navigation.
3. On the API destinations page, select Create API destination.
4. On the Create API destination page
    4.1 Enter api-destination as the Name
    4.2 Enter the API URL identified in Step 1 as the API destination endpoint
    4.3 Select POST as the HTTP method
    4.4 Select Create a new connection for the Connection
    4.5 Enter basic-auth-connection as the Connection name
    4.6 Select Basic (Username/Password) as the Authorization type
    4.7 Enter myUsername as the Username
    4.8 Enter myPassword as the Password
5. Click Create

## Step 3: Configure an EventBridge rule to target the EventBridge API DestinationHeader anchor link
1. From the left-hand menu, select Rules.
2. From the Event bus dropdown, select the Orders event bus.
3. Click Create rule
4. On the Define rule detail page
    4.1 Enter OrdersEventsRule as the Name of the rule
    4.2 Enter Send com.aws.orders source events to API Destination for Description
5. Under Build event pattern
    5.1 Choose Other for your Event source
    5.2 Copy and paste the following into the Event pattern, and select Next to specify your target:
6. Select your rule target:
    6.1 Select EventBridge API destination as the target type.
6.2 Select api-destination from the list of existing API destinations
7. Click Next and finish walking through the rest of the walk-through to create the rule.

## Step 4: Send test Orders event
Below is sample data to test your rule, with a link to open the sample data in the Event Generator. If you don't remember how to use Event Generator to send an event, please refer to the previous section.

Using the Event Generator, send the following Order Notification events from the source com.aws.orders:
    { "category": "lab-supplies", "value": 415, "location": "us-east" }

## Step 5: Verify API Destination
If the event sent to the Orders event bus matches the pattern in your rule, then the event will be sent to an API Gateway REST API endpoint.

1. Open the AWS Management Console for CloudWatch Log Groups  in a new tab or window, so you can keep this step-by-step guide open.
2. Select the Log group with an API-Gateway-Execution-Logs prefix.
3. Select the Log stream.
4. Toggle the log event to verify the basic authorization was successful and the API responds with a 200 status.

Congratulations! You have successfully created your first custom event. We will now present additional challenges for you to complete. A description of the goal, sample data, and verification steps have been provided, but it is up to you to write the correct event pattern. Remember, use CloudWatch Logs to troubleshoot your rule implementation, if you are not able to verify your rule.

## Step Functions Challenge
*Challenge goal*
Process only orders from locations in the EU (eu-west or eu-east) using a AWS Step Functions target (OrderProcessing). In this use case, we are demonstrating how a Step Function execution can be triggered to process orders as they are published by the Orders bus.

## Step 1: Implement an EventBridge rule to target Step Functions

Use the EventBridge Console to:
1. Add a rule to the Orders event bus with the name EUOrdersRule
2. Define an event pattern to match events with a detail location in eu-west or eu-east
3. Target the OrderProcessing Step Functions state machine

## Step 2: Send test EU Orders eventsHeader anchor link
Using the Event Generator, send the following Order Notification events from the source com.aws.orders:

{ "category": "office-supplies", "value": 300, "location": "eu-west" }
{ "category": "tech-supplies", "value": 3000, "location": "eu-east" }

## Step 3: Verify Step Functions workflow executionHeader anchor link
If the event sent to the Orders event bus matches the pattern in your rule, then the event will be sent to the OrderProcessing Step Functions state machine for execution.

1. Open the AWS Management Console for Step Functions  in a new tab or window, so you can keep this step-by-step guide open.
2. On the Step Functions homepage, open the left hand navigation and select State machines.
3. Enter OrderProcessing in the Search for state machines box and verify the state machine execution has succeeded.

The Step Functions state machine will publish an OrderProcessed event back to the Orders event bus, using a new Service Integration for EventBridge which provides a simpler solution for producing events during a workflow execution. We will make use of this event later in the workshop. If you would like to see more detail on the workflow execution, select OrderProcessing from the list of state machines, and then select the workflow execution from the list.

*Congratulations! You have successfully completed the Step Functions Challenge.*


## SNS Challenge
Challenge goal
Process only orders from US locations (us-west or us-east) that are lab-supplies using a Amazon SNS target (Orders). Similar to the previous use case, but using SNS.

## Step 1: Implement an EventBridge rule to target SNS

Use the EventBridge Console to:
1. Add a rule to the Orders event bus with the name USLabSupplyRule
2. With an event pattern to match events with a detail location in us-west or us-east, and a detail category with lab-supplies.
3. Target the Orders SNS topic

## Step 2: Send test US Orders events
One of the following events should match the event rule pattern and one should not. Use CloudWatch Logs to verify events that were successfully sent to EventBridge but were not delivered to the target.

Using the Event Generator, send the following Order Notification events from the source com.aws.orders:
{ "category": "lab-supplies", "value": 415, "location": "us-east" }
{ "category": "office-supplies", "value": 1050, "location": "us-west", "signature": [ "John Doe" ] }

## Step 3: Verify SNS topic
If the event sent to the Orders event bus matches the pattern in your rule, then the event will be sent to the Orders SQS Queue (via Orders SNS Topic).
1. Open the AWS Management Console for SQS  in a new tab or window, so you can keep this step-by-step guide open.
2. On the SQS homepage, select the Orders queue.
3. Select the Send and receive messages button.
4. Select Poll for Messages and verify the first message was delivered and the second was not.
5. To clean up, select the event, select the Delete button, and select the Delete button again on the Delete Messages confirmation dialog.

Congratulations! You have successfully completed the SNS Challenge.

## Next steps
OK, now that you have explored EventBridge Rule patterns, let's look at how we can generate events on a scheduled basis.

## Scheduling expressions for rules

You can create rules that self-trigger on an automated schedule in EventBridge using cron or rate expressions. All scheduled events use UTC time zone and the minimum precision for schedules is 1 minute.

EventBridge supports cron expressions and rate expressions. Rate expressions are simpler to define but don't offer the fine-grained schedule control that cron expressions support. For example, with a cron expression, you can define a rule that triggers at a specified time on a certain day of each week or month. In contrast, rate expressions trigger a rule at a regular rate, such as once every hour or once every day.

Let's give it a try!

## Step 1: Create a scheduled Orders Reconciliation rule
1. Open the AWS Management Console for EventBridge  in a new tab or window, so you can keep this step-by-step guide open.
2. On the EventBridge homepage, select Rules from the left-hand menu.
3. Select the default event bus and click the Create rule button.
4. On the Create rule page:
    4.1 Add OrdersReconciliation as the Name of the rule
    4.2 Add Runs reconciliation routine on orders every minute Monday to Friday for Description
    4.3 Select Schedule for Rule type
    4.4 As we are still using the previous scheduler, you should click on the Continue to create rule button.
5. In Schedule Pattern select the left option, which represnts a cron expression.
6. Add ***"* * ? * MON-FRI *"** in the Cron expression.

This will schedule a message to be delivered every minute, Monday through Friday.

7. Configure your target to be a CloudWatch log group
    7.1 Name the log group /aws/events/orders_reconciliation
8. Click Next and finish walking through the rest of the walk-through to create the rule.

## Step 2: Verify scheduled message delivery
1. Open the AWS Management Console for CloudWatch  in a new tab or window, so you can keep this step-by-step guide open.
2. Choose Log groups in the left navigation and select the /aws/events/orders_reconciliation log group.
3. After a few minutes your streams for /aws/events/orders_reconciliation should look similar to this:

Congratulations! You have just completed your first scheduled expression for an Amazon EventBridge rule. Next, we are going to explore how to create EventBridge partner event sources.


## Event-driven with Lambda
When a function is invoked asynchronously, Lambda sends the event to an internal queue. A separate process reads events from the queue and executes your Lambda function.

A common event-driven architectural pattern is to use a queue or message bus for communication. This helps with resilience and scalability. Lambda asynchronous invocations can put an event or message on Amazon Simple Notification Service (SNS), Amazon Simple Queue Service (SQS), or Amazon EventBridge for further processing.

With Destinations, you can route asynchronous function results as an execution record to a destination resource without writing additional code. An execution record contains details about the request and response in JSON format including version, timestamp, request context, request payload, response context, and response payload. For each execution status such as Success or Failure you can choose one of four destinations: another Lambda function, SNS, SQS, or EventBridge.

Module information
This module extends the AWS Step Functions challenge you completed in Working with EventBridge rules. If you haven't completed this, do so now. The Inventory function and the Inventory event bus have already been create for you.

## Step 1: Configure the Inventory event bus
Configure the Inventory event bus as a successful Lambda Destination on InventoryFunction Lambda function
1. Open the AWS Management Console for Lambda in a new tab or window, so you can keep this step-by-step guide open.
2. Select Functions in the left navigation.
3. Enter InventoryFunction in the Lambda function filter. And select the function name filter when it shows up.
4. Select the Lambda function from the list of functions.
5. In the Lambda function Designer, choose Add destination.
6. In the Add destination dialogue box:
    6.1 For Condition, select On success.
    6.2 For Destination type, select EventBridge event bus.
    6.3 For Destination, select Inventory.
7. Choose Save.

The Lambda function must have permissions through its IAM Execution Policy to access the success and failure Destinations. For simplicity, the function's IAM Execution Policy has been configured with access to EventBridge and SQS (see below).

## Step 2: Create the "Order Processed" rule for the Inventory function
The Inventory function subscribes to events that signal the processing of the order has been completed successfully. The OrderProcessed event is published by the Step Function you created in the AWS Step Functions challenge.

1. Create a rule on the Orders event bus called OrderProcessingRule
2. Add a description: Handles orders successfully processed by the Order Processing state machine
3. Define a rule pattern
{
    "source": [
        "com.aws.orders"
    ],
    "detail-type": [
        "Order Processed"
    ]
}

4. Configure the rule target to point to the Inventory function.

## Step 3: Testing the end-to-end functionality
To test the end-to-end functionality, you will publish a message to the Orders EventBridge event bus with an EU location. This will trigger the following sequence of event-driven actions:

1. The message containing an EU location triggers the rule on the Orders event bus, which you created in the previous module, to route the message to the OrderProcessing StepFunctions workflow.
2. The OrderProcessing StepFunctions workflow processes the order and publishes an event to the Orders event bus.
3. The OrderProcessingRule that you just created on the Orders event bus routes the event to the Inventory function.
4. On successful execution, the InventoryFunction function sends an event to the Inventory event bus through the On success Lambda destination.
5. The InventoryDevRule rule on the Inventory event bus logs the event to the /aws/events/inventory CloudWatch Logs log group.

# Let's test it!
1. Select the EventBridge tab in the Event Generator .
2. Make sure that the Event Generator is populated with the following (if you clicked the link above then you should see this pre-populated)
    2.1 Event Bus selected to Orders
    2.2 Source should be com.aws.orders
    2.3 In the Detail Type add Order Notification
    2.4 JSON payload for the Detail Template should be:
        {
         "category": "office-supplies",
         "value": 1200,
         "location": "eu-west"
        }
3. Click Publish.
4. Open the AWS Management Console for CloudWatch  in a new tab or window, so you can keep this step-by-step guide open.
5. Choose Log groups in the left navigation.
6. Enter /aws/events/inventory in the Log Group Filter and choose /aws/events/inventory log group.
7. In the list of Log Streams, choose the Log Stream.
8. Expand the Log Stream record to verify success and explore the event schema.

Congratulations! You have successfully used Lambda Destinations to send a message to the Inventory EventBridge event bus, following message processing through EventBridge, Step Functions, and SNS.
