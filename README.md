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


