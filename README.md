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


## Step 2: Set up Amazon CloudWatch target (for development work)

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
    6.2 Name your log group **/aws/events/orders**
    

   
